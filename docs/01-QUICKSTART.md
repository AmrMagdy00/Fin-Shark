# Quickstart (تشغيل المشروع)

## المتطلبات

- .NET SDK: `net8.0` (المشروع TargetFramework = `net8.0`)
- SQL Server (أو SQL Server Express) + صلاحيات إنشاء/تحديث قاعدة البيانات

## إعداد الإعدادات (Configuration)

المشروع يعتمد على:

- `ConnectionStrings:DefaultConnection` في `appsettings.json`
- `JWT:Issuer` + `JWT:Audience` + `JWT:SigningKey` في `appsettings.json`

راجع التفاصيل في `03-CONFIGURATION.md`.

## تشغيل المشروع محليًا

من جذر المشروع (نفس مكان `api.csproj`):

```bash
dotnet restore
dotnet run --project api.csproj
```

الـ launch profiles موجودة في `Properties/launchSettings.json`، وفي وضع Development بيتم فتح Swagger على المسار `swagger`.

## إعداد قاعدة البيانات (EF Core)

المشروع يحتوي Migration باسم `20241102094125_Final`. لتطبيقها على قاعدة البيانات:

```bash
dotnet tool restore
dotnet ef database update --project api.csproj
```

ملاحظة: لو `dotnet-ef` مش متاح عندك، ثبته محليًا (global أو local tool) حسب أسلوبك.

## التحقق السريع

- افتح Swagger UI: `/swagger`
- جرّب `POST /api/account/register` ثم `POST /api/account/login`
- استخدم الـ JWT في Swagger: زر `Authorize` ثم اكتب:
  - `Bearer <token>`
- جرّب `GET /api/stock` (ده عليه `[Authorize]`)

