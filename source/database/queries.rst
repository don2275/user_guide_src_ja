#######
クエリ
#######

************
クエリの基本
************

標準的なクエリ
===============

クエリを実行するには、**query** メソッドを使います::

	$this->db->query('ここにクエリを記述');

query() メソッドは、「読み取り」タイプのクエリが実行されたときには、
:doc:`結果を表示する <results>` ために使用するデータベースの結果
**オブジェクト** を返します。 「書き込み」タイプの問い合わせを実行したとき
には、単純にクエリが成功したかどうかによって TRUE か FALSE
が返ります。データを取得するときには、通常は query
メソッドの結果を受け取る変数を次のように用意
します:::

	$query = $this->db->query('YOUR QUERY HERE');

単純化されたクエリ
==================

**simple_query** は、 $this->db->query() メソッドが単純化されたものです。
成功か失敗かを TRUE/FALSE で返します。結果セットは「返しません」。クエリ
時間の計測や、データのバインディングをコンパイルしたり、デバッグ用にクエリ
を保存することもありません。 これを使うと、クエリの単純な送信ができます。
ほとんどのユーザは、このメソッドをまれにしか使用しないでしょう。

データベースドライバの "execute" 関数の実行結果を返します。
大抵は、 INSERT, DELETE, UPDATE などの書き込みクエリに関しては
成功時か失敗時に TRUE/FALSE （本来はこれらのために使われるべきです）、
フェッチ結果があるクエリに関してはリソースかオブジェクトです。

::

	if ($this->db->simple_query('YOUR QUERY'))
	{
		echo "Success!";
	}
	else
	{
		echo "Query failed!";
	}

.. note:: たとえば PostreSQL の ``pg_exec()`` は書き込みクエリでも
	必ず成功時にリソースを返します。 boolean 値を期待している場合は
	それを考慮してください。

************************************************
データベースプリフィックス(接頭辞)を手動で扱う
************************************************

データベースプリフィックス(接頭辞)が設定済みの場合で、ネイティブな SQL
文の使用でテーブル名にプリフィックスを追加したいときは、以下のようにし
ます::

	$this->db->dbprefix('tablename'); // outputs prefix_tablename


何らかの理由で、新しく接続することなしにプログラムでプリフィックスを変
更したい場合は、以下のメソッドを使います::

	$this->db->set_dbprefix('newprefix');
	$this->db->dbprefix('tablename'); // outputs newprefix_tablename


**********************
識別子の保護
**********************

多くのデータベースでは、テーブル名やフィールド名を保護すること - たとえば 
MySQL では、バッククォート(バックチック)を使います - が推奨されます。
**Query Builder のクエリは自動的に保護されます** が、
識別子を手動で保護する必要がある場合は、次を利用できます::

	$this->db->protect_identifiers('table_name');

.. important:: Query Builder は入力されたテーブル名やフィールド名に対して
	適切にクオートを付与するため最大限努めますが、恣意的なユーザ入力に
	対して動作するように設計されていないことを考慮してください。
	サニタイズされていないユーザデータを入力しないでください。

このメソッドは、データベース設定ファイルで指定したデータベースプリフィ
ックス(接頭辞)をテーブル名に追加できます。これを利用するには、第2引数
に TRUE (ブール値)を指定します::

	$this->db->protect_identifiers('table_name', TRUE);


***********************
クエリのエスケープ処理
***********************

データベースにデータを送信する前にデータをエスケープ処理するのは、大変
優れたセキュリティ上のプラクティスです。 CodeIgniter
には、これを支援するメソッドが3つあります:

#. **$this->db->escape()** このメソッドは、データの型を決定し、 文字列デー
   タだけをエスケープ処理します。また、自分でやらなくて済むように
   自動でデータをシングルクォーテーションで囲んでくれます:
   ::

	$sql = "INSERT INTO table (title) VALUES(".$this->db->escape($title).")";

#. **$this->db->escape_str()** このメソッドは、型を無視して渡されたデータを
   エスケープします。 ほとんどの場合はこれを使わず、上のメソッドを利用することに
   なると思います。このメソッドは、次のように使用します:
   ::

	$sql = "INSERT INTO table (title) VALUES('".$this->db->escape_str($title)."')";

#. **$this->db->escape_like_str()** 文字列の中の LIKE 句で使用される
   ワイルドカード('%', '\_')についても適切にエスケープされるように、 
   文字列が LIKE条件で使用されるときには、このメソッドを使用するべきです。

::

        $search = '20% raise'; 
        $sql = "SELECT id FROM table WHERE column LIKE '%" .
            $this->db->escape_like_str($search)."%' ESCAPE '!'";

.. important:: The ``escape_like_str()`` method uses '!' (exclamation mark)
	to escape special characters for *LIKE* conditions. Because this
	method escapes partial strings that you would wrap in quotes
	yourself, it cannot automatically add the ``ESCAPE '!'``
	condition for you, and so you'll have to manually do that.


************************
クエリのバインディング
************************

バインディングを使うと、システムにクエリを組み立てさせることで、クエリ
の構文を単純化することができます。次のような例があげられます::

	$sql = "SELECT * FROM some_table WHERE id = ? AND status = ? AND author = ?";
	$this->db->query($sql, array(3, 'live', 'Rick'));

クエリの中のクエスチョンマークは、query
メソッドの第2引数で指定した配列のデータに自動的に置き換わります。

バインディングは配列でも使用できます。この場合は IN セットに変換されます::

	$sql = "SELECT * FROM some_table WHERE id IN ? AND status = ? AND author = ?";
	$this->db->query($sql, array(array(3, 6), 'live', 'Rick'));

結果のクエリはこのようになります::

	SELECT * FROM some_table WHERE id IN (3,6) AND status = 'live' AND author = 'Rick'

バインディングを利用する第2の利点は、値が自動的にエスケープされ、安全
なクエリが生成されるということです。手動をデータをエスケープするのを気
に留める必要がなく、あなたに代わってエンジンが自動でそれを行ってくれま
す。

*******************
エラーハンドリング
*******************

**$this->db->error();**

最後に発生したエラーを取得した場合、error() メソッドが code と message 
を含んだ配列を返してくれます。こちらが簡単な例
です::

	if ( ! $this->db->simple_query('SELECT `example_field` FROM `example_table`'))
	{
		$error = $this->db->error(); // Has keys 'code' and 'message'
	}

