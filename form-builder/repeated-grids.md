# Repeated grids

## Introduction

Form Builder supports grids with repeated rows. You can repeat a single row (which is the most common case), or multiple heterogeneous (that is with different controls) rows.

## Creating a repeated grid

You insert a new repeated grid with the "New Repeated Grid" toolbox button.

Once the grid is inserted, you can add and remove (using the grid arrow and trash icons which appear on mouseover) columns and rows, and add controls to grid cells as you would in a regular non-repeated grid.

With only one row, the control labels are used as column headers and are not repeated within the grid. If present, control hints are also added to the column headers. That single row is repeatable with the "plus" icon.

![Repeating a single row](images/repeated-grid-single.png)

You can add multiple heterogeneous rows with the arrow icons. In this case the entire group of rows is repeatable. Control labels and hints do not appear in column headers, but appear alongside the controls in the grid.

![Repeating multiple rows](images/repeated-grid-multiple.png)

Whether there is a single or multiple repeatable rows, you can add and remove repetitions (iterations) of those rows with the "plus" icon. You typically let the user add iterations at runtime, but it is possible to create iterations in advance at design time as well.

## How things look at runtime

At runtime, notice how in the first grid a single row is repeated, and in the second grid the two rows are repeated.

![Repeated grids](../form-runner/images/repeated-grids.png)

In review mode and PDF mode, icons and menus disappear and the grid appears entirely readonly.

![Repeated grids in review mode](../form-runner/images/repeated-grids-view.png)

![Repeated grids in PDF mode](../form-runner/images/repeated-grids-pdf.png)

## Grid settings

Once a grid is inserted, you can edit its properties with the "Grid Settings" icon.

![Grid Settings](images/repeated-grid-settings-icon.png)

### Formulas

The "Visibility" and "Read-Only" formulas control whether the entire grid (including it's headers if any) is visible at all or whether its content is entirely readonly.

*NOTE: Since Orbeon Forms 4.8, these settings properly apply to the entire grid. Previously, the grid's repeat headers did not hide properly for example when the grid was hidden. See issue [#635](https://github.com/orbeon/orbeon-forms/issues/635).*

![Basic Settings and Formulas](images/repeated-grid-settings-basic.png)

### Repeat settings

#### Overview

The repeat settings control whether to use a custom iteration name (not recommended in most cases), and the minimum/maximum number of repeat iterations allowed.

![Repeated Content](images/repeated-grid-settings-repeat.png)

#### Minimum and maximum number of iterations

These settings can be predefined numbers, or formulas.

#### Freeze repetitions

[SINCE Orbeon Forms 2018.2]

This setting can be a predefined number, or a formula.

This allows *freezing* the first *n* iterations of a repeated grid or repeated section. Frozen iterations cannot be removed or moved by the user. The grid menus and buttons reflect that those operations are not possible.

The number of frozen iterations must be at most the number of minimal iterations.

#### Initial value formulas

[SINCE Orbeon Forms 2016.1]

The "Apply Initial Value Formulas when Adding Iterations" option specifies whether the "Initial Value" formulas for controls within the grid are evaluated for new iterations.

With Orbeon Forms 2016.1, the option is enabled by default for new forms and new repeated grids. The option is disabled by default for grids created with previous versions of Orbeon Forms.

With the option enabled, new iterations can have dynamic initial values:

![Initial Values](images/iterations-initial-values.png)

#### Initial number of iterations uses template

[SINCE Orbeon Forms 2016.1]

The "Initial Number of Iterations Uses Template" option specifies, when an *enclosing repeated section* creates a new iteration, how many iterations this repeated grid will contains:

- when enabled: the number of iterations shown in Form Builder (which can be no iterations at all, one iteration, two iterations, etc.)
- when disabled: exactly one iteration

The following screenshot shows a case with a repeated grid within nested repeated sections. At first, when the form shows, there are two iterations of the repeated grid.

![](images/iterations-initial.png)

With the option enabled on the grid, adding a new iteration of _Repeated section 2_ causes the new iteration to contain a new repeated grid with two iterations:

![](images/iterations-template.png)

While, with the option disabled on the grid, adding a new iteration of _Repeated section 2_ causes the new iteration to contain a new repeated grid a single iterations:

![](images/iterations-single.png)

<!--

Example:

![Initial Iterations](images/)
-->

## See also

- [Section settings](section-settings.md)
- Support for repeats lands in Form Builder: [older blog post](https://blog.orbeon.com/2012/04/support-for-repeats-lands-in-form.html)
- Inserting and reordering grid rows: [blog post](https://blog.orbeon.com/2013/11/inserting-and-reordering-grid-rows.html)
- Repeated sections: [blog post](https://blog.orbeon.com/2014/01/repeated-sections.html)
- Repeated grids and sections just got more subtle: [blog post](https://blog.orbeon.com/2015/10/repeated-grids-and-sections-just-got.html)
