To create the two tables (`Author` and `Books`) in your database with primary key auto-increment functionality, you can follow these SQL scripts.

Below is the SQL command to create the two tables with primary keys and the foreign key relationship between `Books` and `Author`:

### SQL Script for Creating the Tables

```sql
USE Company;  -- Use your existing database

-- Create the Author table with auto-increment for AuthorId
CREATE TABLE Author (
    AuthorId INT IDENTITY(1,1) NOT NULL PRIMARY KEY,  -- Auto-increment ID
    AuthorName NVARCHAR(50) NOT NULL
);

-- Create the Books table with a foreign key reference to Author
CREATE TABLE Books (
    BookId INT IDENTITY(1,1) NOT NULL PRIMARY KEY,  -- Auto-increment ID starting at 11
    BookName NVARCHAR(50) NOT NULL,
    ReleaseDate DATE,
    AuthorId INT NOT NULL,  -- Foreign key reference to Author table
    CONSTRAINT FK_Books_Author FOREIGN KEY (AuthorId) 
        REFERENCES Author (AuthorId)
        ON DELETE CASCADE 
        ON UPDATE CASCADE
);
```

### Key Notes:
- **IDENTITY(1,1)**: Auto-increment in SQL Server. It starts at 1 and increments by 1 for each new row.
- **FOREIGN KEY**: This ensures the relationship between the `Books` table and the `Author` table. The `ON DELETE CASCADE` and `ON UPDATE CASCADE` ensure that changes to the `Author` table propagate to the `Books` table.
- **Primary Key Auto-increment**: The primary key `AuthorId` and `BookId` will automatically increment when you insert a new record.

### Steps to Execute:

1. Open **SQL Server Management Studio (SSMS)**.
2. Connect to your SQL Server instance.
3. Select your database, for example, `Company`.
4. Open a new query window and paste the above SQL script.
5. Click **Execute** to create the tables in your database.

### After Executing:

Once the tables are created, you can scaffold them into your .NET 8 Web API project using the scaffolding method (as discussed in the previous steps). 

This is the initial database setup step required before generating your `DbContext` and model classes. Let me know if you need further guidance!

# RESTful Web API in .NET 8 Using Entity Framework Core Scaffold

This repository provides step-by-step instructions for building a RESTful Web API in .NET 8 using the scaffolding method of **Entity Framework Core**. With this approach, we will automatically generate `DbContext` and model classes based on an existing database.

## Prerequisites

Before starting, make sure you have the following installed:

- [Visual Studio 2022](https://visualstudio.microsoft.com/downloads/) (with .NET 8 SDK)
- [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) (or any SQL Server instance)
- .NET 8 SDK
- Entity Framework Core tools

## Steps

### 1. Create a New .NET 8 Web API Project

1. Open **Visual Studio 2022**.
2. Select **Create a new project**.
3. Choose **ASP.NET Core Web API** from the project templates.
4. Name your project (e.g., `MultiTableWebAPI`) and click **Next**.
5. Select **.NET 8.0** as the framework and click **Create**.

### 2. Add the Necessary NuGet Packages

Open the **NuGet Package Manager Console** (`Tools > NuGet Package Manager > Package Manager Console`) and install the following packages:

```bash
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
```

These packages enable Entity Framework Core and allow us to scaffold the database.

### 3. Add the Connection String

1. Open the `appsettings.json` file in the root directory of your project.
2. Add the following connection string inside the `ConnectionStrings` section. Replace `YourDatabaseName` with your actual database name:

```json
{
  "ConnectionStrings": {
    "DBConnection": "Server=DESKTOP-RHER41Q; Database=Company; Trusted_Connection=True; TrustServerCertificate=True;"
 }
}
```

### 4. Scaffold the `DbContext` and Models

Now we will use the **Entity Framework Core Scaffold-DbContext** command to automatically generate `DbContext` and models based on the existing database.

1. Open **Package Manager Console** (`Tools > NuGet Package Manager > Package Manager Console`).
2. Run the following command (replace the connection string and context name with your actual values):

```bash
Scaffold-DbContext "Server=localhost;Database=YourDatabaseName;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context YourDbContextName -Force
Or 
 Scaffold-Dbcontext "Server=DESKTOP-RHER41Q; Database=Company;Trusted_Connection=True; TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

- `YourConnectionString`: The connection string to your database.
- `YourDbContextName`: The name of your DbContext (e.g., `AppDbContext`).
- `-OutputDir Models`: Specifies the directory where your models and DbContext will be generated.
- `-Force`: Overwrites existing models if any.

This will generate model classes for your database tables and a `DbContext` class for database access.

### 5. Configure the DbContext in `Program.cs`

1. Open the `Program.cs` file.
2. Register the newly created `DbContext` in the `Program.cs` file so that it is available for dependency injection.

Add the following lines:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();

// Register DbContext with SQL Server
//way 1
builder.Services.AddDbContext<YourDbContextName>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
);

// or 
//way 2
builder.Services.AddDbContext<CompanyContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DBConnection")));

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseHttpsRedirection();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

- `YourDbContextName`: This is the name of the DbContext class you generated using the scaffold command (e.g., `AppDbContext`).

### 6. Create Controllers

You can now create API controllers based on your scaffolded models. Visual Studio offers an easy way to scaffold controllers.

1. Right-click on the **Controllers** folder in Solution Explorer.
2. Select **Add > Controller**.
3. Choose **API Controller with actions, using Entity Framework**.
4. Select the model (e.g., `Book`) and the `DbContext` (e.g., `AppDbContext`) you just created to generate the controller.

Visual Studio will generate the necessary CRUD operations for the selected model.

### 7. Run the Application

1. Press `F5` or click the **Run** button in Visual Studio to start your application.
2. The API should be running on `https://localhost:5001` or `http://localhost:5000`, and you can use tools like **Postman** or **cURL** to interact with your API.

### Example of Database Models

Below is an example of how the scaffolded models might look for a multi-table database structure:

#### `Author.cs`

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }

    // Navigation Property
    public ICollection<Book> Books { get; set; }
}
```

#### `Book.cs`

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Genre { get; set; }

    // Foreign Key
    public int AuthorId { get; set; }

    // Navigation Property
    public Author Author { get; set; }
}
```

These entities represent a one-to-many relationship where an author can have multiple books.

---

## Summary of Commands

- Install necessary NuGet packages:
  ```bash
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  Install-Package Microsoft.EntityFrameworkCore.Tools
  ```

- Scaffold DbContext and models:
  ```bash
  Scaffold-DbContext "YourConnectionString" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context YourDbContextName -Force
  ```

- Add a migration (optional):
  ```bash
  Add-Migration InitialMigration
  ```

- Update the database:
  ```bash
  Update-Database
  ```

---

## Technologies Used

- .NET 8
- Entity Framework Core
- SQL Server
- Visual Studio 2022

---

## License

This project is licensed under the MIT License. You are free to use, modify, and distribute this project.

---

## Conclusion

This guide provides the steps to build a RESTful Web API in .NET 8 using the scaffolding method with Entity Framework Core. By following these steps, you can automatically generate models and `DbContext` from your existing database and focus on building the API logic.

Feel free to explore the project and customize it as needed for your own use cases.
