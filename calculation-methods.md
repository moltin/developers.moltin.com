# Calculation methods

There are two methods that may be used to calculate cart and order totals. The method used depends on the `calculation_method` [setting](https://docs.moltin.com/api/advanced/settings). The default is currently the `simple` method and this will continue to be the case for all stores currently using this method. In future, new stores will use the `line` method by default.

## The `simple` calculation method 

1. Taxes are calculated for each line and are rounded to the nearest penny for the unit.
2. The cart totals are calculated by summing each of the lines and then subtracting any discounts.

Example:

|  Unit Price | Quantity | Line Price | Tax Rate | Unit Tax | Line Tax | Unit Subtotal | Line Subtotal | Total |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| $9.99 | 3 | $29.97 | 0.20 | $1.99 | $5.97 | $9.99 | $29.97 | $35.94 |
| $0.03 | 1 | $0.03 | 0.00 | $0.00 | $0.00 | $0.03 | $0.03 | $0.03 |
| −$10.00 | 1 | −$10.00 | - | - | - | - | - | -$10.00 |
|  |  |  |  | Total Tax | $5.97 |  | Total | $25.97 |

In summary, discounts are applied after tax and rounding occurs at the individual unit level.

## The `line` calculation method

1. Any cart-level discount is distributed among the cart items such that the amount each line gets is in proportion to its value \(before tax\).
2. Discounts are calculated for each line by subtracting from the pre-tax value and the discount is rounded to the nearest penny for the line.
3. Taxes are calculated for each line based on the line value after discounts have been applied and are rounded to the nearest penny for the line.
4. The cart totals are calculated by summing each of the lines.

Example:

| Unit Price | Quantity | Line Price | Unit Discount | Line Discount | Tax Rate | Unit Tax | Line Tax | Unit Subtotal | Line Subtotal | Total |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| $9.99 | 3 | $29.97 | −$3.33 | −$9.99 | 0.20 | $1.3333r | $4.00 | $5.3266666667 | $19.97 | $23.97 |
| $0.03 | 1 | $0.03 | −$0.01 | −$0.01 | 0.00 | $0.00 | $0.00 | $0.02 | $0.02 | $ 0.02 |
|  |  |  |  |  |  | Total tax | $4.00 |  | Total | $23.99 |

In summary, discounts are applied before tax and rounding occurs for whole lines.



