# Expense Tracker

A native macOS personal finance application for recording income, expenses, planned spending, budgets, and savings.

Overview

Expense Tracker started as a simple expense-tracking application and evolved into a broader personal finance system.

The application separates actual financial activity from planned spending so that current savings and budget usage are based on actual income and paid expenses.

Core Features

* Expense tracking
* Income tracking
* Expense categories
* Budget management
* Planned expenses
* Financial summaries
* Local data persistence
* Overview/dashboard

Architecture

The application is built with SwiftUI and SwiftData.

At a high level, the application consists of:

* SwiftUI — user interface and interaction
* Financial Logic — financial calculations and summaries
* SwiftData — local data persistence
* Financial Models — income, expenses, categories, budgets, and planned expenses

The exact architecture and relationships between these components are documented separately as the project develops.

Financial Model

Current savings are calculated from actual financial activity:

Savings = Income − Paid Expenses

Planned but unpaid expenses are kept separate from current spending and savings. When a planned expense is paid, it becomes actual spending and is incorporated into the financial calculations.

Technologies

* Swift
* SwiftUI
* SwiftData
* Xcode
* macOS

Development

The project evolved incrementally from a basic expense tracker into a more structured personal finance application.

Important development decisions included introducing separate models for income, expenses, planned expenses, budgets, and categories, followed by a centralized financial calculation layer.

Roadmap

Financial AI Assistant

A future version of the application is planned to include a Financial AI Assistant that can analyze the user’s financial data and answer questions such as:

* How much could I save if I reduce a particular expense?
* How long would it take to save for a purchase?
* How could I adjust lower-priority spending to reach a financial goal?
* How would a proposed purchase affect my savings?
* What changes could help me reach a particular savings target?

The planned architecture separates deterministic financial calculations from AI reasoning:

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

The Financial AI Assistant is a future development goal and is not currently part of the implemented core application.

Status

Active project — core application implemented; AI capabilities planned.
