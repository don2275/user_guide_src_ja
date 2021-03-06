###################################
3.1.x から 3.2.x へのアップグレード
###################################

アップグレードを行う前に、
index.phpファイルを静的ページに置き換えて、オフラインにする必要があります。

Step 1: CodeIgniter ファイルのアップグレード
============================================

*system/* ディレクトリのすべてのファイルとディレクトリを新しいものに置き換えてください。

.. note:: ユーザバージョンのファイルがディレクトリ内にある場合には、
	最初にそれらのコピーを取っておいてください。

Step 2: データベース接続方法の変更
==================================

*config/autoload.php* 設定や、手動で ``$this->load->database()`` を呼び出すことや
あまり知られていない ``DB()`` 関数を使うかどうかに関わらず
データベースの "ローディング" に失敗すると、現在は ``RuntimeException`` を
スローします。

また、指定された文字コードを設定することができない場合も、
現在は接続の失敗とみなされます。

.. note:: これは今までもほとんどのデータベースドライバ
	（例えば 'mysql' 、'mysqli' 、'postgre' 以外）で
	そうでした。

これが意味していることは、もしデータベースに接続できない場合や
誤った文字コードが設定されている場合、CodeIgniter は黙ってエラーにせずに、
代わりに例外をスローします。

明示的に例外を捕捉することを選択できます。（その用途では :doc:`データベースクラス <../database/index>`
をロードするのに *config/autoload.php* を使うことはできません。）
::

	try
	{
		$this->load->database();
	}
	catch (RuntimeException $e)
	{
		// Handle the failure
	}

または、CodeIgniter のデフォルトの例外ハンドラに残すことができます。
もし development モードで実行していれば、エラーメッセージがログに出力され
エラー画面が表示されます。

db_set_charset() の呼び出しを削除
---------------------------------

上述の変更により、 ``db_set_charset()`` メソッドの目的は、
現在は実行時に接続時の文字コードを変更することだけです。
それは意味がありません。ほとんどのデータベースドライバーが
それをサポートしていないというのが理由です。
従って、 ``db_set_charset()`` はもはや必要でなく、削除されました。

Step 3: CLI リクエストの URI の解析に関連したロジックを確認
===========================================================

CLI から CodeIgniter のアプリケーションを実行する時、
:doc:`URI クラス <../libraries/uri>` は、現在
``$config['url_suffix']`` と ``$config['permitted_uri_chars']`` の設定は
無視されます。

これらの2つのオプションは、コマンドラインでは意味がありません。
（この変更が行われた理由です。）そのため、このことによって影響を受けることは
ありません。しかし、何がしかの理由でそれらを使用してきた場合、コードに
いくつかの変更を加える必要があります。

Step 4: Redis、Memcache(d) キャッシュライブラリの設定ファイルを確認
===================================================================

:doc:`キャッシングドライバ <../libraries/caching>` の
'redis' 、'memcached' ドライバのための新しい改善は、
設定値への小さな修正が必要となる場合があります...

Redis
-----


UNIX ソケット接続を使って 'redis' ドライバを利用してる場合、
ソケットのパスを ``$config['socket']`` から ``$config['host']`` へ
移行する必要があります。

``$config['socket_type']`` オプションもまた削除されました。しかしながら
それはアプリケーションへ影響しません。 - それは無視されます。
そして接続タイプは ``$config['host']`` のフォーマットによって決定されます。

Memcache(d)
-----------

'memcached' は、現在、``host`` 値を指定しない設定を無視します。
（以前は、ホストにデフォルトの '127.0.0.1' を設定していました）

従って、例えば ``port`` のみ設定を追加していた場合、
現在はその上、明示的に ``host`` に '127.0.0.1' を設定する必要があります。

Step 5: doctype() HTML ヘルパーの使用を確認
===========================================

:doc:`HTML ヘルパー <../helpers/html_helper>` の 関数である
:php:func:`doctype()` は ドキュメントタイプが指定されていない場合
'xhtml1-strict' (XHTML 1.0 Strict) をデフォルトで使用していました。デフォルト値は現在、
言うまでもなくモダンな HTML 5 スタンダードを表す 'html5' に変更されました。

どんなものもこの変更によって壊れるべきではありません。もし、アプリケーションが
デフォルト値に依存していた場合、それをダブルチェックし
望むべきフォーマットをセットするか、HTML 5 のフォーマットを使用するように
フロントエンドを適用させるべきです。