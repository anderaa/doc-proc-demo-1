# Failure analysis

Split of 32 examples, aggregate 0.822 (95% CI 0.766-0.874).

At most 3 errors are sampled per task. That cap is deliberate: rules written
against a full error dump key themselves to individual documents and do not survive the holdout.

## agreement_type (multiclass)

macro_f1 **0.657** | P 0.812 R 0.812 F1 0.812 | support 32 | 6 of 32 examples wrong

> no class reaches the support floor; macro-F1 computed over all classes

> rarest class has 0 example(s): not measurable; this task's number should not be quoted on its own

### Confusion

| gold \ predicted | distribution | ip_licensing | marketing | partnership | services | supply_manufacturing |
| --- | --- | --- | --- | --- | --- | --- |
| distribution | 1 | 0 | 0 | 0 | 1 | 0 |
| ip_licensing | 1 | 3 | 0 | 0 | 0 | 0 |
| marketing | 0 | 0 | 5 | 0 | 1 | 0 |
| partnership | 0 | 0 | 0 | 5 | 1 | 0 |
| services | 0 | 1 | 0 | 0 | 10 | 0 |
| supply_manufacturing | 1 | 0 | 0 | 0 | 0 | 2 |

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| distribution | 2 | 0.333 | 0.500 | 0.400 | 3 | not measurable |
| ip_licensing | 4 | 0.750 | 0.750 | 0.750 | 2 | not measurable |
| marketing | 6 | 1.000 | 0.833 | 0.909 | 1 | presence check only |
| non_compete | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| partnership | 6 | 1.000 | 0.833 | 0.909 | 1 | presence check only |
| services | 11 | 0.769 | 0.909 | 0.833 | 4 | detecting total failure |
| supply_manufacturing | 3 | 1.000 | 0.667 | 0.800 | 1 | not measurable |

### Sampled errors (3 of 6)

- **CardlyticsInc_20180112_S-1_EX-10.16_11002987_EX-10.16_Maintenance Agreement3**: gold=services predicted=ip_licensing
- **ConformisInc_20191101_10-Q_EX-10.6_11861402_EX-10.6_Development Agreement**: gold=partnership predicted=services
  - document: ...of its obligations relating to the R&D Program in accordance with standard industry practices. ARTICLE X MISCELLANEOUS 10.1 Agency. Neither this Agreement nor any of the Other Agreements creates any partnership, agency or other relationship among the Parties for any purpose, including for all tax pu...
- **FulucaiProductionsLtd_20131223_10-Q_EX-10.9_8368347_EX-10.9_Content License Agreement**: gold=ip_licensing predicted=distribution

## parties (extract_list)

f1 **0.911** | P 0.921 R 0.901 F1 0.911 | support 91 | 7 of 32 examples wrong

### Sampled errors (3 of 7)

- **TRANSPHORM,INC_02_14_2020-EX-10.12(1)-JOINT VENTURE AGREEMENT**: gold=[aizufujitsu semiconductor, fujitsu semiconductor, transphorm, aizufujitsu semiconductor limited fujitsu semiconductor limited transphorm] predicted=[aizu fujitsu semiconductor, fujitsu semiconductor, transphorm] matched=3 theta=0.90
- **VerizonAbsLlc_20200123_8-K_EX-10.4_11952335_EX-10.4_Service Agreement**: gold=[verizon owner trust, verizon abs, cellco partnership d b a verizon wireless] predicted=[verizon owner trust 2020 a, verizon abs, cellco partnership d b a verizon wireless] matched=2 theta=0.90
- **OFGBANCORP_03_28_2007-EX-10.23-OUTSOURCING AGREEMENT**: gold=[oriental financial group, melavante] predicted=[oriental financial group, metavante] matched=1 theta=0.90

## agreement_date (extract_date)

f1 **0.935** | P 0.935 R 0.935 F1 0.935 | support 31 | 2 of 32 examples wrong

### Sampled errors (2 of 2)

- **CHAPARRALRESOURCESINC_03_30_2000-EX-10.66-TRANSPORTATION CONTRACT**: gold=2000-01-03 predicted=2000-01-31 compared at day
- **CardlyticsInc_20180112_S-1_EX-10.16_11002987_EX-10.16_Maintenance Agreement3**: gold=2011-03-04 predicted=2011-03-03 compared at day

