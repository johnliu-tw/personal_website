---
layout: post
title: 'Ruby on Rails - 小物件結構 Hash 設定'
date: 2018-09-11 08:20
comments: true
categories:
- Ruby on Rails
---
在和別的應用程式做資料交換的時候，
我喜歡製作一個小型 object 去結構化我要傳遞過去的資料，
因此會製作如同下面的 Hash 物件:

``` ruby
person = {
	:name => "John",
  :age => 16,
  :phone => "0988888888"
}
```

而因此我可以跑一個 each 迴圈，去把 Active record 裡的資料取出對方應用程式需要的欄位

``` ruby
people = []
@users = User.all
@users.each do |user|
	 person[:name] = user.name
   person[:age] = user.age
   person[:phone] = user.contact.phone_number
	 people.push(person)
end
```

上面的程式執行後，就可以把 people 轉成 json 格式，跟其他應用程式做資料交換，
但上面的程式有個很嚴重的 Bug，就是我雖然每次迴圈都會更新 Hash 物件的值，並塞入 array 中，
但是呢，**我後來更動的 Hash 的值，會覆蓋掉前面我已經塞入 Array 內 Hash 的值**，
所以最後的陣列，會變成全部值一樣的 Hash 物件....
這是因為塞入的 Hash 都是指向同一個 Hash物件，而不是**新的** Hash 物件，
因此我會做下面的修改:

``` ruby
people = []
@users = User.all
@users.each do |user|
	 new_person = person.clone()
	 new_person[:name] = user.name
   new_person[:age] = user.age
   new_person[:phone] = user.contact.phone_number
   people.push(new_person)
end
```

利用 clone() 方法，直接製作一個新的 Hash 物件，就不會有值被覆蓋的問題。
大致上是資料儲存記憶體指向的問題，但用這個方法可以最簡易的解決此問題。
