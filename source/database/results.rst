########################
クエリ結果の生成
########################

クエリ結果を生成する方法はいくつかあります:

.. contents::
    :local:
    :depth: 2

*************
結果配列
*************

**result()**

このメソッドは、結果を **オブジェクト** の配列として、
または失敗した場合には **空の配列** を返します。
典型的には、次のように、これをforeach ループで使用します::

	$query = $this->db->query("YOUR QUERY");

	foreach ($query->result() as $row)
	{
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

上の メソッド は ``result_object()`` の別名です。

``result()`` には各結果オブジェクトをインスタンス化したいクラス名の
文字列を渡すこともできます（note: クラスはロードされていなければいけません）。

::

	$query = $this->db->query("SELECT * FROM users;");

	foreach ($query->result('User') as $user)
	{
		echo $user->name; // 属性を呼び出す
		echo $user->reverse_name(); // または 'User' クラスに定義されているメソッド
	}

**result_array()**

このメソッドは、結果を純粋な配列として、あるいは結果が生成されなかった
ときには空の配列を返します。典型的には、次のように、foreach
ループで使用されます::

	$query = $this->db->query("YOUR QUERY");

	foreach ($query->result_array() as $row)
	{
		echo $row['title'];
		echo $row['name'];
		echo $row['body'];
	}

***********
結果行
***********

**row()**

このメソッドは単一行を返します。クエリの応答が一つ以上の行になる場合は
、最初の行だけが返ります。 結果は **オブジェクト** で返ります。
使用方法の例です::

	$query = $this->db->query("YOUR QUERY");

	$row = $query->row();

	if (isset($row))
	{
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

特定の行を返したいときは、第１引数に、行番号を数値として渡すことができ
ます::

	$row = $query->row(5);

第2引数に文字列を渡すこともでき、その列をインスタンス化するためのクラ
ス名を指定します::

	$query = $this->db->query("SELECT * FROM users LIMIT 1;");
	$row = $query->row(0, 'User');
	
	echo $row->name; // 属性を呼び出す
	echo $row->reverse_name(); // または 'User' クラスに定義されているメソッド

**row_array()**

配列を返すこと以外は、上の ``row()`` メソッドと同じです。
例::

	$query = $this->db->query("YOUR QUERY");

	$row = $query->row_array();

	if (isset($row))
	{
		echo $row['title'];
		echo $row['name'];
		echo $row['body'];
	}

特定の行を返したいときは、第１引数に、行番号を数値として渡すことができ
ます::

	$row = $query->row_array(5);

さらに、次のようなバリエーションで、結果を
進む/もどる/最初に移動/最後に移動 してデータを見ていくことができます:

	| **$row = $query->first_row()**
	| **$row = $query->last_row()**
	| **$row = $query->next_row()**
	| **$row = $query->previous_row()**

引数に"array"と指定しなければ、デフォルトでは、これらのメソッドはオブ
ジェクトを返します:

	| **$row = $query->first_row('array')**
	| **$row = $query->last_row('array')**
	| **$row = $query->next_row('array')**
	| **$row = $query->previous_row('array')**

.. note:: All the methods above will load the whole result into memory
	(prefetching). Use ``unbuffered_row()`` for processing large
	result sets.

**unbuffered_row()**

このメソッドは、``row()`` のように結果をすべてメモリにプリフェッチせずに
単一行を返します。もしクエリに1行以上ある場合は、現在の行を返し、
内部データポインタを移動します。

::

	$query = $this->db->query("YOUR QUERY");

	while ($row = $query->unbuffered_row())
	{	
		echo $row->title;
		echo $row->name;
		echo $row->body;
	}

オプションで 'object'（デフォルト）か 'array' を渡し、返り値の
型を指定することができます::

	$query->unbuffered_row();		// object
	$query->unbuffered_row('object');	// object
	$query->unbuffered_row('array');	// associative array

************************
カスタム結果オブジェクト
************************

結果は配列か ``stdClass`` のインスタンスの代わりにカスタムクラスの
インスタンスで受け取ることができます。これは ``result()`` と ``result_array()`` 
メソッドで許可されています。クラスはすでにメモリにロードされていなければいけません。
オブジェクトはデータベースから全ての値をプロパティとして返されます。
もし非 public のプロパティとして宣言されている場合は、 ``__set()`` メソッド
を提供しセットされるようにしてください。

例::

	class User {

		public $id;
		public $email;
		public $username;

		protected $last_login;

		public function last_login($format)
		{
			return $this->last_login->format($format);
		}

		public function __set($name, $value)
		{
			if ($name === 'last_login')
			{
				$this->last_login = DateTime::createFromFormat('U', $value);
			}
		}

		public function __get($name)
		{
			if (isset($this->$name))
			{
				return $this->$name;
			}
		}
	}

下記2つのメソッド以外にも、次のメソッドも結果を返すクラス名を指定する
ことができます: ``first_row()`` , ``last_row()`` , ``next_row()`` 
と ``previous_row()`` 。

**custom_result_object()**

結果セットすべてを指定されたクラスのインスタンスの配列として返します。
唯一のパラメータはインスタンス化したいクラス名です。

例::

	$query = $this->db->query("YOUR QUERY");

	$rows = $query->custom_result_object('User');

	foreach ($rows as $row)
	{
		echo $row->id;
		echo $row->email;
		echo $row->last_login('Y-m-d');
	}

**custom_row_object()**

クエリ結果から1行返します。第1引数は結果の行数です。第2引数はインスタンス化
したいクラス名です。

例::

	$query = $this->db->query("YOUR QUERY");

	$row = $query->custom_row_object(0, 'User');

	if (isset($row))
	{
		echo $row->email;   // access attributes
		echo $row->last_login('Y-m-d');   // access class methods
	}

さらに、 ``row()`` メソッドもまったく同じ方法で使うことができます。

例::

	$row = $query->custom_row_object(0, 'User');

*********************
結果ヘルパーメソッド
*********************

**num_rows()**

クエリで返された行数を取得します。Note: 下記の例では $query
は、クエリの結果オブジェクトを代入した変数です::

	$query = $this->db->query('SELECT * FROM my_table');

	echo $query->num_rows();

.. note:: すべてのデータベースドライバが結果セットの全件数を取得する
	ネイティブの方法を持っているわけではありません。その場合、全データは
	プリフェッチされ結果配列に対して ``count()`` が手動で呼ばれることで
	同じ結果を導いています。
	
**num_fields()**

問い合わせ結果のフィールド数（列数）を返します。このメソッドは、クエリ
結果オブジェクトを使っていることを確かめてから呼び出してください::

	$query = $this->db->query('SELECT * FROM my_table');

	echo $query->num_fields();

**free_result()**

結果に関連付けられたメモリを解放し、結果のリソースIDを削除します。ふつ
うは、PHP は、スクリプトの実行を終えると、メモリを自動で解放します。
しかし、特定のスクリプトで多くのクエリを実行しているとき、 メモリの使
用量を削減するために、各クエリの結果が生成されたあとにメモリを開放した
い場合があります。

例::

	$query = $this->db->query('SELECT title FROM my_table');

	foreach ($query->result() as $row)
	{
		echo $row->title;
	}

	$query->free_result();  // The $query result object will no longer be available

	$query2 = $this->db->query('SELECT name FROM some_table');

	$row = $query2->row();
	echo $row->name;
	$query2->free_result(); // The $query2 result object will no longer be available

**data_seek()**

このメソッドは次の結果行の内部ポインタをフェッチするようにします。
``unbuffered_row()`` との組み合わせてのみ有効です。

メソッドは正の整数を受け付け（初期値は 0 ）、成功時は TRUE 、
失敗時は FALSE を返します。 

::

	$query = $this->db->query('SELECT `field_name` FROM `table_name`');
	$query->data_seek(5); // Skip the first 5 rows
	$row = $query->unbuffered_row();

.. note:: すべてのデータベースドライバがこの機能をサポートしておらず、その場合
	FALSE を返します。特筆すべきは PDO で使えないという点です。

*******************
クラスリファレンス
*******************

.. php:class:: CI_DB_result

	.. php:method:: result([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	フェッチされた行を含んだ配列
		:rtype:	array

		``result_array()``, ``result_object()``, と 
		``custom_result_object()`` のラッパーメソッド。

		使い方: `結果配列`_ を参照。

	.. php:method:: result_array()

		:returns:	フェッチされた行を含んだ配列
		:rtype:	array

		クエリ結果を配列として返し、各行は連想配列として
		返します。

		使い方: `結果配列`_ を参照。

	.. php:method:: result_object()

		:returns:	フェッチされた行を含んだ配列
		:rtype:	array

		クエリ結果を配列として返し、各行は ``stdClass()`` 
		型のオブジェクトとして返します。

		使い方: `結果配列`_ を参照。

	.. php:method:: custom_result_object($class_name)

		:param	string	$class_name: 結果行のクラス名
		:returns:	フェッチされた行を含んだ配列
		:rtype:	array

		クエリ結果を配列として返し、各行は指定されたクラスの
		インスタンスとして返します。

	.. php:method:: row([$n = 0[, $type = 'object']])

		:param	int	$n: 返ってくるクエリ結果行の添字
		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	要求された行、あるいは存在しなければ NULL
		:rtype:	mixed

		``row_array()``, ``row_object()``, と
		``custom_row_object()`` ラッパーメソッド。

		使い方: `結果行`_ を参照。

	.. php:method:: unbuffered_row([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	要求された行、あるいは存在しなければ NULL
		:rtype:	mixed

		次の結果行をフェッチし、要求された形式で
		返します。

		使い方: `結果行`_ を参照。

	.. php:method:: row_array([$n = 0])

		:param	int	$n: 返ってくるクエリ結果行の添字
		:returns:	要求された行、あるいは存在しなければ NULL
		:rtype:	array

		要求された結果行を連想配列として返します。

		使い方: `結果行`_ を参照。

	.. php:method:: row_object([$n = 0])

		:param	int	$n: 返ってくるクエリ結果行の添字
		        :returns:	要求された行、あるいは存在しなければ NULL
		:rtype:	stdClass

		要求された結果行を ``stdClass`` 型のオブジェクト
		として返します。

		使い方: `結果行`_ を参照。

	.. php:method:: custom_row_object($n, $type)

		:param	int	$n: 返ってくるクエリ結果行の添字
		:param	string	$class_name: 結果行のクラス名
		:returns:	要求された行、あるいは存在しなければ NULL
		:rtype:	$type

		要求された結果行を、指定のクラスのインスタンス
		として返します。

	.. php:method:: data_seek([$n = 0])

		:param	int	$n: 次に返ってくるクエリ結果行の添字
		:returns:	成功時は TRUE, 失敗時は FALSE
		:rtype:	bool

		内部ポインタを希望のオフセットまで移動します。

		使い方: `結果ヘルパーメソッド`_ を参照。

	.. php:method:: set_row($key[, $value = NULL])

		:param	mixed	$key: カラム名かキー/値ペアの配列
		:param	mixed	$value: $key が単一フィールド名の場合、カラムにアサインする値
		:rtype:	void

		特定のカラムに値をアサイン。

	.. php:method:: next_row([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	結果セットの次の行、あるいは存在しなければ NULL
		:rtype:	mixed

		結果セットの次の行を返します。

	.. php:method:: previous_row([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	結果セットのひとつ前の行、あるいは存在しなければ NULL
		:rtype:	mixed

		結果セットのひとつ前の行を返します。

	.. php:method:: first_row([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	結果セットの最初の行、あるいは存在しなければ NULL
		:rtype:	mixed

		結果セットの最初の行を返します。

	.. php:method:: last_row([$type = 'object'])

		:param	string	$type: 要求された結果の型 - array, object, あるいはクラス名
		:returns:	結果セットの最後の行、あるいは存在しなければ NULL
		:rtype:	mixed

		結果セットの最後の行を返します。

	.. php:method:: num_rows()

		:returns:	結果セットに入っている行数。
		:rtype:	int

		結果セットに入っている行数を返します。

		使い方: `結果ヘルパーメソッド`_ を参照。

	.. php:method:: num_fields()

		:returns:	結果セットに入っているフィールドの数
		:rtype:	int

		結果セットに入っているフィールドの数を返します。

		使い方: `結果ヘルパーメソッド`_ を参照。

	.. php:method:: field_data()

		:returns:	フィールドのメタデータを含んだ配列
		:rtype:	array

		フィールドのメタデータを含んだ ``stdClass`` の配列を
		生成します。

	.. php:method:: free_result()

		:rtype:	void

		結果セットを解放します。

		使い方: `結果ヘルパーメソッド`_ を参照。

	.. php:method:: list_fields()

		:returns:	カラム名の配列
		:rtype:	array

		結果セットに含まれたフィールド名の配列を
		返します。
