---
layout: post
title: 'Ruby on Rails - 面試 interview Ruby 重要概念整理'
date: 2018-12-17 09:55
comments: true
categories:
- Ruby on Rails
---
這篇文章不會把每個重要概念都列出，
但會記錄說較易搞混的一些概念，和語法的使用。

### Class vs Module 
Class 是 Ruby 中非常基礎的 Object，可以被繼承、實例化(instance)和利用變數建立狀態。
Module 的使用和概念上雖然和 Class 類似，但更貼近“工具”，無法達成上述三種 class 的基礎應用，
其他方面可以說是幾乎完全相同，因此不適合作為 core class 使用。

### include vs extend 
某主 class include 任何的 class ，該 class 是只有在被實例化時才能被使用，
而 extend 別的 class，是可以在尚未實例化其他 class 時，使用。

### singleton method 
singleton method 是隸屬於實例，而不是 class 下方。

### private method 
除了大家最熟悉的只能在該 class call 用外，是不能針對 private function 使用 self 做為 receiver 的。
例如:
```ruby 
self.puts "Hi"
```
puts 是 Kernel 這個 class 下得 private method，因此不能使用 self 去指定使用。

### string append
一般來說會使用 concat 而不是使用 +=， concat不會改變記憶體位置，+= 會，效能較高。

### or_equal 
``` ruby
a ||= b #意思是 a ? a : a = b
```

### Block,Proc 和 Lamba 
Block 是最常見的程式執行區塊，就像最常看到的 .each do |u| puts u end

Proc 是一種 block ，但是可以透過變數保留狀態，和讀取參數，而且針對超出的參數範圍，不會跳 exception。
``` ruby
proc_argument = Proc.new {|x,y| “say #{x}” }
proc_argument.call(1,2) #print ‘say 1’
``` 
另外，如果 Proc 內有執行到 return ，會停止 call 用 Proc 的程式，
只執行到 Proc 內有 return 的那行。
``` ruby
def proc_run
  proc = Proc.new { return }
  proc.call
  puts "Run with Proc!"
end
proc_run # Nothing to print
```

Lamba 是一種類似 Proc 的機制，但他會檢查參數數量，如果是 Proc 的第一個例子，會出現 error。
另外，Lamba 內執行到 return 時不會終止整個程式，而是會把剩餘程式也一併執行完。
```ruby
def lambda_run
  lam = -> { return }
  lam.call
  puts "Run with lambda!"
end

lambda_run #=> "Run with lambda!"
```

### Class Instance Variable

Class Instance Variable 又名類別實例變數，是可以直接從 class 取用的變數，例: Animal.skin。
一般的實例變數，需要針對 class 實例化後才能取用，例: an = Animal.new; an.skin
因此很適合拿來當作 class 的預設值使用，因為不會被繼承此 class 的 child class 給 overwritten 該值。
```ruby

Class Animal
class << self; attr_accessor :skin end
@skin = "white"

end

Animal.skin # print white

Class Bird < Animal
@skin = "red"
end

Bird.skin # print red
Animal.skin # print white
```

### Map and Each
兩個都是迭代器，但 Map 會回傳一個新的 array ，each 則不會。

### Alias vs Alias Method
Alias
```ruby
alias name nickname 
```
Alias method 
```ruby
alias_method :name, :nickname
```
原則上 alias method 比較 flexibility，因為當繼承具有 alias method 的 parent class 時，
child class 想 overwritten 這些 alias， 純 alias 會無法成功，只能使用到 parent class 的 alias。
而 alias method 可以針對 child class 做自己想要的客製化。
可以參考這篇文章的例子[alias vs alias_method](https://blog.bigbinary.com/2012/01/08/alias-vs-alias-method.html)

### Freeze 
freeze 可以確保變數、陣列、物件等等的不變性，
設定 freeze 後，就無法用常見的設定值方法改動其內容的值。
針對多筆資料的陣列或物件，需要使用這種方法: my_ages.each(&:freeze)，
不然仍能更動陣列或物件內 element 的值。

### Select
Select 這個 array 下的 method ，可以幫忙篩選滿足條件的 element，集合成一個新的 array。
```ruby
data = ['abc', 'cde', 'dfg']
new_data = data.select {| d | d.include? "c"}
# ['abc', 'cde' ]
```
d.include?，就是用來篩選用的條件。

### Logic symbol
雖然執行上，and, or 和 &&, || 大致相同，
但實務上建議使用 && 和 || ，因為除了 && 和 and 比較起來，&& 的優先級較高外，
和 = 混用時，也是 && 會有較高的優先級

### Equal 
==, 比較值
===, 在 when case 裡比較用的符號
eql?, 比較值和型別(class)
equal?, 比較 obejct_id ，也就是記憶體位置。

### Present and exist 
exist 和 present 都能檢查 receiver 是否為空，不過用在 active record 上，建議使用 exist，
exist 來自 active base class，present 來自 object，因此 exist 的效能高上許多。

### Splat(*)
通常這個符號會用在 method 的參數上，例如: my_method(*arg)，
而這個 *arg，在 method 內會把所有傳進來的參數轉為陣列，
例如: my_method(1,2,3), arg 會變成 [1,2,3]。
但如果參數是 hash ，那就要使用 double splat (**)，例: my_method(**arg)，
這樣才能把多個 hash 參數轉成一個有多個 hash 的 array。





