# RPT6000 (COBOL Program)
**Table of Contents**

- [Summary](#summary)
- [Report Logic](#report-logic)
- [Program Flow](#program-flow)
- [Output Example](#output-example)
- [Maintainers](#maintainers)

## Summary

RPT6000 is a COBOL batch reporting program that reads the Customer Master File and Sales Rep Master File to produce a formatted Year-to-Date Sales Report. The report includes each customer's current and prior year sales figures, along with a calculated change amount and change percent for performance comparison.

The report is organized in a two-level control-break hierarchy — Branch → Sales Rep → Customer — with subtotals printed at each level and a final grand total at the end.


### Report Logic

For each customer, the program calculates:

| Field | Calculation |
|-------|-------------|
| Change Amount | CM-SALES-THIS-YTD minus CM-SALES-LAST-YTD |
| Change Percent | (Change Amount * 100) / CM-SALES-LAST-YTD, rounded |

Special cases for Change Percent:
- If the last YTD value is zero, outputs `N/A`
- If the result overflows the picture clause, outputs `OVRFLW`

The same logic is applied at the Sales Rep total, Branch total, and Grand total levels. Grand total uses `999.9` instead of `OVRFLW` for overflow.

Sales Rep names are looked up from an in-memory table (up to 100 entries) loaded at startup from the Sales Rep Master File. If a sales rep number cannot be found in the table, the name prints as `UNKNOWN`.

### Program Flow

At startup, the program:
- Loads the Sales Rep Master File into an in-memory table indexed by sales rep number
- Formats report headings using the current system date and time

For each customer record, the program:
- Reads the next record from the Customer Master File
- Checks if a page heading is needed (every 55 lines)
- Detects control breaks by comparing branch and sales rep numbers to saved values
- On a Sales Rep break — prints a Sales Rep total line, resets Sales Rep accumulators
- On a Branch break — prints a Sales Rep total line, then a Branch total line, resets Branch accumulators
- Moves customer number, name, and sales fields to the detail line
- Computes change amount and change percent
- Writes the formatted detail line
- Accumulates sales totals into Sales Rep, Branch, and Grand total fields

After all records are processed, the program:
- Prints the final Sales Rep total line
- Prints the final Branch total line
- Computes and writes the Grand total line
- Closes all files and stops

## Output Example

```
DATE:  03/24/2026                    YEAR-TO-DATE SALES REPORT                          PAGE:    1
TIME:  09:45                                                                            RPT6000

                                                        SALES         SALES          CHANGE     CHANGE
BRANCH   SALESREP                   CUSTOMER           THIS YTD      LAST YTD        AMOUNT     PERCENT
------   -------------              --------------------------   ------------   ------------   -------
 01   01 SMITH       10001 ACME SUPPLY CO.          12,345.00      11,000.00       1,345.00      12.2
                     10002 GLOBE INDUSTRIES          8,200.50       9,100.00         -899.50-      9.9-
                     10003 HORIZON TECH              5,000.00           0.00        5,000.00       N/A

                                        SALESREP TOTAL  $25,545.50    $20,100.00     $5,445.50      27.1 *

 01   02 JONES       10004 DELTA CORP               22,000.00      18,500.00       3,500.00      18.9
                     10005 SUMMIT PARTNERS           3,750.00       4,000.00         -250.00-      6.3-

                                        SALESREP TOTAL  $25,750.00    $22,500.00     $3,250.00      14.4 *

                                          BRANCH TOTAL  $51,295.50    $42,600.00     $8,695.50      20.4 **

 02   05 TAYLOR      20001 KEYSTONE INC.            47,890.00      45,000.00       2,890.00       6.4
                     20002 NATIONAL SERVICES        10,200.00      10,200.00           0.00       0.0

                                        SALESREP TOTAL  $58,090.00    $55,200.00     $2,890.00       5.2 *

                                          BRANCH TOTAL  $58,090.00    $55,200.00     $2,890.00       5.2 **

                                           GRAND TOTAL $109,385.50    $97,800.00    $11,585.50      11.8 ***
```

## COBOL Compilers for Output

If you are unsure how to run this program, put the code into one of the following online COBOL compilers. Also, this code is able to be submitted to the mainframe if you have a license to TN3270 software. Remember to edit the JCL to your username before submitting!

- https://www.jdoodle.com/ia/1PcS
- https://onecompiler.com/cobol/44b45fp2x
- https://paiza.io/en/languages/cobol

## Maintainers

- [@kayley-wells](https://github.com/kayley-wells) Kayley Wells
