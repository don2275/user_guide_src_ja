#############
HTML ヘルパー
#############

HTML ヘルパーのファイルは、HTML を処理するのに役立つ関数で
構成されています。

.. contents::
	:local:

.. raw:: html

	<div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('html');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: heading([$data = ''[, $h = '1'[, $attributes = '']]])

	:param	string	$data: 内容
	:param	string	$h: 見出しのレベル
	:param	mixed	$attributes: HTML 属性
	:returns:	HTML 見出しタグ
	:rtype:	string

	HTML 見出しタグを生成できます。第1引数にデータを、
	第2引数には見出しレベルを指定します。例::

		echo heading('Welcome!', 3);

	上記は次のように生成されます: <h3>Welcome!</h3>

	加えて、例えば HTML クラス、 ID 、またはインラインスタイルなどの属性を
	見出しタグに追加するために、第3引数は文字列または配列のどちらかを
	受け付けます。

		echo heading('Welcome!', 3, 'class="pink"');
		echo heading('How are you?', 4, array('id' => 'question', 'class' => 'green'));

	上記は次のように生成されます:

	.. code-block:: html

		<h3 class="pink">Welcome!<h3>
		<h4 id="question" class="green">How are you?</h4>

.. php:function:: img([$src = ''[, $index_page = FALSE[, $attributes = '']]])

	:param	string	$src: 画像ソースデータ
	:param	bool	$index_page: $src をルーティングされた URI 文字列として扱うかどうか
	:param	array	$attributes: HTML 属性
	:returns:	HTML 画像タグ
	:rtype:	string

	<img /> タグを生成できます。第1引数には
	画像のソースを指定します。 例::

		echo img('images/picture.jpg'); // 結果 <img src="http://site.com/images/picture.jpg" />

	*src* が ``$config['index_page']``  に指定されたページを
	アドレスに追加するかどうかという、TRUE/FALSE で指定する
	オプションの第2引数があります。
	おそらく、メディアコントローラを使用していた場合は、次のようになります::

		echo img('images/picture.jpg', TRUE); // 結果 <img src="http://site.com/index.php/images/picture.jpg" alt="" />

	また、すべての属性と値を完全に制御するために
	連想配列を ``img()`` 関数に渡すことができます。もし、*alt* 属性が
	与えられない場合、 CodeIgniter は空の文字列を出力します。

	例::

		$image_properties = array(
			'src' 	=> 'images/picture.jpg',
			'alt' 	=> 'Me, demonstrating how to eat 4 slices of pizza at one time',
			'class' => 'post_images',
			'width' => '200',
			'height'=> '200',
			'title' => 'That was quite a night',
			'rel' 	=> 'lightbox'
		);

		img($image_properties);
		// <img src="http://site.com/index.php/images/picture.jpg" alt="Me, demonstrating how to eat 4 slices of pizza at one time" class="post_images" width="200" height="200" title="That was quite a night" rel="lightbox" />

.. php:function:: link_tag([$href = ''[, $rel = 'stylesheet'[, $type = 'text/css'[, $title = ''[, $media = ''[, $index_page = FALSE]]]]]])

	:param	string	$href: リンクするもの
	:param	string	$rel: リンクの種類
	:param	string	$type: リンクドキュメントの種類
	:param	string	$title: リンクのタイトル
	:param	string	$media: メディアタイプ
	:param	bool	$index_page: $src をルーティングされた URI 文字列として扱うかどうか
	:returns:	HTML link タグ
	:rtype:	string

	<link /> タグを生成できます。これは、スタイルシートのリンクだけでなく
	他のリンクのためにも役立ちます。引数には *href* 、オプションで *rel* 、
	*type* 、 *title* 、 *media* 、*index_page* を指定できます。

	*index_page* は、 *href* が ``$config['index_page']``
	に指定されたページをアドレスに追加するかどうかという真偽値です。

	例::

		echo link_tag('css/mystyles.css');
		// 結果 <link href="http://site.com/css/mystyles.css" rel="stylesheet" type="text/css" />

	その他の例::

		echo link_tag('favicon.ico', 'shortcut icon', 'image/ico');
		// <link href="http://site.com/favicon.ico" rel="shortcut icon" type="image/ico" />

		echo link_tag('feed', 'alternate', 'application/rss+xml', 'My RSS Feed');
		// <link href="http://site.com/feed" rel="alternate" type="application/rss+xml" title="My RSS Feed" />

	また、すべての属性と値を完全に制御するために
	連想配列を ``link()`` 関数に渡すことができます::

		$link = array(
			'href'	=> 'css/printer.css',
			'rel'	=> 'stylesheet',
			'type'	=> 'text/css',
			'media'	=> 'print'
		);

		echo link_tag($link);
		// <link href="http://site.com/css/printer.css" rel="stylesheet" type="text/css" media="print" />


