################################
データベースユーティリティクラス
################################

データベースユーティリティクラスは、データベースの管理に便利なメソッド
を持っています。

.. contents::
    :local:
    :depth: 2

******************************
ユーティリティクラスの初期化
******************************

.. important:: ユーティリティクラスは、データベースドライバに依存しているので、 
	初期化するには、あらかじめデータベースドライバが実行されている必要があります。

ユーティリティクラスは次のようにロードします::

	$this->load->dbutil();

デフォルト以外のDBユーティリティローダを使いたい場合、別のデータベースオブジェクト
を渡すこともできます::

	$this->myutil = $this->load->dbutil($this->other_db, TRUE);

上記の例では、第1引数にカスタムデータベースオブジェクトを渡して、
``$this->dbutil`` に直接アサインするかわりに dbutil オブジェクトを返すように
命令しています。

.. note:: 両方の引数は独立して使えるので、第1引数をスキップしたい場合は
	空の値を渡せば良いです。

いったん初期化されれば、ユーティリティのメソッドは、 ``$this->dbutil``
オブジェクトを利用してアクセスできます::

	$this->dbutil->some_method();

*********************************
データベースユーティリティを使う
*********************************

データベース名の配列を取得
================================

データベース名の配列を返します::

	$dbs = $this->dbutil->list_databases();

	foreach ($dbs as $db)
	{
 		echo $db;
	}


データベースが存在するか確認
==============================

特定のデータベースが存在するかどうかを確認するのが役立つことがあります。
ブール値の TRUE/FALSE を返します。使用例::

	if ($this->dbutil->database_exists('database_name'))
	{
		// some code...
	}

.. note:: *database_name* を探しているテーブル名に置き換えてください。
	このメソッドは大文字小文字を区別します。

テーブルの最適化
=================

第1引数に渡したテーブルを最適化してくれます。
成功か失敗かに応じて TRUE/FALSE を返します::

	if ($this->dbutil->optimize_table('table_name'))
	{
		echo 'Success!';
	}

.. note:: すべてのデータベースプラットフォームがテーブルの最適化を
	サポートしているわけではありません。ほとんどの場合は MySQL で使われます。

テーブルの修復
===============

第1引数に渡したテーブルを修復してくれます。
成功か失敗かに応じて TRUE/FALSE を返します::

	if ($this->dbutil->repair_table('table_name'))
	{
		echo 'Success!';
	}

.. note:: すべてのデータベースプラットフォームがテーブルの修復をサポートしているわけではありません。

データベースの最適化
=====================

DBクラスが現在接続しているデータベースを最適化してくれます。
DBステータスメッセージの配列または、失敗した場合に 
FALSE を返します。

::

	$result = $this->dbutil->optimize_database();

	if ($result !== FALSE)
	{
		print_r($result);
	}

.. note:: すべてのデータベースプラットフォームがデータベースの最適化を
	サポートしているわけではありません。ほとんどの場合は MySQL で使われます。

クエリ結果を CSV ファイルとしてエクスポート
============================================

クエリ結果から CSV ファイルを生成してくれます。
メソッドの第1引数にはクエリの結果オブジェクトを
含む必要があります。例::

	$this->load->dbutil();

	$query = $this->db->query("SELECT * FROM mytable");

	echo $this->dbutil->csv_from_result($query);

第2、第３、第4引数はそれぞれ区切り文字、改行文字、囲み文字
をセットするためのものです。デフォルトでは区切り文字はカンマ、
改行文字"\n"、囲み文字にはダブルクオートが使用されます。
例::

	$delimiter = ",";
	$newline = "\r\n";
	$enclosure = '"';

	echo $this->dbutil->csv_from_result($query, $delimiter, $newline, $enclosure);

.. important:: このメソッドは、CSV をファイルには書き出しません。
	単に、CSV に整形するだけです。ファイルに書き込む必要がある場合は、
	:doc:`ファイルヘルパー <../helpers/file_helper>` を利用します。

クエリ結果を XML ドキュメントとしてエクスポート
=================================================

クエリの結果からXMLを生成します。第1引数には、結果オブジェクトが入り、
第2引数には、オプションで設定パラメータの配列が入ります。
例::

	$this->load->dbutil();

	$query = $this->db->query("SELECT * FROM mytable");

	$config = array (
		'root'		=> 'root',
		'element'	=> 'element',
		'newline'	=> "\n",
		'tab'		=> "\t"
	);

	echo $this->dbutil->xml_from_result($query, $config);

.. important:: このメソッドは、XML をファイルには書き出しません。
	単に、XML に整形するだけです。ファイルに書き込む必要がある場合は、
	:doc:`ファイルヘルパー <../helpers/file_helper>` を利用します。

***************************
データベースのバックアップ
***************************

データベースバックアップの注意事項
=====================================

データベース全体もしくは個別のテーブルをバックアップできます。バックア
ップデータは Zip または Gzip フォーマットのどちらかの形式で圧縮できます。

.. note:: このメソッドは MySQL と Interbase/Firebird データベースでのみ使用できます。

