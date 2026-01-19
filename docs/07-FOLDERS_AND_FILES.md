# Folder & File Guide

## Root

- `api.csproj`: مشروع ASP.NET Core Web API على `net8.0`
- `Program.cs`: DI + middleware pipeline + Identity + JWT + Swagger + CORS
- `appsettings.json`: connection string + JWT settings + مفاتيح أخرى
- `appsettings.Development.json`: logging override
- `FinShark.sln`: solution
- `FinShark.bak`: ملف backup (ليس جزء من runtime)

## Controllers

HTTP API layer:

- `Controllers/AccountController.cs`: register/login + إصدار JWT
- `Controllers/StockController.cs`: CRUD للـ stocks + list with filtering/sorting/pagination
- `Controllers/CommentController.cs`: CRUD للـ comments + list/filter

## Data

- `Data/ApplicationDBContext.cs`: EF Core DbContext + IdentityDbContext + seeding roles

## Models

EF entities:

- `Models/AppUser.cs`: يرث من `IdentityUser`
- `Models/Stock.cs`: كيان Stock + navigation `Comments`
- `Models/Comment.cs`: كيان Comment + FK `StockId` + navigation `Stock`

## Dtos

نقل البيانات بين الـ API والعميل + validations:

- `Dtos/Account/*`: Login/Register/NewUser DTOs
- `Dtos/Stock/*`: Create/Update/StockDto + `FMPStock`
- `Dtos/Comment/*`: Create/Update/CommentDto

## Mappers

تحويل Models <-> DTOs:

- `Mappers/StockMappers.cs`: ToStockDto / ToStockFromCreateDTO / ToStockFromFMP
- `Mappers/CommentMapper.cs`: ToCommentDto / ToCommentFromCreate / ToCommentFromUpdate

## Interfaces

تعريف العقود:

- `Interfaces/IStockRepository.cs`
- `Interfaces/ICommentRepository.cs`
- `Interfaces/ITokenService.cs`

## Repository

EF Core implementations:

- `Repository/StockRepository.cs`
- `Repository/CommentRepository.cs`

## Service

- `Service/TokenService.cs`: إنشاء JWT

## Helpers

Objects لقراءة query params:

- `Helpers/QueryObject.cs`: filters/sorting/pagination للـ stocks
- `Helpers/CommentQueryObject.cs`: filters/sort للـ comments

## Extensions

- `Extensions/ClaimsExtensions.cs`: helper لاستخراج username من ClaimsPrincipal

## Migrations

EF Core migrations + snapshot:

- `Migrations/20241102094125_Final*`
- `Migrations/ApplicationDBContextModelSnapshot.cs`

