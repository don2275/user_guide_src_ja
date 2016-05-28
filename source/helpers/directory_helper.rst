####################
ディレクトリヘルパー
####################

ディレクトリヘルパーファイルは、ディレクトリを処理するのに
役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします:

::

	$this->load->helper('directory');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: directory_map($source_dir[, $directory_depth = 0[, $hidden = FALSE]])

	:param	string	$source_dir: ソースディレクトリのパス
	:param	int	$directory_depth: 横断するディレクトリの深さ (0 = 全てのディレクトリを再帰的に行う、 1 = 現在のディレクトリ、その他)
	:param	bool	$hidden: 隠しディレクトリを含めるかどうか
	:returns:	ファイルの配列
	:rtype:	array

	例::

		$map = directory_map('./mydirectory/');

	.. note:: パスは、index.php ファイルからの相対パスになります。


	ディレクトリに含まれるサブフォルダも同様にマップされます。 第2引数
	(integer) で再帰的にマップされる階層を指定出来ます。1を指定すると
	最上位階層のディレクトリのみマッピングされます。::

		$map = directory_map('./mydirectory/', 1);

	デフォルトでは、戻り値の配列に隠しファイルは含まれません。
	この動作をオーバーライドするには、第3引数に true (ブール値)を指定します::

		$map = directory_map('./mydirectory/', FALSE, TRUE);

	各フォルダ名は、配列の添字になり、それらは、数字のインデックスがつけられた
	ファイルを含みます。 以下は、典型的な配列の例です::

		Array (
			[libraries] => Array
				(
					[0] => benchmark.html
					[1] => config.html
					["database/"] => Array
						(
							[0] => query_builder.html
							[1] => binds.html
							[2] => configuration.html
							[3] => connecting.html
							[4] => examples.html
							[5] => fields.html
							[6] => index.html
							[7] => queries.html
						)
					[2] => email.html
					[3] => file_uploading.html
					[4] => image_lib.html
					[5] => input.html
					[6] => language.html
					[7] => loader.html
					[8] => pagination.html
					[9] => uri.html
				)