############
XML ヘルパー
############

XML ヘルパーファイルは、 XML データを処理するのに役立つ関数で
構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします:

::

	$this->load->helper('xml');

利用できる機能
==============

次の関数が利用できます:

.. php:function:: xml_convert($str[, $protect_all = FALSE])

	:param string $str: 変換する文字列
	:param bool $protect_all: 数値文字エンティティ(例: &foo;)のようなエンティティのように見える可能性のあるコンテンツを全てそのまま出力するかどうか
	:returns: XML 変換文字列
	:rtype:	string

	文字列を入力とし、次の XML の予約文字を XML
	エンティティに変換します:

	  - アンパサンド: &
	  - 不等号: < >
	  - シングルクォーテーションとダブルクォーテーション: ' "
	  - ダッシュ(ハイフン): -

	数値文字エンティティを一部含む場合、アンパサンドを無視します。
	例: &#123; 例::

		$string = '<p>Here is a paragraph & an entity (&#123;).</p>';
		$string = xml_convert($string);
		echo $string;

	outputs:

	.. code-block:: html

		&lt;p&gt;Here is a paragraph &amp; an entity (&#123;).&lt;/p&gt;
