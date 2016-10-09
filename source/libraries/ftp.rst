##########
FTP クラス
##########

CodeIgniter の FTP クラスを使うと、リモートサーバにファイルを転送できます。
リモートにあるファイルは、移動、リネーム、そして削除も可能です。
また、FTP クラスには、FTP経由でローカルディレクトリをすべてリモートに
再作成する "ミラーリング"機能もあります。

.. note:: SFTP と SSL FTP プロトコルはサポートされていません。
          通常の FTPのみサポートされます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************
FTPクラスの機能
***************

クラスの初期化
==============

CodeIgniter の大部分のクラスと同様、FTP クラスは、コントローラの中で
$this->load->library メソッドを使って初期化します::

	$this->load->library('ftp');

一度読み込まれると、FTP オブジェクトは、次のようにして利用できます: $this->ftp

使用例
======

以下の例では、FTPサーバに対してコネクションが開かれ、ローカルファイルが ASCII
ローカルファイルが ASCII モードで読み取られてアップロードされます。
ファイルのパーミッションは755に設定します。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

	$this->ftp->close();

以下の例では、サーバからファイルのリストが取得されます。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$list = $this->ftp->list_files('/public_html/');

	print_r($list);

	$this->ftp->close();

以下の例では、ローカルディレクトリがサーバにミラーされます。
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	$this->ftp->close();

******************
クラスリファレンス
******************

