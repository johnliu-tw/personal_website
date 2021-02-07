---
layout: post
title: 'Ruby on Rails - Image uploader 圖片上傳'
date: 2017-08-31 15:55
comments: true
categories:
- Ruby on Rails
---
圖片上傳是非常常用到的功能，這裡推薦使用[carrierwave](https://github.com/carrierwaveuploader/carrierwave)和[minimagick](https://github.com/minimagick/minimagick)兩個套件，就能很簡單的處理。

#### step1 安裝gem
``` 
*Gemfile*
gem 'carrierwave', '~> 0.10.0'
gem 'mini_magick', '~> 4.3'
```
在終端機跑指令: bundle install

#### step2 設定initializers
設定在讀取ActiveRecord(sqlite3)後，產生initializers
``` 
*config/initializers/carrier_wave.rb*
require 'carrierwave/orm/activerecord'
```
終端機跑指令：rails generate uploader Image
產生ImageUploader，處理圖片上傳的工作
#### step3 設定image_uploader
反註解image_uploader.rb 第七行，引入MiniMagick套件
``` 
*app/uploaders/image_uploader.rb
include CarrierWave::MiniMagick
```
再來針對上傳後的圖片做出壓縮版本的檔案，可以反註解在35-37行的程式碼
``` 
version :thumb do
  process :resize_to_fit => [50, 50]
end
```
50, 50為壓縮後的解析度(50x50)，可以自由調整，讓上傳的圖片不至於太過巨大

然後反註解41-43行的程式碼，建立上傳檔案類型的白名單
``` 
def extension_white_list
  %w(jpg jpeg gif png)
end
```
#### step4 Model設定callback
假設我們有個user的model，設定好image欄位(string)儲存，
可以使用mount_uploader針對某個image做callback:
``` 
*app/model/user.rb*
 
mount_uploader :image, ImageUploader
```
產生的所有callback:
after_save :store_image!
before_save :write_image_identifier
after_commit :remove_image!, on: :destroy
after_commit :mark_remove_image_false, on: :update
after_save :store_previous_changes_for_image
after_commit :remove_previously_stored_image, on: :update
#### step5 設定上傳檔案的表單
如果是用鷹架產生，可以更改image的欄位為：
``` 
*app/views/users/_form.html.erb
 
<div class="field">
  <%= f.label :image %><br>
  <%= f.file_field :image %>
</div>
```
如果用simple_form，可以改為：
``` 
*app/views/users/_form.html.erb
 
<%= f.input :image,as: :file, label: '圖片' , required: true %>
```
#### step6 設定圖片顯示
可透過thumb讀取壓縮後的圖檔，或是直接image.url呼叫原始圖檔
``` 
*app/views/users/show.html.erb
<p>
  <strong>Image:</strong>
  <%= image_tag @user.image.thumb.url %>
</p>
```
#### step7 驗證圖片大小
在model新增def判斷圖片大小：
``` 
*app/model/user.rb
validates_processing_of :image
validate :image_size_validation
 
private
  def image_size_validation
    errors[:image] << "should be less than 500KB" if image.size > 0.5.megabytes
  end
```
#### step8 利用Homebrew安裝imagemagick
利用終端機回到根目錄，輸入指令：
brew install imagemagick

因為carrierwave使用的imagemagick是用C編程的，所以需要另外安裝
這樣就大功告成了！開心的使用你的image uploader吧！


