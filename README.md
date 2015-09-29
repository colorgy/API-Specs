# Colorgy API Specs

API 與資料接口規格

- - -

**Table of Contents**

- [課程資料 (Course)](#課程資料-course)
  - [API 端點](#api-端點-api-endpoint)
  - [資料格式 (Attributes)](#資料格式-attributes)
  - [爬蟲規格 (Crawler Spec)](#爬蟲規格-crawler-spec)
- [上課節次時間資料 (Period Data)](#上課節次時間資料-period-data)
  - [API 端點](#api-端點-api-endpoint-1)
  - [資料格式 (Attributes)](#資料格式-attributes-1)
  - [爬蟲規格 (Crawler Spec)](#爬蟲規格-crawler-spec-1)
- [書籍資料 (Book Data)](#書籍資料-book-data)
  - [API 端點](#api-端點-api-endpoint-2)
  - [資料格式 (Attributes)](#資料格式-attributes-2)
  - [爬蟲規格 (Crawler Spec)](#爬蟲規格-crawler-spec-2)

- - -

## 課程資料 (Course)

Data API

### API 端點 (API Endpoint)

不同學校的課程資料有不同的 API 端點，但所有端點皆統一格式為 `/[學校代碼轉小寫]/courses`，例如 `/ntust/courses`。

### 資料格式 (Attributes)

#### 基本資料

- `code`: (必要、唯一) 唯一代碼，需要完全唯一，即便同一門課連續多年開設，此欄位也會不同（實務上可把「學年度」及「學期」編入此欄位中，以確保唯一性）
- `general_code`: (必要) 通用代碼，此代碼會在連續多年開設的同一門課中相同（因為可串連（通常為）同教師開設的同一門所有課，實務上可運用在「課程評論」、「課程共筆分享」等服務）
- `year`: (必要) 課程所在學年度，統一為西元年
- `term`: (必要) 課程所在學期，`1` 為上學期，`2` 為下學期
- `name`: (必要) 課程名稱
- `lecturer`: 授課教師

#### 上課時間地點

一共為：`day_1` ~ `day_9`、`period_1` ~ `period_9`、`location_1` ~ `location_9` 以及 `day_periods`，共 28 個欄位，最多可以為每門課存下 9 則上課時間、地點資料。`day_1`、`period_1`、`location_1` 為一組，表示該門課第一個上課的星期、時間以及教室。接著 `day_2`、`period_2`、`location_2` 為第二個......以此類推。若有餘下的欄位皆為 null。`day_periods` 為特殊欄位，將所有上課時間區段寫進此欄位以便做「上課時間包含」或「上課時間不包含」篩選。

- `day_1` - `day_9`: integer，上課日（星期），第一堂課的上課日為 `day_1`，第二堂為 `day_2`（以 `1` 到 `7` 表示）
- `period_1` - `period_9`: integer，上課節次（統一以 `1`、`2`、`3`... 表示，可對到[上課節次時間資料](#上課節次時間資料-period-data)得到確切上課時間以及節次代碼
- `location_1` - `location_9`: 上課地點
- `day_periods`: 上課日＋節次，並以空格間隔，例如：`Mon1 Mon2 Mon3 Thu1 Thu2 Thu3`

#### 更多資料

- `credits`: integer，學分數
- `required`: boolean，是否為必修
- `url`: 課程資料網址
- `prerequisites`: 先修課程
- `website`: 課程網站
- `students_enrolled`: integer，目前選課人數
- `full_semester`: boolean，是否為全學年課程

#### 英文資料

在對應的中文資料欄位名稱加上「_en」尾綴即可。

- `name_en`: 英文課程名稱

### 爬蟲規格 (Crawler Spec)

> 此處描述之爬蟲規格為終端爬蟲類別 (class) 介面規格，藉由統一介面達到可抽換、易測試。爬蟲應用程式回報資料的 API 規格請參見 https://github.com/colorgy/Core/wiki。

Each crawler instance represents a set of data.

#### Ruby

```rb
require './crawler.rb'  # The crawler may lives in a different file
crawler = Crawler.new(year: 2012, term: 2)  # The crawler class may have a different name
crawler.courses  # returns the data
```

#### Node.js

```js
var Crawler = require('crawler');  // The crawler may lives in a different file and have a different class name

// Initialize new crawler
var crawler = new Crawler();
crawler.year = 2015;
crawler.term = 1;
// or
crawler = new Crawler({ year: 2015, term: 1 });

// Start crawling, callbacks are provided by promise pattern
crawler.crawl()
  .then(function (data) { console.log(data); })
  .catch(function (err) { console.error(err); });
  
// Start crawling and log the progress down
crawler.crawl({ onProgressUpdate: function (progress) { console.log('Progress: ' + progress); } });

// Run a function after new data received
crawler.crawl({ onDataReceived: function (data) { console.dir(data); } });

// Get the crawled data
crawler.getData();
```

- - -

## 上課節次時間資料 (Period Data)

Data API

### API 端點 (API Endpoint)

不同學校的課程資料有不同的 API 端點，但所有端點皆統一格式為 `/[學校代碼轉小寫]/period_data`，例如 `/ntust/period_data`。

### 資料格式 (Attributes)

所有資料皆為必要。

- `order`: integer，節次序列，由 `1` 開始順序埋列
- `code`: 顯示用節次代碼
- `time`: 上課時間，格式如 `07:10-08:00`

### 爬蟲規格 (Crawler Spec)

暫無，目前資料均為手動收集輸入。

- - -

## 書籍資料 (Book Data)

Data API

### API 端點 (API Endpoint)

### 資料格式 (Attributes)

### 爬蟲規格 (Crawler Spec)
