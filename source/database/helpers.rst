######################
クエリヘルパーメソッド
######################

クエリ実行時の情報
======================

**$this->db->insert_id()**

データベースに行を挿入したときに、挿入した ID 番号を取得します。

.. note:: PostgreSQL で PDO ドライバを使用する場合、この関数は、
	行を挿入した ID 番号を調べるための適切なシーケンスを指定する $name 
	引数が必要です。

**$this->db->affected_rows()**

「書き込み」タイプのクエリ（insert、update、など）
が実行されたとき、処理された行の数を取得します。

.. note:: MySQLでは、"DELETE FROM TABLE" を実行すると0行と返します。
	データベースクラスには、処理した行を訂正して返せるよう、小さな hack 
	が用意されています。デフォルトではこの hack が有効になっていますが、
	データベースドライバファイルの中でオフにすることもできます。

**$this->db->last_query()**

最後に実行されたクエリを返します
（クエリの文字列で、結果ではありません）。例::

	$str = $this->db->last_query();
	
	// 次のように生成されます:  SELECT * FROM sometable....


.. note:: データベース設定の **save_queries** を無効にするとこの
	関数は動作しません。

データベースの情報
======================

**$this->db->count_all()**

特定のテーブルの行数を数えられます。テーブル名を第1引数で渡します。
例::

	echo $this->db->count_all('my_table');
	
	// 25のような整数が返ります

**$this->db->platform()**

実行しているデータベースプラットフォーム(MySQL、MS SQL、Postgres
など…)を出力します::

	echo $this->db->platform();

**$this->db->version()**

実行しているデータベースのバージョンを出力します::

	echo $this->db->version();

クエリを簡単にする
====================

**$this->db->insert_string()**

このメソッドは、データベースにデータを新規追加するのを単純化します。
これは、SQL の INSERT ステートメントを正しくフォーマットして返します。例::

	$data = array('name' => $name, 'email' => $email, 'url' => $url);
	
	$str = $this->db->insert_string('table_name', $data);

第1引数はテーブル名で、第2引数は挿入するデータの連想配列です。上の例で
は、次のような文を生成します::

	INSERT INTO table_name (name, email, url) VALUES ('Rick', 'rick@example.com', 'example.com')

.. note:: 値は自動的にエスケープされ、安全なクエリが生成されます。

**$this->db->update_string()**

このメソッドは、データベースに更新書き込みをするのを単純化します。
これは、SQL の UPDATE ステートメントの文字列を正しくフォーマットして返します。例::

	$data = array('name' => $name, 'email' => $email, 'url' => $url);
	
	$where = "author_id = 1 AND status = 'active'";
	
	$str = $this->db->update_string('table_name', $data, $where);

第1引数はテーブル名で、第2引数は更新するデータの連想配列で、
第3引数は "WHERE" 句になります。上の例は次のような文を生成
します::

	 UPDATE table_name SET name = 'Rick', email = 'rick@example.com', url = 'example.com' WHERE author_id = 1 AND status = 'active'

.. note:: 値は自動的にエスケープされ、安全なクエリが生成されます。
