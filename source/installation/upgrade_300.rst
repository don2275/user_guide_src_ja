###################################
2.2.x から 3.0.0 へのアップグレード
###################################

アップグレードを行う前に、 index.php ファイルを静的ページに置き換えて、サイトをオフラインにする必要があります。

**************************************
Step 1: CodeIgniter ファイルを更新する
**************************************

*system/* ディレクトリのすべてのファイルと index.php を新しいものに
**置き換えてください**。index.php に何か変更を加えていた場合、
新しいファイルに再度適用する必要があります。

.. important:: まず、古い *system/* ディレクトリを削除し、
	それから新しいものを配置する必要があります。単にコピーペーストすると、 
	問題が生じる可能性があります。

.. note:: もし独自に開発したファイルをこれらのフォルダに入れているなら、
	新しいもので置き換える前にコピーを取ってください。

**************************************
Step 2: クラスファイルの名前を更新する
**************************************

CodeIgniter 3.0 以降、全てのクラスファイル
（ライブラリ、ドライバ、コントローラ、そしてモデル）の名前は Ucfirst 形式、
つまり大文字で始まるようにしなければなりません。

例えば、あなたが次のようなライブラリを持っているなら:

	application/libraries/mylibrary.php

このようにファイル名を変更します:

	application/libraries/Mylibrary.php

ドライバライブラリ、それから CodeIgniter 付属の
コアクラスやライブラリを拡張や置き換えしている場合も同様です。

	application/libraries/MY_email.php  
	application/core/MY_log.php

これらのファイル名は次のように変更します:

	application/libraries/MY_Email.php  
	application/core/MY_Log.php

コントローラ:

	application/controllers/welcome.php	->	application/controllers/Welcome.php

モデル:

	application/models/misc_model.php	->	application/models/Misc_model.php

このルールはディレクトリ名、設定ファイル、ビュー、ヘルパー、フック等には適用
されません。クラスのみが対象です。

まとめると、次のシンプルな 1 つのルールになります。
クラス名は Ucfirst、それ以外は小文字です。

**************************************
Step 3: config/mimes.php を置き換える
**************************************

より多くの MIME タイプを含むように更新されています。
これを *application/config/mimes.php* にコピーしてください.

*************************************************************
Step 4: config/autoload.php から $autoload['core'] を削除する
*************************************************************

``$autoload['core']`` は CodeIgniter 1.4.1 で非推奨になっていましたが、ついに削除されました。
もし何か指定されていれば ``$autoload['libraries']`` に移動してください。

**********************************************
Step 5: Log クラスへの拡張・置き換えを移動する
**********************************************

Log クラスは "core" クラスとして扱われるようになったため、
**system/core/** ディレクトリに移動されました。もし Log クラスを拡張・置き換えて
いた場合、それらのファイルを **application/core/** に移動してください:

	application/libraries/Log.php -> application/core/Log.php  
	application/libraries/MY_Log.php -> application/core/MY_Log.php

**********************************************
Step 6: セッションライブラリの使い方を更新する
**********************************************

:doc:`セッションライブラリ </libraries/sessions>` は CodeIgniter 3 で刷新され、
様々な新機能が追加されています。
しかし同時に、注意の必要な変更も加えられています。

最も大きな変更は、（暗号化された）クッキーの代わりに
独立したストレージを使うようになったことです。
データの格納先としてのクッキーは完全に廃止されたので、
今後はサーバ上の何らかのストレージエンジンを使わなければなりません。
デフォルトではファイルシステムが使われます。

新しいセッションクラスは PHP のカスタムセッションハンドラを利用しています。
これによりスーパーグローバルの ``$_SESSION`` からも
セッションデータにアクセスできるようになりました
（これまで同様 "userdata" としても使えます）。

設定オプションも追加・削除されています。
詳しくは :doc:`セッションライブラリのマニュアル </libraries/sessions>` を
すべて読まれることをお勧めしますが、
ここでもいくつかのポイントを説明します。

  - ``$config['sess_driver']`` を設定する

    デフォルトは 'files' ですが、もしあなたが
    ``$config['sess_use_database']`` を設定していた場合は
    'database' になります。

  - ``$config['sess_save_path']`` を設定する

    'database' ドライバでは ``$config['sess_table_name']`` に
    フォールバックしますが、それ以外のドライバではマニュアルを参照して
    選択したドライバでの適切な値を設定する必要があります。

  - ``ci_sessions`` テーブルを変更する ('database' ドライバのみ)

    テーブルの構造が少し変更されています。具体的には:

      - ``session_id`` 列は ``id`` に名前が変わりました
      - ``user_agent`` 列は削除されました
      - ``user_data`` 列は ``data`` に名前が変わり、 MySQL では BLOB 型が使われます
      - ``last_activity`` 列は ``timestamp`` に名前が変わりました

    これに伴いインデックスの更新も必要です。
    詳しくは `Session Database ドライバ
    <../libraries/sessions.html#database-driver>`_ の説明を参照してください。

    .. important:: 現在公式にサポートしているのは MySQL と PostgreSQL のみです。
    	他のデータベースでも動作するかもしれませんが、助言ロックがないために
    	同時アクセスに対して安全ではありません。
    	この場合は他のドライバを検討してください。

  - ``$config['sess_match_useragent']`` を削除する

    ユーザエージェント文字列はブラウザから提供される値、つまりクライアントからの
    入力値です。これを検証しても意味がないので、
    この機能は廃止されました。

  - ``$config['sess_encrypt_cookie']`` を削除する

    前述の通り、今後クッキーにセッションデータが格納されることは
    ありませんから、このオプションもお役御免になりました。

  - ``$config['sess_expire_on_close']`` を削除する

    このオプションはまだ使えますが、後方互換性のためだけに残されています。
    同じ効果は ``$config['sess_expiration']`` を 0 に設定することで
    得ることができます。

  - "flashdata" と "userdata" の衝突をチェックする

    フラッシュデータは次のリクエストで削除するようにマークされている以外は
    ただの "userdata" になりました。つまり、同じ名前の "userdata" と
    "flashdata" を別々に扱うことはできません。

  - セッションメタデータをチェックする

    これまでは 'session_id' 、'ip_address' 、'user_agent' 、'last_activity'
    といったメタデータにも userdata としてアクセスできました。
    今後はできませんので、もしこれらのデータを利用しているなら、
    `セッションメタデータ <../libraries/sessions.html#accessing-session-metadata>`_
    に関する説明を参照してください。

  - ``unset_userdata()`` の使い方をチェックする

    これまでは ``'key' => 'dummy value'`` といった連想配列で、
    一度に複数のキーを削除することができました。
    しかし、この方法は無意味なので、
    今後はキー *だけ* を配列の要素として渡さなければなりません。

    ::

    	// 以前
    	$this->session->unset_userdata(array('item' => '', 'item2' => ''));

    	// 今後
    	$this->session->unset_userdata(array('item', 'item2'));

最後に、もしセッションを拡張して使っていたら、
*application/libraries/Session/* ディレクトリに移動してください。
たぶん、コードの書き換えも必要でしょうが。

***************************************
Step 7: config/database.php を更新する
***************************************

3.0.0 で Active Record から Query Builder に名前が変更されたので、
**config/database.php** の ``$active_record`` 変数も
``$query_builder`` に変更する必要があります::

	$active_group = 'default';
	// $active_record = TRUE;
	$query_builder = TRUE;

**************************************
Step 8: エラーテンプレートを置き換える
**************************************

CodeIgniter 3.0 ではエラーテンプレートもビューとして扱われ、
*application/views/errors* ディレクトリに移動されました。

また、CLI 向けにプレインテキストのエラーテンプレートを
サポートするようになりました。これも新たに分ける必要があります。

これまでのテンプレートは *application/errors* から *application/views/errors/html* にそのまま移動して構いませんが、
新しい *application/views/errors/cli* ディレクトリは CodeIgniter のアーカイブからコピーしてください。

********************************************
Step 9: config/routes.php ファイルを更新する
********************************************

:any を含むルート
======================

歴史的に、 CodeIgniter はこれまでずっとワイルドカード **:any** を
提供してきました。これは URI セグメント **内** で
任意の文字列にマッチします。

しかし実際の **:any** は **.+** という正規表現のエイリアスとして
処理されていました。この正規表現は URI セグメントの
区切り文字である / (スラッシュ) にもマッチするため、
意図しない挙動を生むことになります。これはバグと考えられます。

CodeIgniter 3 では **:any** が **[^/]+** を表わすようになり、
スラッシュにマッチすることはなくなりました。

一方でこのバグを活用していた開発者もたくさんいることでしょう。
もしあなたがその一人で、引き続きスラッシュにマッチさせたいなら、
正規表現 **.+** を使ってください::

	(.+)	// 全ての文字にマッチする
	(:any)	// '/' 以外、全ての文字にマッチする

ディレクトリと 'default_controller'、'404_override'
====================================================

ご存じのように ``$route['default_controller']`` と
``$route['404_override']`` の設定は、コントローラ名だけでなく、
*コントローラ/メソッド* のペアも受け取ります。しかしながら、ルーティングロジック
にバグがあり、
*ディレクトリ/コントローラ* として使っているユーザがみられます。

この振る舞いは意図したものではなく、偶然であり文書化もされていません。
もしあなたのアプリケーションがこれを使っていたら、CodeIgniter 3.0 では動かなく
なります。

バージョン 3 における他の言及すべき変更は、'default_controller' と
'404_override' が *ディレクトリ毎に* 適用されることです。説明のために、
以下の例を用います::

	$route['default_controller'] = 'main';

あなたの Web サイトが *example.com* にあるとすると、ユーザが
``http://example.com/`` にアクセスすると、上記の設定が
'Main' コントローラをロードします。

ところで、もし *application/controllers/admin/* ディレクトリがあり、
ユーザが ``http://example.com/admin/`` にアクセスしたらどうなるでしょう?
CodeIgniter 3 では、ルータが admin/ ディレクトリの下の 'Main' コントローラ
を探します。見つからなければ、Not Found (404) が起こります。

同じルールが '404_override' 設定にも適用されます。

**************************************************************************************
Step 10: 多くの関数が要素がないとき FALSE ではなく NULL を返すようになりました
**************************************************************************************

多くのメソッドと関数は、要求された要素が存在しないとき、FALSE ではなく NULL を返すようになりました:

 - :doc:`共通関数 <../general/common_functions>`

   - config_item()

 - :doc:`Config クラス <../libraries/config>`

   - config->item()
   - config->slash_item()

 - :doc:`Input クラス <../libraries/input>`

   - input->get()
   - input->post()
   - input->get_post()
   - input->cookie()
   - input->server()
   - input->input_stream()
   - input->get_request_header()

 - :doc:`Session クラス <../libraries/sessions>`

   - session->userdata()
   - session->flashdata()

 - :doc:`URI クラス <../libraries/uri>`

   - uri->segment()
   - uri->rsegment()

 - :doc:`Array クラス <../helpers/array_helper>`

   - element()
   - elements()

*******************************
Step 11: XSS フィルタの使い方
*******************************

CodeIgniter の多くの関数は、論理値のパラメータを渡すことで、
XSS フィルタリング機能を使うことができます。
そのデフォルト値は以前は FALSE でしたが、今は NULL に変更され、
``$config['global_xss_filtering']`` の値により動的に
決定されます。

もし、手動で論理値を ``$xss_filter`` パラメータに渡していたり、
``$config['global_xss_filtering']`` に FALSE を設定していた場合は、
この変更は影響しません。

そうでない場合は、次の関数の使い方を確認してください:

 - :doc:`Input ライブラリ <../libraries/input>`

   - input->get()
   - input->post()
   - input->get_post()
   - input->cookie()
   - input->server()
   - input->input_stream()

 - :doc:`Cookie ヘルパー <../helpers/cookie_helper>` :php:func:`get_cookie()`

.. important:: 他の関連する変更点として、 ``$_GET``、``$_POST``、
	``$_COOKIE`` そして ``$_SERVER`` スーパーグローバルは、グローバル XSS
	フィルタリングがオンの場合に、自動的に上書きされなくなりました。

******************************************************
Step 12: URI にまつわる潜在的な XSS 問題をチェックする
******************************************************

:doc:`URI ライブラリ <../libraries/uri>` は以前は特定の "問題のある文字"
が URI セグメントに含まれるとき、HTML エンティティに自動的に
変換していました。

これは、``$config['permitted_uri_chars']`` の設定に加えて、
自動 XSS 保護の提供を目的としていましたが、問題があることが判明したため、
CodeIgniter 3.0 で削除されました。

もし、あなたのアプリケーションがこの機能に頼っているなら、
出力時に ``$this->security->xss_clean()`` で URI セグメントを
フィルタするように更新するべきです。

****************************************************************
Step 13: バリデーションルール 'xss_clean' の使い方をチェックする
****************************************************************

XSS クリーニングについて広く知られていないルールとして、
入力データに対してではなく、*出力にのみ適用すべき* があります。

我々は自動グローバル XSS クリーニング機能 (上の XSS についてのステップを参照)
においてミスを犯しました。そのため、現在、そのような実践に反対するために、
:doc:`フォームバリデーション <../libraries/form_validation>` のルールの
公式サポートリストから、'xss_clean' を削除しています。

何故なら、:doc:`フォームバリデーションライブラリ <../libraries/form_validation>`
は一般的に *入力* データを検証するため、
'xss_clean' ルールは単純にそこに属さないからです。

もし本当に本当にそのルールを適用する必要があるなら、
通常の関数として ``xss_clean()`` が含まれる
:doc:`Security ヘルパー <../helpers/security_helper>` をロードしてください。
通常の関数なら検証ルールとして今でも使用できます。

***********************************************************
Step 14: 入力クラスの get_post() メソッドの使い方を更新する
***********************************************************

以前は、 :doc:`Input クラス <../libraries/input>` の ``get_post()``
メソッドは最初に POST データを探し、それから GET データを探しました。
このメソッドは、その名前が示すように、GET を先に探しその後 POST を
探すように変更されました。

以前と同じようにするために、POST を先に探し GET を探す ``post_get()`` メソッドが追加されました。

**********************************************************************
Step 15: ディレクトリヘルパーの directory_map() 関数の使い方を更新する
**********************************************************************

結果の配列の中で、ディレクトリはディレクトリセパレータ (通常はスラッシュ)
で終わるようになりました。

***********************************************************************
Step 16: データベースフォージの drop_table() メソッドの使い方を更新する
***********************************************************************

今まで ``drop_table()`` は IF EXISTS 句をデフォルトで追加していましたが、
いくつかのドライバでは全く動作しませんでした。
CodeIgniter 3.0 では、IF EXISTS 条件はデフォルトでは追加されず、
オプションの第 2 引数が追加され、デフォルトは FALSE です。

もし、アプリケーションが IF EXISTS を使っているなら、使い方を変更する必要があります。

::

	// 今は DROP TABLE `table_name` だけを生成します
	$this->dbforge->drop_table('table_name');

	// DROP TABLE IF EXISTS `table_name` を生成します
	$this->dbforge->drop_table('table_name', TRUE);

.. note:: この例では MySQL 固有の文法を使っていますが、ODBC 以外のすべての
	ドライバで機能します。

***********************************************************
Step 17: Email ライブラリでの複数メールの扱いを変更する
***********************************************************

:doc:`Email ライブラリ <../libraries/email>` は、メール送信が成功した後に、
自動的に設定されたパラメータをクリアします。
この振る舞いを上書きしたい場合は、``send()`` メソッドの第 1 引数に FALSE
を渡します:

::

	if ($this->email->send(FALSE))
 	{
 		// パラメータはクリアされません
 	}

***************************************************
Step 18: Form_validation の言語ファイルを更新する
***************************************************

:doc:`Form Validation ライブラリ <../libraries/form_validation>` の
:doc:`言語 <../libraries/language>` ファイルとエラーメッセージのフォーマットに
2 つの改良が加えられました:

 - :doc:`Language ライブラリ <../libraries/language>` の行のキーは、衝突を
   避けるために、接頭辞 **form_validation_** を付けなければなりません::

	// 以前
	$lang['rule'] = ...

	// 今後
	$lang['form_validation_rule'] = ...

 - エラーメッセージのフォーマットが名前付きパラメータを使うように変更されました。
   `sprintf()` よりも柔軟にするためです::

	// 以前
	'The %s field does not match the %s field.'

	// 今後
	'The {field} field does not match the {param} field.'

.. note:: 古いフォーマットもまだ動作しますが、接頭辞のない行のキーは廃止予定
	であり、CodeIgniter 3.1+ で削除される予定です。
	よって、早めに更新することが推奨されます。

************************************************************
Step 19: 'base_url' の設定値が空でないことを確認する
************************************************************

``$config['base_url']`` が設定されていないとき、CodeIgniter は Web サイトの
ベース URL を自動的に検知しようとします。これは、純粋に新しいアプリケーションの
開発を開始する際、便利なようになされているものです。

自動検知は決して信頼できるものではなく、セキュリティと関連します。
だから、**いつでも** 手動で設定すべきものです。

CodeIgniter 3.0.3 での変更点の 1 つは、自動検知がどう機能するかです。
より具体的に言えば、クライアントによりリクエストされたホスト名ではなく、
サーバの IP アドレスにフォールバックするようになりました。ゆえに、
もしあなたが自動検知に頼っている場合、Web サイトの振る舞いが変更されるでしょう。

例えば、複数のドメインを使っていたり、http:// と https:// の両方のプリフィックス
をリクエストに応じて動的に使っている場合、
*application/config/config.php* は PHP スクリプトであることを思い出してください。
例えば、次のような数行のコードでロジックを記述できます::

	$allowed_domains = array('domain1.tld', 'domain2.tld');
	$default_domain  = 'domain1.tld';

	if (in_array($_SERVER['HTTP_HOST'], $allowed_domains, TRUE))
	{
		$domain = $_SERVER['HTTP_HOST'];
	}
	else
	{
		$domain = $default_domain;
	}

	if ( ! empty($_SERVER['HTTPS']))
	{
		$config['base_url'] = 'https://'.$domain;
	}
	else
	{
		$config['base_url'] = 'http://'.$domain;
	}


****************************************************************
Step 20: (以前から) 廃止予定の機能を削除する
****************************************************************

``$autoload['core']`` の設定以外にも、CodeIgniter 3.0.0 で削除された機能は
たくさんあります。

SHA1 ライブラリ
================

以前から廃止予定だった SHA1 ライブラリが削除されました。
SHA1 ハッシュを生成するには、PHP のネイティブの ``sha1()`` 関数を使うようにコードを変更してください。

加えて、:doc:`Encrypt ライブラリ <../libraries/encrypt>` の ``sha1()`` メソッド も削除されました。

EXT 定数
================

PHP 4 のサポートをドロップして以来、``EXT`` 定数の使用は推奨されていません。この
新しい CodeIgniter のバージョンで、もはや異なるファイル名の拡張子を保持する必要は
ありません。``EXT`` 定数は削除されました。代わりに、単に '.php' を使って下さい。

Smiley ヘルパー
===============

:doc:`Smiley ヘルパー <../helpers/smiley_helper>` は EllisLab の製品
ExpressEngine 由来のレガシーな機能です。CodeIgniter のような一般的な用途の
フレームワークにとっては、具体的すぎるため、廃止予定となりました。

また、以前から廃止予定の ``js_insert_smiley()`` (バージョン 1.7.2 から) は削除されました。

Encrypt ライブラリ
===================

多数の脆弱性報告に従い、 :doc:`Encrypt ライブラリ <../libraries/encrypt>` は
廃止予定となり、新たな :doc:`Encryption ライブラリ <../libraries/encryption>`
が代わりに追加されました。

新しいライブラリは `MCrypt 機能拡張 <http://php.net/mcrypt>`_ 
(そして /dev/urandom が利用可能なこと) または PHP 5.3.3 と 
`OpenSSL 機能拡張 <http://php.net/openssl>`_ を必要とします。
これは少々不便ですが、暗号の機能を適切に実装するための必要条件です。

.. note:: :doc:`Encrypt ライブラリ <../libraries/encrypt>` はまだ後方互換の
	ために使用可能です。

.. important:: できるだけ早く新しい :doc:`Encryption ライブラリ
	<../libraries/encryption>` に移行することが強く推奨されます!

Cart ライブラリ
================

:doc:`Cart ライブラリ <../libraries/cart>` は :doc:`Smiley ヘルパー
<../helpers/smiley_helper>` と同じように、CodeIgniter にとっては具体的
過ぎます。現在、廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: このライブラリはまだ使用可能ですが、早急に使用を止めることを強く
	推奨します。

データベースドライバ 'mysql'、'sqlite'、'mssql'、'pdo/dblib'
============================================================

**mysql** ドライバは、古いコードベースと多くの低レベルでの問題で知られる、古い 'mysql' PHP 機能拡張を利用します。
この機能拡張は PHP 5.5 で廃止予定となり、CodeIgniter ではバージョン 3.0 で
廃止予定となり、デフォルトの MySQL ドライバ設定は **mysqli** に変更されました。

'mysqli' または 'pdo/mysql' ドライバのいずれかを使用してください。古い
'mysql' ドライバは将来削除されます。

**sqlite**、**mssql** そして **pdo/dblib** (pdo/mssql または pdo/sybase としても知られる) ドライバはすべて、さまざまな理由から PHP 5.3 には存在しない PHP 機能拡張に依存しています。

それゆえ、それらのドライバも廃止予定であり、CodeIgniter の次のバージョンで
削除されます。
より進歩した **sqlite3**、**sqlsrv** または **pdo/sqlsrv** ドライバを
使うべきです。

.. note:: これらのドライバはまだ使用可能ですが、早急に他のドライバに
	変更することを強く推奨します。

Security ヘルパーの do_hash()
===============================

:doc:`Security ヘルパー <../helpers/security_helper>` の関数 ``do_hash()`` は
現在 PHP のネイティブ ``hash()`` 関数の単なるエイリアスです。廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: このライブラリはまだ使用可能ですが、早急に使用を止めることを強く
	推奨します。

$config['global_xss_filtering'] 設定
===========================================

上記ですでに説明したように、XSS フィルタリングは入力データに対しするものではなく、
出力にすべきものです。ゆえに、自動的に *入力* データをフィルタする
``$config['global_xss_filtering']`` は悪い実践だと考えられ、
廃止予定とされました。

代わりに、出力が必要なときに :php:func:`xss_clean()` 関数を使い、
ユーザの提供したデータを手動でエスケープするか、
`HTML Purifier <http://htmlpurifier.org/>`_ のようなライブラリを
使って下さい。

.. note:: この設定はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

File ヘルパーの read_file()
=============================

:doc:`File ヘルパー <../helpers/file_helper>` の ``read_file()`` 関数は、
現在単なる PHP のネイティブ関数 ``file_get_contents()`` のエイリアスです。
廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

String ヘルパーの repeater()
============================

:doc:`String ヘルパー <../helpers/string_helper>` の :php:func:`repeater()`
関数は、現在単なる PHP のネイティブ関数 ``str_repeat()`` のエイリアスです。廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

String ヘルパーの trim_slashes()
================================

:doc:`String ヘルパー <../helpers/string_helper>` の :php:func:`trim_slashes()` 関数は、現在単なる PHP のネイティブ関数
``trim()`` (第 2 引数にスラッシュが渡されて) のエイリアスです。
廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Form ヘルパーの form_prep()
=============================

:doc:`Form ヘルパー <../helpers/form_helper>` の :php:func:`form_prep()`
関数は、現在、 :doc:`共通関数 </general/common_functions>`
:func:`html_escape()` の単なるエイリアスです。 廃止予定であり、将来削除される予定です。

代わりに :php:func:`html_escape()` を使って下さい。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Email ヘルパーの関数
======================

:doc:`Email ヘルパー <../helpers/email_helper>` は 2 つの関数しか持っていません。

 - :php:func:`valid_email()`
 - :php:func:`send_email()`

どちらも現在 PHP ネイティブ関数 ``filter_var()`` と ``mail()`` のエイリアスです。
よって、:doc:`Email ヘルパー <../helpers/email_helper>` は廃止予定とされ、
CodeIgniter 3.1+ で削除される予定です。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Date ヘルパーの standard_date()
================================

:doc:`Date ヘルパー <../helpers/date_helper>` の ``standard_date()`` 関数は、
ネイティブの PHP `定数 <http://php.net/manual/en/class.datetime.php#datetime.constants.types>`_ と ``date()`` を組み合わせて
同じ機能を提供できるため、廃止予定とされました。
さらに、`standard_date()`` でサポートされているものと全く同じ名前を使えます。
置き換えの例は次のようになります:

::

	// 以前の方法
	standard_date(); // デフォルトは standard_date('DATE_RFC822', now());

	// 置き換え
	date(DATE_RFC822, now());

	// 以前の方法
	standard_date('DATE_ATOM', $time);

	// 置き換え
	date(DATE_ATOM, $time);

.. note:: この関数はまだ利用可能ですが、CodeIgniter 3.1+ で削除される予定のため
	早急に使用を止めることを強く推奨します。

HTML ヘルパーの nbs()、br()
============================

:doc:`HTML ヘルパー <../helpers/html_helper>` の関数 ``nbs()`` と ``br()`` は
``&nbsp;`` と ``<br >`` を使った場合のネイティブ関数 ``str_repeat()`` の単なるエイリアスです。

PHP ネイティブ関数の単なるエイリアスというだけなので、廃止予定とされ、
CodeIgniter 3.1+ で削除される予定です。

.. note:: この関数はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Pagination ライブラリの 'anchor_class' 設定
=============================================

:doc:`Pagination ライブラリ <../libraries/pagination>` は現在、'attributes' 設定を使うことで、アンカーにあらゆる HTML 属性を追加できます。
これにより、'class' 属性も設定できるため、別の 'anchor_class' 設定を使う意味は
もはやありません。
そこで 'anchor_class' 設定は廃止予定になり、CodeIgniter 3.1+ で
削除される予定です。

.. note:: この設定はまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

String ヘルパーの random_string() のタイプ 'unique' と 'encrypt'
==================================================================

:doc:`String ヘルパー <../helpers/string_helper>` の 
:php:func:`random_string()` 関数を使う際、 **unique** と **encrypt** をランダム化のタイプとして使うことはできなくなりました。
それらは、単に **md5** と **sha1** へのエイリアスです。
廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: これらのオプションはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

URL ヘルパーの url_title() のセパレータ 'dash' and 'underscore'
===============================================================

:doc:`URL ヘルパー <../helpers/url_helper>` の :php:func:`url_title()` 関数を
使う際、区切り文字として **dash** と **underscore** は使えません。
この関数は現在、どんな文字も受け取りますので、選択した文字を直接渡して下さい。
'dash' の代わりに '-' を、'underscore' の代わりに '_' を記述してください。

**dash** と **underscore** は、現在、エイリアスとして振る舞いますが、これらは廃止予定であり、
CodeIgniter 3.1+ で削除される予定です。

.. note:: これらのオプションはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Session ライブラリの all_userdata() メソッド
=============================================

:doc:`変更履歴 <../changelog>` で見たように、 :doc:`Session ライブラリ <../libraries/sessions>`
の ``userdata()`` メソッドは、パラメータなしの場合、すべてのユーザデータを返します::

	$this->session->userdata();

これにより、``all_userdata()`` メソッドは冗長となり、現在、上記の
``userdata()`` への単なるエイリアスです。
廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: このメソッドはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Database Forge の AFTER 句を伴う add_column() メソッド 
=======================================================

もし、:doc:`Database Forge <../database/forge>` の 
``add_column()`` メソッドの **第 3 引数** を、AFTER 句にフィールドを追加するために使っていたなら、使い方を変更する必要があります。

その第 3 引数は廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

代わりに AFTER 句のフィールド名は、フィールド定義の配列に記述します::

	// 以前の使い方:
	$field = array(
		'new_field' => array('type' => 'TEXT')
	);

	$this->dbforge->add_column('table_name', $field, 'another_field');

	// 新しい使い方:
	$field = array(
		'new_field' => array('type' => 'TEXT', 'after' => 'another_field')
	);

	$this->dbforge->add_column('table_name', $field);

.. note:: このパラメータはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

.. note:: これは MySQL と CUBRID データベースだけで使えます! 他のドライバはこの句をサポートしておらず、
	黙って無視します。

URI ルーティングのメソッド fetch_directory()、fetch_class()、fetch_method()
============================================================================

プロパティ ``CI_Router::$directory``、``CI_Router::$class`` そして ``CI_Router::$method``
は public であり、対応する ``fetch_*()`` はそれらのプロパティを返すだけです。
なので、これらのメソッドを持つ意味はありません。

これらはすべて内部的な文書化されていないメソッドですが、もしもの
後方互換性を維持するため、廃止予定とします。
もし、これらのメソッドを使っている人は、代わりにプロパティにアクセスしてください::

	$this->router->directory;
	$this->router->class;
	$this->router->method;

.. note:: これらのメソッドはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Input ライブラリの is_cli_request() メソッド
=============================================

``CI_Input::is_cli_request()`` メソッドの呼び出しは CodeIgniter の内部の
たくさんの場所で必要であり、しばしば、:doc:`Input Library
<../libraries/input>` がロードされる前に起こります。そのため、共通関数の
:php:func:`is_cli()` 関数に置き換えられ、このメソッドは現在単なるエイリアスです。

新しい関数はいつでも利用可能であり、タイプする文字も少なくて済みます。

::

	// 以前
	$this->input->is_cli_request();

	// 以後
	is_cli();

``CI_Input::is_cli_request()`` は現在廃止予定であり、
CodeIgniter 3.1+ で削除される予定です。

.. note:: このメソッドはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

Config ライブラリの system_url() メソッド
=========================================

``CI_Config::system_url()`` の使用は安全でないコーディングの実践を推奨してしまいます。
すなわち、CodeIgniter の *system/* ディレクトリは、セキュリティの観点から、
外部からアクセス可能であるべきではありません。

そのため、このメソッドは現在、廃止予定であり、
CodeIgniter 3.1+ で削除される予定です。

.. note:: このメソッドはまだ利用可能ですが、早急に使用を止めることを強く
	推奨します。

=========================
The Javascript ライブラリ
=========================

:doc:`Javascript ライブラリ <../libraries/javascript>` は今までずっと '実験' 
状態であり、そんなに役に立たず、また、適切な解決策でもありませんでした。

現在、廃止予定であり、CodeIgniter 3.1+ で削除される予定です。

.. note:: このライブラリはまだ使えますが、早急に使用を中止することを
	強く推奨します。

**************************************************************
Step 21: Text ヘルパーの highlight_phrase() の使い方を確認する
**************************************************************

:doc:`Text ヘルパー <../helpers/text_helper>` の
:func:`highlight_phrase()` 関数が使うデフォルトの HTML タグは
``<strong>`` から 新しい HTML5 の ``<mark>`` タグに変更されました。

独自のハイライトタグを使っていない限り、この変更は Internet Explorer 8 のような
より古い Web ブラウザを使っているユーザにトラブルを起こすかも知れません。
そこで、古いブラウザでの後方互換のために、以下のコードを CSS ファイルに
追加することを提案します::

	mark {
		background: #ff0;
		color: #000;
	};
