# API Endpoints Catalog

هذا الملف يجمع كل الـ endpoints الموجودة فعليًا في `Controllers/` (Account/Stock/Comment).

## Account (`/api/account`)

### `POST /api/account/register`

- **Body**: `RegisterDto`
  - `Username` (Required)
  - `Email` (Required + EmailAddress)
  - `Password` (Required)
- **Response**: `NewUserDto`
  - `UserName`
  - `Email`
  - `Token`

### `POST /api/account/login`

- **Body**: `LoginDto`
  - `Username` (Required)
  - `Password` (Required)
- **Response**: `NewUserDto`

## Stock (`/api/stock`)

### `GET /api/stock` (Authorized)

عليه `[Authorize]`.

- **Query params**: `QueryObject`
  - `Symbol` (nullable)
  - `CompanyName` (nullable)
  - `SortBy` (nullable)
  - `IsDecsending` (bool)
  - `PageNumber` (int)
  - `PageSize` (int)
- **Behavior**
  - Filtering بـ `Contains` على `Symbol` و `CompanyName`
  - Sorting فقط عند `SortBy == "Symbol"` (case-insensitive)
  - Pagination: `skip = (PageNumber - 1) * PageSize`, ثم `Take(PageSize)`
- **Response**: `List<StockDto>` (وتحتوي `Comments` كـ list من `CommentDto`)

### `GET /api/stock/{id:int}`

- **Response**: `StockDto` أو `404`

### `POST /api/stock`

- **Body**: `CreateStockRequestDto`
  - `Symbol` (Required + MaxLength 10)
  - `CompanyName` (Required + MaxLength 10)
  - `Purchase` (Required + Range 1..1000000000)
  - `LastDiv` (Required + Range 0.001..100)
  - `Industry` (Required + MaxLength 10)
  - `MarketCap` (Range 1..5000000000)
- **Response**: `201 Created` + `StockDto`

### `PUT /api/stock/{id:int}`

- **Body**: `UpdateStockRequestDto` (نفس validation تقريبًا)
- **Response**: `StockDto` أو `404`

### `DELETE /api/stock/{id:int}`

- **Response**: `204 NoContent` أو `404`

## Comment (`/api/comment`)

### `GET /api/comment` (Authorized)

عليه `[Authorize]`.

- **Query params**: `CommentQueryObject`
  - `Symbol` (string)
  - `IsDecsending` (bool)
- **Behavior**
  - لو `Symbol` موجود: فلترة على `s.Stock.Symbol == Symbol`
  - لو `IsDecsending == true`: ترتيب بـ `CreatedOn` تنازلي
- **Response**: `IEnumerable<CommentDto>`

### `GET /api/comment/{id:int}`

- **Response**: `CommentDto` أو `404`

### `POST /api/comment/Create/{StockID}`

- **Route param**: `StockID`
- **Body**: `CreateCommentDto`
  - `Title` (Required + MinLength 5 + MaxLength 280)
  - `Content` (Required + MinLength 5 + MaxLength 280)
- **Behavior**
  - يتحقق أن الـ stock موجود بـ `IStockRepository.GetByIdAsync(StockID)`
  - ينشئ comment مرتبط بـ `StockId`
- **Response**: `201 Created` + `CommentDto`

### `PUT /api/comment/{id:int}`

- **Body**: `UpdateCommentRequestDto`
- **Response**: `CommentDto` أو `404`

### `DELETE /api/comment/{id:int}`

- **Response**: `200 OK` ويرجع `commentModel` (Entity) أو `404`

