################
語形変換ヘルパー
################

語形変換ヘルパーのファイルには **英語の** 語句を複数形、単数形、キャメル記法など
に変換できる関数が含まれます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
===================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('inflector');

利用できる機能
===================

次の関数が利用できます:


.. php:function:: singular($str)

	:param	string	$str: 入力文字列
	:returns:	単数形の単語
	:rtype:	string

	複数形の単語を単数形に変換します。例::

		echo singular('dogs'); // 'dog' を返します

.. php:function:: plural($str)

	:param	string	$str: 入力文字列
	:returns:	複数形の単語
	:rtype:	string

	単数形の単語を複数形に変換します。例::

		echo plural('dog'); // 'dogs' を返します

.. php:function:: camelize($str)

	:param	string	$str: 入力文字列
	:returns:	キャメル記法の文字列
	:rtype:	string

	スペースまたはアンダースコアで区切られた語句をキャメル記法に変換します。
	例::

		echo camelize('my_dog_spot'); // 'myDogSpot' を返します

.. php:function:: underscore($str)

	:param	string	$str: 入力文字列
	:returns:	スペースの代わりにアンダースコアを含んだ文字列
	:rtype:	string

	スペースで区切られた複数の単語を取り出して、アンダースコアでつなぎます。
	例::

		echo underscore('my dog spot'); // 'my_dog_spot' を返します

.. php:function:: humanize($str[, $separator = '_'])

	:param	string	$str: 入力文字列
	:param	string	$separator: 入力区切り文字
	:returns:	人が読み易い文字列
	:rtype:	string

	アンダースコアで区切られた複数の単語を取り出して、スペースでつなぎます。
	各単語は大文字ではじめられます。

	例::

		echo humanize('my_dog_spot'); // 'My Dog Spot'を返します

	アンダースコアの代わりにハイフンを使用する::

		echo humanize('my-dog-spot', '-'); // Prints 'My Dog Spot'

.. php:function:: is_countable($word)

	:param	string	$word: 入力文字列
	:returns:	単語が複数形であれば TRUE そうでない場合は FALSE
	:rtype:	bool

	与えられた単語が複数形かどうかをチェックします。例::

		is_countable('equipment'); // Returns FALSE
