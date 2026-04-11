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
DATE:  04/10/2026                          YEAR-TO-DATE SALES REPORT                          PAGE:    1
TIME:  23:54                                                                                  RPT6000
                                                      SALES         SALES        CHANGE     CHANGE
BRANCH   SALESREP             CUSTOMER              THIS YTD      LAST YTD       AMOUNT     PERCENT
------ ------------- --------------------------   ------------  ------------   -----------  -------
  12   12 AJONES     11111 INFORMATION BUILDERS       1,234.56      1,111.11        123.45    +11.1
                     12345 CAREER TRAINING CTR       12,345.67     22,222.22      9,876.55-   -44.4

                                    SALESREP TOTAL  $13,580.23    $23,333.33     $9,753.10-   -41.8*

                                      BRANCH TOTAL  $13,580.23    $23,333.33     $9,753.10-   -41.8**

  22   10 UNKNOWN    22222 HOMELITE TEXTRON CO       34,545.00          0.00     34,545.00     N/A

                                    SALESREP TOTAL  $34,545.00         $0.00    $34,545.00     N/A *

       14 KBAKER     34567 NEAS MEMBER BENEFITS         111.11          0.00        111.11     N/A
                     55555 PILOT LIFE INS. CO.       10,000.00      1,000.00      9,000.00   +900.0

                                    SALESREP TOTAL  $10,111.11     $1,000.00     $9,111.11   +911.1*

                                      BRANCH TOTAL  $44,656.11     $1,000.00    $43,656.11   OVRFLW**

  34   10 UNKNOWN    00111 DAUPHIN DEPOSIT BANK      14,099.00     19,930.00      5,831.00-   -29.3
                     54321 AIRCRAFT OWNERS ASSC       5,426.12     40,420.00     34,993.88-   -86.6

                                    SALESREP TOTAL  $19,525.12    $60,350.00    $40,824.88-   -67.6*

       17 STRACKER   33333 NORFOLK CORP               6,396.35      4,462.88      1,933.47    +43.3

                                    SALESREP TOTAL   $6,396.35     $4,462.88     $1,933.47    +43.3*

                                      BRANCH TOTAL  $25,921.47    $64,812.88    $38,891.41-   -60.0**

  47   11 TSMITH     12121 GENERAL SERVICES CO.      11,444.00     11,059.56        384.44     +3.5
                     24680 INFO MANAGEMENT CO.       17,481.45     11,892.47      5,588.98    +47.0

                                    SALESREP TOTAL  $28,925.45    $22,952.03     $5,973.42    +26.0*

       21 FFRANKLIN  99999 DOLLAR SAVINGS BANK        5,059.00      4,621.95        437.05     +9.5
                     76543 NATL MUSIC CORP.           2,383.46      4,435.26      2,051.80-   -46.3

                                    SALESREP TOTAL   $7,442.46     $9,057.21     $1,614.75-   -17.8*

                                      BRANCH TOTAL  $36,367.91    $32,009.24     $4,358.67    +13.6**

                                       GRAND TOTAL $120,525.72   $121,155.45       $629.73-    -0.5***
```

## COBOL Compilers for Output

If you are unsure how to run this program, put the code into one of the following online COBOL compilers. Also, this code is able to be submitted to the mainframe if you have a license to TN3270 software. Remember to edit the JCL to your username before submitting!

- https://www.jdoodle.com/ia/1PcS
- https://onecompiler.com/cobol/44b45fp2x
- https://paiza.io/en/languages/cobol

## Maintainers

- [@kayley-wells](https://github.com/kayley-wells) Kayley Wells
