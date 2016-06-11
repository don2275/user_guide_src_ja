#####################
クエリビルダクラス
#####################

CodeIgniter は クエリビルダ データベースパターンの改変版を採用して
います。このパターンを利用すると、情報の取得や挿入、そして更新が最小限
のスクリプティングで可能になります。データベース処理をするのに、たった
1、2行しか必要でない場合さえあります。CodeIgniter ではデータベースごと
に専用のクラスを必要としたりはしません。そのおかげで、より単純化された
インターフェースが提供されています。

クエリビルダ 機能を使う大きな利点は、単に単純であるからだけでなく、クエリビルダ
機能を使えば、クエリの構文は各種のデータベースアダプタが生成してくれるの
で、 データベースから独立したアプリケーションを作成できるということで
す。また、システムにより自動的に値のエスケープ処理が行われるので、より
安全なクエリが利用可能になります。

.. note:: クエリを自分で書きたい場合は、データベース設定ファイルでこの
	クラスを使用できないようにすることもできます。そうすることで、コアの
	データベースライブラリとアダプタに、少ないリソースを有効活用させることができます。

.. contents::
    :local:
    :depth: 1

**************
データの選択
**************

次のメソッドを使えば、SQLの **SELECT** 文を組み立てられます。

**$this->db->get()**

SELECT クエリを実行し、結果を返します。次のように、単独で使うと、
テーブルの全レコードを取得するのに使えます::

	$query = $this->db->get('mytable');  // 次の文を生成します: SELECT * FROM mytable

第2、第3引数で、limit 句と offset 句を
セットできます::

	$query = $this->db->get('mytable', 10, 20);

	// Executes: SELECT * FROM mytable LIMIT 20, 10
	// 生成する文: SELECT * FROM mytable LIMIT 20, 10（MySQL での例。他のデータベースでは、若干違う構文になります）

上の例ではメソッドは $query という名前の変数にアサインされていますが、
この変数は、結果を表示させるのに使用できます::

	$query = $this->db->get('mytable');

	foreach ($query->result() as $row)
	{
		echo $row->title;
	}

結果の生成について完全に論じている :doc:`結果メソッド <results>`
ページを見てみてください。

**$this->db->get_compiled_select()**

SELECT クエリを **$this->db->get()** のようにコンパイルしますが、クエリの 
*実行* はしません。単に SQL クエリの文字列を返すだけです。

例::

	$sql = $this->db->get_compiled_select('mytable');
	echo $sql;

	// 次の文を出力します: SELECT * FROM mytable

第2引数でクエリビルダのクエリをリセットするかを指定できます（デフォルトでは
`$this->db->get()` を使用時のようにリセットされます）::

	echo $this->db->limit(10,20)->get_compiled_select('mytable', FALSE);

	// 次の文を出力します: SELECT * FROM mytable LIMIT 20, 10
	// (in MySQL. Other databases have slightly different syntax)

	echo $this->db->select('title, content, date')->get_compiled_select();

	// 次の文を出力します: SELECT title, content, date FROM mytable LIMIT 20, 10

上記例で特筆すべき点として、
2つ目のクエリが **$this->db->from()** を使っておらず、
テーブル名を第1引数に渡していないという点があります。この結果の理由は、
クエリが値をリセットする **$this->db->reset_query()** を直接使っている 
**$this->db->get()** を使って実行されていないためです。

**$this->db->get_where()**

db->where() メソッドを使用する代わりに、 "where" 句を
第2引数で追加できること以外は上のメソッドと
同じです::

	$query = $this->db->get_where('mytable', array('id' => $id), $limit, $offset);

where メソッドについては、詳しくは下をご覧ください。

.. note:: get_where() は、以前は getwhere() という名前でした。getwhere() は [ 訳注: 2.0.0 で ] 廃止されました。

**$this->db->select()**

次のようにすると、クエリの SELECT の部分を指定できます [ 訳注: 選択したい列を指定できるということ ]::

	$this->db->select('title, content, date');
	$query = $this->db->get('mytable');

	// 生成される SQL 文: SELECT title, content, date FROM mytable

.. note:: テーブルからすべての列（\*）を取得する場合は、
	このメソッドは不要です。このメソッドが省略されると、
	CodeIgniter はすべての列を選択する（SELECT \* ... ）ものとします。

