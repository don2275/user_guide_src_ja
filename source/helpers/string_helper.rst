##############
文字列ヘルパー
##############

文字列ヘルパーファイルは、文字列を処理するのに役立つ関数で
構成されています。

.. important:: これらの関数は、いかなる種類のセキュリティ関連のロジックのために
	使われるのに意図されたものではなく、適切ではないことに注意してください。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('string');

利用できる機能
===================

次の関数が利用できます:


.. php:function:: random_string([$type = 'alnum'[, $len = 8]])

	:param	string	$type: ランダム化の種類
	:param	int	$len: 出力する文字の長さ
	:returns:	ランダムな文字列
	:rtype:	string

	指定したタイプとサイズのランダムな文字列を生成します。パスワードを作成
	したり、ランダムなハッシュ値を生成したりするのに役立ちます。

	第1引数には文字列のタイプを指定し、第2引数には文字列のサイズ (文字数)
	を指定します。次の選択肢が使えます:

	-  **alpha**: 大文字小文字の文字列。
	-  **alnum**: 大文字小文字の英数字。
	-  **basic**: ``mt_rand()`` に基づいた乱数。
	-  **numeric**: 数字文字列。
	-  **nozero**: ゼロ以外の数字文字列。
	-  **md5**: ``md5()`` に基づいた暗号化された乱数(固定長32文字)。
	-  **sha1**: ``sha1()`` に基づいた暗号化された乱数(固定長40文字)。

	使用例::

		echo random_string('alnum', 16);

	.. note:: *unique* と *encrypt* は 非推奨です。 それらは、それぞれ
		*md5* と *sha1* の別名となっています。

.. php:function:: increment_string($str[, $separator = '_'[, $first = 1]])

	:param	string	$str: 入力文字列
	:param	string	$separator: 番号を増加し追記するためのセパレータ
	:param	int	$first: 開始番号
	:returns:	番号を増加させた文字列
	:rtype:	string

	番号を付加する、または増やすことで、文字列を増加させます。 "コピー" または
	ファイル、ユニークなタイトルかスラッグを持たせたデータベースの
	コンテンツの複製を作成するのに便利です。

	使用例::

		echo increment_string('file', '_'); // "file_1"
		echo increment_string('file', '-', 2); // "file-2"
		echo increment_string('file_4'); // "file_5"


.. php:function:: alternator($args)

	:param	mixed	$args: 可変長引数
	:returns:	いずれかの文字列
	:rtype:	mixed

	ループの中でローテーションで使うように、2つ以上の項目を入れ替え可能
	にします。例::

		for ($i = 0; $i < 10; $i++)
		{     
			echo alternator('string one', 'string two');
		}

	必要な数だけ追加でき、
	各ループで繰り返す度に次の項目が返ります。

	::

		for ($i = 0; $i < 10; $i++)
		{     
			echo alternator('one', 'two', 'three', 'four', 'five');
		}

	.. note:: この関数を複数回に分けて呼び出すとき、再度初期化するために、
		一度、引数なしでこの関数を呼び出してください。

.. php:function:: repeater($data[, $num = 1])

	:param	string	$data: 入力文字列
	:param	int	$num: 繰り返す回数
	:returns:	繰り返された文字列
	:rtype:	string

	渡したデータのコピーを繰り返し生成します。例::

		$string = "\n";
		echo repeater($string, 30);

	上の例では、30の新しい行が生成されます。

	.. note:: この関数は非推奨です。 ネイティブの ``str_repeat()`` を
		代わりに使用してください。


.. php:function:: reduce_double_slashes($str)

	:param	string	$str: 入力文字列
	:returns:	複数スラッシュを正常化した文字列
	:rtype:	string

	URL プロトコルのプレフィックス(例 http&#58;//) を除いて、
	文字列中のダブルスラッシュをシングルスラッシュに変換します。

	例::

		$string = "http://example.com//index.php";
		echo reduce_double_slashes($string); // "http://example.com/index.php" を返します


.. php:function:: strip_slashes($data)

	:param	mixed	$data: 入力文字列または文字列の配列
	:returns:	スラッシュを除去した文字列
	:rtype:	mixed

	文字列の配列からスラッシュを除去します。

	例::

		$str = array(
			'question'  => 'Is your name O\'reilly?',
			'answer' => 'No, my name is O\'connor.'
		);

		$str = strip_slashes($str);

	上の例では以下の配列が返ります。::

		array(
			'question'  => "Is your name O'reilly?",
			'answer' => "No, my name is O'connor."
		);

	.. note:: 歴史的な理由で、この関数は文字列の入力を受け入れ扱います。
		しかし、この使い方は、単に ``stripslashes()`` のエイリアスと
		見なせます。

.. php:function:: trim_slashes($str)

	:param	string	$str: 入力文字列
	:returns:	スラッシュをトリムした文字列
	:rtype:	string

	文字列から、先頭と末尾のスラッシュを除去します。例::

		$string = "/this/that/theother/";
		echo trim_slashes($string); // this/that/theother 返します

	.. note:: この関数は非推奨です。ネイティブの ``trim()`` を代わりに使ってください。
		|
		| trim($str, '/');

.. php:function:: reduce_multiples($str[, $character = ''[, $trim = FALSE]])

	:param	string	$str: 検索対象文字列
	:param	string	$character: 縮約文字
	:param	bool	$trim: 先頭と末尾の縮約文字を削除するかどうか
	:returns:	縮約した文字列
	:rtype:	string

	連続した文字を縮約します。
	例::

		$string = "Fred, Bill,, Joe, Jimmy";
		$string = reduce_multiples($string,","); //"Fred, Bill, Joe, Jimmy" を返します

	第3引数に TRUE を指定した場合は、先頭と末尾にある
	縮約文字が削除されます。例::

		$string = ",Fred, Bill,, Joe, Jimmy,";
		$string = reduce_multiples($string, ", ", TRUE); //"Fred, Bill, Joe, Jimmy" を返します

.. php:function:: quotes_to_entities($str)

	:param	string	$str: 入力文字列
	:returns:	HTML エンティティに変換したクォートを含む文字列
	:rtype:	string

	文字列の中のシングルおよびダブルクオートを対応する HTML 文字参照に変換します。
	例::

		$string = "Joe's \"dinner\"";
		$string = quotes_to_entities($string); //"Joe&#39;s &quot;dinner&quot;" を返します


.. php:function:: strip_quotes($str)

	:param	string	$str: 入力文字列
	:returns:	クォートを除去した文字列
	:rtype:	string

	文字列からシングルおよびダブルクオートを除去します。例::

		$string = "Joe's \"dinner\"";
		$string = strip_quotes($string); //"Joes dinner" を返します