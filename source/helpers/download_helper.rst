####################
ダウンロードヘルパー
####################

ダウンロードヘルパーを使うと、データをデスクトップにダウンロードさせることができます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('download');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: force_download([$filename = ''[, $data = ''[, $set_mime = FALSE]]])

	:param	string	$filename: Filename
	:param	mixed	$data: File contents
	:param	bool	$set_mime: Whether to try to send the actual MIME type
	:rtype:	void

	データを強制的にデスクトップにダウンロードさせるためのサーバヘッダを
	生成します。 ファイルのダウンロードで使えます。 
	第1引数には、ダウンロードファイルにつけたい名前を指定し、第2引数には、
	ファイルのデータを指定します。

	If you set the second parameter to NULL and ``$filename`` is an existing, readable
	file path, then its content will be read instead.

	If you set the third parameter to boolean TRUE, then the actual file MIME type
	(based on the filename extension) will be sent, so that if your browser has a
	handler for that type - it can use it.

	Example::

		$data = 'Here is some text!';
		$name = 'mytext.txt';
		force_download($name, $data);

	If you want to download an existing file from your server you'll need to
	do the following::

		// Contents of photo.jpg will be automatically read
		force_download('/path/to/photo.jpg', NULL);
