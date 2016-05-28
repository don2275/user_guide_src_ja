################
データベース設定
################

CodeIgniter には、データベース接続用項目(ユーザ名、パスワード、デ
ータベース名など)を保存できる設定ファイルがあります。 設定ファイルは
application/config/database.php にあります。また、特定の :doc:`環境
<../libraries/config>` のためのデータベース接続用項目を設定をするため
に、それぞれの環境の設定フォルダの中に **database.php**
を置くこともできます。

設定は、次のひな形で、多次元配列で保存されて
います::

	$db['default'] = array(
		'dsn'	=> '',
		'hostname' => 'localhost',
		'username' => 'root',
		'password' => '',
		'database' => 'database_name',
		'dbdriver' => 'mysqli',
		'dbprefix' => '',
		'pconnect' => TRUE,
		'db_debug' => TRUE,
		'cache_on' => FALSE,
		'cachedir' => '',
		'char_set' => 'utf8',
		'dbcollat' => 'utf8_general_ci',
		'swap_pre' => '',
		'encrypt' => FALSE,
		'compress' => FALSE,
		'stricton' => FALSE,
		'failover' => array()
	);

いくつかのデータベースドライバ（PDO,PostgreSQL, Oracle, ODBCなど）は
完全なDSN文字列を要求することもあります。その場合は 'dsn' 設定を、ドライバの
ネイティブPHP拡張を使うかのように使用してください。
下記が例になります::

	// PDO
	$db['default']['dsn'] = 'pgsql:host=localhost;port=5432;dbname=database_name';

	// Oracle
	$db['default']['dsn'] = '//localhost/XE';

.. note:: DSN文字列を要求するドライバに対してそれを指定しない場合、CodeIgniterは
	その他の設定を元にビルドを試みます。

.. note:: もし指定したDSN文字列に必要設定が抜けており（データベース文字セットなど）、
	他の設定項目に存在している場合、
	CodeIgniterは自動でそれらを追記します。

加えて、何らかの理由によりメインの接続先に接続できない場合のフェイルオーバー先を指定することも
できます。これらのフェイルオーバー先は、接続先のフェイルオーバー設定をこのように設定できます::

	$db['default']['failover'] = array(
			array(
				'hostname' => 'localhost1',
				'username' => '',
				'password' => '',
				'database' => '',
				'dbdriver' => 'mysqli',
				'dbprefix' => '',
				'pconnect' => TRUE,
				'db_debug' => TRUE,
				'cache_on' => FALSE,
				'cachedir' => '',
				'char_set' => 'utf8',
				'dbcollat' => 'utf8_general_ci',
				'swap_pre' => '',
				'encrypt' => FALSE,
				'compress' => FALSE,
				'stricton' => FALSE
			),
			array(
				'hostname' => 'localhost2',
				'username' => '',
				'password' => '',
				'database' => '',
				'dbdriver' => 'mysqli',
				'dbprefix' => '',
				'pconnect' => TRUE,
				'db_debug' => TRUE,
				'cache_on' => FALSE,
				'cachedir' => '',
				'char_set' => 'utf8',
				'dbcollat' => 'utf8_general_ci',
				'swap_pre' => '',
				'encrypt' => FALSE,
				'compress' => FALSE,
				'stricton' => FALSE
			)
		);

フェイルオーバー先はいくつでも指定できます。

単純な１次元配列を使わずに、多次元配列を使う理由は、オプションで、接続
用のデータを複数セット利用できるようにするためです。 たとえば、ひとつ
のインストール方法で、複数の環境(開発用、製品用、テスト用など)で実行す
るような場合に、 各環境ごとに接続設定をグループ化しておき、動かす環境
に必要なグループに切り替えるといったことが可能です。 たとえば、"test"
用の環境をセットアップするには次のようにします::

	$db['test'] = array(
		'dsn'	=> '',
		'hostname' => 'localhost',
		'username' => 'root',
		'password' => '',
		'database' => 'database_name',
		'dbdriver' => 'mysqli',
		'dbprefix' => '',
		'pconnect' => TRUE,
		'db_debug' => TRUE,
		'cache_on' => FALSE,
		'cachedir' => '',
		'char_set' => 'utf8',
		'dbcollat' => 'utf8_general_ci',
		'swap_pre' => '',
		'compress' => FALSE,
		'encrypt' => FALSE,
		'stricton' => FALSE,
		'failover' => array()
	);

次に、システムに対して全体でそのグループを使うことを通知するため、設定
ファイルにある下記の変数にグループ名をセットします::

	$active_group = 'test';

