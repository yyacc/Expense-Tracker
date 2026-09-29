Expense Tracker — Architecture Documentation

Table of Contents

1. I. Overview
2. II. Technology Stack
3. III. High-Level Architecture
4. IV. Application Entry Point and Persistence
5. V. Data Model
6. VI. Model Relationships
7. VII. Planned Expense Lifecycle
8. VIII. Financial Logic
9. IX. Planned Spending vs. Actual Spending
10. X. User Interface Structure
11. XI. Data Flow
12. XII. Architectural Decisions
13. XIII. Validation and Error Handling
14. XIV. Development Evolution
15. XV. Future Architecture — Financial AI Assistant
16. XVI. Current Architecture Summary
17. XVII. Implementation Status

⸻

I. Overview

Expense Tracker is a native macOS personal finance application built with SwiftUI and SwiftData.

The application manages:

* Income
* Expenses
* Expense categories
* Budgets
* Planned expenses
* Financial summaries

The application separates planned spending from actual financial activity. This allows current savings and budget usage to reflect money that has actually been received or spent.

⸻

II. Technology Stack

* Language: Swift
* UI Framework: SwiftUI
* Persistence: SwiftData
* Development Environment: Xcode
* Platform: macOS

⸻

III. High-Level Architecture

The application uses a local, model-driven architecture.

SwiftUI Views
     │
     ▼
SwiftData Models
     │
     ▼
FinancialManager
     │
     ▼
FinancialSummary


A. SwiftUI Views

* Provide the user interface.
* Handle feature-specific interactions.
* Read and modify application data through SwiftData.

B. SwiftData Models

* Represent the application’s persistent financial data.
* Store expenses, income, categories, budgets, and planned expenses.

C. FinancialManager

* Coordinates shared financial calculations.
* Receives model data from the views.
* Produces financial summaries.

D. FinancialSummary

* Represents calculated financial values.
* Provides savings and budget-related metrics for the interface.

⸻

IV. Application Entry Point and Persistence

A. Application Entry Point

ExpenseTrackerApp is the application’s entry point.

* Creates the main application window through MainTabView.
* Configures the SwiftData model container.
* Registers the application’s persistent models.

B. Persistent Models

The application currently registers:

* Expense
* Category
* Budget
* PlannedExpense
* Income

C. Persistence Flow

The SwiftData model container is configured at the application level and made available to the SwiftUI views.

This allows individual features to query and modify persistent data through SwiftData.

⸻

V. Data Model

A. Expense

* Stores actual financial spending.
* Contains the amount, date, note, and optional category.
* Represents money that has actually been spent.
* Included in financial calculations.

B. Income

* Stores monthly income records.
* The current implementation uses one income record per month.
* The selected date is normalized to the first day of the month.

C. Category

* Defines expense categories and their associated SF Symbol icons.
* A category can be associated with multiple expenses.
* Deleting a category does not delete its associated expenses.
* The expenses remain but no longer have a category assigned.

D. Budget

* Stores the current monthly budget amount.
* The current implementation uses the first available budget record as the active budget.

E. PlannedExpense

* Stores spending that is intended but has not necessarily been paid yet.
* Contains the name, amount, due date, note, paid/unpaid state, and optional category.
* Can maintain a relationship with the Expense created when it is marked as paid.

⸻

VI. Model Relationships

A. Expense → Category

* An Expense can optionally reference a Category.
* An expense can remain uncategorized.

B. Category → Expenses

* A Category can be associated with multiple expenses.
* Deleting a category leaves its expenses intact.
* The category association is removed from those expenses.

C. PlannedExpense → Category

* A planned expense can optionally be assigned to a category.

D. PlannedExpense → Expense

* A planned expense can maintain a reference to the actual Expense created when it is marked as paid.

Relationship Overview

Category
   │
   ├── Expense
   └── Expense
         ...
PlannedExpense
   │
   ├── Category
   │
   └── Created Expense

⸻

VII. Planned Expense Lifecycle

Planned expenses are intentionally kept separate from actual expenses.

Planned Expense
      │
      ▼
   Unpaid
      │
      │ Mark as Paid
      ▼
Create Expense
      │
      ▼
    Paid

A. Marking as Paid

When an unpaid planned expense is marked as paid:

1. A corresponding Expense is created.
2. The planned expense stores a reference to that expense.
3. The planned expense is marked as paid.
4. The new expense becomes part of actual spending calculations.

B. Reverting to Unpaid

