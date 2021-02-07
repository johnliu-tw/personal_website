---
layout: post
title: 'Python - 環境變數設定( env 自動設定 )'
date: 2019-05-22 06:45
comments: true
categories:
- Python
---
`.env` 檔的概念是替專案設定環境變數，
一般的專案時常都會上雲，在如 `github` 的平台與他人共享程式碼，
而一些重要且敏感的系統變數，如 AWS Key or DB Password 等資料，上傳上去就糟了。

在 Python 可以透過下 open source 的專案:[python-dotenv](https://github.com/theskumar/python-dotenv)
透過此專案提供的簡單程式，讓專案自動讀取`.env`檔案內的系統環境變數，並可利用 Python 程式取用。

#### Install 
```bash
$ pip3 install -U python-dotenv
```

#### File Setting
```
project
│   .env
|   .env.example
│   setting.py
|   main.py
```
`.env` 是系統變數設定檔，`.env.sample` 可以設定系統變數格式，避免其他使用此專案者遺漏要新增的系統變數。
`setting.py` 可以設定 python-dotenv 的程式，並使用在 `main.py` 中。

#### Get Started 

1.設定系統變數檔
```bash
//.env
db_host=localhost
db_user=root
db_password=password
db_schema=test

//.env.example
db_host=
db_user=
db_password=
db_schema=
```

2.設定 setting 檔案
```python
// settings.py

from dotenv import load_dotenv
load_dotenv()

# OR, the same with increased verbosity:
load_dotenv(verbose=True)

# OR, explicitly providing path to '.env'
from pathlib import Path  # python3 only
env_path = Path('.') / '.env'
load_dotenv(dotenv_path=env_path)
```

3.引入 setting 檔
```python
// main.py
import os
import setting
import pymysql

DB_HOST = os.getenv("db_host")
DB_USER = os.getenv("db_user")
DB_PASSWORD = os.getenv("db_password")
DB_SCHEMA = os.getenv("db_schema")

db = pymysql.connect(DB_HOST,DB_USER,DB_PASSWORD,DB_SCHEMA)
```

最後記得把 .env 加入 .gitignore 內，就完成囉！！
