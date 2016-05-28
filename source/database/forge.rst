##########################
データベースフォージクラス
##########################

データベースフォージクラスは、データベースを管理するメソッドを
搭載しています。

.. contents:: 目次
   :depth: 3

**********************
フォージクラスの初期化
**********************

.. important:: フォージクラスはデータベースドライバを必要とするので、
	フォージクラスを初期化するには、あらかじめデータベースドライバが動いている必要があります。

フォージクラスは次のようにロードします::

	$this->load->dbforge()

デフォルト意外のデータベースを管理したい場合は、DBフォージのローダーに
別のデータベースオブジェクトを渡すこともできます::

	$this->myforge = $this->load->dbforge($this->other_db, TRUE);

上記の例では、我々はカスタムデータベースオブジェクトを第一引数に渡しており、
``$this->dbforge`` に割り当てる代わりにdbforgeオブジェクトを返すように命令
しています。

.. note:: 両方の引数は別々に使うことができます。第一引数をスキップするには
	空の値を渡してください。

いったん初期化されれば、フォージのメソッドは、 ``$this->dbforge``
オブジェクトを利用してアクセスできます::

	$this->dbforge->some_method();

************************
データベースの作成と削除
************************

**$this->dbforge->create_database('db_name')**

最初の引数で渡した名前でデータベースを作成します。戻り値は TRUE/FALSE
で成功したか失敗したかを示します::

	if ($this->dbforge->create_database('my_db'))
	{
		echo 'Database が作成されました!';
	}

**$this->dbforge->drop_database('db_name')**

最初の引数で示された名前のデータベースを削除します。戻り値は
TRUE/FALSE で成功か失敗を示します::

	if ($this->dbforge->drop_database('my_db'))
	{
		echo 'Database が削除されました!';
	}


********************
テーブルの作成と削除
********************

テーブルを作成するためには、やらなければならないことがいくつかあります
。フィールドを追加したり、キーをテーブルに追加したり、カラムを変更した
りです。CodeIgniterはこのようなことに関してのメカニズムを提供しています。

フィールドの追加
=================

フィールドは連想配列を通して作成されます。配列の中には、フィールドのデ
ータタイプを指定する 'type' キーが必要となります。たとえば、
INT、VARCHAR、TEXT などです。多くのデータタイプ (たとえば VARCHAR
など) は 'constraint' キーも必要です。

::

	$fields = array(
		'users' => array(
			'type' => 'VARCHAR',
			'constraint' => '100',
		),
	);
	// "users VARCHAR(100)" と解釈され、フィールドが追加されます。


さらに、以下のキー/値が使えます:

-  unsigned/true : フィールド定義として "UNSIGNED" を生成します。
-  default/値 : フィールド定義としてデフォルト値を生成します。
-  null/true : フィールド定義として "NULL"
   を生成します。この指定がないと、フィールドは "NOT NULL" となります。
-  auto_increment/true : フィールドのフラグとして、auto_increment を立
   てます。フィールドタイプは整数の様なタイプをサポートするものでないとい
   けません。
-  unique/true : to generate a unique key for the field definition.

::

	$fields = array(
		'blog_id' => array(
			'type' => 'INT',
			'constraint' => 5,
			'unsigned' => TRUE,
			'auto_increment' => TRUE
		),
		'blog_title' => array(
			'type' => 'VARCHAR',
			'constraint' => '100',
			'unique' => TRUE,
		),
		'blog_author' => array(
			'type' =>'VARCHAR',
			'constraint' => '100',
			'default' => 'King of Town',
		),
		'blog_description' => array(
			'type' => 'TEXT',
			'null' => TRUE,
		),
	);


フィールドが定義された後で、 ``$this->dbforge->add_field($fields);`` 
の後に呼ばれる ``create_table()`` メソッドでフィールドが追加
されます。

**$this->dbforge->add_field()**

add_fields メソッドは上のような配列を受け入れます。


フィールド定義を文字列として渡す
--------------------------------