The implementation supports reversing the paid state.

1. The associated created expense is removed.
2. The planned expense reference is cleared.
3. The planned expense is marked as unpaid.

C. Deleting a Planned Expense

* Any associated created expense is removed.
* The planned expense is then deleted.

⸻

VIII. Financial Logic

A. FinancialManager

FinancialManager coordinates shared financial calculations.

1. Responsibilities

* Determines the current month.
* Determines the next month.
* Filters expenses to the current month.
* Calculates total paid expenses.
* Finds current-month income.
* Finds the current budget.
* Creates a FinancialSummary.

2. Data Access

FinancialManager does not directly query SwiftData.

Instead, views provide the relevant model data to the manager for processing.

B. FinancialSummary

FinancialSummary represents the calculated financial state used by the application.

1. Savings

Savings = Monthly Income − Total Paid Expenses

2. Budget Calculations

The summary provides:

* Total paid expenses
* Budget limit
* Budget remaining
* Budget progress

3. Over-Budget Handling

* Budget progress can exceed 100% internally.
* The interface caps the visual progress indicator at 100%.
* The amount over budget is displayed separately.

⸻

IX. Planned Spending vs. Actual Spending

The application treats planned and actual spending differently.

Income
  │
  ├───────────────┐
  │               │
  ▼               ▼
Actual Spending   Planned Spending
  │               │
  ▼               ▼
Current Savings   Future Planning

A. Actual Spending

* Represents expenses that have already been paid.
* Included in current savings calculations.
* Included in budget spending.

B. Planned Spending

* Represents expenses that have not yet been paid.
* Does not reduce current savings.
* Does not count as paid budget spending.

C. Transition

When a planned expense is marked as paid:

1. It creates an actual Expense.
2. The planned expense is marked as paid.
3. The new expense becomes part of financial calculations.

⸻

X. User Interface Structure

A. Dashboard

The dashboard provides the main financial overview.

1. Financial Information

* Monthly income
* Savings
* Total paid expenses
* Budget status

2. Expense Information

* Upcoming planned expenses
* Recent expenses

3. Data Processing

The dashboard obtains its primary financial calculations through FinancialManager.

B. Expense Management

1. Creation and Editing

* Create expenses.
* Edit existing expenses.
* Assign categories.
* Leave expenses uncategorized.
* Add optional notes.

2. Expense Details

* View amount.
* View category.
* View date.
* View notes.
* Delete expenses.

3. Expense History

* Displays expenses sorted by date.
* Provides access to individual expense details.

C. Category Management

1. Category Operations

* Create categories.
* Edit categories.
* Delete categories.
* Assign SF Symbol icons.

2. Organization

* Categories are displayed alphabetically.
* Expenses can reference a category or remain uncategorized.

D. Income Management

1. Monthly Income

* Create or edit income for a selected month.
* The selected date is normalized to the first day of the month.

2. Duplicate Prevention

* The application checks whether income already exists for the selected month.
* If an existing record is found, its amount is updated instead of creating another record.

E. Planning

The planning interface manages planned expenses and their transition into actual spending.

1. Planning Information

* Planned expenses
* Due dates
* Paid/unpaid status
* Overdue status
* Available money after unpaid planned expenses

2. Available Money

Available Money = Current Month Income − Unpaid Planned Expenses

F. Budget Management

1. Budget Information

* Budget limit
* Paid expenses
* Remaining budget
* Budget progress
* Over-budget status

2. Planned Spending

* Unpaid planned expenses are displayed separately.
* They are not included in paid budget spending.

G. Summary and Reporting

1. Monthly Filtering

* Expenses can be viewed by month.
* Previous and next months can be selected.

2. Category Breakdown

* Expenses are grouped by category.
* Uncategorized expenses are grouped separately.
* Categories are ordered from highest spending to lowest spending.

⸻

XI. Data Flow

A typical dashboard calculation follows this flow:

SwiftData
   │
   ├── Expenses
   ├── Income
   ├── Budgets
   └── Planned Expenses
          │
          ▼
  FinancialManager
          │
          ▼
  FinancialSummary
          │
          ▼
     Dashboard

A. Data Retrieval

* SwiftUI views retrieve model data through SwiftData.
* Relevant model collections are passed to the financial calculation layer.

B. Financial Processing

* FinancialManager performs shared aggregation and filtering.
* FinancialSummary calculates the resulting financial state.

C. Presentation

* The resulting values are displayed by the relevant SwiftUI views.