.. php:class:: CI_FTP

	.. php:method:: connect([$config = array()])

		:param	array	$config: Connection values
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		FTP サーバに接続してログインします。
		接続の設定は、配列で渡すか設定ファイルに
		保管しておくことができます。

		以下は、手動で設定をセットする方法を示した例です::

			$this->load->library('ftp');

			$config['hostname'] = 'ftp.example.com';
			$config['username'] = 'your-username';
			$config['password'] = 'your-password';
			$config['port']     = 21;
			$config['passive']  = FALSE;
			$config['debug']    = TRUE;

			$this->ftp->connect($config);

		**設定ファイルでの FTP の設定**

		必要であれば、FTP の設定を設定ファイルに保管することもできます。
		単純に、 ftp.php という名前で新しいファイルを作成し、そのファイルに
		$config という名前の設定用配列を追加します。 *application/config/ftp.php* ファイルに
		保存すると、自動的にそれが使われます。

		**利用できる接続オプション**

		============== =============== =============================================================================
		選択肢         初期値          説明
		============== =============== =============================================================================
		**hostname**   n/a             FTP ホスト名（通常は次のようになります: ftp.example.com）
		**username**   n/a             FTP ユーザ名
		**password**   n/a             FTP パスワード
		**port**       21              FTP ポート番号
		**debug**      FALSE           TRUE/FALSE （ブール値）: デバッグ用にエラーメッセージを表示するかどうか
		**passive**    TRUE            TRUE/FALSE （ブール値）: PASSIVE モードを使用するかどうか
		============== =============== =============================================================================

	.. php:method:: upload($locpath, $rempath[, $mode = 'auto'[, $permissions = NULL]])

		:param	string	$locpath: Local file path
		:param	string	$rempath: Remote file path
		:param	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:param	int	$permissions: File permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		サーバにファイルをアップロードします。ローカルのパスとリモートのパス、オプションで、
		転送モードとパーミッションを設定します。
		例::

			$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

		If 'auto' mode is used it will base the mode on the file extension of the source file.

		If set, permissions have to be passed as an octal value.

	.. php:method:: download($rempath, $locpath[, $mode = 'auto'])

		:param	string	$rempath: Remote file path
		:param	string	$locpath: Local file path
		:param	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		サーバからファイルをダウンロードします。リモートとローカルのパスを指定
		する必要があります。オプションでモードを指定できます。 例::

			$this->ftp->download('/public_html/myfile.html', '/local/path/to/myfile.html', 'ascii');

		If 'auto' mode is used it will base the mode on the file extension of the source file.

		ダウンロードに成功しなかった場合は FALSE を返します。
		（ローカルファイルに対するパーミッションがなかった場合も含む）

	.. php:method:: rename($old_file, $new_file[, $move = FALSE])

		:param	string	$old_file: Old file name
		:param	string	$new_file: New file name
		:param	bool	$move: Whether a move is being performed
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ファイルをリネームします。変更するファイルの名前/パスと、新しいファイルの名前/パスを指定します。
		::

			// green.html を blue.html にリネームします。
			$this->ftp->rename('/public_html/foo/green.html', '/public_html/foo/blue.html');

	.. php:method:: move($old_file, $new_file)

		:param	string	$old_file: Old file name
		:param	string	$new_file: New file name
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ファイルを移動できます。移動元と移動先のパスを指定します::

			// blog.html を "joe" から "fred" に移動します。
			$this->ftp->move('/public_html/joe/blog.html', '/public_html/fred/blog.html');

		.. note:: 移動先のファイル名が元と違う場合はリネームされます。

	.. php:method:: delete_file($filepath)

		:param	string	$filepath: Path to file to delete
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ファイルを削除できます。削除するファイルのパスとファイル名を指定します。
		::

			 $this->ftp->delete_file('/public_html/joe/blog.html');

	.. php:method:: delete_dir($filepath)

		:param	string	$filepath: Path to directory to delete
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ディレクトリとそのディレクトリに含まれるものをすべて削除します。
		削除するディレクトリへのパスを末尾にスラッシュをつけて指定します。

		.. important:: Be VERY careful with this method!
			It will recursively delete **everything** within the supplied path,
			including sub-folders and all files. Make absolutely sure your path
			is correct. Try using ``list_files()`` first to verify that your path is correct.

		::

			 $this->ftp->delete_dir('/public_html/path/to/folder/');

	.. php:method:: list_files([$path = '.'])

		:param	string	$path: Directory path
		:returns:	An array list of files or FALSE on failure
		:rtype:	array

		サーバにあるファイルのリストを取得して 配列 として返します。
		取得したいディレクトリへのパスを指定する必要があります。
		::

			$list = $this->ftp->list_files('/public_html/');
			print_r($list);

	.. php:method:: mirror($locpath, $rempath)

		:param	string	$locpath: Local path
		:param	string	$rempath: Remote path
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ローカルフォルダ内のすべて(サブフォルダ含む)を再帰的に読み取って、
		FTP 経由で読み取ったもののミラーを作成します。
		元のファイルパスのディレクトリ構造がサーバに再作成されます。
		作成元のパスと作成先のパスを指定する必要があります::

			 $this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	.. php:method:: mkdir($path[, $permissions = NULL])

		:param	string	$path: Path to directory to create
		:param	int	$permissions: Permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		サーバにディレクトリを作成できます。作成したいフォルダ名を末尾にスラッ
		シュをつけて指定します。

		パーミッションは、 8進数の値で第2引数に指定できます。
		::

			// "bar"という名前のフォルダを作成します。
			$this->ftp->mkdir('/public_html/foo/bar/', 0755);

	.. php:method:: chmod($path, $perm)

		:param	string	$path: Path to alter permissions for
		:param	int	$perm: Permissions (octal)
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		ファイルのパーミッションをセットできます。パーミッションを設定したいファイルまたは
		ディレクトリのパスを指定します::

			// "bar" に755のパーミッションを設定します。
			$this->ftp->chmod('/public_html/foo/bar/', 0755);

	.. php:method:: changedir($path[, $suppress_debug = FALSE])

		:param	string	$path: Directory path
		:param	bool	$suppress_debug: Whether to turn off debug messages for this command
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Changes the current working directory to the specified path.

		The ``$suppress_debug`` parameter is useful in case you want to use this method
		as an ``is_dir()`` alternative for FTP.

	.. php:method:: close()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		サーバとのコネクションを切断します。アップロードが終わったら、
		このメソッドを使うのをおすすめします。