.. php:function:: ul($list[, $attributes = ''])

	:param	array	$list: リスト項目
	:param	array	$attributes: HTML 属性
	:returns:	HTML フォーマットされた順番なしのリスト
	:rtype:	string

	順番なし HTML リストを単純な配列または
	多次元配列から生成できます。例::

		$list = array(
			'red',
			'blue',
			'green',
			'yellow'
		);

		$attributes = array(
			'class'	=> 'boldlist',
			'id'	=> 'mylist'
		);

		echo ul($list, $attributes);

	上記のコードは以下を生成します:

	.. code-block:: html

		<ul class="boldlist" id="mylist">
			<li>red</li>
			<li>blue</li>
			<li>green</li>
			<li>yellow</li>
		</ul>

	多次元配列を使ったもう少し複雑な例です::

		$attributes = array(
			'class'	=> 'boldlist',
			'id'	=> 'mylist'
		);

		$list = array(
			'colors'  => array(
				'red',
				'blue',
				'green'
			),
			'shapes'  => array(
				'round',
				'square',
				'circles' => array(
					'ellipse',
					'oval',
					'sphere'
				)
			),
			'moods'  => array(
				'happy',
				'upset' => array(
					'defeated' => array(
						'dejected',
						'disheartened',
						'depressed'
					),
					'annoyed',
					'cross',
					'angry'
				)
			)
		);

		echo ul($list, $attributes);

	上記のコードは以下を生成します:

	.. code-block:: html

		<ul class="boldlist" id="mylist">
			<li>colors
				<ul>
					<li>red</li>
					<li>blue</li>
					<li>green</li>
				</ul>
			</li>
			<li>shapes
				<ul>
					<li>round</li>
					<li>suare</li>
					<li>circles
						<ul>
							<li>elipse</li>
							<li>oval</li>
							<li>sphere</li>
						</ul>
					</li>
				</ul>
			</li>
			<li>moods
				<ul>
					<li>happy</li>
					<li>upset
						<ul>
							<li>defeated
								<ul>
									<li>dejected</li>
									<li>disheartened</li>
									<li>depressed</li>
								</ul>
							</li>
							<li>annoyed</li>
							<li>cross</li>
							<li>angry</li>
						</ul>
					</li>
				</ul>
			</li>
		</ul>

.. php:function:: ol($list, $attributes = '')

	:param	array	$list: リスト項目
	:param	array	$attributes: HTML 属性
	:returns:	HTML フォーマットされた順番付きのリスト
	:rtype:	string

	<ul> のかわりに 順序付きリストのための <ol> タグ
	を生成するだけで、 :php:func:`ul()` と同一です。

.. php:function:: meta([$name = ''[, $content = ''[, $type = 'name'[, $newline = "\n"]]]])

	:param	string	$name: メタタグの名前
	:param	string	$content: メタタグの内容
	:param	string	$type: メタタグの種類
	:param	string	$newline: 改行文字
	:returns:	HTML メタタグ
	:rtype:	string

	メタタグの生成を手伝います。この関数には、文字列、または
	単純な配列、または多次元配列を渡す事が出来ます。

	例::

		echo meta('description', 'My Great site');
		// 生成するメタタグ:  <meta name="description" content="My Great Site" />

		echo meta('Content-type', 'text/html; charset=utf-8', 'equiv');
		// Note the third parameter.  Can be "charset", "http-equiv", "name" or "property"
		// 生成するメタタグ:  <meta http-equiv="Content-type" content="text/html; charset=utf-8" />

		echo meta(array('name' => 'robots', 'content' => 'no-cache'));
		// 生成するメタタグ:  <meta name="robots" content="no-cache" />

		$meta = array(
			array(
				'name' => 'robots',
				'content' => 'no-cache'
			),
			array(
				'name' => 'description',
				'content' => 'My Great Site'
			),
			array(
				'name' => 'keywords',
				'content' => 'love, passion, intrigue, deception'
			),
			array(
				'name' => 'robots',
				'content' => 'no-cache'
			),
			array(
				'name' => 'Content-Type',
				'type' => 'http-equiv',
				'content' => 'text/html; charset=utf-8'
			),
			array(
				'name' => 'UTF-8',
				'type' => 'charset'
			)
		);

		echo meta($meta);
		// 生成するメタタグ:
		// <meta name="robots" content="no-cache" />
		// <meta name="description" content="My Great Site" />
		// <meta name="keywords" content="love, passion, intrigue, deception" />
		// <meta name="robots" content="no-cache" />
		// <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
		// <meta charset="UTF-8" />


