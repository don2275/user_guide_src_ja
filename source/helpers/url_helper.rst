############
URL ヘルパー
############

URL ヘルパーファイルは、URL を処理するのに役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('url');

利用できる機能
==============

次の関数が利用できます:

.. php:function:: site_url([$uri = ''[, $protocol = NULL]])

	:param	string $uri: URI 文字列
	:param	string	$protocol: プロトロコル, 例: 'http' or 'https'
	:returns:	サイトの URL
	:rtype:	string

	設定ファイルで指定されているサイトの URL を返します。 index.php
	ファイル (または、設定ファイルで設定しているユーザサイトの **index_page**
	) が URL に追加され、この関数に渡された URI
	セグメントと設定ファイルで指定された **url_suffix** が追加されます。

	ローカルの(サイト内の) URL を生成する必要がある時は、
	いつもこの関数を使うようおすすめします。
	この関数を使うと、URL が変更になった時でも、移植性が高まります。

	オプションで、セグメントを文字列または配列としてこの関数に渡すことがで
	きます。下記は文字列での例です::

		echo site_url('news/local/123');

	上の例では、次のようなものが返されます:
	*http://example.com/index.php/news/local/123*

	Here is an example of segments passed as an array::

		$segments = array('news', 'local', '123');
		echo site_url($segments);

	この関数は ``CI_Config::site_url()`` のエイリアスです。より多くの情報を
	得るには、:doc:`設定クラス <../libraries/config>` を見てください。

.. php:function:: base_url($uri = '', $protocol = NULL)

	:param	string	$uri: URI 文字列
	:param	string	$protocol: プロトロコル, 例: 'http' or 'https'
	:returns:	ベース URL
	:rtype:	string

	設定ファイルで指定されているサイトのベース URL を返します。例::

		echo base_url();

	この関数は *index_page* がないことと、また、 *url_suffix*
	が追加されること以外は :php:func:`site_url()` と同じ結果を返します。

	また :php:func:`site_url()` のように、セグメントを文字列または配列として、
	この関数に渡すことができます。下記は文字列での例です::

		echo base_url("blog/post/123");

	上の例では、次のようなものが返されます:
	*http://example.com/blog/post/123*

	この関数は :php:func:`site_url()` とは違い、画像やスタイルシートなどの
	ファイルへの文字列を渡すことができるのが便利です。例::

		echo base_url("images/icons/edit.png");

	上の例では、次のようなものが返されます:
	*http://example.com/images/icons/edit.png*

	この関数は ``CI_Config::base_url()`` のエイリアスです。より多くの情報を
	得るには、:doc:`設定クラス<../libraries/config>` を見てください。

.. php:function:: current_url()

	:returns:	現在の URL
	:rtype:	string

	現在表示されているページの完全な URL (セグメントを含む) を
	返します。

	.. note:: この関数の呼び出しは次のようなものと同じ意味になります。:
		|
		| site_url(uri_string());


.. php:function:: uri_string()

	:returns:	An URI string
	:rtype:	string

	この関数が呼び出されたページの URI セグメントを返します。たとえば、URL
	が以下のようなものであれば::

		http://some-site.com/blog/comments/123

	この関数は次のような値を返します::

		blog/comments/123

	この関数は ``CI_Config::uri_string()`` のエイリアスです。より多くの情報を
	得るには、:doc:`設定クラス<../libraries/config>` を見てください。


.. php:function:: index_page()

	:returns:	'index_page' value
	:rtype:	mixed

	設定ファイルで指定されているサイトの **index_page** ページを返します。
	例::

		echo index_page();

