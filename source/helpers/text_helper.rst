################
テキストヘルパー
################

テキストヘルパーのファイルは、テキストを処理するのに役立つ関数で構成さ
れています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('text');

利用できる機能
==============

次の関数が利用できます:

.. php:function:: word_limiter($str[, $limit = 100[, $end_char = '&#8230;']])

	:param	string	$str: 入力文字列
	:param	int	$limit: 表示する単語の上限数
	:param	string	$end_char: 終端文字 (通常省略記号(…) )
	:returns:	単語数で制限された文字列
	:rtype:	string

	指定した *単語* 数で、文字列を切り詰めます。[ 訳注: 英語のように単語の間がスペースで区切られている言語でだけ有効です。日本語では通常、正常に動作しません ]。例::

		$string = "Here is a nice text string consisting of eleven words.";
		$string = word_limiter($string, 4);
		// Returns:  Here is a nice

	第3引数はオプションで、文字列に付加するサフィックスを指定します。
	デフォルトでは、省略記号(...) が付加されます。


.. php:function:: character_limiter($str[, $n = 500[, $end_char = '&#8230;']])

	:param	string	$str: 入力文字列
	:param	int	$n: 文字数
	:param	string	$end_char: 終端文字 (通常省略記号(…) )
	:returns:	文字数で制限された文字列
	:rtype:	string

	指定した *文字* 数の位置で文字列を切り詰めます。単語の途中で切り詰められないようになっていますので、
	文字数は指定したもより少し増えたり減ったりします [ 訳注: 英語のように単語の間がスペースで区切られている言語で
	だけ有効です。日本語では通常、正常に動作しません ]。

	例::

		$string = "Here is a nice text string consisting of eleven words.";
		$string = character_limiter($string, 20);
		// Returns:  Here is a nice text string

	第3引数はオプションで、文字列に付加するサフィックスを指定します。
	デフォルトでは、省略記号(...) が付加されます。

	.. note:: 正確な文字数で切り詰めたいのであれば、
		以下の :php:func:`ellipsize()`  を見てください。

.. php:function:: ascii_to_entities($str)

	:param	string	$str: 入力文字列
	:returns:	ASCII 文字コードを HTML エンティティに変換した文字列
		string

	Web ページで使用されると問題を起こす拡張 ASCII 文字コードや MS
	ワードの文字を含む、ASCII の値を HTML の文字列エンティティに変換します。
	こうすることで、ブラウザ設定にかかわらずいつも表示させることができ、
	あるいは、データベースに確かな状態で [値を変更して元の情報を失ってしまうことなく] 保存することができます。
	サーバでサポートされる文字セットにいくらか依存しているので、すべてのケースで
	100%の信頼性はありません。 しかし、多くは、通常の範囲の外にある
	文字(たとえばアクセント記号)も正しく認識できます。

	例::

		$string = ascii_to_entities($string);

.. php:function::entities_to_ascii($str[, $all = TRUE])

	:param	string	$str: 入力文字列
	:param	bool	$all: Whether to convert unsafe entities as well
	:returns:	HTML エンティティを ASCII 文字コードに変換した文字列
	:rtype:	string

	この関数は :php:func:`ascii_to_entities()` と反対になります。
	文字列エンティティを ASCII に戻します。

.. php:function:: convert_accented_characters($str)

	:param	string	$str: 入力文字列
	:returns:	A string with accented characters converted
		string

	
	アクセント記号のついた ASCII 文字列を同等の ASCII 文字列に変換します。
	スタンダードな ASCII 文字列を使用しなければならないところ (例: URL) で英語以外の文字列が使われていた場合に役立ちます。

	例::

		$string = convert_accented_characters($string);

	.. note:: このメソッドは変換の際に、設定ファイル
		`application/config/foreign_chars.php` で定義されている
		配列を使用します。

