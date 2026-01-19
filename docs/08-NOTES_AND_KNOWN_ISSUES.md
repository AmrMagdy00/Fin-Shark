# Notes & Known Issues (ملاحظات تقنية)

الهدف من الملف ده تنبيهك لحاجات في الكود الحالي مهم تبقى واخد بالك منها أثناء التطوير/التجربة.

## FMPKey موجود لكن مفيش تكامل فعلي

- `appsettings.json` فيه `FMPKey`
- موجود DTO `FMPStock` + mapper `ToStockFromFMP`
- لا يوجد Controller/Service بيستدعي API خارجي أو بيقرأ `FMPKey`

## CommentDto يحتوي CreatedBy لكن مش مدعوم في الـ Model

- `Dtos/Comment/CommentDto.cs` يحتوي `CreatedBy`
- `Models/Comment.cs` لا يحتوي `CreatedBy` ولا يوجد FK لـ user
- `CommentMapper.ToCommentDto()` لا يملأ `CreatedBy`

النتيجة: `CreatedBy` في الردود هيكون غير ممثل لمعلومة حقيقية في الداتابيز.

## CommentController حقن UserManager و ClaimsExtensions بدون استخدام

في `CommentController.cs`:

- `UserManager<AppUser> _userManager` موجود لكنه غير مستخدم
- `using api.Extensions;` موجود لكن مفيش استدعاء لـ `GetUsername()`

ده يوحي إن فيه نية لإضافة "CreatedBy" من المستخدم الحالي لكن لم تكتمل.

## CommentRepository.GetAllAsync ممكن يسبب NullReference

داخل `CommentRepository.GetAllAsync`:

- يتم عمل فلترة: `comments.Where(s => s.Stock.Symbol == queryObject.Symbol)`
- لكن لا يوجد `Include(s => s.Stock)`
- و `Stock` navigation ممكن تكون null (خصوصًا مع `StockId` nullable)

ده ممكن يؤدي إلى NullReferenceException عند `s.Stock.Symbol`.

## Update Comment: تمرير id كأنه StockId

في `CommentController.Update`:

- يتم استدعاء: `updateDto.ToCommentFromUpdate(id)`
- لكن `ToCommentFromUpdate` يستقبل `stockId` ويضعه في `StockId`

في نفس الوقت `CommentRepository.UpdateAsync` لا يغيّر `StockId` أصلًا (يحدّث Title/Content فقط).

## Authorization غير متسق

حاليًا `[Authorize]` موجود فقط على:

- `GET /api/stock`
- `GET /api/comment`

باقي عمليات الـ CRUD مفتوحة بدون auth.

## CORS Configuration

في `Program.cs`:

- `AllowCredentials()` + `SetIsOriginAllowed(origin => true)`

ده يسمح لأصول متعددة (origins) مع credentials. لو هتستخدمه في production لازم يتقيد بـ origins محددة.

## SwaggerGen مضاف مرتين

في `Program.cs`:

- `AddSwaggerGen()` ثم `AddSwaggerGen(option => ...)`

الفعلي هو التاني (المعتمد بالـ options)، والأول زائد.

## ClaimsExtensions.GetUsername يفترض وجود claim

`ClaimsExtensions.GetUsername()` يستخدم:

- `SingleOrDefault(...).Value`

لو claim غير موجود، `.Value` يسبب Exception. الأفضل يكون فيه handling قبل استخدامها.

