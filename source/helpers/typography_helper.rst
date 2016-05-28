########################
タイポグラフィーヘルパー
########################

タイポグラフィーヘルパーファイルは、テキストをセマンティックな方法で
整形するのに役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('typography');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: auto_typography($str[, $reduce_linebreaks = FALSE])

	:param	string	$str: 入力文字
	:param	bool	$reduce_linebreaks: 複数の重複した改行を2つにするかどうか
	:returns:	HTML フォーマットされた体裁が整った文字列
	:rtype: string

	意味論的にも文の体裁を整える面でも正しい HTML
	にテキストをフォーマットします。

	この関数は ``CI_Typography::auto_typography()`` のエイリアスです。
	より多くの情報を得るには、:doc:`タイポグラフィークラス<../libraries/typography>`
	を見てください。

	使用例::

		$string = auto_typography($string);

	.. note:: 特に、フォーマットしようとする大量のコンテンツがある場合、
		文の体裁を整える処理は負荷が高くなります。 この関数を使うようにした場合は、
		ページを :doc:`キャッシュする <../general/caching>`
		のを検討する必要があるかもしれません。


.. php:function:: nl2br_except_pre($str)

	:param	string	$str: 入力文字列
	:returns:	HTML フォーマットされた改行を含む文字列
	:rtype:	string

	<pre>タグの中でない改行を<br />タグに変換します。
	この関数は<pre>タグを無視しないという点を除いて、
	PHP に組み込みの ``nl2br()`` 関数と同じものです。

	使用例:

		$string = nl2br_except_pre($string);

.. php:function:: entity_decode($str, $charset = NULL)

	:param	string	$str: 入力文字列
	:param	string	$charset: 入力文字列の文字セット
	:returns:	エンティティデコードされた文字列
	:rtype:	string

	この関数は ``CI_Security::entity_decode()`` のエイリアスです。
	より多くの情報を得るには、:doc:`セキュリティクラス<../libraries/security>`
	を見てください。