フィールド作成をどのようにしたらよいか正確にわかっている場合、
add_field() メソッドを使って、フィールド定義を文字列として渡すこともできます。

::

	$this->dbforge->add_field("label varchar(100) NOT NULL DEFAULT 'default label'");


.. note:: 生の文字列をフィールドとして渡してからそれらのフィールドに ``add_key()`` を呼ぶことはできません。

.. note:: add_field() を複数回呼ぶと、複数回の処理が累積されていきます。

id フィールドの作成
-------------------

ひとつの特別な例外として、id フィールドの作成があります。type が id
のフィールドは、自動的に INT(9) の auto_increment な主キーに
なります。

::

	$this->dbforge->add_field('id');
	// これで id は INT(9) NOT NULL AUTO_INCREMENT になります。


キーの追加
==========

一般的にテーブルにはキーがあります。キーは 
$this->dbforge->add_key('field') で設定できます。オプションの2つ目の引数は、
TRUE を指定すると主キーになります。 add_key() の後に create_table()
を呼ぶ必要があることに注意してください。

複数のカラムの主ではないキーは配列で送る必要があります。以下は MySQL
用のサンプルです。

::

	$this->dbforge->add_key('blog_id', TRUE);
	// PRIMARY KEY は `blog_id`(`blog_id`) になります。

	$this->dbforge->add_key('blog_id', TRUE);
	$this->dbforge->add_key('site_id', TRUE);
	// PRIMARY KEY は `blog_id_site_id` (`blog_id`, `site_id`) になります。

	$this->dbforge->add_key('blog_name');
	// KEY は `blog_name` (`blog_name`) になります。

	$this->dbforge->add_key(array('blog_name', 'blog_label'));
	// KEY は `blog_name_blog_label` (`blog_name`, `blog_label`) になります。


テーブルの作成
==============

フィールドとキーが宣言された後、新しいテーブルを次に紹介するメソッドを
呼ぶことで作成することができます。

::

	$this->dbforge->create_table('table_name');
	// CREATE TABLE table_name になります


オプションの 2つ目の引数が TRUE のとき、"IF NOT EXISTS"
を定義に追加します。

::

	$this->dbforge->create_table('table_name', TRUE);
	// CREATE TABLE IF NOT EXISTS table_name になります

さらに、MySQLの ``ENGINE`` のような任意のテーブル属性を渡すこともできます::

	$attributes = array('ENGINE' => 'InnoDB');
	$this->dbforge->create_table('table_name', FALSE, $attributes);
	// produces: CREATE TABLE `table_name` (...) ENGINE = InnoDB DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci

.. note:: ``CHARACTER SET`` と ``COLLATE`` 属性の両方またはどちらか指定しない限り、
	``create_table()`` は設定済みの *char_set* と *dbcollat* 値で追加されます
	（MySQLのみ）。


テーブルの削除
==============

DROP TABLE sql を実行し、任意で IF EXISTS 文を追加します。

::

	// DROP TABLE table_name になります
	$this->dbforge->drop_table('table_name');

	// DROP TABLE IF EXISTS  table_name になります
	$this->dbforge->drop_table('table_name',TRUE);


テーブル名の変更
================

TABLE rename を実行します。

::

	$this->dbforge->rename_table('old_table_name', 'new_table_name'); 
	// ALTER TABLE old_table_name RENAME TO new_table_name になります


**************
テーブルの変更
**************

テーブルにカラムを追加
======================

**$this->dbforge->add_column()**

``add_column()`` メソッドは既存のテーブルを変更するときに使います。上記と
同じフィールド定義の配列を採り、いくつでもフィールドを追加することがで
きます。

::

	$fields = array(
		'preferences' => array('type' => 'TEXT')
	);
	$this->dbforge->add_column('table_name', $fields);
	// ALTER TABLE table_name ADD preferences TEXT となります

MySQL か CUBIRD をお使いの場合、 AFTER と FIRST 文を活用してカラム位置を
指定することができます。

