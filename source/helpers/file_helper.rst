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

	:param	string	$file: File path
	:returns:	File contents or FALSE on failure
	:rtype:	string

	パスで特定されたファイルのデータを返します。

	例::

		$string = read_file('./path/to/file.php');

	パスは、サーバの相対パスかフルパスで指定します。読み込みに失敗した場合は FALSE (ブール値) を返します。

	.. note:: パスは、メインの index.php ファイルからの相対パスで, コントローラファイルやビューファイルからの相対パスではありません。 CodeIgniter 
		はフロントコントローラを使用するので、常にサイトのインデックスからの
		相対パスになります。

	.. note:: This function is DEPRECATED. Use the native ``file_get_contents()``
		instead.

	.. important:: サーバで **open_basedir** の制限が有効になっている場合、 呼び出したスクリプト
		より上の階層にあるファイルにアクセスしようとするとき、このメソッド
		は動作しないかもしれません。

.. php:function:: write_file($path, $data[, $mode = 'wb'])

	:param	string	$path: File path
	:param	string	$data: Data to write to file
	:param	string	$mode: ``fopen()`` mode
	:returns:	TRUE if the write was successful, FALSE in case of an error
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
			echo 'ファイルが書き込まれました!';
		}

	第3引数に書き込みモードをオプションで指定できます::

		write_file('./path/to/file.php', $data, 'r+');

	デフォルトのモードは、 wb です。指定できるモードについては、 `PHP
	ユーザガイド <http://www.php.net/manual/ja/function.fopen.php>`_ をご覧ください。

	.. note: この関数でファイルにデータを書き込めるようにするには、ファイルのパーミッションを書き込み可能なように
		(666、777、など)に設定しなければなりません。 ファイルがまだ存在しない
		場合は、保存先のディレクトリが書き込み可能でなければなりません。

	.. note:: パスは、メインの index.php ファイルからの相対パスで、コントローラファイルやビューファイルからの相対パスではありません。 CodeIgniter 
		はフロントコントローラを使用するので、常にサイトのインデックスからの
		相対パスになります。

	.. note:: This function acquires an exclusive lock on the file while writing to it.

.. php:function:: delete_files($path[, $del_dir = FALSE[, $htdocs = FALSE]])

	:param	string	$path: Directory path
	:param	bool	$del_dir: Whether to also delete directories
	:param	bool	$htdocs: Whether to skip deleting .htaccess and index page files
	:returns:	TRUE on success, FALSE in case of an error
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

	:param	string	$source_dir: Directory path
	:param	bool	$include_path: Whether to include the path as part of the filenames
	:returns:	An array of file names
	:rtype:	array

	サーバパスを入力として、そのパスに含まれる全ファイル名の配列を返します。
	オプションで、第2引数を TRUE に設定すると、
	ファイルのパスがファイル名に付加されます。

	例::

		$controllers = get_filenames(APPPATH.'controllers/');

.. php:function:: get_dir_file_info($source_dir, $top_level_only)

	:param	string	$source_dir: Directory path
	:param	bool	$top_level_only: Whether to look only at the specified directory (excluding sub-directories)
	:returns:	An array containing info on the supplied directory's contents
	:rtype:	array

	指定されたディレクトリを読み、ファイル名、ファイルサイズ、
	日付、パーミッションから成る配列を作ります。指定ファイル以下のサブフォルダは、第 2 引数を FALSE
	に指定した場合のみすべて同様に
	読まれます。

	例::

		$models_info = get_dir_file_info(APPPATH.'models/');

.. php:function:: get_file_info($file[, $returned_values = array('name', 'server_path', 'size', 'date')])

	:param	string	$file: File path
	:param	array	$returned_values: What type of info to return
	:returns:	An array containing info on the specified file or FALSE on failure
	:rtype:	array

	Given a file and path, returns (optionally) the *name*, *path*, *size* and *date modified*
	information attributes for a file. Second parameter allows you to explicitly declare what
	information you want returned.

	Valid ``$returned_values`` options are: `name`, `size`, `date`, `readable`, `writeable`,
	`executable` and `fileperms`.

.. php:function:: get_mime_by_extension($filename)

	:param	string	$filename: File name
	:returns:	MIME type string or FALSE on failure
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

	:param	int	$perms: Permissions
	:returns:	Symbolic permissions string
	:rtype:	string

	( ``fileperms()`` で返ってくるような) 数字のパーミッションを引数として渡すと、
	文字列のファイルパーミッションを返します。

	::

		echo symbolic_permissions(fileperms('./index.php'));  // -rw-r--r--

.. php:function:: octal_permissions($perms)

	:param	int	$perms: Permissions
	:returns:	Octal permissions string
	:rtype:	string

	( ``fileperms()`` で返ってくるような) 数字のパーミッションを引数として渡すと、
	8進数3文字のファイルパーミッションを返します。

	::

		echo octal_permissions(fileperms('./index.php')); // 644