``$this->db->select()`` は追加で第2引数をセットできます。第2引数に FALSE をセットすると、CodeIgniter はバッククォート（バックチック）でフィールド
名やテーブル名を保護しないようになります [ 訳注: 識別子（テーブル名や列名など）が予約語の場合や、特殊文字が含まれる場合、たとえば MySQL では、バッククォート「`」でそれらを囲む必要があります。第2引数に FALSE
をセットするとこれを自動では行いません ]。
これは、複合的な SELECT 文が必要な場合に有用です。

::

	$this->db->select('(SELECT SUM(payments.amount) FROM payments WHERE payments.invoice_id=4') AS amount_paid', FALSE);
	$query = $this->db->get('mytable');

**$this->db->select_max()**

クエリの "SELECT MAX(field)" の部分を書き出します。
結果フィールドをリネームするために、追加で第2引数を指定できます。

::

	$this->db->select_max('age');
	$query = $this->db->get('members');  // 次を生成: SELECT MAX(age) as age FROM members

	$this->db->select_max('age', 'member_age');
	$query = $this->db->get('members'); // 次を生成: SELECT MAX(age) as member_age FROM members


**$this->db->select_min()**

クエリの "SELECT MIN(field)" の部分を書き出します。
select_max() と同様に、結果フィールドをリネームするために、
追加で第2引数を指定できます。

::

	$this->db->select_min('age');
	$query = $this->db->get('members'); // 次を生成: SELECT MIN(age) as age FROM members


**$this->db->select_avg()**

クエリの "SELECT AVG(field)" の部分を書き出します。
select_max() と同様に、結果フィールドをリネームするために、
追加で第2引数を指定できます。

::

	$this->db->select_avg('age');
	$query = $this->db->get('members'); // 次を生成: SELECT AVG(age) as age FROM members


**$this->db->select_sum()**

クエリの "SELECT SUM(field)" の部分を書き出します。 select_max() と同様
に、結果フィールドをリネームするために、
追加で第2引数を指定できます。

::

	$this->db->select_sum('age');
	$query = $this->db->get('members'); // 次を生成: SELECT SUM(age) as age FROM members

**$this->db->from()**

次のようにすると、クエリの FROM の部分を指定できます::

	$this->db->select('title, content, date');
	$this->db->from('mytable');
	$query = $this->db->get();  // 次を生成: SELECT title, content, date FROM mytable

.. note:: 先に示したとおり、クエリの FROM の部分は、$this->db->get()
	メソッドの中で指定できます。どちらを使うかは好みです。

**$this->db->join()**

次のようにすると、クエリの JOIN の部分を指定できます::

	$this->db->select('*');
	$this->db->from('blogs');
	$this->db->join('comments', 'comments.id = blogs.id');
	$query = $this->db->get();

	// 次を生成:
	// SELECT * FROM blogs JOIN comments ON comments.id = blogs.id

1回のクエリで複数の JOIN が必要な場合は、メソッドを複数回
呼んでください。

特定の種類の結合が必要な場合は、第3引数でその種類を指定できます。
指定可能なのは: left、right、outer、inner、left outer および right
outer。

::

	$this->db->join('comments', 'comments.id = blogs.id', 'left');
	// 次を生成: LEFT JOIN comments ON comments.id = blogs.id

*************************
特定のデータを探す
*************************

**$this->db->where()**

このメソッドを使うと **WHERE** 句を4つの方法で指定することが
できます:

.. note:: このメソッドに渡されるすべての値は自動的にエスケープされ、
	安全なクエリを生成します。

#. **単に キー/値 を指定する方法:**

	::

		$this->db->where('name', $name); // 次を生成: WHERE name = 'Joe'

	等号（=）が付加されることに注意してください。

	複数回このメソッドを呼ぶと、
	それらは AND で連結されます:

	::

		$this->db->where('name', $name);
		$this->db->where('title', $title);
		$this->db->where('status', $status);
		// WHERE name = 'Joe' AND title = 'boss' AND status = 'active'

#. **演算子を指定しながら キー/値 を指定する方法:**

	比較方法を指定するために、
	第1引数に演算子を含めることができます:

	::

		$this->db->where('name !=', $name);
		$this->db->where('id <', $id); // 次を生成: WHERE name != 'Joe' AND id < 45

#. **連想配列を使用する方法:**

	::

		$array = array('name' => $name, 'title' => $title, 'status' => $status);
		$this->db->where($array);
		// 次を生成: WHERE name = 'Joe' AND title = 'boss' AND status = 'active'

	またこの方法を使う場合も、次のように、演算子を含めて指定することができます:

	::

		$array = array('name !=' => $name, 'id <' => $id, 'date >' => $date);
		$this->db->where($array);

#. **自由に指定できる文字列を使用する方法:**
   WHERE 句の中身を自分で書くこともできます::

		$where = "name='Joe' AND status='boss' OR status='active'";
		$this->db->where($where);


``$this->db->where()`` にはオプションで第3の引数を渡すこともできます。FALSE
を渡した場合、CodeIgniter はフィールド名やテーブル名を守りません。

::

	$this->db->where('MATCH (field) AGAINST ("value")', NULL, FALSE);

**$this->db->or_where()**

他の句と OR で連結される以外は、
上のメソッドと同じものです::

	$this->db->where('name !=', $name);
	$this->db->or_where('id >', $id);  // 次を生成: WHERE name != 'Joe' OR id > 50

.. note:: or_where() は、以前は orwhere() という名前でした。 orwhere() は[ 訳注: 2.0.0 で ]
	廃止されました。

**$this->db->where_in()**

適切な場合には、AND で連結して、「WHERE field IN ('item', 'item') 」
SQLクエリを生成します

::

	$names = array('Frank', 'Todd', 'James');
	$this->db->where_in('username', $names);
	// 次を生成: WHERE username IN ('Frank', 'Todd', 'James')


**$this->db->or_where_in()**

適切な場合には、OR で連結して、「WHERE field IN ('item', 'item')」
SQLクエリを生成します

::

	$names = array('Frank', 'Todd', 'James');
	$this->db->or_where_in('username', $names);
	// 次を生成: OR username IN ('Frank', 'Todd', 'James')

**$this->db->where_not_in()**

適切な場合には、AND で連結して、 「WHERE field NOT IN ('item', 'item')」
SQLクエリを生成します

::

	$names = array('Frank', 'Todd', 'James');
	$this->db->where_not_in('username', $names);
	// 次を生成: WHERE username NOT IN ('Frank', 'Todd', 'James')


**$this->db->or_where_not_in()**

適切な場合には、NOT で連結して、「WHERE field NOT IN ('item',
'item')」 SQLクエリを生成します

::

	$names = array('Frank', 'Todd', 'James');
	$this->db->or_where_not_in('username', $names);
	// 次を生成: OR username NOT IN ('Frank', 'Todd', 'James')

************************
類似データを探す
************************

**$this->db->like()**

このメソッドを使うと、検索でよく使う **LIKE** 句を
生成できます。

.. note:: このメソッドに渡されるすべての値は自動でエスケープされます。

#. **単に キー/値 を指定する方法:**

	::

		$this->db->like('title', 'match');
		// 次を生成: WHERE `title` LIKE '%match%' ESCAPE '!'

	複数回このメソッドを呼ぶと、それらは AND で
	連結されます::

		$this->db->like('title', 'match');
		$this->db->like('body', 'match');
		// WHERE `title` LIKE '%match%' ESCAPE '!' AND  `body` LIKE '%match% ESCAPE '!'

	ワイルドカード（%）が付加される場所を制御したい場合は、追加の第3引数を
	利用できます。'before'、'after' そして 'both'（規定値）
	が指定できる選択肢になります。

	::

		$this->db->like('title', 'match', 'before');	// 次を生成: WHERE `title` LIKE '%match' ESCAPE '!'
		$this->db->like('title', 'match', 'after');	// 次を生成: WHERE `title` LIKE 'match%' ESCAPE '!'
		$this->db->like('title', 'match', 'both');	// 次を生成: WHERE `title` LIKE '%match%' ESCAPE '!'

#. **連想配列を使用する方法:**

	::

		$array = array('title' => $match, 'page1' => $match, 'page2' => $match);
		$this->db->like($array);
		// WHERE `title` LIKE '%match%' ESCAPE '!' AND  `page1` LIKE '%match%' ESCAPE '!' AND  `page2` LIKE '%match%' ESCAPE '!'


**$this->db->or_like()**

他の句と OR で連結される以外は、
上のメソッドと同じものです::

	$this->db->like('title', 'match'); $this->db->or_like('body', $match);
	// WHERE `title` LIKE '%match%' ESCAPE '!' OR  `body` LIKE '%match%' ESCAPE '!'

.. note:: or_like() は、以前は orlike()という名前でした。 orlike() は [ 訳注: 2.0.0 で ] 廃止されました。

**$this->db->not_like()**

この関数は、NOT LIKE 文を生成する事を除き、 ``like()`` と
同じです::

	$this->db->not_like('title', 'match');	// WHERE `title` NOT LIKE '%match% ESCAPE '!'

**$this->db->or_not_like()**

この関数は、複数のものが、OR で連結されるということ以外は、 ``not_like()``
と同じです::

	$this->db->like('title', 'match');
	$this->db->or_not_like('body', 'match');
	// WHERE `title` LIKE '%match% OR  `body` NOT LIKE '%match%' ESCAPE '!'

**$this->db->group_by()**

クエリの GROUP BY の部分を指定できます::

	$this->db->group_by("title"); // 次を生成: GROUP BY title

また、次のように、複数の値を配列で渡すこともできます::

	$this->db->group_by(array("title", "date"));  // 次を生成: GROUP BY title, date

.. note:: group_by() は、以前は groupby() という名前でした。groupby()は
	廃止されました。

**$this->db->distinct()**

"DISTINCT" キーワードをクエリに追加します

::

	$this->db->distinct();
	$this->db->get('table'); // 次を生成: SELECT DISTINCT * FROM table

**$this->db->having()**

クエリの HAVING の部分を指定できます。1つまたは2つ引数を渡す
2種類の文法があります。::

	$this->db->having('user_id = 45');  // 次を生成: HAVING user_id = 45
	$this->db->having('user_id',  45);  // 次を生成: HAVING user_id = 45

また、次のように、複数の値を配列で渡すこともできます::

	$this->db->having(array('title =' => 'My Title', 'id <' => $id));
	// 次を生成: HAVING title = 'My Title', id < 45


CodeIgniter がクエリをエスケープすることのできるデータベースを使ってい
る場合は、第3引数を FALSE
にして、エスケープを無効にすることができます。

::

	$this->db->having('user_id',  45);  // 次を生成: HAVING `user_id` = 45 in some databases such as MySQL
	$this->db->having('user_id',  45, FALSE);  // 次を生成: HAVING user_id = 45


**$this->db->or_having()**

複数の句を "OR" で分つ以外は、having() と同じです。

****************
結果の並び替え
****************

**$this->db->order_by()**

ORDER BY 句を指定できます。

第1引数は、並べ替えたい列の名前を指定します。

第2引数は、並べ替え結果の順序を指定します。選択肢は **ASC** または **DESC**
または **RANDOM** です。

::

	$this->db->order_by('title', 'DESC');
	// 次を生成: ORDER BY `title` DESC

第1引数で、自由に文字列で指定することもできます::

	$this->db->order_by('title DESC, name ASC');
	// 次を生成: ORDER BY `title` DESC, `name` ASC

あるいは、複数のフィールドが必要な場合は、複数回のメソッド呼び出しもできます。

::

	$this->db->order_by('title', 'DESC');
	$this->db->order_by('name', 'ASC');
	// 次を生成: ORDER BY `title` DESC, `name` ASC

**RANDOM** 順序オプションを選択した場合、第1引数はシード値を指定しない限り
無視されます。

::

	$this->db->order_by('title', 'RANDOM');
	// 次を生成: ORDER BY RAND()

	$this->db->order_by(42, 'RANDOM');
	// 次を生成: ORDER BY RAND(42)

.. note:: order_by() は、以前は orderby() という名前でした。orderby() は
	廃止されました。

.. note:: 現在のところ Oracle または MSSQL ドライバでは、ランダムな並べ替えはサポートされていません。
	これらは、'ASC' が規定値に設定されます。

****************************
結果を制限するか、数える
****************************

**$this->db->limit()**

クエリで返す結果の行数の上限を指定できます。::

	$this->db->limit(10);  // 次を生成: LIMIT 10

第2引数でオフセットを指定できます。

::

	$this->db->limit(10, 20);  // 次を生成: LIMIT 20, 10 (in MySQL.  Other databases have slightly different syntax)

**$this->db->count_all_results()**

特定の クエリビルダ クエリの行数を調べることができます。
クエリは、``where()``, ``or_where()``, ``like()``, ``or_like()`` などの クエリビルダ
の絞り込みが利用できます。例::

	echo $this->db->count_all_results('my_table');  // 25 のような整数を生成
	$this->db->like('title', 'match');
	$this->db->from('my_table');
	echo $this->db->count_all_results(); // 17 のような整数を生成

ただし、このメソッドは ``select()`` に渡したフィールド値をすべて
リセットします。もしこれらを保持したい場合は、第2引数に ``FALSE`` を
渡してください::

	echo $this->db->count_all_results('my_table', FALSE);

**$this->db->count_all()**

特定のテーブルのデータ件数(行数)をカウントします。
第1引数にテーブル名を指定します。例::

	echo $this->db->count_all('my_table');  // 25 のような整数を生成

*********************
クエリのグルーピング
*********************

クエリのグルーピングでは、 WHERE 句を括弧で囲むことでグループを作ることができます。
これにより複雑な WHERE 句のクエリを作ることが可能です。例::

	$this->db->select('*')->from('my_table')
		->group_start()
			->where('a', 'a')
			->or_group_start()
				->where('b', 'b')
				->where('c', 'c')
			->group_end()
		->group_end()
		->where('d', 'd')
	->get();

	// 次のようになります:
	// SELECT * FROM (`my_table`) WHERE ( `a` = 'a' OR ( `b` = 'b' AND `c` = 'c' ) ) AND `d` = 'd'

.. note:: groups は対称であることが求められます。 group_start() が group_end() と合致していることを確認してください。

**$this->db->group_start()**

WHERE 句に括弧開きを追加することで新しいグループを作ります。

**$this->db->or_group_start()**

WHERE 句に括弧開きを追加することで新しいグループを作り、 'OR' を先頭に置きます。

**$this->db->not_group_start()**

WHERE 句に括弧開きを追加することで新しいグループを作り、 'NOT' を先頭に置きます。

**$this->db->or_not_group_start()**

WHERE 句に括弧開きを追加することで新しいグループを作り、 'OR NOT' を先頭に置きます。

**$this->db->group_end()**

WHERE 句に括弧閉じを追加することで現在のグループを閉じます。

**************
データの挿入
**************

**$this->db->insert()**

与えられたデータをもとに INSERT 文を生成し実行します。
**配列** または **オブジェクト** のどちらかでメソッドにデータを渡せます。
配列を使った例は次の通りです::

	$data = array(
		'title' => 'My title',
		'name' => 'My Name',
		'date' => 'My date'
	);

	$this->db->insert('mytable', $data);
	// 次を生成: INSERT INTO mytable (title, name, date) VALUES ('My title', 'My name', 'My date')

第1引数はテーブル名で、第2引数は、値の連想配列で
指定します。

オブジェクトを使った例は次の通りです::

	/*
	class Myclass {
		public $title = 'My Title';
		public $content = 'My Content';
		public $date = 'My Date';
	}
	*/

	$object = new Myclass;
	$this->db->insert('mytable', $object);
	// 次を生成: INSERT INTO mytable (title, content, date) VALUES ('My Title', 'My Content', 'My Date')

第1引数はテーブル名で、第2引数はオブジェクトに
なります。

.. note:: すべての値は自動的にエスケープされ、安全なクエリを生成します。

**$this->db->get_compiled_insert()**

INSERT クエリを **$this->db->insert()** のようにコンパイルしますが、クエリの 
*実行* はしません。単に SQL クエリの文字列を返すだけです。

Example::

	$data = array(
		'title' => 'My title',
		'name'  => 'My Name',
		'date'  => 'My date'
	);

	$sql = $this->db->set($data)->get_compiled_insert('mytable');
	echo $sql;

	// SQL 文字列を生成: INSERT INTO mytable (`title`, `name`, `date`) VALUES ('My title', 'My name', 'My date')

第2引数でクエリビルダのクエリをリセットするかを指定できます（デフォルトでは
`$this->db->insert()` を使用時のようにリセットされます）::

	echo $this->db->set('title', 'My Title')->get_compiled_insert('mytable', FALSE);

	// SQL 文字列を生成: INSERT INTO mytable (`title`) VALUES ('My Title')

	echo $this->db->set('content', 'My Content')->get_compiled_insert();

	// SQL 文字列を生成: INSERT INTO mytable (`title`, `content`) VALUES ('My Title', 'My Content')

上記例で特筆すべき点として、
2つ目のクエリが **$this->db->from()** を使っておらず、
テーブル名を第1引数に渡していないという点があります。この結果の理由は、
クエリが値をリセットする **$this->db->reset_query()** を直接使っている 
**$this->db->insert()** を使って実行されていないためです。

.. note:: このメソッドはバッチ挿入には対応していません。

**$this->db->insert_batch()**

与えられたデータをもとに INSERT 文を生成し実行します。
**配列** または **オブジェクト** のどちらかでメソッドにデータを渡せます。
配列を使った例は次の通りです::

	$data = array(
		array(
			'title' => 'My title',
			'name' => 'My Name',
			'date' => 'My date'
		),
		array(
			'title' => 'Another title',
			'name' => 'Another Name',
			'date' => 'Another date'
		)
	);

	$this->db->insert_batch('mytable', $data);
	// 次を生成: INSERT INTO mytable (title, name, date) VALUES ('My title', 'My name', 'My date'),  ('Another title', 'Another name', 'Another date')

第1引数はテーブル名で、第2引数は、
値の連想配列で指定します。

.. note:: すべての値は自動的にエスケープされ、安全なクエリを生成します。

*************
データの更新
*************

**$this->db->replace()**

このメソッドは REPLACE 分を実行します, これは基本的には SQL
標準の（必要なら）DELETE + INSERT で、 *PRIMARY* と *UNIQUE*
キーを判定要素として使用します。
これにより、複雑なロジックを組み込む必要を抑えられます。
これがないと  ``select()`` 、 ``update()`` 、
``delete()`` と ``insert()`` の呼び出しを組み合わせることになります。

Example::

	$data = array(
		'title' => 'My title',
		'name'  => 'My Name',
		'date'  => 'My date'
	);

	$this->db->replace('table', $data);

	// 次を生成: REPLACE INTO mytable (title, name, date) VALUES ('My title', 'My name', 'My date')

上記の例では、もし *title* フィールドを主キーと決めつけるならば、
*title* の値に 'My title' を含む行があれば、その行は
新しいデータで置き換えられる形で削除されます。

``set()`` メソッドの使い方もまたすべてのフィールドを
自動的にエスケープします、ちょうど ``insert()`` のように。

**$this->db->set()**

inserts または updates で値をセットするのに使います。

**これは次のように、 insert または update メソッドに直接データの
配列を渡す代わりに使用できます:**

::

	$this->db->set('name', $name);
	$this->db->insert('mytable');  // 次を生成: INSERT INTO mytable (`name`) VALUES ('{$name}')

もし複数のメソッドをコールした場合、それらは insert か update
かに基づき適切に組み立てられます::

	$this->db->set('name', $name);
	$this->db->set('title', $title);
	$this->db->set('status', $status);
	$this->db->insert('mytable');

また、 **set()** は、FALSE をセットするとデータをエスケープするのを回避する、
第3引数（``$escape``）をセットできます。違いを示すため、escape パラメータを
利用する場合と利用しない場合、両方の ``set()`` の使用の
説明を挙げます。

::

	$this->db->set('field', 'field+1', FALSE);
	$this->db->where('id', 2);
	$this->db->update('mytable'); // gives UPDATE mytable SET field = field+1 WHERE id = 2

	$this->db->set('field', 'field+1');
	$this->db->where('id', 2);
	$this->db->update('mytable'); // gives UPDATE `mytable` SET `field` = 'field+1' WHERE `id` = 2

このメソッドに連想配列を渡すこともできます::

	$array = array(
		'name' => $name,
		'title' => $title,
		'status' => $status
	);

	$this->db->set($array);
	$this->db->insert('mytable');

あるいはオブジェクトを渡すこともできます::

	/*
	class Myclass {
		public $title = 'My Title';
		public $content = 'My Content';
		public $date = 'My Date';
	}
	*/

	$object = new Myclass;
	$this->db->set($object);
	$this->db->insert('mytable');

**$this->db->update()**

指定されたデータをもとに UPDATE 文を生成してクエリを実行します。
**配列** または **オブジェクト** をメソッドに渡すことができます。
配列を使った例は次の通りです::

	$data = array(
		'title' => $title,
		'name' => $name,
		'date' => $date
	);

	$this->db->where('id', $id);
	$this->db->update('mytable', $data);
	// 次を生成:
	//
	//	UPDATE mytable
	//	SET title = '{$title}', name = '{$name}', date = '{$date}'
	//	WHERE id = $id

あるいは、次のようにオブジェクトを渡すこともできます::

	/*
	class Myclass {
		public $title = 'My Title';
		public $content = 'My Content';
		public $date = 'My Date';
	}
	*/

	$object = new Myclass;
	$this->db->where('id', $id);
	$this->db->update('mytable', $object);
	// 次を生成:
	//
	// UPDATE `mytable`
	// SET `title` = '{$title}', `name` = '{$name}', `date` = '{$date}'
	// WHERE id = `$id`

.. note:: すべての値は自動的にエスケープされ、安全なクエリを生成します。

$this->db->where() メソッドを使えば WHERE 句をセットできます。
次のように、オプションで、更新メソッドに直接文字列で情報を渡すことも
できます::

	$this->db->update('mytable', $data, "id = 4");

あるいは、配列でも渡せます::

	$this->db->update('mytable', $data, array('id' => $id));

先に述べた、 $this->db->set() メソッドを更新に利用することも
できます。

**$this->db->update_batch()**

与えられたデータをもとに UPDATE 文を生成し実行します。
**配列** または **オブジェクト** のどちらかでメソッドにデータを渡せます。
配列を使った例は次の通りです::

	$data = array(
	   array(
	      'title' => 'My title' ,
	      'name' => 'My Name 2' ,
	      'date' => 'My date 2'
	   ),
	   array(
	      'title' => 'Another title' ,
	      'name' => 'Another Name 2' ,
	      'date' => 'Another date 2'
	   )
	);

	$this->db->update_batch('mytable', $data, 'title');

	// 次を生成:
	// UPDATE `mytable` SET `name` = CASE
	// WHEN `title` = 'My title' THEN 'My Name 2'
	// WHEN `title` = 'Another title' THEN 'Another Name 2'
	// ELSE `name` END,
	// `date` = CASE
	// WHEN `title` = 'My title' THEN 'My date 2'
	// WHEN `title` = 'Another title' THEN 'Another date 2'
	// ELSE `date` END
	// WHERE `title` IN ('My title','Another title')

第1引数はテーブル名、第2引数は値の連想配列、第3引数は where
句を指定します。

.. note:: すべての値は自動的にエスケープされ、安全なクエリを生成します。

.. note:: ``affected_rows()`` は内部仕様のため、このメソッドで適切な結果を返しません。
	そのかわり ``update_batch()`` は影響された行数を
	返します。

**$this->db->get_compiled_update()**

これは ``$this->db->get_compiled_insert()`` とまったく同じように動作します。違いは
INSERT SQL 文字列のかわりに UPDATE SQL 文字列を生成することです。

詳しくは `$this->db->get_compiled_insert()` のドキュメントをご覧ください。

.. note:: このメソッドは update バッチでは動きません。

*************
データの削除
*************

**$this->db->delete()**

SQL の DELETE 文を生成して実行します。

::

	$this->db->delete('mytable', array('id' => $id));  // 次を生成: // DELETE FROM mytable  // WHERE id = $id

第1引数はテーブル名で、第2引数は、WHERE
句です。次のように、メソッドの第2引数にデータを渡す代わりに、 where()
または or_where() メソッドを使うこともできます::

	$this->db->where('id', $id);
	$this->db->delete('mytable');

	// 次を生成:
	// DELETE FROM mytable
	// WHERE id = $id


1つよりも多いテーブルを削除したい場合は、delete()
にテーブル名の配列を渡すことができます

::

	$tables = array('table1', 'table2', 'table3');
	$this->db->where('id', '5');
	$this->db->delete($tables);


テーブルの全データを削除したい場合は、 truncate() メソッドか
empty_table() が利用できます。

**$this->db->empty_table()**

「delete」 SQL 文字列 を生成し、クエリを
実行します。::

	  $this->db->empty_table('mytable'); // 次を生成: DELETE FROM mytable

**$this->db->truncate()**

「truncate」 SQL 文字列を生成し、クエリを実行します。

::

	$this->db->from('mytable');
	$this->db->truncate();

	// または

	$this->db->truncate('mytable');

	// Produce:
	// TRUNCATE mytable

.. note:: If the TRUNCATE command isn't available, truncate() will
	execute as "DELETE FROM table".

**$this->db->get_compiled_delete()**

このメソッドは ``$this->db->get_compiled_insert()`` とまったく同じように動作します。違いは
INSERT SQL 文字列のかわりに DELETE SQL 文字列を生成します。

詳しくは $this->db->get_compiled_insert() のドキュメントをご覧ください。

***************
メソッドの連結
***************

メソッドの連結を使えば、複数のメソッドをつなぐのがシンプルになります。
次のような例が挙げられます::

	$query = $this->db->select('title')
			->where('id', $id)
			->limit(10, 20)
			->get('mytable');

.. _ar-caching:

****************************
クエリビルダ キャッシング
****************************

"本当の" キャッシングではないのですが、クエリビルダ では、後で再利用
するためにクエリの特定の部分を保存（あるいは、"キャッシュ"）することができます。
通常は、クエリビルダ の呼び出しが完了したときには、保存された全情報は、
次の呼び出しのためにリセットされます。キャッシングを利用すると、
このリセットを回避することができ、情報を簡単に再利用できます。

キャッシュされた呼び出しは、累積されます。2回のキャッシュされた
select() を呼び出し、その後に 2回キャッシュされていないselect() を呼び出した場合、
4回 select() を呼び出したことになります。3つのキャッシュ関連メソッドが利用できます:

**$this->db->start_cache()**

このメソッドは、キャッシュを開始する際にコールされる必要があります。適合する
タイプ(サポートされるクエリについては下記をご覧ください)のすべての クエリビルダ クエリが、
後の使用のために保管されます。

**$this->db->stop_cache()**

このメソッドは、キャッシュを停止するときに呼ぶことができます。

**$this->db->flush_cache()**

このメソッドは、クエリビルダ キャッシュからすべてのアイテムを削除します。

キャッシングの例
---------------------

次は使用例です::

	$this->db->start_cache();
	$this->db->select('field1');
	$this->db->stop_cache();
	$this->db->get('tablename');
	//次のようになります: SELECT `field1` FROM (`tablename`)

	$this->db->select('field2');
	$this->db->get('tablename');
	//次のようになります:  SELECT `field1`, `field2` FROM (`tablename`)

	$this->db->flush_cache();
	$this->db->select('field2');
	$this->db->get('tablename');
	//次のようになります:  SELECT `field2` FROM (`tablename`)



.. note:: 次のフィールドがキャッシュ可能です: select、from、join、
	where、like、group_by、having、order_by

*************************
クエリビルダのリセット
*************************

**$this->db->reset_query()**

クエリビルダのリセットを使うと、クエリを $this->db->get() や $this->db->insert() 
などで実行せずにゼロから始めることができます。
クエリを実行するメソッドのように、 `クエリビルダ キャッシング`_ によりキャッシュ
されたアイテムはリセット *されません* 。

これが有用な場面は、たとえば、クエリビルダでSQLを生成して
(例: ``$this->db->get_compiled_select()``) 、そのあとにクエリを実行すると判断した
場合などです::

	// get_compiled_select メソッドの第二引数が FALSE なのに注意してください
	$sql = $this->db->select(array('field1','field2'))
					->where('field3',5)
					->get_compiled_select('mytable', FALSE);

	// ...
	// SQL コードで何かすごく複雑なことをするとします... たとえば後で実行するため
	// cron スクリプトに追加するなど...
	// ...

	$data = $this->db->get()->result_array();

	// 次のクエリを実行し、結果の配列を返します:
	// SELECT field1, field1 from mytable where field3 = 5;

.. note:: クエリビルダのキャッシュ機能を使い、クエリをリセットせずに 
	``get_compiled_select()`` を二回呼び出すと、キャッシュが2度マージされます。
	それにより、たとえば ``select()`` をキャッシュしている場合、同じ
	フィールドが2回 select されてしまいます。

*******************
クラスリファレンス
*******************

.. php:class:: CI_DB_query_builder

	.. php:method:: reset_query()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		現在のクエリビルダ状態をリセット。ある特定条件下でキャンセルしたい
		クエリを構築したい場合に有用です。

	.. php:method:: start_cache()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリビルダのキャッシュを開始します。

	.. php:method:: stop_cache()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリビルダのキャッシュを停止します。

	.. php:method:: flush_cache()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリビルダのキャッシュを空にします。

	.. php:method:: set_dbprefix([$prefix = ''])

		:param	string	$prefix: 新しく使うプリフィックス
		:returns:	使われているプリフィックス
		:rtype:	string

		データベースのプリフィックスを再接続せずに設定します。

	.. php:method:: dbprefix([$table = ''])

		:param	string	$table: プリフィックスを追加するテーブル名
		:returns:	プリフィックスが追加されたテーブル名
		:rtype:	string

		データベースのプリフィックスが設定されている場合プリフィックスを追加します。

	.. php:method:: count_all_results([$table = '', [$reset = TRUE]])

		:param	string	$table: テーブル名
		:param	bool	$reset: SELECT の値をリセットするか
		:returns:	クエリ結果の行数
		:rtype:	int

		クエリビルダのクエリにより返された全レコードを数える
		プラットフォーム固有のクエリ文字列を生成します。

	.. php:method:: get([$table = ''[, $limit = NULL[, $offset = NULL]]])

		:param	string	$table: クエリするテーブル
		:param	int	$limit: LIMIT 句
		:param	int	$offset: OFFSET 句
		:returns:	CI_DB_result インスタンス（メソッドチェイン）
		:rtype:	CI_DB_result

		すでに呼ばれたクエリビルダのメソッドを元に生成された SELECT 文
		をコンパイルして実行します。

	.. php:method:: get_where([$table = ''[, $where = NULL[, $limit = NULL[, $offset = NULL]]]])

		:param	mixed	$table: データをフェッチするテーブル（複数可）; 文字列か配列
		:param	string	$where: WHERE 句
		:param	int	$limit: LIMIT 句
		:param	int	$offset: OFFSET 句
		:returns:	CI_DB_result インスタンス（メソッドチェイン）
		:rtype:	CI_DB_result

		``get()`` と同じですが、WHERE を直接追加できます。

	.. php:method:: select([$select = '*'[, $escape = NULL]])

		:param	string	$select: クエリの SELECT 部分
		:param	bool	$escape: 値や識別子をエスケープするか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに SELECT 句 を追加します。

	.. php:method:: select_avg([$select = ''[, $alias = '']])

		:param	string	$select: 平均値を計算するフィールド
		:param	string	$alias: 結果の値のエイリアス
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに SELECT AVG(field) 句 を追加します。

	.. php:method:: select_max([$select = ''[, $alias = '']])

		:param	string	$select: 最大値を計算するフィールド
		:param	string	$alias: 結果の値のエイリアス
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに SELECT MAX(field) 句 を追加します。

	.. php:method:: select_min([$select = ''[, $alias = '']])

		:param	string	$select: 最小値を計算するフィールド
		:param	string	$alias: 結果の値のエイリアス
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに SELECT MIN(field) 句 を追加します。

	.. php:method:: select_sum([$select = ''[, $alias = '']])

		:param	string	$select: 累計を計算するフィールド
		:param	string	$alias: 結果の値のエイリアス
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに SELECT SUM(field) 句 を追加します。

	.. php:method:: distinct([$val = TRUE])

		:param	bool	$val: "distinct" フラグの値
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリビルダの SELECT 部分に DISTINCT 句を追加するか
		判断するフラグを設定します。

	.. php:method:: from($from)

		:param	mixed	$from: テーブル名(複数可); 文字列か配列
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリの FROM 句 を指定します。

	.. php:method:: join($table, $cond[, $type = ''[, $escape = NULL]])

		:param	string	$table: join したいテーブル
		:param	string	$cond: JOIN ON 条件
		:param	string	$type: JOIN の種類
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		Adds a JOIN 句 to a query.

	.. php:method:: where($key[, $value = NULL[, $escape = NULL]])

		:param	mixed	$key: 比較するフィールド名か、連想配列
		:param	mixed	$value: 単一のキーを指定した場合、この値に比較される
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリのWHERE部分を生成する。
		 `AND` で複数のコールを分割する。

	.. php:method:: or_where($key[, $value = NULL[, $escape = NULL]])

		:param	mixed	$key: 比較するフィールド名か、連想配列
		:param	mixed	$value: 単一のキーを指定した場合、この値に比較される
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリのWHERE部分を生成する。
		 `OR` で複数のコールを分割する。

	.. php:method:: or_where_in([$key = NULL[, $values = NULL[, $escape = NULL]]])

		:param	mixed	$key: 検索するフィールド名
		:param	mixed	$value: 検索する値
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリの WHERE IN('item', 'item') を生成する。
		適宜 `OR` で結合する。

	.. php:method:: or_where_not_in([$key = NULL[, $values = NULL[, $escape = NULL]]])

		:param	mixed	$key: 検索するフィールド名
		:param	mixed	$value: 検索する値
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリの WHERE NOT IN('item', 'item') を生成する。
		適宜 `OR` で結合する。

	.. php:method:: where_in([$key = NULL[, $values = NULL[, $escape = NULL]]])

		:param	string	$key: 検索するフィールド名
		:param	array	$values: 対象の値の配列
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリの WHERE IN('item', 'item') を生成する。
		適宜 `AND` で結合する。

	.. php:method:: where_not_in([$key = NULL[, $values = NULL[, $escape = NULL]]])

		:param	string	$key: 検索するフィールド名
		:param	array	$values: 対象の値の配列
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	DB_query_builder インスタンス
		:rtype:	object

		クエリの WHERE NOT IN('item', 'item') を生成する。
		適宜 `AND` で結合する。

	.. php:method:: group_start()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		group 表現を開始し、中の条件内は AND で結合する。

	.. php:method:: or_group_start()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		group 表現を開始し、中の条件内は OR で結合する。

	.. php:method:: not_group_start()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		group 表現を開始し、中の条件内は AND NOT で結合する。

	.. php:method:: or_not_group_start()

		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		group 表現を開始し、中の条件内は OR NOT で結合する。

	.. php:method:: group_end()

		:returns:	DB_query_builder インスタンス
		:rtype:	object

		group 表現を終了する。

	.. php:method:: like($field[, $match = ''[, $side = 'both'[, $escape = NULL]]])

		:param	string	$field: フィールド名
		:param	string	$match: マッチさせるテキスト
		:param	string	$side: 文のどの側に '%' ワイルドカードを挿入するか
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに LIKE 句を追加する。複数のコールは AND で分割する。

	.. php:method:: or_like($field[, $match = ''[, $side = 'both'[, $escape = NULL]]])

		:param	string	$field: フィールド名
		:param	string	$match: マッチさせるテキスト
		:param	string	$side: 文のどの側に '%' ワイルドカードを挿入するか
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに LIKE 句を追加する。複数のコールは OR で分割する。

	.. php:method:: not_like($field[, $match = ''[, $side = 'both'[, $escape = NULL]]])

		:param	string	$field: フィールド名
		:param	string	$match: マッチさせるテキスト
		:param	string	$side: 文のどの側に '%' ワイルドカードを挿入するか
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに NOT LIKE 句を追加する。複数のコールは AND で分割する。

	.. php:method:: or_not_like($field[, $match = ''[, $side = 'both'[, $escape = NULL]]])

		:param	string	$field: フィールド名
		:param	string	$match: マッチさせるテキスト
		:param	string	$side: 文のどの側に '%' ワイルドカードを挿入するか
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに NOT LIKE 句を追加する。複数のコールは OR で分割する。

	.. php:method:: having($key[, $value = NULL[, $escape = NULL]])

		:param	mixed	$key: 識別子（文字列）か、フィールド／値の連想配列
		:param	string	$value: $key が識別子の場合の値
		:param	string	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに HAVING 句を追加する。複数のコールは AND で分割する。

	.. php:method:: or_having($key[, $value = NULL[, $escape = NULL]])

		:param	mixed	$key: 識別子（文字列）か、フィールド／値の連想配列
		:param	string	$value: $key が識別子の場合の値
		:param	string	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに HAVING 句を追加する。複数のコールは OR で分割する。

	.. php:method:: group_by($by[, $escape = NULL])

		:param	mixed	$by: グループするフィールド（複数可）；文字列か配列
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに GROUP BY 句を追加する。

	.. php:method:: order_by($orderby[, $direction = ''[, $escape = NULL]])

		:param	string	$orderby: ソートするフィールド
		:param	string	$direction: 指定の順序 - ASC, DESC, かランダム
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに ORDER BY 句を追加する。

	.. php:method:: limit($value[, $offset = 0])

		:param	int	$value: 結果を制限する行数
		:param	int	$offset: スキップする行数
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに LIMIT と OFFSET 句を追加する。

	.. php:method:: offset($offset)

		:param	int	$offset: スキップする行数
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		クエリに OFFSET 句を追加する。

	.. php:method:: set($key[, $value = ''[, $escape = NULL]])

		:param	mixed	$key: フィールド名か、フィールド／値の配列
		:param	string	$value: $key が単一フィールドの場合の値
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		``insert()``, ``update()``, か ``replace()`` に渡される
		フィールド／値を追加する。

	.. php:method:: insert([$table = ''[, $set = NULL[, $escape = NULL]]])

		:param	string	$table: テーブル名
		:param	array	$set: フィールド／値の連想配列
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	成功の場合 TRUE, 失敗の場合 FALSE
		:rtype:	bool

		INSERT 文をコンパイルして実行する。

	.. php:method:: insert_batch($table[, $set = NULL[, $escape = NULL[, $batch_size = 100]]])

		:param	string	$table: テーブル名
		:param	array	$set: 挿入するデータ
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:param	int	$batch_size: 一度に挿入する行数
		:returns:	挿入された行数か、失敗時は FALSE
		:rtype:	mixed

		複数の ``INSERT`` 文のバッチをコンパイルして実行する。

		..note:: ``$batch_size`` より多くの行数が渡された場合、
		複数の ``INSERT`` クエリが実行され、それぞれ ``$batch_size`` 行数
		まで挿入を試みる。

	.. php:method:: set_insert_batch($key[, $value = ''[, $escape = NULL]])

		:param	mixed	$key: フィールド名か、フィールド名／値の配列
		:param	string	$value: $key が単一フィールドの場合のフィールド値
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		``insert_batch()`` で挿入されるべきフィールド／値を追加。

	.. php:method:: update([$table = ''[, $set = NULL[, $where = NULL[, $limit = NULL]]]])

		:param	string	$table: テーブル名
		:param	array	$set: フィールド／値の連想配列
		:param	string	$where: WHERE 句
		:param	int	$limit: LIMIT 句
		:returns:	成功の場合 TRUE, 失敗の場合 FALSE
		:rtype:	bool

		UPDATE 文をコンパイルして実行。

	.. php:method:: update_batch($table[, $set = NULL[, $value = NULL[, $batch_size = 100]]])

		:param	string	$table: テーブル名
		:param	array	$set: フィールド名か、フィールド／値の連想配列
		:param	string	$value: $set が単一フィールドの場合のフィールド値
		:param	int	$batch_size: 単一のクエリでまとめる条件文の数
		:returns:	更新された行数か、失敗の場合 FALSE
		:rtype:	mixed

		複数の UPDATE 文のバッチをコンパイルして実行する。

		..note:: ``$batch_size`` より多くの行数が渡された場合、
		複数のクエリが実行され、それぞれ ``$batch_size`` のフィールド／値ペア
		の分だけ操作を行う。

	.. php:method:: set_update_batch($key[, $value = ''[, $escape = NULL]])

		:param	mixed	$key: フィールド名か、フィールド名／値の配列
		:param	string	$value: $key が単一フィールドの場合のフィールド値
		:param	bool	$escape: 値や識別子をエスケープするかどうか
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）
		:rtype:	CI_DB_query_builder

		``update_batch()`` で更新されるべきフィールド／値を追加。

	.. php:method:: replace([$table = ''[, $set = NULL]])

		:param	string	$table: テーブル名
		:param	array	$set: フィールド／値の連想配列
		:returns:	成功の場合 TRUE, 失敗の場合 FALSE
		:rtype:	bool

		REPLACE 文をコンパイルして実行。

	.. php:method:: delete([$table = ''[, $where = ''[, $limit = NULL[, $reset_data = TRUE]]]])

		:param	mixed	$table: 削除するテーブル（複数可）。文字列か配列。
		:param	string	$where: WHERE 句
		:param	int	$limit: LIMIT 句
		:param	bool	$reset_data: クエリの "write" 句をリセットするには TRUE
		:returns:	CI_DB_query_builder インスタンス（メソッドチェイン）or FALSE on failure
		:rtype:	mixed

		DELETE クエリをコンパイルして実行。

	.. php:method:: truncate([$table = ''])

		:param	string	$table: テーブル名
		:returns:	成功の場合 TRUE, 失敗の場合 FALSE
		:rtype:	bool

		テーブルに TRUNCATE を実行。

		.. note:: もしデータベースプラットフォームが TRUNCATE をサポートしてない場合、
			代わりに DELETE 文が使用されます。

	.. php:method:: empty_table([$table = ''])

		:param	string	$table: テーブル名
		:returns:	成功の場合 TRUE, 失敗の場合 FALSE
		:rtype:	bool

		DELETE 文でテーブルから全レコードを削除する。

	.. php:method:: get_compiled_select([$table = ''[, $reset = TRUE]])

		:param	string	$table: テーブル名
		:param	bool	$reset: 現在のクエリビルダ値をリセットするかどうか
		:returns:	コンパイルされた SQL 文の文字列
		:rtype:	string

		SELECT 文をコンパイルして文字列として返す。

	.. php:method:: get_compiled_insert([$table = ''[, $reset = TRUE]])

		:param	string	$table: テーブル名
		:param	bool	$reset: 現在のクエリビルダ値をリセットするかどうか
		:returns:	コンパイルされた SQL 文の文字列
		:rtype:	string

		INSERT 文をコンパイルして文字列として返す。

	.. php:method:: get_compiled_update([$table = ''[, $reset = TRUE]])

		:param	string	$table: テーブル名
		:param	bool	$reset: 現在のクエリビルダ値をリセットするかどうか
		:returns:	コンパイルされた SQL 文の文字列
		:rtype:	string

		UPDATE 文をコンパイルして文字列として返す。

	.. php:method:: get_compiled_delete([$table = ''[, $reset = TRUE]])

		:param	string	$table: テーブル名
		:param	bool	$reset: 現在のクエリビルダ値をリセットするかどうか
		:returns:	コンパイルされた SQL 文の文字列
		:rtype:	string

		DELETE 文をコンパイルして文字列として返す。
