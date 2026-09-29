# Awesome US Tax Calculators [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of high-quality US tax calculation resources, open-source tools, official IRS data, and formulas for individuals, freelancers, and developers building tax-related software.

**Last Updated:** April 2026 | **Tax Year Coverage:** 2025–2026

All resources are vetted for accuracy, official sourcing, and practical utility. This list is maintained by the community — contributions welcome!

> **Disclaimer:** All resources listed here are for educational and informational purposes only. Nothing in this repository constitutes tax, legal, or financial advice. Please consult a qualified tax professional or CPA for your specific situation.

---

## Contents

- [Official IRS Resources](#official-irs-resources)
- [Federal Tax Formulas & Rate Data](#federal-tax-formulas--rate-data)
- [Open-Source Tax Libraries](#open-source-tax-libraries)
- [Withholding & W-4 Tools](#withholding--w-4-tools)
- [State Tax Resources](#state-tax-resources)
- [Self-Employed & Freelancer Tax](#self-employed--freelancer-tax)
- [Payroll & Salary Calculators](#payroll--salary-calculators)
- [Capital Gains & Investment Tax](#capital-gains--investment-tax)
- [Retirement Account Tax Tools](#retirement-account-tax-tools)
- [Tax Filing Resources](#tax-filing-resources)
- [Developer APIs & Data Sets](#developer-apis--data-sets)
- [Learning Resources](#learning-resources)

---

## Official IRS Resources

- [IRS Tax Withholding Estimator](https://www.irs.gov/individuals/tax-withholding-estimator) — Official W-4 planning tool from the IRS.
- [IRS Free File](https://www.irs.gov/filing/free-file-do-your-federal-taxes-for-free) — File federal taxes for free if income ≤ $79,000 (2025).
- [IRS Publication 15-T](https://www.irs.gov/publications/p15t) — Federal Income Tax Withholding Methods — the authoritative source for payroll tax tables.
- [IRS Publication 505](https://www.irs.gov/publications/p505) — Tax Withholding and Estimated Tax — essential for freelancers and self-employed.
- [IRS Publication 17](https://www.irs.gov/publications/p17) — Your Federal Income Tax — comprehensive individual tax guide.
- [IRS EITC Assistant](https://www.irs.gov/credits-deductions/individuals/earned-income-tax-credit/use-the-eitc-assistant) — Check Earned Income Tax Credit eligibility.
- [IRS AMT Assistant](https://www.irs.gov/businesses/small-businesses-self-employed/alternative-minimum-tax-amt-assistant-for-individuals) — Determine if you owe the Alternative Minimum Tax.

---

## Federal Tax Formulas & Rate Data

### 2026 Federal Tax Brackets (Single Filers)

| Taxable Income | Marginal Rate |
|---|---|
| $0 – $11,925 | 10% |
| $11,925 – $48,475 | 12% |
| $48,475 – $103,350 | 22% |
| $103,350 – $197,300 | 24% |
| $197,300 – $250,525 | 32% |
| $250,525 – $626,350 | 35% |
| Over $626,350 | 37% |

> Source: [IRS Rev. Proc. 2025-38](https://www.irs.gov/pub/irs-drop/rp-25-38.pdf)

### Standard Deductions 2026

| Filing Status | Standard Deduction |
|---|---|
| Single | $15,000 |
| Married Filing Jointly | $30,000 |
| Head of Household | $22,500 |

### FICA Tax Rates 2026

- **Social Security Tax:** 6.2% (employee) + 6.2% (employer) up to $176,100 wage base
- **Medicare Tax:** 1.45% (employee) + 1.45% (employer), no cap
- **Additional Medicare Tax:** 0.9% on wages over $200,000 (single) / $250,000 (MFJ)
- **Self-Employment Tax:** 15.3% (up to SS wage base) + 2.9% above

### Core Tax Calculation Formula

```
Taxable Income = Gross Income - Adjustments - Standard/Itemized Deductions

Federal Income Tax = Σ(bracket_rate × income_in_bracket)

Effective Tax Rate = Federal Income Tax / Gross Income × 100

After-Tax Income = Gross Income - Federal Tax - State Tax - FICA
```

### Tax Foundation Data

- [Tax Foundation Tax Data](https://taxfoundation.org/data/) — Annual tax rate tables, state comparisons, historical data.
- [Tax Foundation State Tax Rates](https://taxfoundation.org/location/united-states/) — Compare all 50 states.
- [Urban-Brookings Tax Policy Center](https://www.taxpolicycenter.org/statistics) — Distributional tax analysis datasets.

---

## Open-Source Tax Libraries

- [taxjar/taxjar-node](https://github.com/taxjar/taxjar-node) — Sales tax API client for Node.js.
- [taxjar/taxjar-python](https://github.com/taxjar/taxjar-python) — Python client for TaxJar sales tax API.
- [usdigitalresponse/covid-fund-taxes](https://github.com/usdigitalresponse/covid-fund-taxes) — Example open government tax calculation logic.
- [obernardoff/tax-calculator](https://github.com/obernardoff/tax-calculator) — Simple US federal + state income tax estimator in Python.
- [tmaher/bracket_calculator](https://github.com/tmaher/bracket_calculator) — Marginal vs effective federal tax rate calculator.

### Python: Federal Tax Calculation Snippet

```python
def calculate_federal_tax(taxable_income: float, filing_status: str = "single") -> float:
    """
    Calculate 2026 US federal income tax using marginal brackets.
    Source: IRS Rev. Proc. 2025-38
    """
    brackets_single = [
        (11925,    0.10),
        (48475,    0.12),
        (103350,   0.22),
        (197300,   0.24),
        (250525,   0.32),
        (626350,   0.35),
        (float('inf'), 0.37),
    ]
    tax = 0.0
    prev_limit = 0
    for limit, rate in brackets_single:
        if taxable_income <= prev_limit:
            break
        taxable_at_rate = min(taxable_income, limit) - prev_limit
        tax += taxable_at_rate * rate
        prev_limit = limit
    return round(tax, 2)

# Example
gross = 85_000
standard_deduction = 15_000  # Single, 2026
taxable = gross - standard_deduction
federal_tax = calculate_federal_tax(taxable)
print(f"Federal Tax: ${federal_tax:,.2f}")
print(f"Effective Rate: {federal_tax / gross * 100:.1f}%")
```

### JavaScript: FICA Calculator

```javascript
function calculateFICA(grossWage) {
  const SS_WAGE_BASE = 176100;  // 2026
  const SS_RATE = 0.062;
  const MEDICARE_RATE = 0.0145;
  const ADDL_MEDICARE_THRESHOLD = 200000;
  const ADDL_MEDICARE_RATE = 0.009;

  const socialSecurity = Math.min(grossWage, SS_WAGE_BASE) * SS_RATE;
  const medicare = grossWage * MEDICARE_RATE;
  const additionalMedicare = grossWage > ADDL_MEDICARE_THRESHOLD
    ? (grossWage - ADDL_MEDICARE_THRESHOLD) * ADDL_MEDICARE_RATE
    : 0;

  return {
    socialSecurity: socialSecurity.toFixed(2),
    medicare: (medicare + additionalMedicare).toFixed(2),
    total: (socialSecurity + medicare + additionalMedicare).toFixed(2),
  };
}
```

---

## Withholding & W-4 Tools

- [IRS W-4 Form (2026)](https://www.irs.gov/pub/irs-pdf/fw4.pdf) — Current official W-4 form PDF.
- [IRS W-4 Instructions](https://www.irs.gov/pub/irs-pdf/iw4.pdf) — Step-by-step W-4 completion guide.
- [IRS Tax Withholding Estimator](https://www.irs.gov/individuals/tax-withholding-estimator) — Official tool to check withholding accuracy.
- [Paycheck City](https://www.paycheckCity.com) — Payroll calculator with state-by-state withholding.

### W-4 Step 2 Formula (Multiple Jobs)

```
If two jobs with similar pay:
  Check box in Step 2(c) on BOTH W-4s
  This applies the higher standard withholding tables

If jobs with different pay:
  Use IRS Pub 505 Worksheet 2 or the online estimator
```

---

## State Tax Resources

### States with No Income Tax (2026)

| State | Notes |
|---|---|
| Alaska | No income or sales tax |
| Florida | No income tax; sales tax 6% |
| Nevada | No income tax; sales tax 6.85% |
| New Hampshire | No wage income tax (interest/dividends taxed until 2027) |
| South Dakota | No income tax |
| Tennessee | No income tax on wages |
| Texas | No income tax; high property tax |
| Washington | No income tax; capital gains tax on high earners (7%) |
| Wyoming | No income tax |

### State Tax Authority Websites

- [California FTB](https://www.ftb.ca.gov/) — Highest marginal rate: 13.3%
- [New York DTF](https://www.tax.ny.gov/) — NYC adds 3.876% local tax
- [Texas Comptroller](https://comptroller.texas.gov/) — No income tax; sales tax varies by city
- [NASBO State Fiscal Data](https://www.nasbo.org/) — State budget and tax revenue comparisons

### State Tax Rate Summary (2026, highest marginal rate)

| State | Top Rate | Notes |
|---|---|---|
| California | 13.3% | Highest in US |
| Hawaii | 11.0% | |
| New Jersey | 10.75% | |
| Oregon | 9.9% | |
| Minnesota | 9.85% | |
| New York | 10.9% | + NYC local 3.876% |

> For detailed after-tax salary breakdowns by state, [TaxLogic.cc's US Salary Tax Calculator](https://taxlogic.cc/tools/tax/us-salary-tax-calculator) provides interactive estimates for all 50 states including local taxes.

---

## Self-Employed & Freelancer Tax

### Self-Employment Tax Formula

```
Net Self-Employment Income = Gross Business Income - Business Expenses

SE Tax Base = Net SE Income × 0.9235  (IRS allows 7.65% deduction)

SE Tax = SE Tax Base × 0.153  (up to SS wage base)
       + SE Tax Base × 0.029  (above SS wage base, Medicare only)

Deductible SE Tax = SE Tax × 0.5  (deducted from gross income, Line 15)

Taxable Income = Gross Income - Deductible SE Tax - Standard Deduction
```

### Quarterly Estimated Tax Due Dates 2026

| Payment | Period | Due Date |
|---|---|---|
| Q1 | Jan 1 – Mar 31 | April 15, 2026 |
| Q2 | Apr 1 – May 31 | June 16, 2026 |
| Q3 | Jun 1 – Aug 31 | September 15, 2026 |
| Q4 | Sep 1 – Dec 31 | January 15, 2027 |

### Safe Harbor Rule
Pay the lesser of:
- 100% of prior year's tax liability (110% if prior AGI > $150,000), OR
- 90% of current year's tax liability

### Key Deductions for Self-Employed

- Home Office (Form 8829 or simplified $5/sq ft)
- Health Insurance Premiums (if not eligible for employer plan)
- SE Tax Deduction (50% of SE tax)
- Vehicle (Actual expenses or IRS Standard Mileage Rate: 67 cents/mile in 2025)
- Business Equipment (Section 179 immediate expensing)
- Retirement Contributions (SEP-IRA: up to 25% of net SE income or $70,000)

### Resources

- [IRS Schedule SE](https://www.irs.gov/forms-pubs/about-schedule-se-form-1040) — Official self-employment tax form.
- [IRS Publication 334](https://www.irs.gov/publications/p334) — Tax Guide for Small Businesses.
- [IRS Publication 587](https://www.irs.gov/publications/p587) — Business Use of Your Home.
- [IRS Form 1040-ES](https://www.irs.gov/pub/irs-pdf/f1040es.pdf) — Estimated Tax for Individuals.
- [CanYouCalculate](https://canyoucalculate.com) — 50+ free online calculators & converters across 14 categories, incl. a federal income tax estimator and salary/hourly conversion calculators. No signup required.

---

## Payroll & Salary Calculators

### After-Tax Pay Formula

```
Gross Pay (Annual)
- Federal Income Tax      (from IRS Pub 15-T bracket tables)
- Social Security Tax     (6.2% up to $176,100)
- Medicare Tax            (1.45% + 0.9% above $200k)
- State Income Tax        (varies by state)
- Local Tax               (NYC, PA boroughs, OH cities, etc.)
= Net After-Tax Pay

Monthly After-Tax = Net Annual / 12
Biweekly After-Tax = Net Annual / 26
Hourly Equivalent = Net Annual / (hours/week × 52)
```

### Open Datasets

- [BLS National Occupational Employment Statistics](https://www.bls.gov/oes/) — Salary data by occupation and state.
- [Census Bureau PUMS](https://www.census.gov/programs-surveys/acs/microdata/access.html) — Individual-level earnings and tax microdata.
- [SSA Wage Statistics](https://www.ssa.gov/cgi-bin/netcomp.cgi) — Social Security wage distribution data.

---

## Capital Gains & Investment Tax

### 2026 Long-Term Capital Gains Rates

| Filing Status | 0% Rate | 15% Rate | 20% Rate |
|---|---|---|---|
| Single | ≤ $48,350 | $48,350 – $533,400 | > $533,400 |
| Married Filing Jointly | ≤ $96,700 | $96,700 – $600,050 | > $600,050 |
| Head of Household | ≤ $64,750 | $64,750 – $566,700 | > $566,700 |

- **Net Investment Income Tax (NIIT):** 3.8% on investment income if MAGI > $200k (single) / $250k (MFJ)
- **Short-term gains:** Taxed as ordinary income

### Resources

- [IRS Topic 409: Capital Gains and Losses](https://www.irs.gov/taxtopics/tc409)
- [IRS Schedule D](https://www.irs.gov/forms-pubs/about-schedule-d-form-1040) — Capital Gains and Losses form.
- [IRS Form 8949](https://www.irs.gov/forms-pubs/about-form-8949) — Sales and Other Dispositions of Capital Assets.

---

## Retirement Account Tax Tools

### 2026 Contribution Limits

| Account | Limit | Catch-up (50+) |
|---|---|---|
| 401(k) / 403(b) | $23,500 | +$7,500 |
| IRA (Traditional / Roth) | $7,000 | +$1,000 |
| SEP-IRA | 25% of compensation or $70,000 | N/A |
| SIMPLE IRA | $16,500 | +$3,500 |
| HSA (Individual) | $4,300 | +$1,000 |
| HSA (Family) | $8,550 | +$1,000 |

### Roth IRA Phase-Out 2026

| Filing Status | Phase-out Range |
|---|---|
| Single | $150,000 – $165,000 |
| Married Filing Jointly | $236,000 – $246,000 |

### Resources

- [IRS Publication 590-A](https://www.irs.gov/publications/p590a) — Contributions to IRAs.
- [IRS Publication 590-B](https://www.irs.gov/publications/p590b) — Distributions from IRAs (includes RMD tables).
- [DOL 401k Fee Disclosure](https://www.dol.gov/agencies/ebsa/about-ebsa/our-activities/resource-center/faqs/401k-plan-fees) — Understanding fees in employer plans.

---

## Tax Filing Resources

- [IRS Free File Alliance](https://www.irs.gov/filing/free-file-do-your-federal-taxes-for-free) — Free federal filing options.
- [IRS VITA Program](https://www.irs.gov/individuals/free-tax-return-preparation-for-qualifying-taxpayers) — Free in-person tax help.
- [IRS Where's My Refund](https://www.irs.gov/refunds) — Official refund tracker.
- [IRS Identity Protection PIN](https://www.irs.gov/identity-theft-fraud-scams/get-an-identity-protection-pin) — Protect against identity theft.
- [AICPA Tax Resources](https://www.aicpa-cima.com/resources/tax) — Professional guidance from the accounting association.

### Key Filing Deadlines 2026

| Deadline | Date | Notes |
|---|---|---|
| Individual Return | April 15, 2026 | Form 1040 |
| Extension Request | April 15, 2026 | Form 4868 — extends to Oct 15 |
| Extended Deadline | October 15, 2026 | Payment still due April 15 |
| FBAR | April 15, 2026 | FinCEN 114 for foreign accounts |

---

## Developer APIs & Data Sets

- [IRS Data Book](https://www.irs.gov/statistics/soi-tax-stats-irs-data-book) — Annual statistics on tax filings and collections.
- [Tax Foundation API](https://taxfoundation.org/api/) — State and federal tax rate data (limited free tier).
- [OpenFisca-US](https://github.com/PolicyEngine/policyengine-us) — Open-source US tax and benefit policy microsimulation.
- [PolicyEngine US](https://github.com/PolicyEngine/policyengine-us) — Python-based tax policy simulation engine used by researchers.

---

## Learning Resources

### Books

- *J.K. Lasser's Your Income Tax 2026* — The most comprehensive annual tax guide for individuals.
- *Tax-Free Wealth* by Tom Wheelwright CPA — Tax strategy for business owners and investors.
- *The Tax and Legal Playbook* by Mark Kohler — Practical strategies for self-employed.

### Websites & Newsletters

- [Tax Foundation](https://taxfoundation.org/) — Non-partisan tax policy research.
- [Kiplinger Tax Center](https://www.kiplinger.com/taxes) — Practical tax tips and news.
- [TaxProf Blog](https://taxprof.typepad.com/) — Law school professor commentary on tax law.
- [Journal of Accountancy](https://www.journalofaccountancy.com/issues/tax.html) — CPA-level technical analysis.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on adding resources, updating rate tables, or improving formulas.

---

## Repo Info

**GitHub Topics:** `tax`, `us-tax`, `income-tax`, `irs`, `personal-finance`, `salary-calculator`, `tax-calculator`, `awesome-list`

**About:** A curated list of US tax calculators, open-source formulas, official IRS resources, and datasets for individuals, freelancers, and developers.