.. php:function:: anchor($uri = '', $title = '', $attributes = '')

	:param	string	$uri: URI 文字列
	:param	string	$title: アンカータイトル
	:param	mixed	$attributes: HTML 属性
	:returns:	HTML ハイパーリンク (アンカータグ)
	:rtype:	string

	サイトの URL にもとづいて、標準の HTML アンカーリンクを生成します。

	第1引数は、URL に追加したいセグメントを指定します。
	上の :php:func:`site_url()` 関数のように、文字列または、
	配列でセグメントを指定します。

	.. note:: アプリケーション内部のリンクを生成するときは、ベースURL(http&#58;//...) を含まないようにしてください。
		設定ファイルで指定されている情報から、ベース URL
		は自動的に追加されます。URL に追加したい URI
		セグメントだけを含めるようにしてください。

	第2引数は、リンクに指定したいテキストになります。空のままにしておくと
	、URL が使用されます。

	第3引数はリンクタグに追加したい属性のリストを指定できます。
	属性は、文字列または、
	連想配列で指定します。

	いくつか例を挙げます::

		echo anchor('news/local/123', 'My News', 'title="News title"');
		// Prints: <a href="http://example.com/index.php/news/local/123" title="News title">My News</a>

		echo anchor('news/local/123', 'My News', array('title' => 'The best news!'));
		// Prints: <a href="http://example.com/index.php/news/local/123" title="The best news!">My News</a>

		echo anchor('', 'Click here');
		// Prints: <a href="http://example.com">Click Here</a>


.. php:function:: anchor_popup($uri = '', $title = '', $attributes = FALSE)

	:param	string	$uri: URI 文字列
	:param	string	$title: アンカータイトル
	:param	mixed	$attributes: HTML 属性
	:returns:	ポップアップ ハイパーリンク
	:rtype:	string

	新しいウィンドで URL を開くこと以外は、 :php:func:`anchor()` 関数とほとんど同じです。
	ウィンドウの開き方をコントロールするために、JavaScript の window
	オブジェクトの属性(プロパティ)を第3引数で指定できます。 第3引数が設定
	されていない場合は、ユーザのブラウザの設定により新しいウィンドウを開き
	ます。

	下記は、属性を指定する場合の例です::

		$atts = array(
			'width'       => 800,
			'height'      => 600,
			'scrollbars'  => 'yes',
			'status'      => 'yes',
			'resizable'   => 'yes',
			'screenx'     => 0,
			'screeny'     => 0,
			'window_name' => '_blank'
		);

		echo anchor_popup('news/local/123', 'Click Me!', $atts);

	.. note:: 上の属性は、この関数の初期値になりますので、
		これと異なる値にしたい箇所を設定するだけで構いません。
		すべての属性に初期値を使用する場合は、
		単に第3引数に空の配列を渡してください:
		|
		| echo anchor_popup('news/local/123', 'Click Me!', array());

	.. note:: **window_name** は実際の属性ではありませんが、
		window name または window target のどちらかを受け入れる JavaScriptの `window.open() <http://www.w3schools.com/jsref/met_win_open.asp>`
		メソッドへの引数です。

	.. note:: 上の属性にないどのような属性も HTML 属性として
		アンカータグへパースされます。


.. php:function:: mailto($email, $title = '', $attributes = '')

	:param	string	$email: メールアドレス
	:param	string	$title: アンカータイトル
	:param	mixed	$attributes: HTML 属性
	:returns:	"mail to" ハイパーリンク
	:rtype:	string

	標準の HTML メールリンクを作成します。使用例::

		echo mailto('me@my-site.com', 'Click Here to Contact Me');

	上の :php:func:`anchor()` 関数のように、第3引数で
	属性を指定できます。::

		$attributes = array('title' => 'Mail me');
		echo mailto('me@my-site.com', 'Contact Me', $attributes);

.. php:function:: safe_mailto($email, $title = '', $attributes = '')

	:param	string	$email: メールアドレス
	:param	string	$title: アンカータイトル
	:param	mixed	$attributes: HTML 属性
	:returns:	スパムセーフな "mail to" ハイパーリンク
	:rtype:	string

	この関数は、スパムロボットにメールアドレスが収集されてしまうのを防ぐため、
	メールアドレスのリンクを JavaScript で書き出すために、
	序数を使った難読化バージョンの *mailto* タグを書き出します。この点を除いて、:php:func:`mailto()` 関数と同じです。

.. php:function:: auto_link($str, $type = 'both', $popup = FALSE)

	:param	string	$str: 入力文字列
	:param	string	$type: リンクタイプ ('email', 'url' or 'both')
	:param	bool	$popup: ポップアップリンクを生成するかどうか
	:returns:	リンク可能な文字列
	:rtype:	string

	自動で、文字列に含まれる URL とメールアドレスをリンクに変換します。
	例::

		$string = auto_link($string);

	第2引数は、URL とメールアドレスのどちらを変換するか、あるいは両方を変換するか
	を指定します。指定しない場合、デフォルトでは両方を変換するようになっています。
	Email リンクは上にあるように :php:func:`safe_mailto()` を使って
	エンコードされます。

	URL のみを変換する場合::

		$string = auto_link($string, 'url');

	メールアドレスのみを変換する場合::

		$string = auto_link($string, 'email');

	第3引数は、リンクを新しいウィンドウで開くかどうかを指定します。
	値は、TRUE または FALSE (ブール値) になります::

		$string = auto_link($string, 'both', TRUE);


.. php:function:: url_title($str, $separator = '-', $lowercase = FALSE)

	:param	string	$str: 入力文字列
	:param	string	$separator: 単語区切り
	:param	bool	$lowercase: 小文字に変換して出力するかどうか
	:returns:	URL フォーマットの文字列
	:rtype:	string

	入力として文字列をとり、人間にわかりやすい URL 文字列を生成します。
	これはたとえば、ブログを作成していたとして、その中で記事のタイトルを
	URL に使いたいときなどに役立ちます。例::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title);
		// Produces: Whats-wrong-with-CSS

	第2引数で、単語の区切りを決めます。初期状態では、ダッシュ ( "-" )
	が使用されます。次のオプションが選べます: **-**( dash ) または **_** ( underscore )

	例::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title, 'underscore');
		// Produces: Whats_wrong_with_CSS

	.. note:: 第2引数として'dash' や 'underscore' という古い使用法は
		非推奨になっています。

	第3引数で、文字列を強制的に小文字に変換するかどうかを決めます。
	デフォルトでは変換されません。次のオプション (ブール値) が選べます: TRUE / FALSE :

	例::

		$title = "What's wrong with CSS?";
		$url_title = url_title($title, 'underscore', TRUE);
		// Produces: whats_wrong_with_css


