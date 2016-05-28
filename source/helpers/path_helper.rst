############
パスヘルパー
############

パスヘルパーのファイルは、サーバのファイルパスを操作する関数で構成され
ています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('path');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: set_realpath($path[, $check_existance = FALSE])

	:param	string	$path: パス
	:param	bool	$check_existance: パスが実在するかをチェックするかどうか
	:returns:	絶対パス
	:rtype:	string

	この関数はシンボリックリンクか相対的ディレクトリ構造になっていない
	サーバのパスを返します。オプションの2番目の引数は
	パスが存在しないとエラーを返します。

	例::

		$file = '/etc/php5/apache2/php.ini';
		echo set_realpath($file); // '/etc/php5/apache2/php.ini' を返します

		$non_existent_file = '/path/to/non-exist-file.txt';
		echo set_realpath($non_existent_file, TRUE);	// パスが解決できないとして error を返します
		echo set_realpath($non_existent_file, FALSE);	// '/path/to/non-exist-file.txt' を返します

		$directory = '/etc/php5';
		echo set_realpath($directory);	// '/etc/php5/' を返します

		$non_existent_directory = '/path/to/nowhere';
		echo set_realpath($non_existent_directory, TRUE);	// パスが解決できないとして error を返します
		echo set_realpath($non_existent_directory, FALSE);	// '/path/to/nowhere' を返します