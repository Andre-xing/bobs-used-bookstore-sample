# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a NuGet package restore to ensure all dependencies are resolved correctly before building:

```bash
dotnet restore
```

Review the output for any warnings about deprecated or incompatible packages. If any packages reference `netstandard` or older `net4x` target frameworks, consider updating them to their latest versions that support the current target framework.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Address any warnings that surface during the build, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to validate that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output carefully. A passing build does not guarantee correct runtime behavior, so all tests should pass before proceeding.

---

## 4. Verify Runtime Behavior of the Web Project

Run the web application locally to confirm it starts and operates correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Check the following:

- The application starts without runtime exceptions.
- All routes and pages load as expected.
- Database connectivity works correctly if `Bookstore.Data` interacts with a database.
- Any configuration values (connection strings, API keys, etc.) previously stored in `Web.config` have been correctly migrated to `appsettings.json` or environment variables.

---

## 5. Validate Data Layer

If `Bookstore.Data` uses Entity Framework or another ORM, verify that:

- The database context is correctly configured for the new target framework.
- Any pending migrations are applied:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- Data reads and writes function correctly during local testing.

---

## 6. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Verify that:

- All AWS CDK or infrastructure dependencies are compatible with the current .NET version.
- The CDK project synthesizes without errors:

```bash
dotnet run --project app/Bookstore.Cdk/Bookstore.Cdk.csproj
```

---

## 7. Review Configuration and Environment Settings

Confirm that the following have been correctly migrated from any legacy configuration files:

- Connection strings
- Application settings
- Logging configuration
- Authentication or authorization settings

These should now reside in `appsettings.json`, `appsettings.{Environment}.json`, or as environment variables.

---

## 8. Check for Platform-Specific Code

Search the solution for any remaining Windows-specific APIs or dependencies that may not behave correctly on Linux or macOS if cross-platform support is required:

```bash
grep -rn "System.Windows" app/
grep -rn "Registry" app/
grep -rn "System.Web" app/
```

Replace or abstract any identified platform-specific code.

---

## 9. Publish the Application

Once all validation steps pass, publish the application:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Verify the contents of the `./publish` directory and confirm the output is complete before deploying to the target environment.