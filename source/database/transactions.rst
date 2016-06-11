#################
トランザクション
#################

CodeIgniter の抽象データベースでは、トランザクションセーフなテーブルタイプ [ 訳注:
テーブルの管理方式がトランザクションをサポートするものという意味 ]
をサポートしているデータベースで トランザクションを利用することができます。 
MySQL では、より一般的な MyISAM ではなくInnoDB か BDB のテーブルタイプで
実行されている必要があります。他のほとんどのデータベースでは、トランザクションは
ネイティブにサポートされています。
トランザクションについてあまり知らない場合は、
利用中のデータベースのトランザクションについて学習するために、
良いオンラインリソースを探すことをおすすめします。

CodeIgniter のトランザクションに対するアプローチ
================================================

CodeIgniter のトランザクションに対するアプローチでは、ポピュラーなデー
タベースクラスである ADODB で使われている手順と大変よく似たものが採用
されています。 トランザクションの実行プロセスが非常に単純になるので、
このアプローチを採用しています。ほとんどの場合、それは2行のコードが必要になるだけです。

従来は、クエリを絶えず追跡し、クエリの成否にもとづいて、 コミット
するか ロールバック するかを決めなければならないため、
トランザクションの実装には多くの作業を必要としてきました。
これは、入れ子になったクエリでは特に邪魔になります。 対照的に、私たちは
自動的にそれら全部を実行するスマートなトランザクションシステムを
実装しました（実際のところ利点はないですが、やりたければ手動で
トランザクションを管理することもできます）。

トランザクションの実行
=======================

トランザクションを使ってクエリを実行するには、次のように
$this->db->trans_start() メソッドと $this->db->trans_complete()
メソッドを使います::

	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->query('AND YET ANOTHER QUERY...');
	$this->db->trans_complete();

start メソッドと complete
メソッドの間に、どれだけ多くのクエリを実行しても構いません。
各クエリの成否により、それら全体がコミットまたはロールバックされます。

厳密な（Strict）モード
======================

デフォルトでは CodeIgniter はすべてのトランザクションを Strict モード
で実行します。 Strict モードが有効な場合、複数のトランザクションのグル
ープを実行している場合、ひとつのグループの実行が失敗すると他のすべての
グループのトランザクションもロールバックされます。 Strict モードが無効
の場合、各グループは独立したものとして扱われ、ひとつのグループのトラン
ザクションが失敗しても他のグループに影響を与えません。
Strict モードは以下のようにして無効にします::

	$this->db->trans_strict(FALSE);

エラーの管理
===============

コミットが失敗した場合に、標準のエラーメッセージを見たい場合は、
config/database.php ファイルでエラー報告機能を有効化できます。
デバッグ機能が OFF の場合は、次のように自分でエラーを管理することもできます::

	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->trans_complete();
	
	if ($this->db->trans_status() === FALSE)
	{
		// generate an error... or use the log_message() function to log your error
	}

トランザクションの有効化
=========================

$this->db->trans_start() を使った瞬間にトランザクションが
自動的に有効になります。トランザクションを無効にしたい場合は 
$this->db->trans_off() を実行することで無効化できます::

	$this->db->trans_off();
	
	$this->db->trans_start();
	$this->db->query('AN SQL QUERY...');
	$this->db->trans_complete();

トランザクション機能が無効になっている場合、トランザクションなしにクエ
リが実行されたときのように、クエリは自動的にコミットされます。

テストモード
=============

オプションで、クエリが正しい結果になった場合でもロールバックされる
"テストモード" のトランザクションを実行することができます。
テストモードを使うには、 $this->db->trans_start() メソッドの第1引数に
TRUE を設定するだけです::

	$this->db->trans_start(TRUE); // Query will be rolled back
	$this->db->query('AN SQL QUERY...');
	$this->db->trans_complete();

トランザクションを手動で実行する
================================

手動でトランザクションを実行したい場合は、次のように行えます::

	$this->db->trans_begin();
	
	$this->db->query('AN SQL QUERY...');
	$this->db->query('ANOTHER QUERY...');
	$this->db->query('AND YET ANOTHER QUERY...');
	
	if ($this->db->trans_status() === FALSE)
	{
		$this->db->trans_rollback();
	}
	else
	{
		$this->db->trans_commit();
	}

.. note:: 手動でトランザクションを実行する場合は #this->db->trans_start() 
	**ではなく**、 $this->db->trans_begin() を使うようにしてください。
