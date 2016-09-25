################
ファイルヘルパー
################

ファイルヘルパーのファイルは、ファイルを処理するのに役立つ関数で構成されます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('file');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: read_file($file)

	:param	string	$file: ファイルパス
	:returns:	ファイルの内容または失敗時は FALSE
	:rtype:	string

	パスで特定されたファイルのデータを返します。

	例::

		$string = read_file('./path/to/file.php');

	パスは、サーバの相対パスかフルパスで指定します。読み込みに失敗した場合は FALSE（ブール値）を返します。

	.. note:: パスは、メインの index.php ファイルからの相対パスで, コントローラファイルやビューファイルからの相対パスではありません。 CodeIgniter 
		はフロントコントローラを使用するので、常にサイトのインデックスからの
		相対パスになります。

	.. note:: この関数は廃止予定です。
		かわりに ``file_get_contents()`` を使ってください。

	.. important:: サーバで **open_basedir** の制限が有効になっている場合、 呼び出したスクリプト
		より上の階層にあるファイルにアクセスしようとするとき、このメソッド
		は動作しないかもしれません。

.. php:function:: write_file($path, $data[, $mode = 'wb'])

	:param	string	$path: ファイルパス
	:param	string	$data: ファイルに書き込むデータ
	:param	string	$mode: ``fopen()`` モード
	:returns:	書き込みに成功した場合は TRUE 、エラーの場合は FALSE
	:rtype:	bool

	パスで指定されたファイルにデータを書き込みます。 ファイルが存在しない場合には、
	このメソッドによってファイルが作成されます。

	例::

		$data = 'Some file data';
		if ( ! write_file('./path/to/file.php', $data))
		{     
			echo 'ファイルに書き込めません';
		}
		else
		{     
			echo 'ファイルが書き込まれました！';
		}

	第3引数に書き込みモードをオプションで指定できます::

		write_file('./path/to/file.php', $data, 'r+');

	デフォルトのモードは、 wb です。指定できるモードについては、 `PHP
	ユーザガイド <http://www.php.net/manual/ja/function.fopen.php>`_ をご覧ください。

	.. note: この関数でファイルにデータを書き込めるようにするには、ファイルのパーミッションを書き込み可能なように
		（666、777、など）に設定しなければなりません。 ファイルがまだ存在しない
		場合は、保存先のディレクトリが書き込み可能でなければなりません。

	.. note:: パスは、メインの index.php ファイルからの相対パスで、コントローラファイルやビューファイルからの相対パスではありません。 CodeIgniter 
		はフロントコントローラを使用するので、常にサイトのインデックスからの
		相対パスになります。

	.. note:: この関数はファイルへデータを書き込んでいる間排他ロックを取得します。

.. php:function:: delete_files($path[, $del_dir = FALSE[, $htdocs = FALSE]])

	:param	string	$path: ディレクトリパス
	:param	bool	$del_dir: ディレクトリも合わせて削除するかどうか
	:param	bool	$htdocs: .htaccess やインデックスファイルの削除をスキップするかどうか
	:returns:	成功した場合は TRUE 、失敗した場合は FALSE
	:rtype:	bool

	パスに含まれるすべてのファイルを削除します。

	例::

		delete_files('./path/to/directory/');

	第2引数を true にセットすると、指定したパスに含まれるいずれの
	ディレクトリも削除されます。

	例::

		delete_files('./path/to/directory/', TRUE);

	.. note:: 削除するには、ファイルを書き込み可能にするか、所有者をシステムにしてください。

.. php:function:: get_filenames($source_dir[, $include_path = FALSE])

	:param	string	$source_dir: ディレクトリパス
	:param	bool	$include_path: ファイルまでのパスを含めるかどうか
	:returns:	ファイル名の配列
	:rtype:	array

	サーバパスを入力として、そのパスに含まれる全ファイル名の配列を返します。
	オプションで、第2引数を TRUE に設定すると、
	ファイルのパスがファイル名に付加されます。

	例::

		$controllers = get_filenames(APPPATH.'controllers/');

.. php:function:: get_dir_file_info($source_dir, $top_level_only)

	:param	string	$source_dir: ディレクトリパス
	:param	bool	$top_level_only: 指定されたディレクトリのみを参照するかどうか (サブディレクトリを除外するということ)
	:returns:	指定されたディレクトリの情報が含まれた配列
	:rtype:	array

	指定されたディレクトリを読み、ファイル名、ファイルサイズ、
	日付、パーミッションから成る配列を作ります。指定ファイル以下のサブフォルダは、第 2 引数を FALSE
	に指定した場合のみすべて同様に
	読まれます。

	例::

		$models_info = get_dir_file_info(APPPATH.'models/');

.. php:function:: get_file_info($file[, $returned_values = array('name', 'server_path', 'size', 'date')])

	:param	string	$file: ファイルパス
	:param	array	$returned_values: 戻り値の情報の形式
	:returns:	指定されたファイルの情報が含まれた配列または失敗した場合 FALSE
	:rtype:	array

	ファイルとパスを引数に取り、*name* 、 *path* 、 *size* や *date modified* 等の
	ファイルの属性情報を返却(オプション)します。 第2引数は返却して欲しい情報を明示的に
	指定することが可能です。

	有効な ``$returned_values`` のオプション: `name` 、 `size` 、 `date` 、 `readable` 、 `writeable` 、
	`executable` や `fileperms`.

.. php:function:: get_mime_by_extension($filename)

	:param	string	$filename: ファイル名
	:returns:	MIME タイプまたは失敗した場合 FALSE
	:rtype:	string

	*config/mimes.php* にある設定を元にファイル拡張子を  MIMEタイプに変換します。
	タイプが分からないときや MIME 設定ファイルが開けなかったときは FALSE を返します。

	::

		$file = 'somefile.png';
		echo $file.' には以下のmimeタイプがついています '.get_mime_by_extension($file);

	.. note:: この方法は正確にファイルの MIME タイプを判別するものではなく、
		あくまで簡単に取得するためだけのものです。セキュリティ用には
		使わないでください。

.. php:function:: symbolic_permissions($perms)

	:param	int	$perms: パーミッション
	:returns:	シンボリックモードのパーミッション文字列
	:rtype:	string

	( ``fileperms()`` で返ってくるような) 数字のパーミッションを引数として渡すと、
	文字列のファイルパーミッションを返します。

	::

		echo symbolic_permissions(fileperms('./index.php'));  // -rw-r--r--

.. php:function:: octal_permissions($perms)

	:param	int	$perms: パーミッション
	:returns:	8進数表記のパーミッション文字列
	:rtype:	string

	( ``fileperms()`` で返ってくるような) 数字のパーミッションを引数として渡すと、
	8進数3文字のファイルパーミッションを返します。

	::

		echo octal_permissions(fileperms('./index.php')); // 644
