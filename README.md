# Tool Store Management

A full-stack application for managing the operations of a tool store. The system provides a catalogue of tools and supports purchases, rentals, repair requests and promotional offers.

> **Academic project** — Developed for *Software Engineering II (Ingeniería del Software II)*, third year of the Bachelor's Degree in Computer Engineering at the **University of Castilla-La Mancha (UCLM)**, School of Computer Engineering (Albacete, Spain), during the 2025–2026 academic year.

## Table of contents

- [Overview](#overview)
- [Main features](#main-features)
- [Technology stack](#technology-stack)
- [Architecture](#architecture)
- [Project structure](#project-structure)
- [Getting started](#getting-started)
- [Database configuration](#database-configuration)
- [Running the tests](#running-the-tests)
- [Authentication status](#authentication-status)
- [Screenshots](#screenshots)
- [Development workflow](#development-workflow)
- [Team](#team)

## Overview

Tool Store Management is designed to manage the main processes of a tool shop. Users can browse the tool catalogue and create different types of operations. The application is divided into a REST API, a Blazor web client and a .NET MAUI Blazor Hybrid client.

The project was developed as a team assignment, following the software engineering practices covered in the course: requirements analysis, domain modelling, iterative development, version control, automated testing and collaborative work.

## Main features

| Area | Description |
|---|---|
| **Tool catalogue** | Browse tools and their information, including name, material, price, repair time and manufacturer. |
| **Purchases** | Select tools and quantities, provide a shipping address and payment method, and review the purchase details. |
| **Rentals** | Select tools and a rental period. The operation includes start and end dates and a total price. |
| **Repair requests** | Submit tools for repair, specifying the relevant dates, problem description and price information. |
| **Promotional offers** | Create offers with a validity period and discounts for selected tools and a target customer group. |
| **User accounts** | Login and registration interfaces are included through ASP.NET Core Identity. See [Authentication status](#authentication-status) for the current limitations. |

The application models credit card, PayPal and cash as payment method options.

## Technology stack

**Backend**
- C# and .NET 8
- ASP.NET Core Web API
- Entity Framework Core 8
- SQL Server / LocalDB
- SQLite (in-memory database option)
- ASP.NET Core Identity
- Swagger / OpenAPI

**Frontend**
- Blazor Web App
- .NET MAUI Blazor Hybrid
- Razor components
- Bootstrap

**Additional tools and libraries**
- NSwag-generated API client
- xUnit and Moq for automated tests
- GitHub Actions for continuous integration
- GitHub Projects for task and sprint management

## Architecture

The solution follows a client–server architecture. The web and MAUI clients communicate with the backend through HTTP using a generated API client. The API contains the business endpoints and uses Entity Framework Core to access the database.

```text
+--------------------------+       +--------------------------+
|   AppForSEII2526.Web     |       |   AppForSEII2526.Maui    |
|     Blazor Web App       |       |   Blazor Hybrid client    |
+------------+-------------+       +-------------+------------+
             |                                   |
             +----------------+------------------+
                              |
                       HTTP / REST API
                              |
                 +------------v-------------+
                 |     AppForSEII2526.API    |
                 |       ASP.NET Core       |
                 | Controllers, DTOs,       |
                 | domain and data access   |
                 +------------+-------------+
                              |
                         EF Core
                              |
                 +------------v-------------+
                 | SQL Server / SQLite      |
                 +--------------------------+
```

The API is organised around the main business areas, with controllers for purchases, rentals, offers, repairs, tools and manufacturers. Data Transfer Objects (DTOs) define the data exchanged between the clients and the API.

The database provider can be selected through the `DBConnection2Use` environment variable:
- `SQLite`: in-memory SQLite database.
- `AzureSQL`: Azure SQL using the `AzureSQL` environment variable as the connection string.
- Any other value or an unset variable: SQL Server LocalDB using the configured `DefaultConnection`.

## Project structure

```text
.
├── .github/
│   └── workflows/                 # GitHub Actions workflows
├── req/                            # Requirements and design documentation
├── src/
│   ├── AppForSEII2526.API/         # ASP.NET Core REST API
│   │   ├── Controllers/
│   │   ├── Data/
│   │   ├── DTOs/
│   │   ├── Migrations/
│   │   └── Models/
│   ├── AppForSEII2526.Web/         # Blazor web application
│   │   └── Components/
│   └── AppForSEII2526.Maui/       # .NET MAUI Blazor Hybrid application
├── test/
│   ├── AppForSEII2526.UT/          # Unit tests
│   └── AppForSEII2526.UIT/         # UI tests
└── AppForSEII2526.sln
```

## Getting started

### Prerequisites

- .NET 8 SDK
- Visual Studio 2022 with the **ASP.NET and web development** workload
- SQL Server LocalDB (usually installed with Visual Studio), or configure the API to use SQLite
- .NET MAUI workload if you want to run the MAUI client

### Clone the repository

```bash
git clone https://github.com/ivangomezr/tool-store-management.git
cd tool-store-management
git checkout development
```

### Configure and run

1. Open `AppForSEII2526.sln` in Visual Studio.
2. Check the `DefaultConnection` connection strings in the API and Web project configuration files.
3. Configure the API base URL in `src/AppForSEII2526.Web/appsettings.json`, under `AppForHerramientas_API`, so that it matches the URL and port used by the API launch profile.
4. Set `AppForSEII2526.API` and `AppForSEII2526.Web` as startup projects.
5. Start the solution.

The API applies Entity Framework Core migrations at startup when using SQL Server. When using the SQLite option, the API creates the in-memory database on startup.

> **Note:** The API and Web projects must use matching connection settings and the Web project's API URL must point to the actual address where the API is listening. These values can differ between Visual Studio launch profiles and machines.

## Database configuration

The default configuration uses SQL Server LocalDB. To select the in-memory SQLite provider when starting the API from PowerShell:

```powershell
$env:DBConnection2Use = "SQLite"
dotnet run --project src/AppForSEII2526.API/AppForSEII2526.API.csproj
```

For Azure SQL, set `DBConnection2Use` to `AzureSQL` and provide the connection string through the `AzureSQL` environment variable.

Do not commit personal credentials or production connection strings to the repository.

## Running the tests

Run the solution's automated tests from the repository root:

```bash
dotnet test
```

The solution contains separate unit-test and UI-test projects under `test/`.

## Authentication status

The Web application includes ASP.NET Core Identity pages for login and registration.

**Current limitation:** the registration page can now be opened, but the email sender / account-confirmation service is not configured as a working email delivery service. Consequently, confirmation emails are not delivered and the complete registration and account-confirmation workflow is **not operational**. The presence of the registration page should not be interpreted as evidence that email-based registration is fully implemented.

Authentication is therefore a known incomplete area of the current project.

## Screenshots

Screenshots of the running application will make the repository easier to understand. Add the images to `docs/screenshots/` and embed them here as the project evolves.

Suggested screenshots:

1. **Main page / dashboard** — the first screen users see when opening the application.
2. **Tool catalogue** — the list of tools, including filters and relevant product information.
3. **Purchase workflow** — tool selection and the purchase details/confirmation screen.
4. **Rental workflow** — rental selection and the date-range form.
5. **Repair workflow** — repair request form, including the problem description.
6. **Offers** — offer creation or offer listing, showing discount and validity dates.
7. **API Swagger UI** — the API endpoints grouped by controller.
8. **Login and registration pages** — useful to show the implemented interface, but make sure the README's authentication limitation remains clear.

Example Markdown to add once the screenshots are available:

```md
![Tool catalogue](docs/screenshots/tool-catalogue.png)
```

Prefer screenshots at a consistent viewport size. Crop out personal information, credentials, tokens, connection strings and any real customer data. A good set of four to six representative images is usually enough; the catalogue, main page, one complete workflow and Swagger are the best starting points.

## Development workflow

The project was developed collaboratively using an iterative workflow based on Scrum practices. Work was organised through issues, a project board, feature branches and pull requests. GitHub Actions was used for continuous integration.

## Team

Developed by **TheHookahMasters (Group A)** for Software Engineering II at UCLM:

| Member | GitHub |
|---|---|
| Iván Gómez | [@ivangomezr](https://github.com/ivangomezr) |
| Alejandro Navarrete | [@AlejandroNvarrete](https://github.com/AlejandroNvarrete) |
| Sergio Requena | [@SergioReque](https://github.com/SergioReque) |
| José Enrique Ortiz | [@kikolonx](https://github.com/kikolonx) |

---

*This repository is an academic project and is not intended to be presented as a production-ready commercial application.*
