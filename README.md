# Employee Management Age System (example.com)

This repository contains the source code for the technical assessment (interview-question-001). It is a full-stack web application designed to manage employee records with an automated age calculation feature.

## Tech Stack
- **Frontend:** Vue 3 , TypeScript, Vite, SweetAlert2
- **Backend:** C# (.NET 8)
- **Database:** PostgreSQL

## Prerequisites
Ensure you have the following installed on your local machine:
- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Bun](https://bun.sh/) (or Node.js/npm)
- [PostgreSQL](https://www.postgresql.org/)

## How to Run the Project

### 1. Backend Setup (API)
1. Navigate to the API directory:
```bash
cd example.com.api
```
2.Update the Database Connection String in appsettings.json with your local PostgreSQL credentials:
  ```JSON
  "DefaultConnection": "Host=localhost;Database=ExampleDb;Username=postgres;Password=YOUR_PASSWORD"
```
3.Apply database migrations to create the required tables:
  ```Bash
  dotnet ef database update
```
4.Start the backend server:
  ```Bash
  dotnet run
```
The API will be running at http://localhost:5188

### 2. Frontend Setup (Web)
1.Open a new terminal and navigate to the Web directory:
  ```Bash
  cd example.com.web
```
2.Install the required dependencies:
  ```Bash
  bun install
```
3.Start the development server:
  ```Bash
  bun run dev
```
The web application will be accessible at http://localhost:5173.

## Testing Data (Mockup)

You can use the following mock data to test the application's "Add" and "Age Calculation" functionalities.

| First Name | Last Name | Date of Birth (DD/MM/YYYY) | Address |
| :--- | :--- | :--- | :--- |
| สมชาย | ใจดี | 15/05/1990 | 123 ม.1 ต.คลองศก อ.พนม จ.สุราษฎร์ธานี 84250 |
| วันดี | มีสุข | 22/08/1995 | 45/6 ถ.สุขุมวิท แขวงคลองเตย เขตคลองเตย กรุงเทพฯ 10110 |
| ภูมิใจ | รักชาติ | 10/12/1998 | 88/8 ม.5 ต.หนองปรือ อ.บางละมุง จ.ชลบุรี 20150 |
| ดาริกา | ฟ้าใส | 05/02/2002 | 99 ถ.นิมมานเหมินท์ ต.สุเทพ อ.เมือง จ.เชียงใหม่ 50200 |
| ก้องภพ | นพรัตน์ | 30/09/1985 | 11/22 ถ.มิตรภาพ ต.ในเมือง อ.เมือง จ.นครราชสีมา 30000 |
| นลินี | ศรีสวัสดิ์ | 14/04/1997 | 77 ม.3 ต.ตลาดขวัญ อ.เมือง จ.นนทบุรี 11000 |
| ธนากร | พรหมพิทักษ์ | 25/11/1987 | 55/5 ถ.เพชรเกษม แขวงบางแค เขตบางแค กรุงเทพฯ 10160 |
| อารยา | เมตตา | 08/07/2005 | 234 ม.9 ต.บ้านเป็ด อ.เมือง จ.ขอนแก่น 40000 |

> **Note:** The system will automatically calculate the employee's age using the formula `Current Year - Birth Year`. Additionally, the Date of Birth is formatted to the Thai Buddhist Era (พ.ศ.) in the view modal as required.
