# WealthWise — Design System

**Document Version:** 1.0  
**Status:** Product Definition  
**Project Type:** Major Project  
**Product:** WealthWise  
**Technology Direction:** MERN + Generative AI

---

# 1. Purpose

This document defines the visual and interaction language of WealthWise.

It establishes:

- brand personality,
- visual direction,
- color system,
- typography,
- spacing,
- layout principles,
- cards,
- buttons,
- forms,
- tables,
- charts,
- status indicators,
- insight components,
- AI components,
- navigation,
- responsive behavior,
- accessibility principles.

The purpose is to ensure that all WealthWise screens feel like parts of the same product.

---

# 2. Design Philosophy

WealthWise is a financial intelligence product.

Its design should therefore communicate:

```text
Trust
+
Clarity
+
Intelligence
+
Calmness
+
Control

The interface should feel:

Modern enough to feel intelligent, but calm enough to feel trustworthy.

3. Design Personality

WealthWise should be:

Intelligent

The product analyzes complex financial information.

Calm

Money management can already create anxiety. The UI should not amplify it.

Clear

Financial information should be understandable quickly.

Professional

The product should feel credible enough to handle sensitive financial information.

Human

The AI layer should feel approachable rather than robotic.

Action-oriented

The interface should help users move from understanding to action.

4. What WealthWise Should NOT Feel Like

Avoid making WealthWise look like:

A traditional banking portal
A cryptocurrency trading dashboard
A stock-trading terminal
A generic SaaS admin panel
A gamified budgeting app
An overly futuristic AI interface
A spreadsheet

The visual identity should sit between:

Financial Product
        +
Modern AI Product
        +
Personal Productivity Tool
5. Core Visual Principle

The interface should visually communicate:

DATA
 ↓
UNDERSTANDING
 ↓
INSIGHT
 ↓
ACTION

This should influence hierarchy.

Raw financial data should be visually quieter.

Important insights should receive more emphasis.

Actions should be obvious without being aggressive.

6. Brand Direction
Brand Name

WealthWise

Tagline

Understand your money. Improve your decisions.

Brand Attributes
Trustworthy
Intelligent
Modern
Personal
Clear
Balanced
Purposeful
7. Color Philosophy

Color should communicate meaning rather than decoration.

The primary palette should remain restrained.

Use color primarily for:

Brand Identity
Financial Status
Warnings
Positive Outcomes
Interactive States
Charts

Do not turn every component into a colored card.

8. Primary Brand Color

The primary brand color should communicate:

Trust
Intelligence
Growth
Stability

Recommended direction:

Deep Emerald / Intelligent Green

Example conceptual value:

Primary:
#176B5B

The exact final value may be refined during implementation.

9. Primary Color Scale

The brand color should have multiple shades.

Conceptually:

Primary 50
Primary 100
Primary 200
Primary 300
Primary 400
Primary 500
Primary 600
Primary 700
Primary 800
Primary 900

Usage:

Light shades
→ backgrounds / subtle highlights

Middle shades
→ interactive elements

Dark shades
→ emphasis / headings / strong controls
10. Neutral Color System

Neutral colors form the majority of the interface.

Use neutrals for:

Backgrounds
Cards
Borders
Text
Dividers
Disabled states

Conceptual scale:

Neutral 50
Neutral 100
Neutral 200
Neutral 300
Neutral 400
Neutral 500
Neutral 600
Neutral 700
Neutral 800
Neutral 900
11. Application Background

The primary application background should be:

Very light neutral

rather than pure white everywhere.

Purpose:

visually separate cards,
reduce harsh contrast,
create hierarchy,
make dashboards feel calmer.
12. Card Background

Cards should generally use:

White / Near White

against the application background.

Cards should not rely heavily on shadows.

Prefer:

Subtle Border
+
Very Light Shadow
13. Semantic Colors

Semantic colors should have consistent meanings.

Positive

Used for:

Savings improvement
Goal progress
Budget within limit
Positive trend
Successful action

Recommended family:

Green
Warning

Used for:

Budget approaching limit
Goal at risk
Significant spending increase
Incomplete data

Recommended family:

Amber
Negative

Used for:

Budget exceeded
Goal behind
Significant negative change
Destructive action

Recommended family:

Red
Informational

Used for:

System information
Neutral insight
Educational messages
AI context

Recommended family:

Blue
14. Important Color Rule

Do not communicate financial status using color alone.

For example:

Green
+
"On Track"

rather than:

Green only

This improves accessibility and clarity.

15. Financial Color Semantics

WealthWise should maintain the following mental model:

Green
→ Positive / healthy movement

Amber
→ Attention needed

Red
→ Action may be required

Blue
→ Information / explanation

Neutral
→ No significant signal
16. Typography

Typography should prioritize:

Readability
Hierarchy
Professionalism
Numerical clarity

Recommended font direction:

Inter

Alternative system fallback:

Inter,
-apple-system,
BlinkMacSystemFont,
"Segoe UI",
sans-serif
17. Typography Hierarchy
Display

Used for:

Landing page headline
Major product statements

Characteristics:

Large
Bold
Tight line height
H1

Used for:

Page titles
H2

Used for:

Major sections
H3

Used for:

Card titles
Subsections
Body

Used for:

Descriptions
Explanations
Normal content
Caption

Used for:

Metadata
Dates
Supporting information
Chart labels
18. Suggested Typography Scale
Display
48–64 px

H1
32–40 px

H2
24–32 px

H3
18–22 px

Body Large
16–18 px

Body
14–16 px

Caption
12–14 px

Exact values should be implemented through design tokens.

19. Numerical Typography

Financial numbers are especially important.

Large financial values should use:

High contrast
Strong weight
Consistent formatting

Example:

₹60,000

rather than visually burying the value inside explanatory text.

20. Currency Formatting

The application should consistently use the user's configured currency.

For INR:

₹60,000
₹1,240
₹5,000

Avoid inconsistent representations such as:

60000 INR
Rs. 60000
60K

unless a compact format is intentionally used.

21. Compact Financial Numbers

For dense visualizations, compact notation may be used:

₹60K
₹1.2L

However, detailed financial views should prefer exact values.

22. Spacing System

Use a consistent spacing scale.

Recommended base unit:

4px

Suggested scale:

4
8
12
16
20
24
32
40
48
64
80
96
23. Spacing Principles

Use larger spacing for:

Page sections
Major cards
Primary content groups

Use smaller spacing for:

Related labels
Form fields
Metadata
Inline elements
24. Layout Grid

Desktop applications should use a structured content grid.

Conceptually:

Page
│
├── Sidebar
│
└── Main Content
      │
      ├── Header
      ├── Content Grid
      └── Sections
25. Content Width

Main content should not become excessively wide.

Recommended maximum:

1200–1440px

depending on the screen.

Financial dashboards should preserve readable chart and card widths.

26. Dashboard Grid

The dashboard may use:

12-column conceptual grid

Example:

┌──────────────────────────────────────────────┐
│ Financial Summary                            │
├──────────┬──────────┬──────────┬─────────────┤
│ Income   │ Expense  │ Savings  │ Savings %   │
├──────────┴──────────┼──────────┴─────────────┤
│ Spending Trend      │ Expense Distribution   │
├─────────────────────┼────────────────────────┤
│ Insights            │ Goals / Budgets        │
└─────────────────────┴────────────────────────┘

The exact layout can vary based on viewport.

27. Border Radius

WealthWise should use moderate rounding.

Recommended conceptual scale:

Small
6px

Medium
10px

Large
14px

Extra Large
20px

Avoid extremely rounded interfaces that feel playful or toy-like.

28. Cards

Cards are a primary content container.

A card should contain:

Title
Optional Description
Content
Optional Action

Example:

┌─────────────────────────────────┐
│ Monthly Savings          ⋯      │
│                                 │
│ ₹18,000                         │
│ +8% from last month             │
│                                 │
│ [View Analytics]                │
└─────────────────────────────────┘
29. Card Hierarchy

Not every card should have equal visual weight.

Use:

Primary Card
Secondary Card
Supporting Card

The most important financial information should receive the strongest hierarchy.

30. Financial Metric Card

Used for:

Income
Expenses
Savings
Savings Rate

Structure:

Label
Value
Change
Supporting Context

Example:

Savings

₹18,000

↑ 8%

vs previous month
31. Insight Card

Insight cards are a signature WealthWise component.

Structure:

Status Indicator
Insight Title
Short Explanation
Evidence
CTA

Example:

┌─────────────────────────────────────┐
│ ⚠  Spending Change                 │
│                                     │
│ Food spending increased             │
│                                     │
│ 59% above your recent average.      │
│                                     │
│ [View Details] [Explore What-If]   │
└─────────────────────────────────────┘
32. Insight Card Design Rule

The card should communicate:

What happened?

without requiring the user to open it.

Detailed evidence can remain behind the drill-down.

33. Goal Card

Goal cards should visually emphasize progress.

Structure:

Goal Name
Current / Target
Progress Bar
Target Date
Status

Example:

Travel

₹30,000 / ₹50,000

████████████░░░░ 60%

December 2026

On Track
34. Budget Card

Structure:

Category
Spent / Budget
Progress
Remaining
Status

Example:

Food

₹4,600 / ₹5,000

██████████████░ 92%

₹400 remaining

Approaching Limit
35. Progress Bars

Progress bars should communicate:

Goal Completion
Budget Usage

They should not be used for arbitrary decorative purposes.

For budgets:

0–70%
Normal

70–90%
Attention

90–100%
Near Limit

>100%
Over Budget

These are initial UI thresholds and may be adjusted by product logic.

36. Status Badges

Status badges should be compact.

Examples:

On Track
Approaching Limit
Over Budget
At Risk
Behind
Insufficient Data

Use:

Icon + Label

where useful.

37. Buttons

Button hierarchy:

Primary
Secondary
Tertiary
Destructive
38. Primary Button

Used for the most important action.

Examples:

Add Transaction
Create Goal
Import Transactions
Ask WealthWise

Visual characteristics:

Strong brand color
High contrast text
Medium radius
Clear hover state
39. Secondary Button

Used for supporting actions.

Examples:

View Details
Compare
Cancel

Can use:

Border
Neutral background
40. Tertiary Button

Used for low-emphasis actions.

Examples:

Learn More
View Data
Back

Should not compete visually with primary actions.

41. Destructive Button

Used for:

Delete Transaction
Delete Goal
Delete Budget
Delete Account

Must communicate consequence clearly.

42. Button States

Every interactive button should support:

Default
Hover
Focus
Active
Disabled
Loading
43. Forms

Forms should prioritize financial data accuracy.

Required characteristics:

Clear Labels
Helpful Placeholders
Validation
Error Messages
Input Formatting
Loading State
Success Feedback
44. Amount Input

Amount inputs should:

Clearly display currency
Accept valid numeric values
Reject invalid input
Prevent accidental formatting errors

Example:

Amount

₹ [ 1,240.00 ]
45. Date Input

Dates should use a consistent format.

The display format may follow locale preferences, while the backend stores standardized date values.

46. Select / Category Input

Category selection should use the WealthWise controlled vocabulary.

Example:

Category
[ Food                  ▼ ]

Categories should not be freely created by the AI.

47. Tables

Transactions require a dense but readable table.

Tables should support:

Sorting
Filtering
Pagination
Row Actions
Responsive behavior
48. Table Row Hierarchy

Important fields:

Amount
Description
Category
Date

Supporting fields:

Merchant
Source
Notes
49. Charts

Charts should prioritize:

Accuracy
Readability
Comparison
Context

rather than decoration.

50. Chart Types

WealthWise may use:

Line Chart

For:

Spending trends
Income trends
Savings trends
Bar Chart

For:

Category comparison
Period comparison
Donut / Pie

For:

Category distribution

Use sparingly.

Progress

For:

Goals
Budgets
51. Chart Interaction

Where useful:

Hover
Tooltip
Period Selection
Category Selection
Drill-Down

Tooltips should show exact values.

52. Chart Color Rules

Charts should use a limited palette.

Do not assign arbitrary colors to every category.

Category colors should remain stable.

For example:

Food
→ consistent visual identity

Transport
→ consistent visual identity

Shopping
→ consistent visual identity

This makes charts easier to learn.

53. Chart Accessibility

Charts should not rely on color alone.

Use:

Labels
Tooltips
Legends
Patterns / symbols where appropriate
54. Trend Indicators

Trend indicators may show:

↑ Increase
↓ Decrease
→ Stable

But the meaning depends on the metric.

For example:

Savings ↑

is generally positive.

While:

Expenses ↑

may require attention.

Therefore, arrows should be paired with context.

55. AI Components

The AI Advisor should have a distinct but integrated visual identity.

It should feel:

Part of WealthWise
+
Clearly AI-assisted

It should not look like a completely separate chatbot application.

56. AI Message Style

AI responses should prioritize:

Short Summary
Evidence
Explanation
Recommendation

Avoid unnecessarily long responses.

57. AI Message Example
Your food spending increased this month.

You spent ₹6,200, compared with a recent average
of ₹3,900.

That's approximately 59% higher.

You could explore reducing discretionary dining
to bring this closer to your usual range.

[Explore What-If]
58. AI Context Indicator

When useful, the UI may indicate the data context used.

Example:

Based on:
This month · 3-month spending history · Food budget

This increases transparency.

59. AI Suggested Prompt Chips

Suggested questions may appear as compact buttons:

Why did spending increase?
Am I on track?
What should I improve?
What if I save ₹2,000 more?
60. AI Typing / Loading State

Avoid overly human-like animations.

Prefer:

Analyzing your financial data...

or a subtle loading indicator.

61. AI Error State

Use calm language:

The AI advisor is temporarily unavailable.

Your financial analytics are still available.
62. Insight Severity Visual Language

Initial mapping:

Info
→ Blue / neutral emphasis

Low
→ Subtle positive or neutral treatment

Medium
→ Amber

High
→ Red

Severity should never dominate the entire screen.

63. Positive Insight

Example:

✓ Savings improved

Your savings increased by 8% compared
with last month.
64. Warning Insight

Example:

⚠ Budget approaching limit

You've used 92% of your food budget.
65. Critical Insight

Example:

! Goal at risk

Your current savings pace may not be
enough to reach your target date.

Use strong visual emphasis only when genuinely important.

66. Empty State Design

An empty state should contain:

Visual
Title
Explanation
Primary Action
Optional Secondary Action

Example:

No financial data yet.

Add a transaction or import your history
to start understanding your spending.

[Add Transaction]
[Import CSV]
67. Skeleton Loading

For dashboards and analytics, prefer skeleton loading where possible.

Example:

┌──────────────┐
│ ███████      │
│ █████        │
│              │
│ ████████     │
└──────────────┘

Avoid making the entire application appear frozen.

68. Toast Notifications

Use toasts for lightweight feedback.

Examples:

Transaction added.
Budget updated.
Goal created.
CSV imported successfully.

Do not use toasts for critical errors that require user action.

69. Modal Usage

Use modals for:

Confirmation
Short forms
Focused actions
Destructive operations

Avoid putting entire complex workflows inside modals.

70. Navigation Sidebar

The sidebar should communicate the product hierarchy.

Suggested order:

Dashboard

Money
  Transactions
  Analytics

Planning
  Budgets
  Goals

Intelligence
  Insights
  Scenarios
  AI Advisor

Settings

This grouping reinforces the WealthWise mental model.

71. Sidebar Active State

The current section should be clearly identifiable using:

Background treatment
Brand accent
Text weight
Optional icon

Do not rely on color alone.

72. Icons

Icons should be:

Simple
Consistent
Minimal
Recognizable

Potential icon categories:

Dashboard
Wallet
Chart
Budget
Target
Lightbulb
Spark / AI
Settings

Avoid using too many decorative icons.

73. Icon + Text Principle

Important navigation actions should generally use:

Icon + Label

rather than icons alone.

Icon-only controls should have accessible labels.

74. Search

Global search may be added later.

For MVP, transaction search is required.

If global search is introduced, it should not become an uncontrolled natural-language database interface.

75. Responsive Design
Desktop

Primary design target.

Use:

Sidebar
Multi-column dashboard
Large charts
Dense tables
Tablet

Use:

Collapsible sidebar
Reduced grid columns
Responsive charts
Mobile

Use:

Bottom navigation or compact navigation
Single-column cards
Scrollable tables or alternate transaction layout
Stacked analytics
76. Mobile Dashboard

Conceptually:

Financial Snapshot
        ↓
Important Insight
        ↓
Spending Trend
        ↓
Goals
        ↓
Budgets
        ↓
AI Advisor

The most important information should appear first.

77. Accessibility

WealthWise should target WCAG-compatible accessibility practices.

Requirements include:

Keyboard Navigation
Visible Focus
Readable Contrast
Semantic HTML
Accessible Forms
Screen Reader Labels
Non-Color Status Communication
78. Focus States

Interactive elements must have visible focus states.

This applies to:

Buttons
Inputs
Links
Navigation
Cards
Menus
79. Contrast

Text and controls should maintain sufficient contrast.

Financial information should never become difficult to read because of muted styling.

80. Motion

Animations should be:

Subtle
Purposeful
Fast
Non-distracting

Good uses:

Chart transition
Card appearance
Modal opening
Progress update

Avoid:

Constant floating animations
Excessive AI effects
Decorative financial number animations
81. Number Animation

Financial values may animate when changing, but the animation must not interfere with readability.

Example:

₹17,500
   ↓
₹18,000

The final value should remain immediately understandable.

82. Design Tokens

The implementation should centralize visual values.

Conceptually:

tokens/
├── colors
├── typography
├── spacing
├── radius
├── shadows
├── breakpoints
└── motion

This prevents inconsistent styling.

83. Color Token Example
color.primary
color.primaryHover
color.primarySoft

color.success
color.warning
color.danger
color.info

color.background
color.surface
color.border

color.textPrimary
color.textSecondary
color.textMuted
84. Typography Token Example
font.family
font.size.display
font.size.h1
font.size.h2
font.size.h3
font.size.body
font.size.small

font.weight.regular
font.weight.medium
font.weight.semibold
font.weight.bold
85. Spacing Token Example
space.1 = 4px
space.2 = 8px
space.3 = 12px
space.4 = 16px
space.5 = 20px
space.6 = 24px
space.8 = 32px
space.10 = 40px
space.12 = 48px
space.16 = 64px

Exact implementation may use a framework-specific token system.

86. Component Hierarchy

The UI component architecture should roughly follow:

Atoms
  ↓
Components
  ↓
Patterns
  ↓
Sections
  ↓
Screens
87. Atoms

Examples:

Button
Input
Label
Badge
Icon
Divider
Tooltip
88. Components

Examples:

MetricCard
InsightCard
GoalCard
BudgetCard
TransactionRow
ChartContainer
AIMessage
89. Patterns

Examples:

FinancialSummary
FilterBar
InsightSection
GoalOverview
BudgetOverview
AIInput
90. Sections

Examples:

DashboardHeader
DashboardFinancialSummary
DashboardInsights
DashboardGoals
DashboardBudgets
91. Screens

Examples:

DashboardPage
TransactionsPage
AnalyticsPage
BudgetsPage
GoalsPage
InsightsPage
ScenariosPage
AdvisorPage
92. Component Reuse Principle

A visual pattern should not be recreated separately for every screen.

For example:

MetricCard

should be reusable across:

Dashboard
Analytics
Goals
Reports
93. Financial Component Reuse

Core reusable components should include:

MoneyAmount
MetricCard
TrendIndicator
ProgressBar
StatusBadge
InsightCard
GoalCard
BudgetCard
TransactionTable
ChartCard
94. AI Component Reuse

Reusable AI components:

AIMessage
AIInput
PromptSuggestion
AIContextBadge
AIError
AIThinkingState
95. Trust Components

Because WealthWise handles financial information, the UI should provide subtle trust signals.

Examples:

Based on your transactions
Based on your recent history
Projected result
Estimated impact
Insufficient data

These should be contextual rather than displayed everywhere.

96. Projection vs Fact

Projected values should have a distinct but consistent visual treatment.

Example:

Current
₹6,200

Projected
₹4,960

Use labels such as:

Current
Projected
Estimated
Potential
97. AI vs System Data

The UI should distinguish between:

Verified financial data

and:

AI interpretation

Example:

Your spending was ₹6,200.

WealthWise thinks this is significantly higher
than your recent pattern.

The first is a system fact.

The second is an interpretation.

98. Design Anti-Patterns

Avoid:

Too many cards
Too many colors
Huge dashboards
Excessive gradients
Excessive shadows
Overly rounded components
Tiny financial text
Dense unexplained charts
AI everywhere
Decorative numbers
Unexplained financial scores
99. Dashboard Anti-Pattern

Do not create:

20+ cards
10 charts
5 AI summaries
Multiple competing CTAs

The dashboard should prioritize.

100. AI Anti-Pattern

Do not make:

"AI says..."
"AI thinks..."
"AI recommends..."

the visual center of every screen.

The user's financial reality should remain the center.

101. Financial Anxiety Principle

WealthWise should avoid language that judges the user.

Avoid:

You failed.
You're spending irresponsibly.
Bad financial behaviour.
You wasted money.

Prefer:

Your spending increased.
This category is above your recent average.
You're approaching your budget.
Your current savings pace may put this goal at risk.
102. Tone of Voice

WealthWise should sound:

Clear
Respectful
Calm
Practical
Non-judgmental
Confident but not absolute
103. Microcopy Examples
Success

Transaction added successfully.

Warning

You're approaching your food budget.

Insight

Food spending is above your recent average.

Goal

You're currently on track for this goal.

Insufficient Data

We need more history before we can identify a reliable trend.

Scenario

This change could increase your monthly savings by approximately ₹1,240.

AI

Based on your recent spending, here's what stands out.

104. Final Visual Identity

The overall WealthWise design should feel like:

                    WEALTHWISE

        ┌──────────────────────────────┐
        │                              │
        │        Calm                  │
        │        Financial             │
        │        Intelligence          │
        │                              │
        └──────────────────────────────┘

             Data → Insight → Action

The visual system should combine:

Financial Trust
      +
Modern Product Design
      +
Responsible AI
105. Design System Success Criteria

The design system is successful when:

A new screen can be designed
without inventing new visual rules.

A new component can reuse existing tokens.

Financial information is immediately readable.

Important insights are visually distinguishable.

AI feels integrated rather than gimmicky.

The product feels calm and trustworthy.

Desktop and mobile experiences remain coherent.