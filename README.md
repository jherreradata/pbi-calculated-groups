# Power BI – Calculated Groups for Time Comparisons

This repository contains a Power BI example demonstrating how to use **Calculation Groups** to handle time comparisons (current month, previous month, and month-over-month difference) without duplicating measures across your model.

## The Problem

A common pattern in Power BI development is creating one measure per time comparison per metric:

- `Sales Current Month`
- `Sales Previous Month`
- `Sales MoM Difference`
- `Units Current Month`
- `Units Previous Month`
- `Units MoM Difference`
- ...

This approach works, but it doesn't scale. Every new metric means three new measures, and any change to the time logic requires updating each one individually.

## The Solution

A single Calculation Group with three calculation items replaces all those duplicated measures:

| Calculation Item | Description |
|---|---|
| `MesPuntual` | Value for the selected month |
| `MesAnterior` | Value for the previous month |
| `DifMesAnterior` | Difference between current and previous month |

The key insight is that `DifMesAnterior` doesn't reimplement the previous month logic — it references `MesAnterior` directly from within the calculation group using `CALCULATE` with a filter on the group column. This means the logic lives in one place only.

```dax
DifMesAnterior =
VAR _Actual = SELECTEDMEASURE()
VAR _MesAnterior = CALCULATE(
    SELECTEDMEASURE(),
    'GrupoCalculo'[InteligenciaTemporal] = "MesAnterior"
)
RETURN _Actual - _MesAnterior
```

When a new base measure is added to the model (e.g. `Margin €`), the calculation group applies automatically — no additional measures needed.

## Requirements

- Power BI Desktop (any recent version)
- [Tabular Editor 2](https://github.com/TabularEditor/TabularEditor/releases) or [Tabular Editor 3](https://tabulareditor.com/) to view and edit the calculation group (Power BI Desktop does not support creating calculation groups natively)

## Repository Contents

```
pbi-calculated-groups/
│
├── CalculatedGroups.pbix   # Power BI file with the working example
└── README.md
```

## How to Use

1. Download the `.pbix` file and open it in Power BI Desktop.
2. Explore the report page to see `Ventas Und.` and `Ventas €` compared month by month with the difference.
3. Open the model in Tabular Editor to inspect the calculation group structure and DAX expressions.
4. Try adding a new base measure to the model and drop it into the visual — the calculation group will handle the rest.

## A Note on Limitations

This approach works well when you want to apply the same logic uniformly across all measures. If you need a calculation item to behave differently depending on which measure is being evaluated, you will need more advanced DAX using `ISSELECTEDMEASURE()` or conditional logic inside the calculation item itself.

That scenario is covered in a separate post and repository.

## Related

- LinkedIn post (in Spanish): [Grupos de cálculo en Power BI: una medida, muchas comparativas](https://www.linkedin.com/in/jherreradata)
- Repository: [https://github.com/jherreradata/pbi-calculated-groups](https://github.com/jherreradata/pbi-calculated-groups)

---

*Part of a series on practical Power BI and DAX patterns. Contributions and questions welcome.*