.. php:function:: word_censor($str, $censored[, $replacement = ''])

	:param	string	$str: 入力文字列
	:param	array	$censored: 禁止したい単語の配列
	:param	string	$replacement: 禁止したい単語を置き換える文字列
	:returns:	検閲後の文字列
		string

	テキストの文字を検閲できます。第1引数は、元の文字列になります。
	第2引数は、許可しない単語の配列を指定します。第3引数(オプション)は、
	単語を置き換える文字列を指定します。
	指定されない場合は、許可されない文字は、
	ナンバーサインで置き換えられます: ####。

	例::

		$disallowed = array('darn', 'shucks', 'golly', 'phooey');
		$string = word_censor($string, $disallowed, 'Beep!');

.. php:function:: highlight_code($str)

	:param	string	$str: 入力文字列
	:returns:	HTML をハイライトしたコードを含む文字列
	:rtype:	string

	コード ( PHP 、 HTML など)の文字列を色分けします。例::

		$string = highlight_code($string);

	この関数は、PHP の ``highlight_string()`` 関数を使いますので、php.ini
	ファイルで指定されている色が使用されます。


.. php:function:: highlight_phrase($str, $phrase[, $tag_open = '<mark>'[, $tag_close = '</mark>']])

	:param	string	$str: 入力文字列
	:param	string	$phrase: ハイライトしたいフレーズ
	:param	string	$tag_open: ハイライトのための開始タグ
	:param	string	$tag_close: ハイライトのための終了タグ
	:returns:	HTML をハイライトしたフレーズを含む文字列
	:rtype:	string

	テキスト中のフレーズをハイライトします。 第1引数は元の文字列で、
	第2引数にハイライトしたいフレーズを指定します。
	第3、第4引数は、フレーズを囲みたい開始/終了タグを
	指定します。

	例::

		$string = "Here is a nice text string about nothing in particular.";
		echo highlight_phrase($string, "nice text", '<span style="color:#990000;">', '</span>');

	上のテキストは下記のように出力されます::

		Here is a <span style="color:#990000;">nice text</span> string about nothing in particular.

	.. note:: この関数はデフォルトで ``<strong>`` タグを使います。古いブラウザは
		新しい HTML5 の新しい mark タグをサポートしていない可能性があります。
		もしそのようなブラウザをサポートしたいのであれば、 スタイルシートの中に
		以下の CSS コードを書き加えることを推奨します。::

			mark {
				background: #ff0;
				color: #000;
			};

.. php:function:: word_wrap($str[, $charlim = 76])

	:param	string	$str: 入力文字列
	:param	int	$charlim: [ 改行の目安となる ] 文字の上限数
	:returns:	ワードラップされた文字列
	:rtype:	string

	指定した *文字* 数の位置で、テキストを折り返します。
	単語の途中で折り返されません [ 訳注: 英語のように単語の間がスペースで区切られている言語でだけ有効です。日本語では通常、正常に動作しません ]。

	例::

		$string = "Here is a simple string of text that will help us demonstrate this function.";
		echo word_wrap($string, 25);

		// Would produce:  
		// Here is a simple string
		// of text that will help us
		// demonstrate this
		// function.

.. php:function:: ellipsize($str, $max_length[, $position = 1[, $ellipsis = '&hellip;']])

	:param	string	$str: 入力文字列
	:param	int	$max_length: 文字列の上限数
	:param	mixed	$position: [ 省略記号との ]分割位置 (整数または小数)
	:param	string	$ellipsis: 省略記号(…)
	:returns:	省略された文字列
	:rtype:	string

	この関数は文字列からタグを取り除き、指定された最大長に分割して省略記号
	を挿入します。

	第1引数は省略する文字列、第2引数は最終的な文字列の長さ、
	第3引数は後半の文字列の開始位置を指定します。
	第3引数は、省略記号が現れる位置を0(左端)から1(右端)までの数値で指定します。
	たとえば、1を指定すると省略記号は文字列の右端、
	.5なら真ん中、0なら左端になります。

	オプションの第4引数では、省略記号を指定できます。デフォルトでは
	&hellip; が使用されます。

	例::

		$str = 'this_string_is_entirely_too_long_and_might_break_my_design.jpg';
		echo ellipsize($str, 32, .5);

	Produces::

		this_string_is_e&hellip;ak_my_design.jpg
