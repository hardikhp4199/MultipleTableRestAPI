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
    "DefaultConnection": "Server=localhost;Database=YourDatabaseName;Trusted_Connection=True;"
  }
}
```

### 4. Scaffold the `DbContext` and Models

Now we will use the **Entity Framework Core Scaffold-DbContext** command to automatically generate `DbContext` and models based on the existing database.

1. Open **Package Manager Console** (`Tools > NuGet Package Manager > Package Manager Console`).
2. Run the following command (replace the connection string and context name with your actual values):

```bash
Scaffold-DbContext "Server=localhost;Database=YourDatabaseName;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context YourDbContextName -Force
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
builder.Services.AddDbContext<YourDbContextName>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
);

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

### 7. Run Migrations (Optional)

If you need to make changes to your database schema, you can use **migrations** to apply those changes.

1. Run the following command to create an initial migration:
   ```bash
   Add-Migration InitialMigration
   ```

2. Apply the migration to your database:
   ```bash
   Update-Database
   ```

### 8. Run the Application

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

---

### You can now paste this into your GitHub repository’s `README.md` file and push it.

Let me know if you need further customization or edits for this file!