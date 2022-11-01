# API

## 通用错误

一些错误是所有 API 都可能会返回的。
具体说来，每个 API 都可能返回如下错误之一：

### 找不到页面

> `404 Not Found`

```json
{
    "code": 1000,
    "message": "NOT_FOUND",
    "data": {}
}
```

### 请求体格式错误

例如，期望请求体是 JSON 格式，但解析 JSON 时出现错误，或缺少必要参数。

> `400 Bad Request`

```json
{
    "code": 1005,
    "message": "INVALID_FORMAT",
    "data": {}
}
```

### 未认证

> `401 Unauthorized`

```json
{
    "code": 1001,
    "message": "UNAUTHORIZED",
    "data": {}
}
```

### 外部错误

数据库连接错误等外部错误。

> `500 Internal Server Error`

```json
{
    "code": 1002,
    "message": "EXTERNAL_ERROR",
    "data": {}
}
```

### 内部错误

其他错误类型没有覆盖到的服务器内部错误。

> `500 Internal Server Error`

```json
{
    "code": 1003,
    "message": "INTERNAL_ERROR",
    "data": {}
}
```

## POST /register

注册一个用户。

### 请求

请求附带 JSON 格式的正文。
样例：

```json
{
    "user_name": "Alice",
    "password": "Bob19937"
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`user_name`|字符串|是|用户名|
|`password`|字符串|是|密码|

### 行为

后端接受请求后，应当验证用户名是否重复、是否合法、密码是否合法（用户名与密码的合法性在前后端都需要验证）。

用户名与密码直接使用明文传输（由 HTTPS 保证安全性），但后端应将密码进行哈希后存储。

若用户名与密码都合法，则注册用户并返回登录 Token，否则返回错误信息。

### 响应

#### 成功注册用户

若成功注册用户，则返回如下响应并转入已登录状态：

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {
        "id": 1,
        "user_name": "Alice",
        "token": "SECRET_TOKEN"
    }
}
```

其中 `data` 是一个字典，各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`id`|整数|是|用户 ID|
|`user_name`|字符串|是|用户名|
|`token`|字符串|是|Bearer token|

### 错误

#### 用户名已占用

> `400 Bad Request`

```json
{
    "code": 1,
    "message": "USER_NAME_CONFLICT",
    "data": {}
}
```

#### 用户名格式不合法

> `400 Bad Request`

```json
{
    "code": 2,
    "message": "INVALID_USER_NAME_FORMAT",
    "data": {}
}
```

#### 密码格式不合法

> `400 Bad Request`

```json
{
    "code": 3,
    "message": "INVALID_PASSWORD_FORMAT",
    "data": {}
}
```

## POST /login

尝试登录。

### 请求

请求附带 JSON 格式的正文。
样例：

```json
{
    "user_name": "Alice",
    "password": "Bob19937"
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`user_name`|字符串|是|用户名|
|`password`|字符串|是|密码|

### 行为

后端接受请求后，验证用户是否存在，若是则验证密码的哈希与存储的密码哈希值是否匹配。
二者都通过则成功登录并返回 token，否则返回用户名**或**密码错误。

### 响应

#### 成功登录

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {
        "id": 1,
        "user_name": "Alice",
        "token": "SECRET_TOKEN"
    }
}
```

其中 `data` 是一个字典，各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`id`|整数|是|用户 ID|
|`user_name`|字符串|是|用户名|
|`token`|字符串|是|Bearer token|

### 错误

#### 用户名或密码错误

> `400 Bad Request`

```json
{
    "code": 4,
    "message": "WRONG_PASSWORD",
    "data": {}
}
```

## POST /modify_password

修改密码接口。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

请求正文样例：

```json
{
    "user_name": "Alice",
    "old_password": "Bob19937",
    "new_password": "Carol48271"
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`user_name`|字符串|是|用户名|
|`old_password`|字符串|是|旧密码|
|`new_password`|字符串|是|新密码|

### 行为

后端接受到请求之后，先检验 token 是否合理。
如果合理，则判断 `old_password` 是否是该用户的原本密码。
如果原密码正确，则检验新密码是否符合格式，如果符合格式则进行修改。

### 响应

#### 修改成功

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {}
}
```

### 错误

#### 原密码错误

> `400 Bad Request`

```json
{
    "code": 4,
    "message": "WRONG_PASSWORD",
    "data": {}
}
```

#### 新密码格式不合法

> `400 Bad Request`

```json
{
    "code": 3,
    "message": "INVALID_PASSWORD_FORMAT",
    "data": {}
}
```

