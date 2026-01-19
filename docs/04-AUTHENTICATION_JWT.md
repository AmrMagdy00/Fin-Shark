# Authentication & Authorization (Identity + JWT)

## Identity (المستخدمين والأدوار)

المشروع يستخدم ASP.NET Identity عبر:

- `AddIdentity<AppUser, IdentityRole>(...)`
- `AddEntityFrameworkStores<ApplicationDBContext>()`

### سياسات الباسورد

في `Program.cs`:

- RequireDigit = true
- RequireLowercase = true
- RequireUppercase = true
- RequireNonAlphanumeric = true
- RequiredLength = 12

### Seed للـ Roles

في `ApplicationDBContext.OnModelCreating(...)` يتم عمل Seed لروْلين:

- `Admin`
- `User`

وده بينعكس في الـ migration كـ `InsertData` داخل جدول `AspNetRoles`.

## JWT

### إنشاء التوكن (TokenService)

`TokenService.CreateToken(AppUser user)` يبني JWT بهذه البيانات:

- Claims:
  - Email (`JwtRegisteredClaimNames.Email`)
  - GivenName (`JwtRegisteredClaimNames.GivenName`) ويُحط فيه `user.UserName`
- Issuer/Audience من `appsettings.json` (`JWT:Issuer`, `JWT:Audience`)
- Signing: `HmacSha512Signature` بمفتاح `JWT:SigningKey`
- Expiration: `DateTime.Now.AddDays(7)`

### قراءة الـ username من الـ claims

`Extensions/ClaimsExtensions.cs` فيه:

- `GetUsername(this ClaimsPrincipal user)` يرجع claim من نوع `givenname`

ملاحظة: الدالة تفترض وجود claim، ولو غير موجود يحصل `NullReferenceException` عند `.Value`.

## Endpoints الخاصة بالحسابات

### `POST /api/account/register`

- ينشئ مستخدم جديد في Identity
- ثم يضيف المستخدم إلى Role باسم `"User"`
- يرجع `NewUserDto` وفيه JWT

### `POST /api/account/login`

- يبحث عن المستخدم بـ `UserName == loginDto.Username.ToLower()`
- يتحقق من الباسورد بـ `SignInManager.CheckPasswordSignInAsync(...)`
- يرجع `NewUserDto` وفيه JWT

## Authorization على الـ API

تم وضع `[Authorize]` على:

- `GET /api/stock`
- `GET /api/comment`

باقي الـ endpoints لا يوجد عليها `[Authorize]` حاليًا.

## Swagger + Bearer

`Program.cs` يضيف SecurityDefinition باسم `Bearer` ويطلب Header:

- `Authorization: Bearer <token>`