.. php:function:: prep_url($str = '')

	:param	string	$str: URL 文字列
	:returns:	プロトコル接頭辞を付与した URL 文字列
	:rtype:	string

	この関数は、与えられた URL の文字列にプロトロルがない場合に http&#58;//
	を追加します。

	次のように URL の文字列を渡します:

		$url = prep_url('example.com');


.. php:function:: redirect($uri = '', $method = 'auto', $code = NULL)

	:param	string	$uri: URI 文字列
	:param	string	$method: リダイレクトメソッド ('auto', 'location' or 'refresh')
	:param	string	$code: HTTP Response Code (通常 302 or 303)
	:returns:	void

	指定した URI に対して "ヘッダ リダイレクト" します。完全な URL
	(http://...) を指定しても生成されますが、
	ローカルのリンクなら単にコントローラのセグメントから指定します。URL
	は設定ファイルの値を元に生成されます。

	オプションの第2引数は、特定のリダイレクトメソッドを強制させること
	許可します。利用可能なメソッドは **auto**, **location** と **refresh** です。
	Location は高速ですが、 IIS サーバでは信頼性は低下します。
	初期状態は **auto** で、サーバー環境に基づいたメソッドを知的に選択しようと
	試みます。

	任意の第3引数は特定の HTTP Response Code を設定することを許可します。
	- 例えばこれは検索エンジンのために301リダイレクトを生成するために利用できます。
	デフォルトの Response Code は302です。第3引数は **location** *だけで*使え、
	*refresh* では使えません。例::

		if ($logged_in == FALSE)
		{      
			redirect('/login/form/');
		}

		// with 301 redirect
		redirect('/article/13', 'location', 301);

	.. note:: この関数はサーバのヘッダを利用するので、
		動作させるにはブラウザに何かを出力する前に、この関数を使う必要があります。

	.. note:: とても細かいヘッダのコントロールを行う場合は、:doc:`出力クラス<../libraries/output>` の
		``set_header()`` メソッドを使うべきです。

	.. note:: IIS ユーザへ: もし `Server` HTTP ヘッダを隠している場合、 *auto*
		メソッドは IIS であることを検知しません。その場合、明示的に
		**refresh** メソッドを使用することを推奨します。

	.. note:: **location** メソッドを利用する際、
		表示しているページが現在 POST 経由でアクセスされており HTTP/1.1 が
		利用されている場合、 *自動的に* 303 HTTP ステータスコードが選択されます。

	.. important:: この関数はスクリプトの実行を終了させます。