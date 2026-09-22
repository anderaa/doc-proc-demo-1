# Production QA

509 of 509 documents produced output; 0 failed every retry and are recorded as failures. 0 came through the Batch API and 509 live.

| check | result | detail |
| --- | --- | --- |
| coverage | pass | 509 outputs for 509 documents |
| schema | pass | 0 parse or call failure(s), 0.00% against a ceiling of 0.50% |
| class distribution | **FAIL** | shifted: has_liability_cap/false +15.7%, has_liability_cap/true -15.7% |
| null rates | pass | abstention rates are in line with validation |
| manual spot-check | pass | 50 random document(s) flagged for review |

**Gates did not all pass; do not hand these results over yet.**

## Human review routes

The random slice is not optional. The four targeted routes select documents that are
already suspect, so a quality estimate built on them alone reads worse than the corpus is.

| route | documents |
| --- | --- |
| transcribed_or_unread | 2: KALLOINC_11_03_2011-EX-10.1-STRATEGIC ALLIANCE AGREEMENT, PRIMEENERGYRESOURCESCORP_04_02_2007-EX-10.28-COMPLETION AND LIQUIDITY MAINTENANCE AGREEMENT |
| truncated | 0 |
| nulls_on_answered_tasks | 287: 2ThemartComInc_19990826_10-12G_EX-10.10_6700288_EX-10.10_Co-Branding Agreement_ Agency Agreement, ABILITYINC_06_15_2020-EX-4.25-SERVICES AGREEMENT, ACCELERATEDTECHNOLOGIESHOLDINGCORP_04_24_2003-EX-10.13-JOINT VENTURE AGREEMENT, ACCURAYINC_09_01_2010-EX-10.31-DISTRIBUTOR AGREEMENT, ADAPTIMMUNETHERAPEUTICSPLC_04_06_2017-EX-10.11-STRATEGIC ALLIANCE AGREEMENT, ADMA BioManufacturing, LLC -  Amendment #3 to Manufacturing Agreement, AFSALABANCORPINC_08_01_1996-EX-1.1-AGENCY AGREEMENT, ALAMOGORDOFINANCIALCORP_12_16_1999-EX-1-AGENCY AGREEMENT, and 279 more |
| low_confidence | 50: ALCOSTORESINC_12_14_2005-EX-10.26-AGENCY AGREEMENT, ATENTOSA_07_06_2020-EX-99.1-JOINT FILING AGREEMENT, AULAMERICANUNITTRUST_04_24_2020-EX-99.8.77-SERVICING AGREEMENT, ArcGroupInc_20171211_8-K_EX-10.1_10976103_EX-10.1_Sponsorship Agreement, BANGIINC_05_25_2005-EX-10-Premium Managed Hosting Agreement, BANUESTRAFINANCIALCORP_09_08_2006-EX-10.16-AGENCY AGREEMENT, BIOAMBERINC_04_10_2013-EX-10.34-DEVELOPMENT AGREEMENT - First Amendment, BLACKROCKMUNIHOLDINGSINVESTMENTQUALITYFUND_04_07_2020-EX-99.01-JOINT FILING AGREEMENT, and 42 more |
| random_slice | 50: ASPIRITYHOLDINGSLLC_05_07_2012-EX-10.6-OUTSOURCING AGREEMENT, ATHENSBANCSHARESCORP_11_02_2009-EX-1.2-AGENCY AGREEMENT , 2009, Antares Pharma, Inc. - Manufacturing Agreement, BERKELEYLIGHTS,INC_06_26_2020-EX-10.12-COLLABORATION AGREEMENT, BIOFRONTERAAG_04_29_2019-EX-4.17-SUPPLY AGREEMENT, BLUEROCKRESIDENTIALGROWTHREIT,INC_06_01_2016-EX-1.1-AGENCY AGREEMENT, CANOPETROLEUM,INC_12_13_2007-EX-10.1-Sponsorship Agreement, CCAINDUSTRIESINC_04_14_2014-EX-10.1-OUTSOURCING AGREEMENT, and 42 more |
| failed | 0 |