.. note:: Interbase/Firebird データベースをお使いの場合は、バックアップファイル名が唯一の引数です。

		$this->dbutil->backup('db_backup_filename');

.. note:: 実行制限時間と PHPが利用できる空きメモリ容量に注意してください。 
	大規模なデータベースのバックアップはできないかもしれません。
	データベースが大規模な場合は、サーバで直接 SQL を使ってバックアップ
	する必要があるかしれません。ルート権限を持たない場合には
	サーバ管理者がそれを行う必要があります。

使用例
=============

::

	// DB ユーティリティクラスをロード
	$this->load->dbutil();

	// データベース全体をバックアップしその結果を変数に代入
	$backup = $this->dbutil->backup();

	// ファイルヘルパーをロードし、サーバにファイルを書き出す
	$this->load->helper('file');
	write_file('/path/to/mybackup.gz', $backup);

	// ダウンロードヘルパーをロードし、ファイルをデスクトップに送信する
	$this->load->helper('download');
	force_download('mybackup.gz', $backup);

バックアップオプションの設定
===============================

バックアップのオプションは、 ``backup()`` メソッドの第1引数で配列で渡す
ことができます。例::

	$prefs = array(
		'tables'	=> array('table1', 'table2'),	// バックアップするテーブルの配列。
		'ignore'	=> array(),			// バックアップしないテーブルのリスト。
		'format'	=> 'txt',			// gzip, zip, txt
		'filename'	=> 'mybackup.sql',		// ファイル名 - ZIP ファイルのときだけ必要
		'add_drop'	=> TRUE,			// バックアップファイルにDROP TABLE 文を追加するかどうか
		'add_insert'	=> TRUE,			// バックアップファイルにINSERT 文を追加するかどうか
		'newline'	=> "\n"				// バックアップファイルで使う改行文字
	);

	$this->dbutil->backup($prefs);

バックアップオプションの説明
=================================

======================= ======================= ======================= ========================================================================
設定項目                  初期値                   選択肢                 説明
======================= ======================= ======================= ========================================================================
**tables**               空の配列                なし                    バックアップしたいテーブルの配列。空のままにしておけば、全テーブルが
                                                                         エクスポートされます。
**ignore**               空の配列                なし                    バックアップ処理の対象外にするテーブルの配列
**format**               gzip                    gzip, zip, txt          エクスポートファイルのファイル形式。
**filename**             現在日時                なし                    バックアップファイルの名前。名前は、ZIP圧縮を使う場合のみ必要
                                                                         になります。
**add_drop**             TRUE                    TRUE/FALSE              SQL エクスポートファイルに、DROP TABLE 文を追加するかどうか。
**add_insert**           TRUE                    TRUE/FALSE              SQL エクスポートファイルに、INSERT 文を追加するかどうか。
**newline**              "\\n"                   "\\n", "\\r", "\\r\\n"  SQL エクスポートファイルで使う改行文字の種類。
**foreign_key_checks**   TRUE                    TRUE/FALSE              出力が外部キーチェックを有効にするかどうか。
======================= ======================= ======================= ========================================================================

*********************
クラスリファレンス
*********************

.. php:class:: CI_DB_utility

	.. php:method:: backup([$params = array()])

		:param	array	$params: オプションの連想配列
		:returns:	生の/(g)zip された SQL クエリ文字列
		:rtype:	string

		ユーザーオプションに基づいてデータベースバックアップを実行。

	.. php:method:: database_exists($database_name)

		:param	string	$database_name: データベース名
		:returns:	存在する場合 TRUE , それ以外は FALSE
		:rtype:	bool

		データベースの存在を確認。

	.. php:method:: list_databases()

		:returns:	見つかったデータベース名の配列
		:rtype:	array

		すべてのデータベース名の配列を取得。

	.. php:method:: optimize_database()

		:returns:	最適化メッセージの配列か、失敗時は FALSE
		:rtype:	array

		データベースを最適化。

	.. php:method:: optimize_table($table_name)

		:param	string	$table_name:	最適化するテーブル名
		:returns:	最適化メッセージの配列か、失敗時は FALSE
		:rtype:	array

		データベース内のテーブルを最適化。

	.. php:method:: repair_table($table_name)

		:param	string	$table_name:	修復したいテーブル名
		:returns:	修復メッセージの配列か、失敗時は FALSE
		:rtype:	array

		データベース内のテーブルを修復。

	.. php:method:: csv_from_result($query[, $delim = ','[, $newline = "\n"[, $enclosure = '"']]])

		:param	object	$query:	データベース結果オブジェクト
		:param	string	$delim: CSV フィールド区切り文字
		:param	string	$newline: 改行文字
		:param	string	$enclosure: 囲み文字
		:returns:	生成された CSV ドキュメントの文字列
		:rtype:	string

		データベース結果オブジェクトを CSV ドキュメントに変換。

	.. php:method:: xml_from_result($query[, $params = array()])

		:param	object	$query: データベース結果オブジェクト
		:param	array	$params: オプションの連想配列
		:returns:	生成された XML ドキュメントの文字列
		:rtype:	string

		データベース結果オブジェクトを XML ドキュメントに変換。
