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

> `400 Invalid Argument`

```json
{
    "code": 1,
    "message": "USER_NAME_CONFLICT",
    "data": {}
}
```

#### 用户名格式不合法

> `400 Invalid Argument`

```json
{
    "code": 2,
    "message": "INVALID_USER_NAME_FORMAT",
    "data": {}
}
```

#### 密码格式不合法

> `400 Invalid Argument`

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

后端接受请求后，验证用户是否存在，若是则验证密码的哈希与存储的密码哈希值是否匹配。二者都通过则成功登录并返回 token，否则返回用户名**或**密码错误。

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

> `400 Invalid Argument`

```json
{
    "code": 4,
    "message": "WRONG_PASSWORD",
    "data": {}
}
```

## GET /all_news

返回主页新闻。

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

无需正文。

### 行为

后端接受到请求之后，先检验 token 是否有效，如果有效则返回相应新闻内容，是否按用户进行喜好筛选由后期决定。

### 响应

返回一个 JSON 格式的正文，包含新闻对象的数组。

```json
{
    "code": 0,
    "message": "SUCCESS",
    "data": [
        {
            // 新闻的标题
            "title": String,
            // 新闻的网址
            "url": String,
            // 新闻所属的类别，如政治、生活
            "category": String,
            // 新闻的优先级，在前端以字号大小加以区分
            // 现在认为优先级分三类，1 代表最高级，2 代表次高级，3 代表一般级
            // 初步认为仅在“热点”这一类别中有最高级的新闻，整个正文中有且仅有一条最高级
            // "热点"中次高级数目不限，其他类别次高级新闻为 2 条
            // 所有类别的一般级新闻不限
            "priority": int,
            // 新闻所属的图片 URL（若有）
            // 若没有 URL，则该字段为 null
            "picture_url": String | null,
        }
    ]
}
```

### 错误

本 API 不应该出错。

## POST /modify_password

修改密码接口

### 请求

请求需要在请求头中携带 `Authorization` 字段，记录 `token` 值。

请求正文为：

```json
{
    "user_name": String,
    "old_password": String,
    "new_password": String,
}
```

### 行为

后端接受到请求之后，先检验token是否合理。如果合理，则判断`old_password`是否是该用户的原本代码。如果原密码正确，则检验新密码是否符合格式，如果符合格式则进行修改。

### 响应

* 如果原密码错误，则返回码为`400`。返回正文为

  ```json
  {
      "code": 1,
      "mes": "原密码错误"
  }
  ```

* 如果是，则检验新密码是否符合格式（仅包含字母与数字，长度不超过18）

  * 如果不符合格式，则返回正文为

    ```json
    {
        "code":2,
        "mes":"新密码不符合格式"
    }
    ```

  * 如果符合格式，则修改密码，返回码为200，正文为

    ```json
    {
        "code":0,
        "mes":"修改成功"
    }
    ```

    