## POST /modify_username

修改用户名接口。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

请求正文样例：

```json
{
    "old_user_name": "Alice",
    "new_user_name": "Bob"
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`old_user_name`|字符串|是|旧用户名|
|`new_user_name`|字符串|是|新用户名|

### 行为

后端接受到请求之后，先检验`token`是否合理。
如果合理，则判断 `old_user_name` 是否是该用户的原本用户名。
如果原用户名正确，则检验新用户名是否符合格式，如果符合格式则进行修改。
原用户名的所有token将会全部过期，后端返回新token。

### 响应

#### 修改成功

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {
        "id": 1,
        "user_name": "Bob",
        "token": "SECRET_TOKEN"
    }
}
```

### 错误

#### 原用户名错误

> `400 Bad Request`

```json
{
    "code": 6,
    "message": "WRONG_USERNAME",
    "data": {}
}
```

#### 新用户名格式不合法

> `400 Bad Request`

```json
{
    "code": 7,
    "message": "INVALID_USERNAME_FORMAT",
    "data": {}
}
```

## POST /logout
尝试登出该用户。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

不需要附带其他内容。

### 行为

后端接受到请求之后，先检验 `token` 是否有效，如有效，将该用户登出。

### 响应

#### 成功登出

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {}
}
```

### 错误

#### 用户未登录

> `401 Unauthorized`

```json
{
    "code": 1001,
    "message": "UNAUTHORIZED",
    "data": {}
}
```

## POST /checklogin
检查用户登陆状态。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

不需要附带其他内容。

### 行为

后端接受到请求之后，检验 `token` 是否有效，并返回登陆状态。

### 响应

#### 登录状态有效

> `200 OK`

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {}
}
```

### 错误

#### 登陆状态失效

> `401 Unauthorized`

```json
{
    "code": 1001,
    "message": "UNAUTHORIZED",
    "data": {}
}
```

## GET /user_info

返回用户信息。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

无需正文。

### 行为

后端接受到请求之后，先检验 token 是否有效，如果有效则返回用户签名与用户标签。

### 响应

> `200 OK`

返回一个 JSON 格式的正文，包含用户签名与用户标签。

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": [
        {
            "id": 1,
            "user_name": "Bob",
            "signature": "This is my signature.",
            "tags": [
                "C++",
                "中年",
                "アニメ"
            ],
            "mail": "waifu@diffusion.com",
            "avatar": "data:image/bmp;base64,Qk0+AgAAAAAAAD4AAAAoAAAAQAAAAEAAAAABAAEAAAAAAAACAAB0EgAAdBIAAAAAAAAAAAAAAAAAAP///wD/////////////////////////D+H///////n//z//////7///5/////+////5/////n/////////9/////7////P/////////77oAALnP///fsgAQnM///75wAAg9b///ePgACH6P//7y8AAAPv///ubgAAg8///9z0AAAA37///fgAAAA/3/+5/AAAAH/j//v+AAAA//f/+/8AAAD/+////4AAAf/7//f/wHgD////9wfh/gf//f/+//P/D/////n////+P/v/+f/////P+////////+f3////974H9/f////HOHv7z////zX/Pfg////+d//e8/////7v/+9////fff/////////8///tv///7vPf/X2////14bV/v//////9N3+7v/////73/b+//////ub5r7/////+57kvv/////7gPm+//////vP37z/////+/fXvP//////+d+9//////39//3//////f//+f/////+///7//////7///P//////3//5///////v//v///////f/5///////+/+P///////+fD/////////D///////////////////////////////////////////////////////////////////////////////////////////////////////////////w==",
        }
    ]
}
```

其中 `data` 是一个数组，其中每个对象各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`id`|整数|是|用户 ID|
|`user_name`|字符串|是|用户名|
|`signature`|字符串|是|用户签名|
|`tags`|字符串列表|是|用户标签|
|`mail`|字符串|是|用户邮箱|
|`avatar`|字符串|是|用户头像|

### 错误

本 API 不应该出错。

## GET /all_news

返回主页新闻。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

无需正文

### 行为

后端接受到请求之后，先检验 token 是否有效，如果有效则返回相应新闻内容，是否按用户进行喜好筛选由后期决定。为向下兼容，如不存在请求正文，默认返回要闻。

### 响应

> `200 OK`

返回一个 JSON 格式的正文，包含新闻对象的数组。

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": [
        {
            category:"科技",
            news:[
                {
                    "title": "Breaking News",
                    "url": "https://breaking.news",
                    "picture_url": "https://breaking.news/picture.png",
                    "media": "Foobar News",
                    "pub_time": "2022-10-21T19:02:16.305Z",
                }
            ]
        }
        
    ]
}
```

