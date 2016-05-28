########################
データベースのメタデータ
########################

********************
テーブルのメタデータ
********************

これらのメソッドを使ってテーブル情報をフェッチできます。

データベース内のテーブルをリストする
====================================

**$this->db->list_tables();**

現在接続しているデータベース内のすべてのテーブル名の
配列を返します。例::

	$tables = $this->db->list_tables();
	
	foreach ($tables as $table)
	{
		echo $table;
	}


テーブルが存在するかを確認する
==============================

**$this->db->table_exists();**

テーブルに対して操作をする前に、テーブルが存在するかを確認した方が役立つ
ことがあります。 TRUE/FALSE boolean を返します。使用例::

	if ($this->db->table_exists('table_name'))
	{
		// some code...
	}

.. note:: *table_name* は探したいテーブル名の名前に替えてください。

**********************
フィールドのメタデータ
**********************


テーブル内のフィールドをリストする
==================================

**$this->db->list_fields()**

フィールド名を含んだ配列を返します。このクエリは２つの方法で
呼び出すことができます:

1. $this->db-> オブジェクトにテーブル名を渡して呼び出す
ことができます::

	$fields = $this->db->list_fields('table_name');
	
	foreach ($fields as $field)
	{
		echo $field;
	}

2. クエリ結果オブジェクトから呼び出すことで、
クエリに関連付けられたフィールド名を集めることができます::

	$query = $this->db->query('SELECT * FROM some_table');
	
	foreach ($query->list_fields() as $field)
	{
		echo $field;
	}


テーブル内にフィールドが存在するか確認する
==========================================

**$this->db->field_exists()**

何かの操作をする前に、フィールドが存在するか確認した方が役立つ
ことがあります。 TRUE/FALSE boolean を返します。使用例::

	if ($this->db->field_exists('field_name', 'table_name'))
	{
		// some code...
	}

.. note:: *field_name* は探したいフィールド名に、
	*table_name* は探したいテーブル名に替えて
	ください。


フィールドのメタデータを取得
============================

**$this->db->field_data()**

フィールド情報を含んだオブジェクトの配列を返します。

フィールド名やカラム型や最大長などのメタデータを集めるのが
役立つことがあります。

.. note:: メタデータを提供していないデータベースもあります。

使用例::

	$fields = $this->db->field_data('table_name');
	
	foreach ($fields as $field)
	{
		echo $field->name;
		echo $field->type;
		echo $field->max_length;
		echo $field->primary_key;
	}

すでにクエリを実行させた場合は、テーブル名のかわりにクエリの
結果オブジェクトを使うことができます::

	$query = $this->db->query("YOUR QUERY");
	$fields = $query->field_data();

次のデータがメソッドから得ることができます（データベースで
サポートされている場合のみ）:

-  name - カラム名
-  max_length - カラムの最大長
-  primary_key - カラムが主キーの場合は1
-  type - カラムの型
