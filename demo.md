# Configuration Split Demo(時短Ver.)


## gitリポジトリについて

https://github.com/snize/demo-config-split

- コミットログにあるコマンドは各コミットに対応
- タグは以降のプレゼンの見出しに対応
- 実際に手元で試すと**UUIDが一致しない**



## STEP 00<br>プロジェクトの準備


### step_00a<br>BLTプロジェクトの新規作成

	$ composer clear-cache
	$ export COMPOSER_PROCESS_TIMEOUT=2000
	$ composer create-project --no-interaction acquia/blt-project demo-config-split


### step_00b<br>BLTプロジェクトの設定変更

今回はデモのためにコミットメッセージの<br>バリデーションを無効化

	# blt/project.yml
	...
	git:
	  default_branch: master
	  remotes: {  }
	  hooks:
	    commit-msg: false
	...

以下のコマンドで.git/hooks/commit-msgを削除

	$ blt setup:git-hooks


### step_00c<br>Drupal VMの初期化

VMの設定を変更するので、bootはしない<br>
後から`vagrant reload --provision`でもOK

	$ blt vm
	Drupal VM is not currently installed. Install it now?  (y/n) y
	Do you want to boot Drupal VM? (y/n) n


### step_00d<br>VMの設定変更と起動

Tips:phpのメモリ上限を増やしておく

	# box/config.yml
	# Increasing the Memory Limit
	php_memory_limit: "512M"


初回はVMのプロビジョニングが実行されるため、<br>このステップは20分程度時間がかかる（環境による）

	$ vagrant up


### step_00e<br>Drupal 8のインストール

	$ blt setup
	# Install dependencies, builds docroot, installs Drupal.
	# see ./vendor/acquia/blt/phing/tasks/setup.xml



## STEP 01<br>初期設定のエクスポート


### step_01a<br>インストール直後の状態で<br>設定をエクスポート

	$ drush @demo-config-split.local config-export -y

`config/default`へYAMLファイルが書き出される<br>
これはCore CMの標準機能で管理画面からも可能



## STEP 02<br>モジュールとライブラリの依存解決
Note:
ここからが今日のメインテーマの下準備


### step_02a<br>composerでプロジェクトに追加

Configuration Split

#### 以下はオプション

jQueryライブラリの[Chosen](https://harvesthq.github.io/chosen/)<br>
[Chosen](https://www.drupal.org/project/chosen)のDrupalモジュール


packagistに無いリポジトリのためにcomposer.jsonへ追記<br>
今回の場合Chosenのライブラリをgithubから追加

     ...
        "geerlingguy/drupal-vm": "~4.3"
    },
    "repositories": [
        {
            "type": "package",
            "package": {
                "name": "harvesthq/chosen",
                "version": "1.7.0",
                "type": "drupal-library",
                "dist": {
                    "url": "https://github.com/harvesthq/chosen/releases/download/v1.7.0/chosen_v1.7.0.zip",
                    "type": "zip"
                }
            }
        }
    ],
    "extra": {
        "enable-patching": true,
    ...


### composerでパッケージを追加

	$ composer require drupal/config_split drupal/chosen harvesthq/chosen



## STEP 03<br>設定の保存先の準備


### step_03a<br>環境ごとの設定を保存する準備

BLTにデフォルトで用意されているdefaultとciに加え、<br>localとprodを作成

	$ mkdir ./config/local ./config/prod
	$ cp ./config/default/.htaccess ./config/local
	$ cp ./config/default/.htaccess ./config/prod



## STEP 04<br>Drupalへモジュールのインストールと設定のエクスポート


### step_04a

Drupalへモジュールのインストール

	$ drush @demo-config-split.local en config_split chosen -y

今モジュールをインストールした状態をエクスポート

	$ drush @demo-config-split.local config-export -y


# Demoはここから
手を動かします
Note:
git checkout step_04a; composer install && blt setup:config-import && blt setup:settings;noti;



## STEP 05<br>config_splitからそれぞれの環境に設定を追加


### step_05a

#### 管理画面へログイン

	drush @demo-config-split.local uli 1 admin/config


### local,prodの設定を追加
#### ポイント

Activeのチェックを外す

#### 再び設定をエクスポート

	drush @demo-config-split.local config-export -y

環境ごとの出力先の設定は完了<!-- .element: class="fragment" data-fragment-index="1" -->


## STEP 06<br>settings.phpの変更


### step_06a<br>settings.phpへそれぞれの<br>環境用にフラグを設定


通常は環境変数などを利用するのが良い。以下はデモ用

	// Set TRUE to make config active for specific environment.
	$config['config_split.config_split.local']['status'] = TRUE;
	$config['config_split.config_split.prod']['status'] = FALSE;

もしsettings.phpが編集できなかったらファイルの書き込み権限を確認または以下を実行して権限を修正

	$ blt setup:settings



## STEP 07<br>ローカル用の構成管理


### step_07a<br>config_splitでブラックリストの設定

管理画面からlocal用にDevelとViews UIを<br>ブラックリストとして追加


ブラックリストに追加した設定をエクスポート

	drush @demo-config-split.local config-export -y

先ほど設定した ../config/localへ設定が書き出され、共通設定の ../config/defaultから削除された
<!-- .element: class="fragment" data-fragment-index="1" -->


## STEP 08<br>本番環境での設定


### step_08a<br>設定を切り替えとインポート


#### 本番環境用に設定を切り替え（以下はデモ用）

	// Set TRUE to make config active for specific environment.
	$config['config_split.config_split.local']['status'] = FALSE;
	$config['config_split.config_split.prod']['status'] = TRUE;


#### 設定をインポート

	$ blt setup:config-import

設定が切り替わっているか確認<!-- .element: class="fragment" data-fragment-index="1" -->



### step_08b<br>本番環境へモジュールのインストールと<br>構成のエクスポート


#### 本番環境へモジュールのインストール

	$ drush @demo-config-split.local en \
	ban big_pipe \
	dynamic_page_cache syslog -y

- ban
- big_pipe
- dynamic_page_cache
- syslog

インストールされたか確認<!-- .element: class="fragment" data-fragment-index="1" -->



### 本番環境のブラックリストへ登録

- ban
- big_pipe
- dynamic_page_cache
- syslog
- syslog.settings


#### 構成のエクスポート

	$ drush @demo-config-split.local config-export -y



## STEP 09<br>再びlocalへ


### step_09a<br>settings.php

	// Set TRUE to make config active for specific environment.
	$config['config_split.config_split.local']['status'] = TRUE;
	$config['config_split.config_split.prod']['status'] = FALSE;


設定をインポート

	$ blt setup:config-import

設定が切り替わっているか確認<!-- .element: class="fragment" data-fragment-index="1" -->



# まとめ

local, prod **それぞれの設定** をエクスポートした<br>
環境変数などを利用して、 **任意の設定** をインポートした<br>
**CoreのCMで扱えるもは管理可能**<br>
モジュール以外にも, Views, Taxonomy, Menuなど<br>
テキストや画像などは **スコープ外**<br>

Slack, Twitterなどでフィードバックお待ちしてます！<!-- .element: class="fragment" data-fragment-index="1" -->


# おわり！

Tomotsugu Kaneko
[@snize](https://twitter.com/snize)<br>
kaneko@zerobase.jp<br>
