# Configuration Management with Drupal

Tomotsugu Kaneko
[@snize](https://twitter.com/snize)<br>
kaneko@zerobase.jp<br>


## 本日の流れ
1. Configuration Managementとは
2. Drupalの良いところ
3. DrupalでのConfiguration Management
4. Demo

Note:
今日はConfiguration Management（構成管理）の話と、<br>
Configration Splitで**環境ごとの設定を管理するデモ**をやります


# Configuration Managementとは
[ソフトウェア構成管理 - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E6%A7%8B%E6%88%90%E7%AE%A1%E7%90%86)

> ソフトウェア開発プロジェクトをその成果物を通して制御・管理する方法論である。<br>ソースコードや文書などの**成果物の変更履歴を管理し**、製品のバージョンやリビジョンに個々の成果物のどのバージョンが対応しているかを識別し、**任意のバージョンの製品を再現可能**とする。


## [質問]Configuration Managementしてますか？

 (｡・ω・｡)ﾉ ハイッ


## なぜConfiguration Managementが<br>重要か

それは...<br>
この本に書いてあります！

<a href="https://www.amazon.co.jp/%E7%B6%99%E7%B6%9A%E7%9A%84%E3%83%87%E3%83%AA%E3%83%90%E3%83%AA%E3%83%BC-%E4%BF%A1%E9%A0%BC%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E3%83%AA%E3%83%AA%E3%83%BC%E3%82%B9%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AE%E3%83%93%E3%83%AB%E3%83%89-%E3%83%86%E3%82%B9%E3%83%88-%E3%83%87%E3%83%97%E3%83%AD%E3%82%A4%E3%83%A1%E3%83%B3%E3%83%88%E3%81%AE%E8%87%AA%E5%8B%95%E5%8C%96-David-Farley/dp/4048707876/ref=as_li_ss_il?ie=UTF8&qid=1497751623&sr=8-2&keywords=%E7%B6%99%E7%B6%9A%E7%9A%84&linkCode=li3&tag=cycle083-22&linkId=9f85ad3f4e8a6c8d284237901965862e" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4048707876&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=cycle083-22" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=cycle083-22&l=li3&o=9&a=4048707876" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />


### ざっくり解説
[アジャイルマニフェスト](http://agilemanifesto.org/iso/ja/manifesto.html)の最初の条文の<br>「我々が最も価値を置くのは、価値のあるソフトウェアを早いうちから**継続的にデリバリーすることを通じて顧客を満足させる**ことである」

この継続的デリバリーを実現するためのソフトウェアデリバリーの原則の中のひとつ**「すべてバージョン管理にいれよ」** <br>と、言っている

注：アジャイルの話はしません


## 要するに、<br>Configuration Managementは<br>顧客に価値を提供する重要なパーツのひとつ
と理解しています<!-- .element: class="fragment" data-fragment-index="1" -->


## Drupalで<br>Configuration Management<br>する理由


### 複雑なフローをシンプルにする
複数人・複数拠点での環境再現<br>
複数機能の平行開発<br>

### デプロイとロールバック
いつでもリリース<br>
デプロイを自動化<br>
誰でもデプロイ


## DrupalでConfiguration Managementしない場合

	$ drush sql-dump --result-file=../20170618_add_jsonapi.sql
<!-- .element: class="fragment" data-fragment-index="1" -->


### 増え続ける謎のSQLダンプ
どれをインポートすれば良いのかわからない<br>順序があったりコンフリクトしてたり<!-- .element: class="fragment" data-fragment-index="1" -->
### 結果として蘇る・消える設定
バグだと思ったら設定変わってただけ orz<!-- .element: class="fragment" data-fragment-index="2" -->
### 設定のデプロイはどうする？
...手作業そして属人化<!-- .element: class="fragment" data-fragment-index="3" -->


<a href="https://www.amazon.co.jp/%E7%B6%99%E7%B6%9A%E7%9A%84%E3%83%87%E3%83%AA%E3%83%90%E3%83%AA%E3%83%BC-%E4%BF%A1%E9%A0%BC%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E3%83%AA%E3%83%AA%E3%83%BC%E3%82%B9%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AE%E3%83%93%E3%83%AB%E3%83%89-%E3%83%86%E3%82%B9%E3%83%88-%E3%83%87%E3%83%97%E3%83%AD%E3%82%A4%E3%83%A1%E3%83%B3%E3%83%88%E3%81%AE%E8%87%AA%E5%8B%95%E5%8C%96-David-Farley/dp/4048707876/ref=as_li_ss_il?ie=UTF8&qid=1497751623&sr=8-2&keywords=%E7%B6%99%E7%B6%9A%E7%9A%84&linkCode=li3&tag=cycle083-22&linkId=9f85ad3f4e8a6c8d284237901965862e" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4048707876&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=cycle083-22" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=cycle083-22&l=li3&o=9&a=4048707876" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />


## DBの同期での作業は
コンテンツ側が日々更新されている場合や<br>
平行開発では**現実的ではない**<br>
手作業では**ミスが起こる可能性が高い**<br>

つらい<!-- .element: class="fragment" data-fragment-index="1" -->


# つらくない仕事をして<br>よろこばれたい！



## Configuration Management

成果物の変更履歴を管理し任意のバージョンの製品を<br>再現可能とする<br>

つらさを取り除き、**本質的な作業に集中できる**

他のOSSのCMSにはない機能(発表者の観測範囲)<!-- .element: class="fragment" data-fragment-index="1" -->



# Drupalの良いところ3つ


### 1.コーディングレスで<br>WEBサイトやAPIサーバが構築できる

自分のコードでバグや脆弱性が生まれにくい<br>
Drupalコアやモジュール自体の脆弱性などは別の話
#### コーディングレスをサポートするモジュールたち
Views (core)<br>
Blocks (core)<br>
CKEditor (core)<br>
[Paragraphs](https://www.drupal.org/project/paragraphs) (contrib)<br>
[JSON API](https://www.drupal.org/project/jsonapi) (contrib)<br>
...


## 2.モジュール間の連携がしやすい

依存関係が明示的<br>
車輪の再発明が少ない<br>
モノリシックになりにくい（もちろんそうでないものもある）

### なので
モジュールのサブモジュールが多い<br>
多くの人に再利用されるから品質が上がりやすい<br>
モジュールが豊富<br>
コミュニティのコミュニケーションが活発（個人の感想）


## 3.アプリケーションレベルでのConfiguration Managementがgitなどでできる

インフラ系のConfiguration Managementではないです<br>
[Rails](http://rubyonrails.org/)や[Laravel](https://laravel.com/)など最近のウェブアプリケーションフレームワークでは **普通**



# DrupalでのConfiguration Management


## Drupal 7 でのConfiguration Management
[Features](https://www.drupal.org/project/features) (contrib)<br>
[Strongarm](https://www.drupal.org/project/strongarm) (contrib)<br>


## Drupal 8 でのConfiguration Management
[Features](https://www.drupal.org/project/features) (contrib)<br>
[Configuration management](https://www.drupal.org/docs/8/configuration-management) (core)<br>
[Configuration Split](https://www.drupal.org/project/config_split) (contrib)<br>


## Drupal 8 Core Configuration Management
基本的に **サイト丸ごと** import/export だけ<br>
特定の環境のためだけのモジュールや設定の管理はできない

#### 特定の環境のためだけのモジュール
Syslog/Database Logging (core)<br>
Views UI (core)<br>
[Devel](https://www.drupal.org/project/devel) (contrib)<br>
[Twig Xdebug](https://www.drupal.org/project/twig_xdebug) (contrib)<br>



## Drupal 8 Configuration Split Module
**環境ごとに分割** 必要に応じて設定を読み込む<br>
例えば、Local, Dev, Test, Prod<br>
共通の設定はDefaultに格納



# Demo