其中

`data`是一个数组，其中每个对象各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`category`|字符串|是|新闻类别|
|`news`|数组|是|新闻内容|

对于爬虫有明确分类的类别（如科技、生活），返回其对应类别下的新闻，每一个类别新闻条数不超过200.`data`的元素数目与爬虫数据库中新闻类别种数相同。

`news` 是一个数组，其中每个对象各字段含义如下：

|字段|类型|必选|含义|
|:-|-|-|-|
|`title`|字符串|是|新闻标题|
|`url`|字符串|是|新闻网址|
|`picture_url`|字符串|允许为null|新闻图片 URL|
|`media`|字符串|是|媒体|
|`pub_time`|字符串|是|新闻发布时间|

### 错误

本 API 不应该出错。

## POST /search

进行搜索。

### 请求

请求附带 JSON 格式的正文。
样例：

```json
{
    "query": "Hello",
    "page": 2,
    "include": [
        "world",
        "again"
    ],
    "exclude": [
        "goodbye"
    ]
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`query`|字符串|是|查询关键词|
|`page`|整数|否|页码，正整数，默认为 1|
|`include`|数组|否|必含词|
|`exclude`|数组|否|排除词|

其中 `include` 与 `exclude` 为字符串数组，包含需要包括在内/排除在外的词。

### 行为

后端接收到请求后，向搜索后端发送查询关键词的搜索请求，并返回指定页码的搜索结果，以及该搜索词的结果共有多少页。
搜索结果应包含新闻正文中与搜索词相关的上下文，以及需要标红的位置。

一页定义为 10 条搜索结果，页码从 1 开始计数。
若 `page` 不为正整数则应当报错，错误响应在下面定义。
若 `page` 为正整数则总是正常响应，即使对应的页码并没有搜索结果也是如此。
此时返回的新闻列表为空。

### 响应

> `200 OK`

返回一个 JSON 格式的正文，包含搜索结果的数组。

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {
        "page_count": 15,
        "news": [
            {
                "title": "Breaking News",
                "media": "Foobar News",
                "url": "https://breaking.news",
                "pub_time": "2022-10-21T19:02:16.305Z",
                "content": "BREAKING NEWS!!!",
                "picture_url": "https://breaking.news/picture.png",
                "title_keywords": [
                    [1, 3],
                    [7, 9],
                    [10, 15]
                ],
                "keywords": [
                    [1, 3],
                    [7, 9],
                    [10, 15]
                ]
            }
        ]
    }
}
```

`page_count` 表示这个搜索词的结果一共有多少页。

`news` 是一个数组，其中每个对象各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`title`|字符串|是|标题|
|`media`|字符串|是|媒体|
|`url`|字符串|是|新闻 URL|
|`pub_time`|字符串|是|新闻发布时间|
|`content`|字符串|是|新闻内容与关键词相关的上下文|
|`picture_url`|字符串|否|图片 URL，若有|
|`title_keywords`|数组|是|标题中需要标红的关键词位置|
|`keywords`|数组|是|需要标红的关键词位置|

其中 `title_keywords` 和 `keywords` 是一个数组，每个元素是一个包含两个整数的数组，为一个需要标红的关键词的位置，从 0 开始计数，左闭右开。

### 错误

#### 页码不为正整数

> `400 Bad Request`

```json
{
    "code": 5,
    "message": "INVALID_PAGE",
    "data": {}
}
```

## POST /search/suggest

获取搜索建议。

### 请求

请求附带 JSON 格式的正文。
样例：

```json
{
    "query": "Hello",
    "include": [
        "world",
        "again"
    ],
    "exclude": [
        "goodbye"
    ]
}
```

正文的 JSON 包含一个字典，字典的各字段含义如下：

|字段|类型|必选|含义|
|-|-|-|-|
|`query`|字符串|是|查询关键词|
|`include`|数组|否|必含词|
|`exclude`|数组|否|排除词|

其中 `include` 与 `exclude` 为字符串数组，包含需要包括在内/排除在外的词。

### 行为

后端接收到请求后，向搜索后端发送获取搜索建议的请求，并返回一个搜索建议列表。

### 响应

> `200 OK`

返回一个 JSON 格式的正文，包含搜索建议的数组。

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": {
        "suggestions": [
            "Hello world again!",
            "Hello world again and again!"
            "Hello world again, again and again!"
        ]
    }
}
```

### 错误

此 API 不应该返回错误。