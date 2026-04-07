# RPT2000 (COBOL Program)

<b>Table of Contents</b>
- [Summary](#summary)
- [Report Logic](#report-logic)
- [Program Flow](#program-flow)
- [Output Example](#output-example)
- [Maintainers](#maintainers)

## Summary
RPT2000 is a COBOL batch reporting program that reads the Customer Master File and produces a formatted Year-to-Date Sales Report. The report includes each customer's current and prior year sales figures, along with a calculated change amount and change percent for performance comparison.

Only customers with current YTD sales of $10,000.00 or greater are included in the report output.


The program is designed to run in a mainframe environment using:
-TN3270
-ISPF (editing and dataset navigation)
-SDSF (job output and error review)

### Report Logic

For each qualifying customer, the program calculates:

| Field | Calculation |
|-------|-------------|
| Change Amount | CM-SALES-THIS-YTD minus CM-SALES-LAST-YTD |
| Change Percent | (Change Amount * 100) / CM-SALES-LAST-YTD, rounded |

Special cases for Change Percent:
- If CM-SALES-LAST-YTD is zero, outputs 999.9
- If the result overflows the picture clause, outputs 999.9

Grand totals use the same logic applied to the accumulated totals for all qualifying customers.

### Program Flow
For each customer record, the program:
- Reads the next record from the Customer Master File
- Skips the record if CM-SALES-THIS-YTD is less than $10,000.00
- Checks if a new heading page is needed (every 55 lines)
- Moves customer number, name, and sales fields to the detail line
- Computes change amount and change percent
- Writes the formatted detail line
- Accumulates sales totals for the grand total line

After all records are processed, the program:
- Computes grand total change amount and change percent
- Writes the grand total line and stops

## Output Example
```
DATE:  03/05/2026           YEAR-TO-DATE SALES REPORT                 PAGE:    1
TIME:  13:20                                                          RPT2000
 
BRANCH SALES CUST                           SALES         SALES          CHANGE      CHANGE
 NUM    REP  NUM    CUST NAME              THIS YTD      LAST YTD        AMOUNT      PERCENT
------ ----- -----  --------------------  ----------    ----------     ----------    -------
  12    12   11111  INFORMATION BUILDERS    1,234.56      1,111.11        123.45       11.1
  12    12   12345  CAREER TRAINING CTR    12,345.67     22,222.22      9,876.55-      44.4-
  22    10   22222  HOMELITE TEXTRON CO    34,545.00          0.00     34,545.00      999.9
  22    14   34567  NEAS MEMBER BENEFITS      111.11          0.00        111.11      999.9
  22    14   55555  PILOT LIFE INS. CO.    10,000.00      1,000.00      9,000.00      900.0
  34    10   00111  DAUPHIN DEPOSIT BANK   14,099.00     19,930.00      5,831.00-      29.3-
  34    10   54321  AIRCRAFT OWNERS ASSC    5,426.12     40,420.00     34,993.88-      86.6-
  34    17   33333  NORFOLK CORP            6,396.35      4,462.88      1,933.47       43.3
  47    11   12121  GENERAL SERVICES CO.   11,444.00     11,059.56        384.44        3.5
  47    11   24680  INFO MANAGEMENT CO.    17,481.45     11,892.47      5,588.98       47.0
  47    21   99999  DOLLAR SAVINGS BANK     5,059.00      4,621.95        437.05        9.5
  47    21   76543  NATL MUSIC CORP.        2,383.46      4,435.26      2,051.80-      46.3-
                                        ============= ============= =============     ======
                                          120,525.72    121,155.45        629.73-       0.0 
```
## COBOL Compilers for Output
If you are unsure how to run this program, put the code into one of the following online COBOL compilers. Also, this code is able to be submitted to the mainframe if you have a license to TN3270 software. Remember to edit the JCL to your username before submitting!
- https://www.jdoodle.com/ia/1PcS
- https://onecompiler.com/cobol/44b45fp2x
- https://paiza.io/en/languages/cobol

## Maintainers
- [@kayley-wells](https://github.com/kayley-wells) Kayley Wells
