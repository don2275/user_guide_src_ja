############
言語ヘルパー
############

言語ヘルパーファイルは、
言語ファイルでの動作に役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('language');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: lang($line[, $for = ''[, $attributes = array()]])

	:param	string	$line: 言語文字列キー
	:param	string	$for: HTML "for" 属性 (作成するラベルの for に使用する要素の ID )
	:param	array	$attributes: 追加で指定する HTML 属性
	:returns:    	HTML フォーマットされた言語文字列のラベル
	:rtype:	string

	この関数は、ビューファイルで言語クラスの ``CI_Lang::line()`` 
	メソッドを呼び出すよりもより簡単な構文で
	ロードされた言語ファイルからのテキスト行を返します。

	例::

		echo lang('language_key', 'form_item_id', array('class' => 'myClass'));
		// 出力: <label for="form_item_id" class="myClass">Language line</label>
