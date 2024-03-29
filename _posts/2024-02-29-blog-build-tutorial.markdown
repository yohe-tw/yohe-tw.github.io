---
layout: posts
title:  "透過minimal-mistake在github page上架設blog"
categories: tutorial
date:   2024-02-29 00:44:13 +0800
excerpt: 本篇文會教你從github page套用jekyll的模板到建起minimal-mistake的網站
---

## 前言

> 工欲善其事，必先利其器 --- 孔子

前陣子(2022, 好像也不算前陣子)時，我跑去修了**黃鐘揚**教授開的**網路服務程式設計**，這門是個好課，基本上教了許多網頁前後端的技術。過了一年後，偶然看到同系同學在github page上架設屬於自己的部落格，剛好大三下這學期修的課不算多，空閒時間很多。於是就心想不然來自己架設一個部落格好了，由於在架設的過程中不免俗會遇到一些問題，為了讓後人以及後來的自己在改動blog時有一個整理過的筆記，於是在架設部落格時就有了這篇的誕生。

### 先備知識

由於本人是資訊系本科出身，並且我們目前所使用的方法是透過github page架設部落格而不是倚靠一些原本就設定好的部落格網站，如medium。因此有一些資訊相關的知識點是我認為閱讀此文章前須具備的知識：
#### 1. 熟悉markdown的語法  
基本上我們在寫部落格文章時都會使用這個文體，如果不會寫markdown的話基本上完全無法學習在github page上發表文章，不過他相當好學習，並且還有一個名叫 [hackMD](https://hackmd.io/) 的網站可以很方便的在網路上做筆記，有點像是雲端筆記的概念，如果對markdown語法不熟的也可以練習在上面作筆記。
#### 2. 熟悉git的用法以及有一個github帳號  
git是一個版本控制軟體，而我們為何要學會使用他的原因除了版本控制很強大很屌之外，還有一個原因是因為有一個更屌的網站名叫github<font color='red'><strike>根據維基百科所述由於使用者性別比失衡又稱為gayhub</strike></font>是透過git進行上傳下載的，基本上會點進來的人應該不會不知道github是啥吧(畢竟標題都寫了...)。不過如果對git不熟的人也沒關係，因為我們只需要下面的四行指令就可以運作了(當然會更多技巧會更方便debug拉...)
{% highlight bash %}
git clone https://github.com/your-github-id/your-github-id.github.io.git
git add .
git commit -m "message"
git push
{% endhighlight %}

#### 3. <font color='red'><strike>你要有電腦、會用一些基本指令以及一顆很想寫blog的心</strike></font>

### Jekyll介紹

Jekyll是基於Ruby的模板來產生靜態網頁的產生器，並且是github page欽定使用的模板，因此只要我們想做github page blog，就繞不開這個產生器，並且Jekyll有很完整的社群生態，想要查資料、debug都相當容易。

### 版本與時間

對於像前端這種變化性相當高、日新月異的領域，這篇文章可能過幾年就因為版本問題不能用了，因此各個軟體的版本相當重要，如果在設定或安裝軟體時跳出許多bug，不如先到此章節，看看有沒有版本不同的地方，如果有那有可能是版本問題。(機車億點來講就是版本不對出事概不負責)
- 作業系統：windows 10
- 創立時間：2024/2/29 (有沒有人要試試看跟我同日期創站)
- ruby：3.2.3
- rubygems：3.5.6
- gcc、g++：6.3.0
- make：3.81

## 主題開始

簡單介紹完緣起與背景知識後，接下來要開始進入主題了。本章將會完全介紹從創立github repo到引入minimal-mistakes主題為止。

### 創立github repository

首先我們第一步當然是先創立一個專屬於我們網站的專案拉！將這個repository的名字設為`<name>.github.io`，其中name是你的github帳號名稱。然後先放著！不要動！先來設定我們最複雜的地方：電腦環境設定。

### Jekyll電腦環境設定

由於我們希望能同時在本地端與遠端跑起來，這樣我們就不用每次修改一個小小的地方就要push上github，因此接下來的步驟就是將電腦的環境設好，使我們可以自然的在本地端電腦上跑Jekyll。要使Jekyll跑起來我們必須要先安裝好ruby與rubygems，後者為ruby的管理套件，在這個基礎上再安裝完gcc和g++以及make後就可以在電腦上順利安裝Jekyll了。以下為安裝詳細步驟：

#### 1. 安裝Ruby

到[Ruby的官網](https://www.ruby-lang.org/en/downloads/)安裝 ruby，記得要選擇有Devkit，這樣以後才不會遇到問題。筆者就是沒有裝有Devkit的版本結果遇到問題...。並且在安裝的最後會跳出一個cmd，選擇選項3安裝即可。

#### 2. 安裝RubyGems

到[RubyGems的官網](https://rubygems.org/pages/download#formats)照著官網的說明安裝。

#### 3. 安裝gcc、g+\+與cmake

這步算是安裝過程中最難的一塊，因為對於windows用戶而言，本身沒有這三個東西(相比類unix系統)，安裝的軟體又不會幫忙設定系統環境變數。對於gcc與g+\+的安裝教學請出我[大一的計程教授P教授教學網站](https://sites.google.com/site/mycprogrammingbook/bu-chong-cai-liao/gccanzhuang)來代為指導。另外[make for windows](https://gnuwin32.sourceforge.net/packages/make.htm)的網站在這裡，安裝後如果打`make -v`沒有反應代表需要設定環境變數，設定環境變數的部分跟前面[大一的計程教授P教授教學網站](https://sites.google.com/site/mycprogrammingbook/bu-chong-cai-liao/gccanzhuang)講的一樣，只是要注意是不同的path，為`C:\Program Files (x86)\GnuWin32\bin`(每台電腦不太一樣)。

#### 4. 檢查是否安裝成功

輸入以下指令，若皆跳版本號，代表安裝成功。  
{% highlight bash %}
ruby -v
gem -v
gcc -v
g++ -v
make -v
{% endhighlight %}

#### 5. 使用下面的指令來安裝Jekyll與bundler，可能會花億點點時間。

{% highlight bash %}
gem install jekyll bundler
{% endhighlight %}
好的，環境都設好了！接下來嘗試是否能在本地端把網頁跑起來。

### 在本地端將網頁跑起來

接下來我們要嘗試在本地用jekyll將repo初始化，並嘗試在本地把網頁跑起來。

#### 1. 下載repo

將之前創好的repo clone下來

#### 2. 初始化repo

在這個repo裡輸入以下指令，使jekyll幫你初始化成它的形狀。
{% highlight bash %}
jekyll new .
{% endhighlight %}

#### 3. 安裝套件

輸入以下指令來安裝需要的套件
{% highlight bash %}
bundle install
{% endhighlight %}

#### 4. 在本地端跑起網頁

輸入
{% highlight bash %}
bundle exec jekyll serve
{% endhighlight %}
嘗試在本地跑起網頁，並在瀏覽器輸入
{% highlight bash %}
localhost:4000
{% endhighlight %}
如果順利的話應該就會在瀏覽器裡看到一個非常陽春的網頁。

### 加入minimal-mistakes主題

有了jekyll還不夠，jekyll只是一個網站產生器，我們選用jekyll的原因是因為其中有一個超棒的主題：minimal-mistakes。這個主題簡潔、在github page社群有廣大的使用者使用它，因此我們將主要使用minimal-mistakes進行主題布置。由於之前已經架好了jekyll了，所以我們將用remote theme的方式將minimal-mistakes加入主題。

#### 1. 替換Gemfile

用記事本(強者都用記事本打code)或是vscode之類的編輯器打開**Gemfile**，將裡面的內容全部換成以下的程式碼打掉重練：
{% highlight ruby %}
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
gem "jekyll-include-cache", group: :jekyll_plugins
{% endhighlight %}

#### 2. 修改_config.yml

打開_config.yml，在開頭為plugins的那一行下方加入
{% highlight ruby %}
jekyll-include-cache
{% endhighlight %}

#### 3. 安裝套件

輸入以下指令來安裝需要的套件
{% highlight bash %}
bundle install
{% endhighlight %}

#### 4. 修改_config.yml

打開_config.yml並刪除開頭為 theme 或 remote-theme 的欄位，並將
{% highlight ruby %}
remote_theme: "mmistakes/minimal-mistakes@4.24.0"
{% endhighlight %}
加入此檔案。

#### 5. 複製minimal-mistakes的檔案

到minimal-mistakes的[github頁面](https://github.com/mmistakes/minimal-mistakes)把它通通clone下來，將以下的5個資料夾複製到自己的repo裡面。
{% highlight bash %}
_data
_includes
_layouts
_sass
assets
{% endhighlight %}
#### 6. 本地執行
執行
{% highlight bash %}
bundle exec jekyll serve
{% endhighlight %}
並確認是否在本地端跑起來了，如果沒有的話輸入以下指令，如果再沒有的話我也不知道該怎麼辦了...<font color='red'><strike>超級不負責任</strike></font>  
{% highlight bash %}
bundle add webrick
bundle add wdm
{% endhighlight %}
#### 7. 遠端部屬(deploy)

將完成的repo推上去github，並在<your name>.github.io查看是否有成功運行

## 結語

後續對於minimal-mistake的一些版面設計基本上都可以在[Minimal Mistakes的官網](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)上找到，這篇文主要就是教大家怎麼建一個minimal-mistakes的網頁而已。對於我後續研究如何改一些css的樣式我會再寫另一篇文。[下篇文連結](/tutorial/minimal-mistakes-modify)



## reference
1. [用 Jekyll 和 Github Page 來架設靜態 Markdown 部落格](https://medium.com/@starshunter/%E7%94%A8-jekyll-%E5%92%8C-github-page-%E4%BE%86%E6%9E%B6%E8%A8%AD%E9%9D%9C%E6%85%8B-markdown-%E9%83%A8%E8%90%BD%E6%A0%BC-fcaa288d4dd7)
2. [透過 Jekyll 與 GitHub Pages 建立自己的部落格(2)](https://ktinglee.github.io/install-my-blog(2)/)
3. [一碰minimal-mistakes就上手](https://kodeworker.github.io/%E6%95%99%E5%AD%B8/%E4%B8%80%E7%A2%B0minimal-mistakes%E5%B0%B1%E4%B8%8A%E6%89%8B/)
4. [Minimal Mistakes tutorial](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
5. [Jekyll serve fails on Ruby 3.0](https://github.com/jekyll/jekyll/issues/8523)