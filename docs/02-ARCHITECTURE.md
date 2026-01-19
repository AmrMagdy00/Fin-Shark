# Architecture (نظرة معمارية)

## الصورة الكبيرة

FinShark API مبني على ASP.NET Core (Minimal hosting) ومعتمد على:

- **Controllers**: تعريف الـ HTTP endpoints والتحقق من `ModelState`
- **DTOs**: شكل الـ request/response + validation annotations
- **Mappers**: تحويل بين الـ DTOs والـ EF Models
- **Repositories**: تنفيذ الـ data access باستخدام EF Core (`ApplicationDBContext`)
- **Service Layer**: خدمة واحدة حاليًا لإنشاء JWT (`TokenService`)
- **Identity**: إدارة المستخدمين والـ roles باستخدام ASP.NET Identity + EF stores

## فلو الطلب (Request Flow)

مثال على فلو `GET /api/stock`:

1. `StockController.GetAll()` يستقبل `QueryObject` من QueryString
2. يستدعي `IStockRepository.GetAllAsync(query)`
3. `StockRepository` يبني `IQueryable` على `DbSet<Stock>` + `Include(Comments)` ثم يطبق:
   - فلترة `CompanyName` و `Symbol` (Contains)
   - Sorting لو `SortBy = Symbol`
   - Pagination باستخدام `PageNumber` و `PageSize`
4. يرجّع List من `Stock` (Models)
5. الـ controller يحولها إلى `StockDto` باستخدام `StockMappers.ToStockDto()`

## الـ Dependency Injection (DI)

تم تسجيل dependencies في `Program.cs`:

- `ApplicationDBContext` عبر `AddDbContext(...)`
- `Identity` عبر `AddIdentity<AppUser, IdentityRole>().AddEntityFrameworkStores<ApplicationDBContext>()`
- Repositories/Services:
  - `IStockRepository -> StockRepository`
  - `ICommentRepository -> CommentRepository`
  - `ITokenService -> TokenService`

## Middleware pipeline (مختصر)

في `Program.cs`:

- Swagger في Development
- `UseHttpsRedirection()`
- CORS مفتوح مع `AllowCredentials` + `SetIsOriginAllowed(origin => true)`
- `UseAuthentication()` ثم `UseAuthorization()`
- `MapControllers()`

