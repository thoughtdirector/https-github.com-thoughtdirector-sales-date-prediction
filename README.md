# Sales Date Prediction – Fullstack Application

## Project Overview

Sales Date Prediction is a full-stack web application for managing customer sales orders and predicting the next order date for each customer based on historical data. The application consists of a **frontend** and a **backend** working together:
- The **frontend** is built with **Angular 17** and provides an interface to view customers, their past order history, and the predicted next order date. Users can search customers in real-time, view detailed order lists in a modal, and create new orders via a form. It also includes a D3.js-powered bar chart component for data visualization.
- The **backend** is an **ASP.NET Core 8** Web API (C#) that handles data storage and business logic. It exposes REST endpoints to retrieve customers, orders, employees, shippers, products, and to create new orders. The backend uses a SQL Server database (with sample data) and implements the prediction algorithm for the next order date per customer.

Together, these components allow users to browse customer information, add new sales orders, and see when each customer is likely to place their next order.

## Folder Structure

This project is divided into two separate Git repositories (one for the backend API and one for the frontend UI). To run the full application, you will need to clone and set up **both** repositories:

- **Backend** – *Sales Date Prediction API* (ASP.NET Core Web API)  
  Repository: **`thoughtdirector/sales-date-prediction-backend`** (GitHub)  
  This contains the Visual Studio solution with multiple projects:
  - `SalesDatePrediction.API` – The Web API project exposing REST endpoints.
  - `SalesDatePrediction.Core` – Domain and application logic (entities, DTOs, interfaces).
  - `SalesDatePrediction.Infrastructure` – Data access layer (repository implementations, database context).
  - `SalesDatePrediction.Tests` – Unit tests for the core logic.
  
- **Frontend** – *Sales Date Prediction UI* (Angular 17 app)  
  Repository: **`thoughtdirector/sales-date-prediction-frontend`** (GitHub)  
  This contains the Angular project structure:
  - Angular components such as `order-list`, `order-details`, `order-form`, `d3-chart` (for the bar chart).
  - Services for API calls to the backend.
  - Angular Material modules for UI elements.
  - Environment configuration files for setting the API base URL.

> **Note:** These repositories are separate; there is no single monolithic repository containing both. The instructions below will guide you through setting up each part and running them together.

## Prerequisites

Make sure your development environment has the following installed:

- **Node.js** (v18 or higher) and **npm** – Required to run the Angular frontend. You can download Node from the official website. Having npm (Node's package manager) is typically included with Node installations.
- **Angular CLI** – Recommended for running and serving the Angular project. Install it globally with `npm install -g @angular/cli`. (Ensure this matches Angular 17 compatibility, which the latest CLI will.)
- **.NET 8 SDK** – Required to build and run the backend Web API. Download the .NET 8 SDK from Microsoft's official site if not already installed.
- **SQL Server** – A running SQL Server instance for the backend database. This can be:
  - **On Windows:** SQL Server or SQL Express installed locally. 
  - **On Linux/macOS:** Docker container for SQL Server, or Azure SQL, etc. (Any accessible SQL Server will work.)
- **Git** – To clone the repositories.

Optional but helpful tools:
- **SQL Server Management Studio (SSMS)** or **Azure Data Studio** – to execute the provided database setup script (`DBSetup.sql`) and view the database.
- An IDE or text editor: *Visual Studio 2022* (for C# backend) or *Visual Studio Code / IntelliJ / etc.* for editing code if needed.

## Getting Started (Setup Instructions)

Follow these steps to set up and run the Sales Date Prediction app on your local machine:

1. **Clone the Backend and Frontend Repositories**  
   Choose a directory on your machine and clone both projects from GitHub:  
   ```bash
   # Clone the backend API repository
   git clone https://github.com/thoughtdirector/sales-date-prediction-backend.git
   # Clone the frontend Angular repository
   git clone https://github.com/thoughtdirector/sales-date-prediction-frontend.git
   ```  
   This will create two folders: **`sales-date-prediction-backend`** and **`sales-date-prediction-frontend`**. You will handle each separately in the next steps.

2. **Install Dependencies**  
   - **Backend Dependencies:** The backend is a .NET project, so it uses NuGet for dependencies. Navigate to the backend folder and restore the .NET packages:  
     ```bash
     cd sales-date-prediction-backend
     dotnet restore
     ```  
     This will download all NuGet packages needed for the solution. (You can also run `dotnet build`, which will implicitly restore and compile the project. If you plan to use Visual Studio, opening the solution file `SalesDatePrediction.sln` will also restore dependencies automatically.)  
   - **Frontend Dependencies:** The frontend uses npm for dependencies. In a new terminal, navigate to the frontend project folder and run:  
     ```bash
     cd sales-date-prediction-frontend
     npm install
     ```  
     This will install all the Node modules required for the Angular app (as specified in `package.json`). Make sure you have an internet connection for this step.

3. **Configure Environment & Database**  
   Before running the applications, some configuration is needed for the backend database and API URLs:
   - **Setup the Database (Backend):** The backend expects a SQL Server database named **`StoreSample`** with certain tables and sample data. In the backend repository, locate the file `DBSetup.sql` (found in the root of the project). Execute this SQL script on your SQL Server instance to create and populate the `StoreSample` database:
     - If using **SQL Server Management Studio (SSMS)** or **Azure Data Studio**, open the script file and run it against your server.
     - This will create the necessary tables (Customers, Orders, Products, etc.) and some sample data used for predictions.
     - Ensure your SQL Server is running and accessible. The default connection string uses Windows Integrated Security (`Trusted_Connection=True`), which works on Windows if SQL is on the same machine. If you are on Linux/macOS or using SQL Auth, you may need to adjust the connection string for your environment.
   - **Verify Backend Connection String:** Open the backend project's configuration file (`SalesDatePrediction.API/appsettings.json`). Check the `"ConnectionStrings:DefaultConnection"` value and make sure it matches your SQL Server setup. By default it is:  
     ```json
     "ConnectionStrings": {
       "DefaultConnection": "Server=.;Database=StoreSample;Trusted_Connection=True;MultipleActiveResultSets=true"
     }
     ```  
     - **If you installed SQL Server locally on Windows**, the default `"Server=."` (which points to the local SQL instance) with `Trusted_Connection=True` should work.  
     - **If using a different server/instance or SQL authentication**, update this connection string. For example: `"Server=localhost;Database=StoreSample;User Id=<username>;Password=<password>;MultipleActiveResultSets=true;"`.  
     *(Alternatively, you can set an environment variable `ASPNETCORE_ConnectionStrings__DefaultConnection` to override this at runtime, but editing appsettings.json is straightforward for local development.)*
   - **Frontend API URL Configuration:** The Angular app needs to know how to reach the backend API. By default, the development environment file is configured to use `https://localhost:7299/api` (which is the default URL of the .NET API when run locally with HTTPS). Open the file **`sales-date-prediction-frontend/src/environments/environment.ts`** and confirm it has the correct base URL for the API:  
     ```ts
     export const environment = {
       production: false,
       apiUrl: 'https://localhost:7299/api'
     };
     ```  
     If you plan to run the backend on a different host or port, update `apiUrl` accordingly. For example, if your backend runs on port 8000 in HTTP, you might set `apiUrl: 'http://localhost:8000/api'`. In production builds, ensure the `environment.prod.ts` is also updated if needed.

4. **Run the Backend Server**  
   Now start the backend API server:
   - Make sure you are in the backend project directory (where the .sln file is, or specifically in the API project folder). If not already, navigate to the backend directory: `cd sales-date-prediction-backend`.  
   - If you have multiple projects in the solution, you should run the API project. From the command line:  
     ```bash
     dotnet run --project SalesDatePrediction.API
     ```  
     This will build and launch the Web API. By default (as configured in launchSettings), it should start on **HTTPS port 7299** (and an HTTP port, possibly 5248 or similar). You should see console output from the .NET runtime indicating it's listening on some URL(s). For example, you might see: *"Now listening on: https://localhost:7299"*.
   - Alternatively, you can run the API using an IDE:
     - In Visual Studio, open `SalesDatePrediction.sln`, set **SalesDatePrediction.API** as the startup project, and click the Run/Debug button. This will also launch the API on the configured port.
   - **CORS Note:** The frontend will be served from a different origin (port 4200 by default), so the backend must allow cross-origin requests. The API is configured for development, but if you encounter any CORS errors (Cross-Origin Resource Sharing), you may need to enable CORS in the API. In a typical ASP.NET Core setup, this involves adding a CORS policy to allow `http://localhost:4200`. (Check the API's startup configuration or add one if needed. For local testing, you can allow all origins.) By default, when running via Visual Studio or `dotnet run`, the template may already allow it, but just be aware in case of issues.

5. **Run the Frontend Development Server**  
   Next, start the Angular frontend:
   - Open a new terminal and navigate to the frontend project folder (if not already there): `cd sales-date-prediction-frontend`.
   - Use the Angular CLI to serve the application:  
     ```bash
     ng serve
     ```  
     This will compile the Angular application and start a dev server on **http://localhost:4200/** by default. After a successful compilation, you should see a message like: *"**√** Compiled successfully"*, and *"Listening on: localhost:4200"*.  
   - If you don't have the Angular CLI globally, you can also run the npm script: `npm start` (assuming it's configured to call `ng serve`). The effect is the same.
   - **Custom Port (Optional):** If port 4200 is already in use or you prefer a different port (for example, the question mentioned port 5173 which is Vite's default), you can run `ng serve --port 5173`. Just ensure that if you change the port, the backend's CORS settings (and any proxy or environment config) are adjusted to allow that port. For most cases, sticking with 4200 is simplest.

6. **Access the Application in the Browser**  
   Once both the backend and frontend servers are up and running:
   - Open your web browser and navigate to **[http://localhost:4200](http://localhost:4200)**. This is the Angular application’s interface.
   - The app will load, and it will make API calls to the backend (e.g., to `https://localhost:7299/api/...`) to fetch data. Ensure the backend is running; if not, the frontend will show errors when trying to retrieve data.
   - You should see the **Sales Date Prediction** UI, which includes:
     - A list of customers with their last order date and next predicted order date.
     - A search box to filter customers by name.
     - Buttons like **VIEW ORDERS** (to open a modal with that customer's order history) and **NEW ORDER** (to open a form for adding a new order).
     - A section for a D3.js bar chart where you can input numbers and see a dynamic chart.
   - You can interact with the app: try searching for a customer, viewing orders, or creating a new order. When creating an order, after submission, the backend will save it to the database and it should reflect in the list (and potentially affect the next predicted date).
   - If everything is set up correctly, the frontend and backend will communicate seamlessly. The default configuration uses HTTPS for the API; your browser might prompt to trust the self-signed development certificate the first time. It's usually fine to accept that for localhost.

## Tech Stack

This project utilizes the following technologies:

- **Frontend:** Angular 17 (TypeScript) framework for building the single-page application. It uses Angular Material for UI components (tables, forms, modals) and D3.js for data visualization (interactive bar chart). The development server is powered by Angular CLI (Webpack dev server under the hood).
- **Backend:** ASP.NET Core 8 Web API written in C#. It follows a Clean Architecture/Layered approach (separating API controllers, core logic, and infrastructure). Data is handled via a repository pattern (with likely use of Entity Framework Core or similar for database access). The backend exposes RESTful endpoints and includes logic to predict the next order date for customers based on their order history.
- **Database:** Microsoft SQL Server is used to store application data (customers, orders, products, employees, shippers, etc.). A SQL script is provided to set up the schema and seed sample data. In development, the database can be a local SQL Server instance.
- **Communication:** The frontend and backend communicate over HTTP/HTTPS using JSON APIs. In development, CORS is enabled to allow the Angular frontend (running on localhost:4200) to talk to the API (running on localhost:7299).

---
You now have both the backend and frontend running locally. If you run into any issues, double-check the configuration steps (database setup and URLs). 