## governing_law (multiclass)

macro_f1 **0.173** | P 1.000 R 1.000 F1 1.000 | support 29 | 0 of 32 examples wrong

> no class reaches the support floor; macro-F1 computed over all classes

> rarest class has 0 example(s): not measurable; this task's number should not be quoted on its own

### Confusion

| gold \ predicted | (null) | CA | DE | FL | FOREIGN | MA | NV | NY | OH | TX |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| (null) | 3 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| CA | 0 | 6 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| DE | 0 | 0 | 3 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| FL | 0 | 0 | 0 | 2 | 0 | 0 | 0 | 0 | 0 | 0 |
| FOREIGN | 0 | 0 | 0 | 0 | 4 | 0 | 0 | 0 | 0 | 0 |
| MA | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| NV | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
| NY | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 10 | 0 | 0 |
| OH | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
| TX | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| AK | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| AL | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| AR | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| AZ | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| CA | 6 | 1.000 | 1.000 | 1.000 | 0 | presence check only |
| CO | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| CT | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| DC | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| DE | 3 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| FL | 2 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| FOREIGN | 4 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| GA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| HI | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| IA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| ID | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| IL | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| IN | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| KS | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| KY | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| LA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MA | 1 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| MD | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| ME | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MI | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MN | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MO | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MS | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| MT | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NC | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| ND | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NE | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NH | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NJ | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NM | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| NV | 1 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| NY | 10 | 1.000 | 1.000 | 1.000 | 0 | detecting total failure |
| OH | 1 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| OK | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| OR | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| PA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| RI | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| SC | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| SD | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| TN | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| TX | 1 | 1.000 | 1.000 | 1.000 | 0 | not measurable |
| UT | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| VA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| VT | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| WA | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| WI | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| WV | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |
| WY | 0 | 0.000 | 0.000 | 0.000 | 0 | not measurable |

## competition_restrictions (multilabel)

macro_f1 **0.696** | P 0.714 R 0.800 F1 0.755 | support 25 | 9 of 32 examples wrong

> no class reaches the support floor; macro-F1 computed over all classes

> rarest class has 2 example(s): not measurable; this task's number should not be quoted on its own

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| exclusivity | 12 | 0.750 | 1.000 | 0.857 | 4 | detecting total failure |
| no_solicit_customers | 2 | 0.500 | 0.500 | 0.500 | 2 | not measurable |
| no_solicit_employees | 4 | 1.000 | 0.750 | 0.857 | 1 | not measurable |
| non_compete | 7 | 0.571 | 0.571 | 0.571 | 6 | presence check only |

### Sampled errors (3 of 9)

- **PANDIONTHERAPEUTICSHOLDCOLLC_05_22_2020-EX-10.17-CONSULTING AGREEMENT**: gold={no_solicit_employees} predicted={no_solicit_employees, non_compete}
- **ConformisInc_20191101_10-Q_EX-10.6_11861402_EX-10.6_Development Agreement**: gold={exclusivity} predicted={exclusivity, non_compete}
- **SightLife Surgical, Inc. - STRATEGIC SALES _ MARKETING AGREEMENT**: gold={non_compete} predicted={exclusivity, non_compete}

## has_liability_cap (binary)

f1_positive **0.600** | P 0.750 R 0.750 F1 0.750 | support 32 | 8 of 32 examples wrong

### Confusion

| gold \ predicted | false | true |
| --- | --- | --- |
| false | 18 | 0 |
| true | 8 | 6 |

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| false | 18 | 0.692 | 1.000 | 0.818 | 8 | detecting total failure |
| true | 14 | 1.000 | 0.429 | 0.600 | 8 | detecting total failure |

### Sampled errors (3 of 8)

- **BIOFRONTERAAG_04_29_2019-EX-4.17-SUPPLY AGREEMENT**: gold=True predicted=False
  - document: ...iaries” and “affiliates” shall mean any entity controlling, controlled by, or under common control with, either of the Parties hereto. 12.5 Governing Law. This Agreement shall be governed by, and construed in accordance with, the laws of the State of New York, without reference to any principles of ...