.. php:function:: doctype([$type = 'html5'])

	:param	string	$type: Doctype 名
	:returns:	HTML DocType タグ
	:rtype:	string

	DOCTYPE 宣言、または DTD 生成を手伝います。HTML 5 が
	デフォルトで使用されますが、多くの DOCTYPE が利用可能です。
	
	例::

		echo doctype(); // <!DOCTYPE html>

		echo doctype('html4-trans'); // <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

	以下は、DOCTYPE 選択肢の一覧です。これらは、
	application/config/doctypes.php で指定可能です。

	=============================== =================== ==================================================================================================================================================
	DOCTYPE                         引数                生成結果
	=============================== =================== ==================================================================================================================================================
	XHTML 1.1                       xhtml11             <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
	XHTML 1.0 Strict                xhtml1-strict       <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
	XHTML 1.0 Transitional          xhtml1-trans        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	XHTML 1.0 Frameset              xhtml1-frame        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
	XHTML Basic 1.1                 xhtml-basic11       <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML Basic 1.1//EN" "http://www.w3.org/TR/xhtml-basic/xhtml-basic11.dtd">
	HTML 5                          html5               <!DOCTYPE html>
	HTML 4 Strict                   html4-strict        <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
	HTML 4 Transitional             html4-trans         <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	HTML 4 Frameset                 html4-frame         <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
	MathML 1.01                     mathml1             <!DOCTYPE math SYSTEM "http://www.w3.org/Math/DTD/mathml1/mathml.dtd">
	MathML 2.0                      mathml2             <!DOCTYPE math PUBLIC "-//W3C//DTD MathML 2.0//EN" "http://www.w3.org/Math/DTD/mathml2/mathml2.dtd">
	SVG 1.0                         svg10               <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.0//EN" "http://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd">
	SVG 1.1 Full                    svg11               <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
	SVG 1.1 Basic                   svg11-basic         <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1 Basic//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11-basic.dtd">
	SVG 1.1 Tiny                    svg11-tiny          <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1 Tiny//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11-tiny.dtd">
	XHTML+MathML+SVG (XHTML host)   xhtml-math-svg-xh   <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1 plus MathML 2.0 plus SVG 1.1//EN" "http://www.w3.org/2002/04/xhtml-math-svg/xhtml-math-svg.dtd">
	XHTML+MathML+SVG (SVG host)     xhtml-math-svg-sh   <!DOCTYPE svg:svg PUBLIC "-//W3C//DTD XHTML 1.1 plus MathML 2.0 plus SVG 1.1//EN" "http://www.w3.org/2002/04/xhtml-math-svg/xhtml-math-svg.dtd">
	XHTML+RDFa 1.0                  xhtml-rdfa-1        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.0//EN" "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-1.dtd">
	XHTML+RDFa 1.1                  xhtml-rdfa-2        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.1//EN" "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-2.dtd">
	=============================== =================== ==================================================================================================================================================

.. php:function:: br([$count = 1])

	:param	int	$count: タグを繰り返す数
	:returns:	HTML 改行タグ
	:rtype:	string

	改行タグ（<br />）を指定した回数だけ生成します。
	例::

		echo br(3);

	上記は次のように生成されます:

	.. code-block:: html

		<br /><br /><br />

	.. note:: この関数は廃止予定です。かわりに ``<br />`` を組み合わせて
		PHP標準の ``str_repeat()`` を使ってください。

.. php:function:: nbs([$num = 1])

	:param	int	$num: 生成するスペースエンティティの数
	:returns:	改行なしスペース HTML エンティティを連続させたもの
	:rtype:	string

	改行なしスペース（&nbsp;）を指定した数だけ生成します。
	例::

		echo nbs(3);

	上記は次のように生成されます:

	.. code-block:: html

		&nbsp;&nbsp;&nbsp;

	.. note:: この関数は廃止予定です。かわりに ``&nbsp;`` を組み合わせて
		PHP標準の ``str_repeat()`` を使ってください。
