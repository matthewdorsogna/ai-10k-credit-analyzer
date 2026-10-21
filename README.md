# AI 10-K Credit Analyzer

A Python tool that screens the credit health of U.S. airlines using SEC filings and an AI model. It pulls each company's reported financials from SEC EDGAR, calculates credit ratios, has Claude read the 10-K to summarize debt and liquidity, and then checks the AI's numbers against what the company actually reported.

## What it does

1. **Pulls financial data from SEC EDGAR** for six airlines (Southwest, Delta, United, American, Alaska, and JetBlue) using the SEC's XBRL company facts API
2. **Calculates credit ratios** including interest coverage, EBITDA, total debt, and debt to EBITDA
3. **Flags weak credits** with interest coverage below 2.0x or debt to EBITDA above 4.0x
4. **Uses the Claude API to read each 10-K** and summarize upcoming debt maturities, covenants, liquidity sources, and top credit risks
5. **Validates the AI's work** by having Claude extract revenue and operating income from each income statement and comparing those figures to the SEC-reported values

## Results (fiscal year 2025)

| Airline | Interest Coverage | Debt / EBITDA | Flag |
|---|---|---|---|
| Delta (DAL) | 8.57x | 1.61x | |
| United (UAL) | 3.43x | 2.78x | |
| Southwest (LUV) | 2.56x | 2.47x | |
| Alaska (ALK) | 1.11x | 4.84x | Weak coverage, high leverage |
| American (AAL) | 0.85x | 7.88x | Weak coverage, high leverage |
| JetBlue (JBLU) | -0.63x | 26.56x | Weak coverage, high leverage |

**Key findings**

- Three of six airlines fell below 2.0x interest coverage and above 4.0x leverage
- JetBlue posted an operating loss with $8.5B of debt, pushing leverage to 26.6x
- Southwest's liquidity fell from $9.7B to $4.7B in one year, driven by debt repayment and $2.6B of share buybacks
- American also carries about $7.0B of operating lease liabilities and a $3.7B stockholders' deficit, so reported leverage understates its true position

## AI validation

Claude's extracted figures matched SEC-reported values within 1% for 11 of 12 data points. The one miss was American's operating income ($1,511M extracted vs. $1,467M reported). Tracing it back to the filing showed that American files a combined 10-K for both its parent company (American Airlines Group) and its subsidiary (American Airlines, Inc.), and the AI had read the subsidiary's statement.

## Methodology notes

- Companies tag the same line item differently, so the tool tries several XBRL tags in order and records which one it used for each company
- Delta reports net interest rather than gross interest expense, so its coverage uses net interest and is labeled accordingly
- Total debt includes current and long-term debt but excludes lease liabilities, so leverage is understated for airlines that lease heavily

## Tools

Python, pandas, Requests, BeautifulSoup, Claude API (Anthropic), SEC EDGAR APIs

## How to run

1. Install the packages with `pip install requests pandas openpyxl beautifulsoup4 lxml anthropic`
2. Add your name and email to the SEC `User-Agent` header (the SEC requires it)
3. Run the notebook cells in order and paste your Anthropic API key when prompted

The API key is entered at runtime and never saved in the code.