- **IbioInc_20200313_8-K_EX-10.1_12052678_EX-10.1_Development Agreement**: gold=True predicted=False
- **MPLXLP_06_17_2015-EX-10.1-TRANSPORTATION SERVICES AGREEMENT**: gold=True predicted=False
  - document: ...40 Attention: President Fax: (419) 421-3125 or to such other address as such Party may indicate by a notice delivered in accordance with this Section 13. 14. Governing Law This Agreement shall be construed and interpreted in accordance with the laws of the State of Ohio, without recourse to any prin...

## liability_cap_clause (span)

token_f1 **0.234** | P 0.277 R 0.202 F1 0.234 | support 2577 | 13 of 32 examples wrong

### Sampled errors (3 of 13)

- **IbioInc_20200313_8-K_EX-10.1_12052678_EX-10.1_Development Agreement**: gold=[20923:21222] "Except for claims arising out of Articles 4.3 and 7.0, or as..." predicted=null
- **LUCIDINC_04_15_2011-EX-10.9-DISTRIBUTOR AGREEMENT**: gold=[12623:13392] "Lucid will not have any liability or responsibility to Distr..." predicted=[13393:13616] "In no event will the aggregate liability incurred by Lucid i..." overlap=0.000 threshold=0.50
- **InvendaCorp_20000828_S-1A_EX-10.2_2588206_EX-10.2_Co-Branding Agreement**: gold=[70331:70937] "Except as provided by Sections 19(a)(iii)(2), (a)(iii)(3), (..." predicted=[70825:70937] "Either party's liability for damages shall be limited to the..." overlap=0.185 threshold=0.50

## has_audit_rights (binary)

f1_positive **0.889** | P 0.906 R 0.906 F1 0.906 | support 32 | 3 of 32 examples wrong

### Confusion

| gold \ predicted | false | true |
| --- | --- | --- |
| false | 17 | 3 |
| true | 0 | 12 |

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| false | 20 | 1.000 | 0.850 | 0.919 | 3 | detecting total failure |
| true | 12 | 0.800 | 1.000 | 0.889 | 3 | detecting total failure |

### Sampled errors (3 of 3)

- **GridironBionutrientsInc_20171206_8-K_EX-10.1_10972555_EX-10.1_Endorsement Agreement**: gold=False predicted=True
- **LUCIDINC_04_15_2011-EX-10.9-DISTRIBUTOR AGREEMENT**: gold=False predicted=True
- **TICKETSCOMINC_06_22_1999-EX-10.22-SPONSORSHIP AGREEMENT**: gold=False predicted=True
  - document: ...proprietary rights or rights of publicity or privacy; (ii) violate any law, statue, ordinance or regulation, including without limitation any laws regarding unfair competition, antidiscrimination or false advertising; (iii) be pornographic or obscene; (iv) be defamatory or trade libelous; or (v) con...

## renewal_notice_days (extract_numeric)

f1 **1.000** | P 1.000 R 1.000 F1 1.000 | support 7 | 0 of 32 examples wrong

> rarest class has 7 example(s): presence check only; this task's number should not be quoted on its own

## termination_for_convenience (binary)

f1_positive **0.667** | P 0.750 R 0.750 F1 0.750 | support 32 | 8 of 32 examples wrong

### Confusion

| gold \ predicted | false | true |
| --- | --- | --- |
| false | 16 | 5 |
| true | 3 | 8 |

### Per class

| class | support | P | R | F1 | errors | reads as |
| --- | --- | --- | --- | --- | --- | --- |
| false | 21 | 0.842 | 0.762 | 0.800 | 8 | detecting total failure |
| true | 11 | 0.615 | 0.727 | 0.667 | 8 | detecting total failure |

### Sampled errors (3 of 8)

- **InvendaCorp_20000828_S-1A_EX-10.2_2588206_EX-10.2_Co-Branding Agreement**: gold=False predicted=True
- **BLUEROCKRESIDENTIALGROWTHREIT,INC_06_01_2016-EX-1.1-AGENCY AGREEMENT**: gold=True predicted=False
  - document: ...any post-effective amendment thereto complied and will comply in all respects to the requirements of the Act and the Rules and Regulations thereunder, and did not, does not and will not include any untrue statement of a material fact or omitted, omits or will omit to state any material fact required...
- **Freecook_20180605_S-1_EX-10.3_11233807_EX-10.3_Hosting Agreement**: gold=True predicted=False