.. note:: 名前の "test"は任意です。好きなように設定できます。初期状態では、"default" 
	を優先（プライマリ）接続として使っていますが、この名前もプロジェクトにふさわし
	い名前にリネームすることができます。

Query Builder
--------------

:doc:`Query Builder クラス <query_builder>`
はデータベース設定ファイルの $query_builder 変数に TRUE/FALSE(ブール値
)をセットすることによりグローバルに有効または無効にできます。
query builder クラスを使用しない場合は、
FALSEをセットするとデータベースクラスを初期化したときに、
少ないリソースを有効活用できます。

::

	$query_builder = TRUE;

.. note:: Sessionなど、いくつかのCodeIgniterのクラスは特定の機能に
	アクセスするために Query Builder を有効にする必要があります。

設定データの説明:
----------------------

======================  ===========================================================================================================
 設定名                  説明
======================  ===========================================================================================================
**dsn**			DSN接続文字列（包括的な設定シーケンス）。
**hostname** 		データベースサーバのホスト名。"localhost"になることがよくあります。
**username**		データベースに接続するために使用するユーザ名。
**password**		データベースに接続するために使用するパスワード。
**database**		接続したいデータベース名。
**dbdriver**		データベースの種類。例: mysql、postgres、odbc など。小文字で指定しなければなりません。
**dbprefix**		オプションのテーブル名プリフィックス(接頭辞)。これは、 :doc:`Query Builder <query_builder>` 
			を使った問い合わせを実行するときに、テーブル名に付加される接頭辞になります。これを指定すると、
			単一のデータベースを共有して CodeIgniter を複数インストールすることが可能になります。

**pconnect**		TRUE/FALSE (boolean) - 永続的な接続を使うかどうか。
**db_debug**		TRUE/FALSE (boolean) - データベースエラーを表示するかどうか。
**cache_on**		TRUE/FALSE (boolean) - データベースクエリのキャッシュを有効にするかどうか。
			:doc:`データベースキャッシュクラス <caching>` を参照してください。
**cachedir**		データベースクエリのキャッシュディレクトリへの絶対サーバパス。
**char_set**		データベースとの通信に使用される文字セット。
**dbcollat**		データベースとの通信に使用される照合順序。

			.. note:: 'mysql' および 'mysqli' ドライバのみで使用。

**swap_pre**		デフォルトのテーブル接頭辞が dbprefix と置き換えられます。
			これは、配布されたアプリケーションに手動で記述されたクエリが含まれる場合でも、
			接頭辞をエンドユーザにカスタマイズ可能にする必要がある場合に役に立ちます。
**schema**		データベースのスキーマで、デフォルトは 'public' 。PostgreSQL および ODBC ドライバで使用。
**encrypt**		暗号化された接続を使用するかどうか。
			  - 'mysql' (非推奨)、 'sqlsrv' および 'pdo/sqlsrv' ドライバは TRUE/FALSE を受け付けます。
			  - 'mysqli' および 'pdo/mysql' ドライバは次のオプションを含んだ配列を受け付けます:
			    - 'ssl_key'    - 秘密鍵ファイルへのパス
			    - 'ssl_cert'   - 公開鍵証明書ファイルへのパス
			    - 'ssl_ca'     - 認証局ファイルへのパス
			    - 'ssl_capath' - PEM形式のCA証明書を含んだディレクトリへのパス
			    - 'ssl_cipher' - コロン(:)区切りの、 *許可された* 暗号化用cipherのリスト
			    - 'ssl_verify' - TRUE/FALSE; サーバー証明書の正当性を確認するかどうか（'mysqli' のみ）

**compress**		クライアント圧縮を使用するかどうか（MySQLのみ）。
**stricton**		TRUE/FASLE (boolean) - "Strict Mode" での接続を強制するかどうか、アプリケーションの開発中に SQL
			の厳密性を確保するのに良い方法です。
**port**		データベースのポート番号。この値を利用するには、データベース
 			設定の配列に以下のような行を追加する必要があります。
			::

				$db['default']['port'] =  5432;

======================  ===========================================================================================================

.. note:: 使用しているデータベースプラットフォーム (MySQL、Postgres など) によっては、
	すべての設定データが必要になるとは限りません。たとえば、SQLiteを使うときは、
	ユーザ名やパスワードは不要で、 データベース名はデータベースファイルへのパスになります。
	上の情報は、MySQLを利用していると仮定しています。