例::

	// 新しいカラムを `another_field` カラムの後ろに配置:
	$fields = array(
		'preferences' => array('type' => 'TEXT', 'after' => 'another_field')
	);

	// 新しいカラムを `another_field` テーブル定義の先頭に配置:
	$fields = array(
		'preferences' => array('type' => 'TEXT', 'first' => TRUE)
	);


テーブルのカラムを削除
======================

**$this->dbforge->drop_column()**

テーブルのカラムを削除します。

::

	$this->dbforge->drop_column('table_name', 'column_to_drop');



テーブルのカラムを変更
======================

**$this->dbforge->modify_column()**

このメソッドの使用法は、``add_column()`` と同じです。ただし、新しいカラム
を追加するのではなく、既存のカラムを変更します。カラム名を変更するには
配列の "name" キーで指定します。

::

	$fields = array(
		'old_name' => array(
			'name' => 'new_name',
			'type' => 'TEXT',
		),
	);
	$this->dbforge->modify_column('table_name', $fields);
	// ALTER TABLE table_name CHANGE old_name new_name TEXT になります


******************
クラスリファレンス
******************

.. php:class:: CI_DB_forge

	.. php:method:: add_column($table[, $field = array()[, $_after = NULL]])

		:param	string	$table: カラムを追加するテーブル名
		:param	array	$field: カラム定義（複数可）
		:param	string	$_after: AFTER 文のためのカラム（非推奨）
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		テーブルにカラムを追加します。使い方は `テーブルにカラムを追加`_ を参照。

	.. php:method:: add_field($field)

		:param	array	$field: 追加するフィールド定義
		:returns:	CI_DB_forge インスタンス（メソッドチェイン用）
		:rtype:	CI_DB_forge

		テーブル作成用セットにフィールドを追加します。使い方は `フィールドの追加`_ を参照。

	.. php:method:: add_key($key[, $primary = FALSE])

		:param	array	$key: キーのフィールド名
		:param	bool	$primary: 主キーの場合はTRUEに設定
		:returns:	CI_DB_forge インスタンス（メソッドチェイン用）
		:rtype:	CI_DB_forge

		テーブル作成用セットにキーを追加します。使い方は `キーの追加`_ を参照。

	.. php:method:: create_database($db_name)

		:param	string	$db_name: 作成するデータベース名
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		新しいデータベースを作成します。使い方は `データベースの作成と削除`_ を参照。

	.. php:method:: create_table($table[, $if_not_exists = FALSE[, array $attributes = array()]])

		:param	string	$table: 作成するテーブル名
		:param	string	$if_not_exists: TRUE にすると 'IF NOT EXISTS' 文を追加
		:param	string	$attributes: テーブル属性の連想配列
		:returns:  成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		新しいテーブルを作成。使い方は `テーブルの作成`_ を参照。

	.. php:method:: drop_column($table, $column_name)

		:param	string	$table: テーブル名
		:param	array	$column_name: 削除するカラム名
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		テーブルのカラムを削除。使い方は `テーブルのカラムを削除`_ を参照。

	.. php:method:: drop_database($db_name)

		:param	string	$db_name: 削除するデータベース名
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		データベースを削除。使い方は `データベースの作成と削除`_ を参照。

	.. php:method:: drop_table($table_name[, $if_exists = FALSE])

		:param	string	$table: 削除するテーブル名
		:param	string	$if_exists: TRUE にすると 'IF EXISTS' 文を追加
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		テーブルを削除。使い方は `テーブルの削除`_ を参照。

	.. php:method:: modify_column($table, $field)

		:param	string	$table: テーブル名
		:param	array	$field: フィールド定義（複数可）
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		テーブルのカラムを変更。使い方は `テーブルのカラムを変更`_ を参照。

	.. php:method:: rename_table($table_name, $new_table_name)

		:param	string	$table: 現在のテーブル名
		:param	string	$new_table_name: 新しいテーブル名
		:returns:	成功時 TRUE、失敗時 FALSE
		:rtype:	bool

		テーブル名を変更。使い方は `テーブル名の変更`_ を参照。
