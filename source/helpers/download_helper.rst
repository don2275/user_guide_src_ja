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

	:param	string	$filename: ファイル名
	:param	mixed	$data: ファイルのデータ
	:param	bool	$set_mime: 実際の MIME タイプを送信するかどうか
	:rtype:	void

	データを強制的にデスクトップにダウンロードさせるためのサーバヘッダを
	生成します。 ファイルのダウンロードで使えます。 
	第1引数には、ダウンロードファイルにつけたい名前を指定し、第2引数には、
	ファイルのデータを指定します。

	第2引数に NULL を設定している場合、 ``$filename`` が読込み可能であれば、ファイルのデータを
	読み出します。

	第3引数に TRUE を設定した場合、指定したファイルの 
	MIME タイプ(ファイル名の拡張子が指すものになります)が送信され、
	ブラウザはそのタイプのハンドラを使用します。

	例::

		$data = 'Here is some text!';
		$name = 'mytext.txt';
		force_download($name, $data);

	サーバに存在するファイルをダウンロードさせたい場合は次のようにする
	必要があります。::

		// photo.jpg のデータは自動的に読み出されます
		force_download('/path/to/photo.jpg', NULL);
