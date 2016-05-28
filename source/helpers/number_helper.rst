############
数字ヘルパー
############

数字ヘルパーのファイルは、数字データを扱う関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('number');

利用できる機能
==============

次の関数が利用できます:



.. php:function:: byte_format($num[, $precision = 1])

	:param	mixed	$num: バイト数
	:param	int	$precision: 小数点の桁数
	:returns:	フォーマットされたデータサイズの文字列
	:rtype:	string

	サイズに基づいて、バイト (bytes)としての数字をフォーマットして、
	適切な接尾語を加えます。 例::

		echo byte_format(456); // 456 Bytes を返します
		echo byte_format(4567); // 4.5 KB を返します
		echo byte_format(45678); // 44.6 KB を返します
		echo byte_format(456789); // 447.8 KB を返します
		echo byte_format(3456789); // 3.3 MB を返します
		echo byte_format(12345678912345); // 1.8 GB を返します
		echo byte_format(123456789123456789); // 11,228.3 TB を返します

	オプションの第2引数で、結果の精度を
	指定出来ます。::

		 echo byte_format(45678, 2); // 44.61 KB を返します

	.. note:: この関数によって生成されたテキストは次の言語ファイルにあります
		: *language/<your_lang>/number_lang.php*