Some feature-specific views also perform their own aggregation for information such as monthly category totals or planned spending.

⸻

XII. Architectural Decisions

A. Local Persistence

1. Decision

The application uses SwiftData for persistent financial data.

2. Reason

* Keeps the core application local.
* Does not require a remote database for its financial records.

B. Decimal Financial Values

1. Decision

Financial amounts use Decimal.

2. Reason

Decimal provides a more appropriate numeric representation for monetary calculations than binary floating-point values.

C. Separate Planned and Actual Expenses

1. Decision

Planned expenses are modeled separately from actual expenses.

2. Reason

This prevents future or unpaid spending from being treated as money that has already been spent.

D. Centralized Financial Summary

1. Decision

Common financial calculations are coordinated through FinancialManager and represented through FinancialSummary.

2. Reason

This keeps the primary savings and budget calculations consistent across major parts of the application.

E. Direct SwiftData Access in Feature Views

1. Decision

Feature views can interact directly with SwiftData through @Query and modelContext.

2. Reason

This keeps the current application relatively straightforward while shared financial calculations remain in the financial logic layer.

⸻

XIII. Validation and Error Handling

A. Financial Input

Forms validate monetary input before saving.

* Removes commas from entered amounts.
* Converts input into Decimal.
* Rejects zero or negative amounts.

B. Text Input

* Category names must contain text.
* Planned expense names must contain text.

C. Persistence Errors

* SwiftData save operations are handled for errors.
* Errors are surfaced or logged depending on the feature.

D. Destructive Actions

* Expense deletion requires confirmation.
* Planned expense deletion handles associated created expenses.
* Marking planned expenses as paid can be reversed.

⸻

XIV. Development Evolution

The project began as a basic expense tracker and gradually expanded into a broader personal finance application.

Basic Expense Tracking
        │
        ▼
Income Tracking
        │
        ▼
Categories & Budgets
        │
        ▼
Planned Expenses
        │
        ▼
Financial Summaries
        │
        ▼
Future Financial AI

A. Initial Scope

* Record expenses.
* View spending.

B. Expanded Scope

* Track income.
* Organize expenses with categories.
* Manage budgets.
* Plan future expenses.
* Generate financial summaries.

C. Key Architectural Change

A major development decision was separating planned spending from actual expenses and introducing a dedicated financial calculation layer.

⸻

XV. Future Architecture — Financial AI Assistant

A future version is planned to include a Financial AI Assistant capable of analyzing the application’s financial data.

A. Potential Interactions

* Estimate how savings would change after reducing an expense.
* Plan for a future purchase.
* Explore ways to reach a savings target.
* Compare the effect of different spending changes.
* Identify lower-priority expenses that could be reduced to reach a financial goal.

B. Planned Architecture

The AI layer would sit above the application’s deterministic financial calculations.

Financial Data
      │
      ▼
Deterministic Calculations
      │
      ▼
Financial Analysis
      │
      ▼
AI Assistant
      │
      ▼
User-facing Explanation

C. Separation of Responsibilities

* The application remains responsible for authoritative financial calculations.
* The AI interprets financial information and communicates it to the user.
* The AI should not independently determine authoritative financial arithmetic.

D. Implementation Status

The Financial AI Assistant is planned and is not currently part of the implemented core application.

⸻

XVI. Current Architecture Summary

SwiftUI
   │
   ├── Dashboard
   ├── Expenses
   ├── Income
   ├── Planning
   ├── Budgets
   ├── Categories
   └── Summary
          │
          ▼
      SwiftData
          │
          ▼
 Financial Models
          │
          ▼
 FinancialManager
          │
          ▼
 FinancialSummary

A. Current Implementation

* Native macOS application.
* SwiftUI interface.
* SwiftData persistence.
* Separate financial models.
* Deterministic financial calculations.
* Planned-to-actual expense workflow.
* Local financial data management.

B. Future Direction

The architecture leaves room for an AI-assisted analysis layer without making the AI responsible for the application’s core financial calculations.

⸻

XVII. Implementation Status

Component---------------------------------------Status
Core application--------------------------------Implemented
SwiftData persistence---------------------------Implemented
Expense tracking--------------------------------Implemented
Income tracking---------------------------------Implemented
Category management-----------------------------Implemented
Budget management-------------------------------Implemented
Planned expenses--------------------------------Implemented
Financial calculation layer---------------------Implemented
Planned-to-actual expense workflow--------------Implemented
Financial AI Assistant--------------------------Planned
