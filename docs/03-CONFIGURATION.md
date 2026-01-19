# Configuration (appsettings)

الملفات:

- `appsettings.json`: إعدادات التشغيل الأساسية (محلي فقط، وموجود في `.gitignore`)
- `appsettings.Development.json`: Override لإعدادات Development (محلي فقط)
- `appsettings.Example.json`: مثال آمن مرفوع على GitHub، تقدر تنسخه وتعدّل عليه محليًا

## ConnectionStrings

### `ConnectionStrings:DefaultConnection`

يستخدمه EF Core لتوصيل SQL Server داخل `Program.cs`:

- `builder.Services.AddDbContext<ApplicationDBContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));`

## JWT

### `JWT:Issuer`

يُستخدم في:

- إصدار التوكن داخل `TokenService`
- التحقق من التوكن داخل `AddJwtBearer` في `Program.cs`

### `JWT:Audience`

يُستخدم في:

- إصدار التوكن داخل `TokenService`
- التحقق من التوكن داخل `AddJwtBearer` في `Program.cs`

### `JWT:SigningKey`

المفتاح المستخدم في توقيع JWT (Symmetric) داخل:

- `TokenService` لتوقيع التوكن (`HmacSha512Signature`)
- `Program.cs` للتحقق من التوقيع أثناء الـ authentication

ملاحظة: حفظ `SigningKey` داخل `appsettings.json` مناسب للتجربة المحلية فقط. للإنتاج استخدم Secret Manager / environment variables / Vault حسب بيئتك.

## FMPKey

### `FMPKey`

موجود في `appsettings.json` لكن لا يوجد كود حاليًا يقرأه أو ينفذ تكامل مع FMP API.
الجزء الموجود فعليًا هو:

- DTO: `Dtos/Stock/FMPStock.cs`
- Mapper: `StockMappers.ToStockFromFMP(...)`

