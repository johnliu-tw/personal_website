---
layout: post
title: 'Python - 新手六大常見 error 錯誤訊息'
date: 2020-03-07 07:31
comments: true
categories:
- Python
---
這裡要跟各位分享一些 python 開發過程常見的 error 錯誤訊息，
相信可以減少初階開發者寶貴的 Debug 時間。

#### 1. SyntaxError ：invalid syntax
1.在 if , elif , else , for , while , class ,def 後面少了『：』符號

```python
if a == 10  #error
	print('cool')
```

2.使用 = 而不是 ==
一個『 = 』是設定變數裡的值，兩個『 == 』才是指比對兩邊的值是否一樣
```python
if a = 10:  #error
	print('cool')
```

3.少加了單引號或雙引號
```python
print('Hello world!) #error
```
```python
print(Heelo banana") #error
```



#### 2. IndentationError：【expected an indented block】, 【unexpected indent】, 【unindent does not match any outer indetation level】

1.通常就是指縮排的問題，python 的縮排很嚴格滴！
```python
print(123)
  print(456) #error
```
```python
if a == 10:
    print(10)
   print(456) #error
```
```python
if a == 10:
print(10) # error
```

2.除了程式撰寫時的縮排，如果各位是用如sublime, vscode 的編輯器，
可能偶爾用 tab，偶爾用 space 打程式前的空格，
對於 python 來說，可能是不一樣的喔！
可以對程式做反白，就能看出差異
![http://imgs.cc/image/aaaabDKAo](http://imgs.cc/image/aaaabDKAo)

#### 3. TypeError: Can't convert 'int' object to str implicitly
在程式的世界裡，『資料類別』也是很敏感要注意的地方，字串加整數或浮點數加字串都是不被允許的。
甚至比較進階，有些印出來很像字串，但其實不是字串的變數，也有可能造成這個問題唷！
```python
a = 10
print('I am ' + a) #error
print('I am ' + str(a)) #good!
```

可以在 print 變數前，用下面的語法去檢視資料類別喔！ 
```python
type(weird_variable)
```

#### 4. NameError: name 'myAga' is not defined
最簡單判斷的錯誤之一，就是變數名稱打錯或是複製網路上的程式碼時，忘記建立用到的相關變數
```python
myAge = 100
print(myAga) # error
```
```python
print(greatLord) # error
```

#### 5. AttributeError: 'int' object has no attribute 'split'
通常會發生在，錯判一個變數的資料類別，所以呼叫了錯誤的函式。當程式開始多起來，沒有好好理解每個變數背後會存什麼值時，很常發生。
```python
c = 10
c.split(' ') #error
```

#### 6. IndexError: list index out of range
這錯誤通常發生在對著空的陣列取值時，因為有些函式我們都會預設(心理)一定取得到我們要的值，而忽略到了可能空的可能性(爬蟲經常發生)。
```python
...
...
...  # mulitple code
data = getWebPageData()
title = data[0].text  # may error
```

先跟各位分享這些，如果有經驗，
歡迎跟我說還有什麼你常常發生的 error 訊息，
我再幫你補充上去:)。
