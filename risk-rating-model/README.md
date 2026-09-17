# Security Risk Rating Model

A practical spreadsheet for producing consistent, explainable, and
defensible security risk ratings using established risk-scoring
methodologies.

The workbook currently supports:

-   DREAD
-   CVSS 2.0 Base Metrics
-   CVSS 3.0 Base Metrics
-   CVSS 3.1 Base Metrics
-   OWASP Risk Rating Methodology (RRM)

## Why This Exists

This project grew out of two recurring problems with security risk
assessments.

### "Why is this High risk?"

The first was inconsistency in assigning High, Medium, and Low ratings.

When asked why a finding was rated High, the explanation was sometimes
little more than:

> "Because it is high risk."

That makes a risk rating difficult to defend, reproduce, or meaningfully
challenge.

The original version of this spreadsheet used DREAD to change the
discussion. Instead of selecting an overall severity, the security
engineer assessed the individual factors that contribute to the risk.
The spreadsheet calculated the resulting score and severity.

The conversation changed from:

> "I don't think this is High."

to questions such as:

> "Why did you rate the impact this high?"

That is a much more useful discussion.

### Compensating controls must affect the risk

A second problem appeared when compensating controls and mitigating
factors were used to lower risk ratings.

Sometimes the identified control had little or no relationship to the
factor being reduced. For example, encrypting a database at rest is an
important security control, but it does not prevent SQL injection and
does not by itself justify reducing the exploitability of a SQL
injection finding.

This led to the introduction of two **Risk States**:

-   **Inherent** --- the risk presented by the original finding before
    compensating controls or mitigating factors are considered.
-   **Residual** --- the remaining risk after applicable compensating
    controls or mitigating factors are considered.

A residual rating should not simply be a lower score. A compensating
control or mitigating factor must affect a specific factor in the
selected risk model, and the justification should explain why that
factor changed.

The objective is not to prevent risk ratings from being challenged. It
is to make those challenges productive and evidence-based.

------------------------------------------------------------------------

# How to Use the Workbook

## 1. Select the appropriate risk model

Use the worksheet corresponding to the methodology appropriate for the
finding:

-   **DREAD**
-   **CVSS 2.0**
-   **CVSS 3.0**
-   **CVSS 3.1**
-   **OWASP RRM**

Do not manually enter the calculated severity or score. The workbook
calculates these values from the selected factors.

## 2. Create the inherent-risk assessment

Every new finding should initially be entered with:

**Risk State = Inherent**

Enter:

-   Finding ID
-   Finding Title
-   Finding Description
-   The appropriate value for each risk factor
-   A brief justification for each selected factor value

The justification does not need to be lengthy. It should contain enough
information for another security practitioner to understand why the
value was selected.

For example, instead of:

> Exploitability: High

the justification might explain:

> Exploitation can be performed remotely without authentication using
> publicly available tooling.

The spreadsheet calculates the applicable subscores, final numeric
score, and severity rating from the selected values.

## 3. Identify compensating controls and mitigating factors

After determining the inherent risk, identify controls or other factors
that materially affect the risk.

For each one, ask:

> **Which factor in the risk model does this control or mitigating
> factor change, and why?**

If that question cannot be answered, the control should not be used to
reduce the rating.

A control can be valuable without affecting the risk represented by a
particular finding.

For example, database encryption at rest protects stored data if the
underlying storage is exposed. It does not, by itself, reduce the
likelihood or impact of SQL injection occurring through an application.

## 4. Create the residual-risk assessment

Do **not** overwrite the inherent-risk row.

Create another row for the same finding and set:

**Risk State = Residual**

Start with the inherent-risk values.

Change only those factors that are materially affected by an identified
compensating control or mitigating factor.

For every changed factor, update the justification to identify:

1.  The compensating control or mitigating factor.
2.  How it affects that particular factor.
3.  Why the new factor value is appropriate.

For example:

**Inherent Exploitability**

> The vulnerable endpoint is Internet accessible, requires no
> authentication, and can be exploited using commonly available tooling.

**Residual Exploitability**

> The endpoint remains Internet accessible, but the deployed WAF blocks
> the known exploit patterns associated with this finding. Exploitation
> would require bypassing the WAF rules; therefore Exploitability has
> been reduced from \[original rating\] to \[residual rating\].

The resulting calculated score represents the residual risk.

## 5. Preserve both assessments

The inherent and residual rows serve different purposes and should
normally both remain in the workbook.

Together they show:

**Finding → Inherent Risk → Compensating Controls / Mitigating Factors →
Residual Risk**

This provides an audit trail explaining not only the final risk rating,
but also why the organization believes the risk was reduced.

------------------------------------------------------------------------

# Assessment Principles

The workbook is built around several principles:

**Rate factors, not feelings.**\
The final severity should result from the factors in the selected
methodology rather than starting with a desired High, Medium, or Low
rating.

**Explain the selection.**\
Factor-level justifications make assessments reproducible and allow
disagreements to focus on specific assumptions.

**Establish inherent risk first.**\
Determine the risk of the finding before considering compensating
controls or mitigating factors.

**Controls must affect a factor.**\
A control should only reduce residual risk when there is a defensible
relationship between the control and a factor in the selected risk
model.

**Preserve the inherent assessment.**\
Residual risk supplements the inherent assessment; it does not replace
it.

**Do not engineer the desired score.**\
The purpose of the workbook is to characterize risk consistently, not to
manipulate factor values until a preferred severity is reached.

------------------------------------------------------------------------

# Workbook Design

Dropdown values and their associated numeric values are maintained
centrally on the **Data** worksheet using Excel named ranges.

Each risk-model worksheet is implemented as an Excel table so additional
findings can be added without manually recreating the worksheet
structure.

The calculated columns appear near the beginning of each row:

**Finding ID → Finding Title → Finding Description → Risk State →
Severity Rating → Impact Subscore → Exploitability/Likelihood Subscore →
Final Numeric Score**

The individual risk factors and their corresponding justification fields
follow.

This layout makes the overall assessment immediately visible while
preserving the reasoning used to produce it.

## DREAD

DREAD evaluates:

-   Damage
-   Reproducibility
-   Exploitability
-   Affected Users
-   Discoverability

For additional visibility, the workbook groups these into:

-   **Impact Subscore** --- Damage and Affected Users
-   **Exploitability Subscore** --- Reproducibility, Exploitability, and
    Discoverability

The final DREAD score is calculated from all five factors.

## CVSS

The CVSS worksheets intentionally implement **Base Metrics only**.

They are intended to provide the standard CVSS Base assessment rather
than reproduce the complete Temporal or Environmental scoring models.

## OWASP Risk Rating Methodology

The OWASP RRM worksheet evaluates the methodology's likelihood and
impact factors and calculates the resulting risk severity.

------------------------------------------------------------------------

# What This Workbook Is --- and Is Not

This workbook is intended to make security risk assessments more:

-   Consistent
-   Explainable
-   Reproducible
-   Reviewable
-   Defensible

It does not eliminate professional judgment.

Risk methodologies provide structure for that judgment. Different
practitioners can reasonably disagree about a factor value. The purpose
of the justification fields is to make the assumptions behind that
judgment explicit so they can be discussed.

A productive review is therefore not:

> "That isn't High."

It is:

> "I disagree with your Exploitability rating because..."

That is exactly the conversation this project is intended to encourage.
