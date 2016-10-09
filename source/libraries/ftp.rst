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

以下の例では、FTPサーバに対してコネクションが開かれ、
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

		:param	array	$config: 接続情報
		:returns:	成功時は TRUE 、 失敗時は FALSE
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

		:param	string	$locpath: ローカルのファイルパス
		:param	string	$rempath: リモートのファイルパス
		:param	string	$mode: FTP モード、 デフォルトは 'auto' （'auto' 、 'binary' 、 'ascii' を指定する）
		:param	int	$permissions: File パーミッション（8進数）
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		サーバにファイルをアップロードします。ローカルのパスとリモートのパス、オプションで、
		転送モードとパーミッションを設定します。
		例::

			$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

		'auto' モードが使用されているときは、転送するファイルの拡張子によって転送モードが決められます。

		パーミッションを設定する場合は8進数の値を渡さなければなりません。

	.. php:method:: download($rempath, $locpath[, $mode = 'auto'])

		:param	string	$rempath: リモートのファイルパス
		:param	string	$locpath: ローカルのファイルパス
		:param	string	$mode: FTP モード、 デフォルトは 'auto' （'auto' 、 'binary' 、 'ascii' を指定する）
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		サーバからファイルをダウンロードします。リモートとローカルのパスを指定
		する必要があります。オプションでモードを指定できます。 例::

			$this->ftp->download('/public_html/myfile.html', '/local/path/to/myfile.html', 'ascii');

		'auto' モードが使用されているときは、転送するファイルの拡張子によって転送モードが決められます。

		ダウンロードに成功しなかった場合は FALSE を返します。
		（ローカルファイルに対するパーミッションがなかった場合も含む）

	.. php:method:: rename($old_file, $new_file[, $move = FALSE])

		:param	string	$old_file: 変更するファイルの名前
		:param	string	$new_file: 新しいファイルの名前
		:param	bool	$move: move を行うかどうか
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ファイルをリネームします。変更するファイルの名前/パスと、新しいファイルの名前/パスを指定します。
		::

			// green.html を blue.html にリネームします。
			$this->ftp->rename('/public_html/foo/green.html', '/public_html/foo/blue.html');

	.. php:method:: move($old_file, $new_file)

		:param	string	$old_file: 変更するファイルの名前
		:param	string	$new_file: 新しいファイルの名前
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ファイルを移動できます。移動元と移動先のパスを指定します::

			// blog.html を "joe" から "fred" に移動します。
			$this->ftp->move('/public_html/joe/blog.html', '/public_html/fred/blog.html');

		.. note:: 移動先のファイル名が元と違う場合はリネームされます。

	.. php:method:: delete_file($filepath)

		:param	string	$filepath: 削除するファイルのパス
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ファイルを削除できます。削除するファイルのパスとファイル名を指定します。
		::

			 $this->ftp->delete_file('/public_html/joe/blog.html');

	.. php:method:: delete_dir($filepath)

		:param	string	$filepath: 削除するディレクトリのパス
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ディレクトリとそのディレクトリに含まれるものをすべて削除します。
		削除するディレクトリへのパスを末尾にスラッシュをつけて指定します。

		.. important:: このメソッドを使うときは、「厳重に」注意してください！！
			渡されたパス以下のサブフォルダと全ファイルの **すべてのもの** を再帰的に削除します。
			パスが完全に正しいかを確認するようにしてください。
			``list_files()`` メソッドをまず使って、パスが正しいかを検証するようにしてください。

		::

			 $this->ftp->delete_dir('/public_html/path/to/folder/');

	.. php:method:: list_files([$path = '.'])

		:param	string	$path: ディレクトリのパス
		:returns:	ファイルの配列、失敗時は FALSE
		:rtype:	array

		サーバにあるファイルのリストを取得して 配列 として返します。
		取得したいディレクトリへのパスを指定する必要があります。
		::

			$list = $this->ftp->list_files('/public_html/');
			print_r($list);

	.. php:method:: mirror($locpath, $rempath)

		:param	string	$locpath: ローカルのパス
		:param	string	$rempath: リモートのパス
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ローカルフォルダ内のすべて(サブフォルダ含む)を再帰的に読み取って、
		FTP 経由で読み取ったもののミラーを作成します。
		元のファイルパスのディレクトリ構造がサーバに再作成されます。
		作成元のパスと作成先のパスを指定する必要があります::

			 $this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	.. php:method:: mkdir($path[, $permissions = NULL])

		:param	string	$path: 作成するディレクトリのパス
		:param	int	$permissions: パーミッション（8進数）
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		サーバにディレクトリを作成できます。作成したいフォルダ名を末尾にスラッ
		シュをつけて指定します。

		パーミッションは、 8進数の値で第2引数に指定できます。
		::

			// "bar"という名前のフォルダを作成します。
			$this->ftp->mkdir('/public_html/foo/bar/', 0755);

	.. php:method:: chmod($path, $perm)

		:param	string	$path: パーミッションを変更するファイルまたはディレクトリのパス
		:param	int	$perm: パーミッション（8進数）
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		ファイルのパーミッションをセットできます。パーミッションを設定したいファイルまたは
		ディレクトリのパスを指定します::

			// "bar" に755のパーミッションを設定します。
			$this->ftp->chmod('/public_html/foo/bar/', 0755);

	.. php:method:: changedir($path[, $suppress_debug = FALSE])

		:param	string	$path: ディレクトリのパス
		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		作業ディレクトリを指定したパスへ変更します。


	.. php:method:: close()

		:returns:	成功時は TRUE 、 失敗時は FALSE
		:rtype:	bool

		サーバとのコネクションを切断します。アップロードが終わったら、
		このメソッドを使うのをおすすめします。
