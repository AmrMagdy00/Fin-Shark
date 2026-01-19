# Database Schema (EF Core + SQL Server)

المصدر الأساسي هنا هو:

- `Data/ApplicationDBContext.cs`
- `Migrations/20241102094125_Final.cs`
- `Migrations/ApplicationDBContextModelSnapshot.cs`

## DbContext

`ApplicationDBContext` يرث من `IdentityDbContext<AppUser>`، وبالتالي الداتابيز فيها جداول Identity القياسية + جداول الدومين.

### DbSets

- `DbSet<Stock> Stocks`
- `DbSet<Comment> Comments`

## Tables

### Identity tables (مختصر)

- `AspNetUsers` (يمثل `AppUser`)
- `AspNetRoles`
- `AspNetUserRoles`
- جداول claims/logins/tokens القياسية

### Domain tables

#### `Stocks`

الأعمدة الأساسية:

- `Id` (int, Identity)
- `Symbol` (nvarchar(max), not null)
- `CompanyName` (nvarchar(max), not null)
- `Purchase` (decimal(18,2), not null)
- `LastDiv` (decimal(18,2), not null)
- `Industry` (nvarchar(max), not null)
- `MarketCap` (bigint, not null)

#### `Comments`

الأعمدة الأساسية:

- `Id` (int, Identity)
- `Title` (nvarchar(max), not null)
- `Content` (nvarchar(max), not null)
- `CreatedOn` (datetime2, not null)
- `StockId` (int, nullable)

## Relationships

### Stock (1) -> (Many) Comments

في الـ snapshot:

- `Comment.StockId` مفتاح أجنبي إلى `Stock.Id`
- العلاقة اختيارية لأن `StockId` nullable
- لا يوجد cascade delete صريح في الـ migration (Default EF behavior هنا)

## Seed Data

### Roles

في الـ migration يتم إدخال:

- Role: `Admin`
- Role: `User`

وده مهم لأن `AccountController.Register` يحاول إضافة المستخدم إلى Role `"User"`.

