##########
入力クラス
##########

入力クラスには2つの目的があります:

#. グローバルな入力データをセキュリティのために前処理します。
#. 入力データを取り出し、前処理するためのヘルパーメソッドを提供します。

.. note:: このクラスは、システムで自動的に初期化されるので、
	手動で初期化する必要はありません。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******************
入力フィルタリング
******************

セキュリティフィルタリング
==========================

セキュリティフィルタ機能は、新しい :doc:`コントローラ<../general/controllers>` が
起動されると自動的に呼び出されます。
フィルタ機能は、次のことを行います:

-  ``$config['allow_get_array']`` が FALSE に設定されている場合（デフォルトは TRUE ）、
   グローバルな GET配列($_GET) のデータを消去します。
-  register_globals が ON
   に設定されているときにセットされるグローバル変数は消去されます。
-  英数字 (と少数の他の文字) だけを許可するよう GET/POST/COOKIE
   配列のキーがフィルタリングされます。
-  XSS (クロスサイトスクリプティング攻撃) フィルタリングを提供します。
   この機能は、グローバルまたは、リクエストごとに有効化できます。
-  改行文字を ``PHP_EOL`` に統一します( UNIX ベースでのOSであれば \\n 、
   Windowsでは \\r\\n )。これは設定可能です。

XSS フィルタリング
==================

入力クラスではクロスサイトスクリプティング攻撃を防ぐ為、自動的に入力値
をフィルタする機能が備わっています。 *application/config/config.php* ファイルを
次のように設定することで POST または COOKIE
データを扱う際、自動的にフィルタを実行することができます
this::

	$config['global_xss_filtering'] = TRUE;

アプリケーション内で XSS フィルタリングを利用する方法については
:doc:`セキュリティクラス <security>` を参照してください。

.. important:: 'global_xss_filtering' を設定することは推奨されていません。
	後方互換の為にのみ維持されているものです。 XSS エスケープは *出力値* にのみ行われ、
	*入力値* には行われません！

*******************
Accessing form data
*******************

POST、GET、COOKIE あるいは SERVER データの使用
==========================================

CodeIgniter には、POST、GET、COOKIE あるいは SERVER
のデータを取得するためのヘルパーメソッドが備わっています。 
直接項目を取得する(例: ``$_POST['something']`` )のでなく、このクラスが
提供するメソッドを使う主な利点は、メソッドにより値がセットされているかチェックされ、
セットされていない場合は、 NULL を返すということです。 
このメソッドを利用すれば、項目が存在するかどうかをまずテストすることなく、便利にデータを使えます。 
つまり、通常は、次のようなコードになりますが::

	$something = isset($_POST['something']) ? $_POST['something'] : NULL;

CodeIgniter の組み込みメソッドを使うと、単純に次のようになります::

	$something = $this->input->post('something');

よく使用するメソッドは次の通りです:

-  ``$this->input->post()``
-  ``$this->input->get()``
-  ``$this->input->cookie()``
-  ``$this->input->server()``

php://input streamを使う
========================

PUT、DELETE、PATCH 及び他の特殊なリクエストメソッドを使用する場合、
リクエスト値は特殊な入力ストリーム経由で一度だけ読み出すことが出来ます。
これらの値を読み出すことは簡単ではありません。
``$_POST`` の配列は常に存在し、
POST データに一度だけアクセスしたかを気にすることなく、複数の値に
アクセスすることができるからです。

CodeIgniter は ``$raw_input_stream`` 変数を使用することで、
**php://input** ストリームからの
データをいつでも読み出すことができます::

	$this->input->raw_input_stream;

加えて、入力ストリームを $_POST のように
フォームエンコードされた値で取得したい場合は
``input_stream()`` を使用してください::

	$this->input->input_stream('key');

他の ``get()`` や ``post()`` と同様に、
リクエストされたデータが見つからない場合、 NULL を返却します。
リクエストデータに ``xss_clean()`` を適用するかどうかを制御したい場合は
第二引数にブール値を渡してください。::

	$this->input->input_stream('key', TRUE); // XSS Clean
	$this->input->input_stream('key', FALSE); // No XSS filter

.. note:: 読み込んだデータが PUT、 DELETE または PATCH かを知りたい場合
	``method()`` を使用してください。

******************
クラスリファレンス
******************

.. php:class:: CI_Input

	.. attribute:: $raw_input_stream
		
		php://input データから返却される読み取り専用の値。
		
		この変数は何度でも参照可能です。

	.. php:method:: post([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: POST パラメータの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	$_POST パラメータが指定されていない場合は NULL 、それ以外は $_POST パラメータの値
		:rtype:	mixed

		第1引数は、コレクションの中から探し出す POST
		された項目の名前になります::

			$this->input->post('some_data');

		このメソッドは、取り出そうとして見つからなかった場合、 NULL
		を返します。

		第二引数は XSSフィルタをデータに適用できるようにするオプションのパラメータです。
		第二引数にブール値の TRUE を設定するか ``$config['global_xss_filtering']``を
		ブール値の TRUE にすることで有効になります。
		::

			$this->input->post('some_data', TRUE);

		引数を指定せずに呼び出すことで、 POST されたすべての値を連想配列で返します。

		第1引数を NULL 、第2引数にブール値の TRUE を指定することで、POST
		されたすべての値を XSS フィルタに通すことができます。
		::

			$this->input->post(NULL, TRUE); // POST された値を XSS フィルタを通して返します
			$this->input->post(NULL, FALSE); // POST された値を XSS フィルタを通さずに返します

		複数の POST パラメータの配列を返却したい時は必要なキーを
		配列で渡してください。
		::

			$this->input->post(array('field1', 'field2'));

		ここではXSS フィルタリングを有効にしてパラメータを取得するために
		同じルールを適用し、第二引数のパラメータにブール値の TRUE を設定してください。
		::

			$this->input->post(array('field1', 'field2'), TRUE);

	.. php:method:: get([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: GET パラメータの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	$_GET パラメータが指定されていない場合はNULL、それ以外は $_GET パラメータの値
		:rtype:	mixed

		このメソッドは、get データを取り出すということ以外は、 ``post()`` メソッドと同じです
		::

			$this->input->get('some_data', TRUE);

		引数を指定せずに呼び出すことで、 GET されたすべての値を連想配列で返します。

		第1引数を NULL 、第2引数にブール値の TRUE を指定することで、
		GET されたすべての値を XSS フィルタに通すことができます。
		::

			$this->input->get(NULL, TRUE); // GET された値を XSS フィルタを通して返します
			$this->input->get(NULL, FALSE); // GET された値を XSS フィルタを通さずに返します

		複数の POST パラメータの配列を返却したい時は必要なキーを
		配列で渡してください。
		::

			$this->input->get(array('field1', 'field2'));

		ここではXSS フィルタリングを有効にしてパラメータを取得するために
		同じルールを適用し、第二引数のパラメータにブール値の TRUE を設定してください。
		::

			$this->input->get(array('field1', 'field2'), TRUE);

	.. php:method:: post_get($index[, $xss_clean = NULL])

		:param	string	$index: POST/GET パラメータの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	POST/GET の値があれば POST/GET の値、ない場合は NULL
		:rtype:	mixed

		このメソッドは組み合わされているだけで、``post()`` や ``get()`` と
		同様に動作します。 POST と GET の両方のストリームデータを探し、
		初めにPOST、その後に GET を探します。::

			$this->input->post_get('some_data', TRUE);

	.. php:method:: get_post($index[, $xss_clean = NULL])

		:param	string	$index: GET/POST のパラメータの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	GET/POST の値があれば POST/GET の値、ない場合は NULL
		:rtype:	mixed

		このメソッドは GET データを初めに探しにいく以外 ``post_get()`` と
		同じように動作します。

			$this->input->get_post('some_data', TRUE);

		.. note:: このメソッドは ``post_get()`` と同じように動作していました。しかし、
			CodeIgniter 3.0でこの動作は変更されました。

	.. php:method:: cookie([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: COOKIE の名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	$_COOKIE パラメータが指定されていない場合はNULL、それ以外は COOKIE の値
		:rtype:	mixed

		このメソッドは、クッキーデータを取り出すということ以外は、
		``post()`` や ``get()`` メソッドと同じです::

			$this->input->cookie('some_cookie');
			$this->input->cookie('some_cookie', TRUE); // with XSS filter

		複数のクッキーの配列を返却したい時は必要なキーを
		配列で渡してください。
		::

			$this->input->cookie(array('some_cookie', 'some_cookie2'));

		.. note:: doc:`Cookie Helper <../helpers/cookie_helper>`
			function :php:func:`get_cookie()` とは異なり、
			このメソッドは先頭に ``$config['cookie_prefix']`` に設定された値を付与しません。

	.. php:method:: server($index[, $xss_clean = NULL])

		:param	mixed	$index: 値の名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	$_SERVER の値があれば　$_SERVER の値、ない場合は NULL
		:rtype:	mixed

		このメソッドは、 SERVER データ(``$_SERVER``)を取り出すということ以外は、
		``post()`` 、 ``get()`` や ``cookie()`` と同じです:: 

			$this->input->server('some_data');

		複数の ``$_SERVER`` の配列を返却したい時は必要なキーを
		配列で渡してください。
		::

			$this->input->server(array('SERVER_PROTOCOL', 'REQUEST_URI'));

	.. php:method:: input_stream([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: キーの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	入力ストリームのパラメータが指定されていない場合は NULL、 それ以外は入力ストリームの値
		:rtype:	mixed

		このメソッドは *php://input* ストリームデータを取得する以外は
		``get()``、 ``post()`` および ``cookie()`` と同じです。

	.. php:method:: set_cookie($name = ''[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = FALSE[, $httponly = FALSE]]]]]]])

		:param	mixed	$name: クッキー名または配列のパラメータ
		:param	string	$value: クッキーの値
		:param	int	$expire: クッキーの有効期限の秒数
		:param	string	$domain: クッキーのドメイン
		:param	string	$path: クッキーのパス
		:param	string	$prefix: クッキーの値の接頭辞
		:param	bool	$secure: HTTPS 経由でのみクッキーを転送するかどうか
		:param	bool	$httponly: HTTP リクエストでクッキーのアクセスを可能とするかどうか ( JavaScript でアクセスさせるかどうか)
		:rtype:	void


		指定した値を含むクッキーをセットします。 クッキーをセットする為に
		このメソッドに情報を渡すには、2つの方法があります: 配列で渡す方法と
		個々のパラメータを渡す方法です:

		**配列で渡す方法**

		この方法では、第1引数に連想配列が
		渡されます::

			$cookie = array(
				'name'   => 'The Cookie Name',
				'value'  => 'The Value',
				'expire' => '86500',
				'domain' => '.some-domain.com',
				'path'   => '/',
				'prefix' => 'myprefix_',
				'secure' => TRUE
			);

			$this->input->set_cookie($cookie);

		**Notes**

		name と value のみが必須属性となりますクッキーを削除するには
		有効期限に空白をセットします。

		有効期限は現在時刻から数えた **秒数** で指定します。
		時刻を指定するのではなく、クッキーを *現在時刻* から
		何秒間有効かを秒数だけで指定します。 有効期限を 0 にセットすると、
		ブラウザが開いている間だけ、クッキーが有効になります。

		どのようにリクエストを受け付けたかにかかわらず、サイト全体で使うクッキーには、
		次のように **domain** にピリオドから始まる URL を追加してください:
		.your-domain.com

		パスは、メソッドがルートパスをセットするので通常は必要ありません。

		プリフィックスは、同一のサーバによってセットされたクッキーにおける
		名前の衝突を回避したい場合にのみ必要です。

		セキュアのブール値は、セキュアなクッキーを使用したい場合のみ TRUE
		にする必要があります。

		**個々のパラメータを渡す方法**

		希望であれば、個別のパラメータを使ってデータを渡してクッキーを
		セットすることができます::

			$this->input->set_cookie($name, $value, $expire, $domain, $path, $prefix, $secure);

	.. php:method:: ip_address()

		:returns:	アクセスしてきたIPアドレスまたはアドレスが正しくない場合は '0.0.0.0'
		:rtype:	string

		現在のユーザの IP アドレスを返します。
		アドレスが正しくない場合、このメソッドは、 '0.0.0.0' を返却します::

			echo $this->input->ip_address();

		.. important:: このメソッドは ``$config['proxy_ips']`` の設定値を考慮して、
			報告された HTTP_X_FORWARDED_FOR、HTTP_CLIENT_IP、 HTTP_X_CLIENT_IP または
			HTTP_X_CLUSTER_CLIENT_IP のアドレスを有効なIPアドレスとして返却します。

	.. php:method:: valid_ip($ip[, $which = ''])

		:param	string	$ip: IPアドレス
		:param	string	$which: IPプロトコル ('ipv4' または 'ipv6')
		:returns:	正常あれば TRUE、そうでない場合は FALSE
		:rtype:	bool

		入力値にIPアドレスを渡すとIPアドレスが有効かどうかを
		TRUE または FALSE (ブール値) で返却します。

		.. note:: $this->input->ip_address() 
			validates the IP address.
		.. note:: 上記の $this->input->ip_address() メソッドは自動的に
			IPアドレスの検証を行います。

		::

			if ( ! $this->input->valid_ip($ip))
			{
				echo 'Not Valid';
			}
			else
			{
				echo 'Valid';
			}

		第二引数はオプションで特定のIPの形式を 'ipv4' または 'ipv6' で指定します。
		デフォルトはどちらの形式もチェックします。

	.. php:method:: user_agent([$xss_clean = NULL])

		:returns:	ユーザーエージェント または設定されていない場合は NULL
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:rtype:	mixed

		現在のユーザが使用しているユーザエージェント(Webブラウザ)を返します。
		利用できないときは NULL を返します。
		::

			echo $this->input->user_agent();

		ユーザエージェントの文字列から情報を抽出する方法は
		:doc:`ユーザエージェントクラス <user_agent>` を参照してください。

	.. php:method:: request_headers([$xss_clean = FALSE])

		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	HTTP リクエストヘッダの配列
		:rtype:	array

		HTTP リクエストヘッダの配列を返します。
		Apache 以外 の( `apache_request_headers()
		<http://php.net/apache_request_headers>`_ をサポートしない)
		環境で有効です。
		::

			$headers = $this->input->request_headers();

	.. php:method:: get_request_header($index[, $xss_clean = FALSE])

		:param	string	$index: HTTP リクエストヘッダの名前
		:param	bool	$xss_clean: XSS フィルタリングを適用するかどうか
		:returns:	    見つからなかった場合は NULL、 それ以外は HTTP リクエストヘッダの値
		:rtype:	string

		検索されたヘッダが見つかればリクエストヘッダの配列の要素を返却し、
		見つからない場合は NULL を返却します。
		::

			$this->input->get_request_header('some-header', TRUE);

	.. php:method:: is_ajax_request()

		:returns:	Ajax リクエストであれば TRUE、それ以外は FALSE
		:rtype:	bool

		サーバのヘッダに HTTP_X_REQUESTED_WITH がセットされているかチェックし、
		セットされている場合はブール値の TRUE 、セットされていない場合は FALSE を返します。

	.. php:method:: is_cli_request()

		:returns:	CLI リクエストの場合は TRUE、そうでない場合は FALSE
		:rtype:	bool

		アプリケーションがコマンドラインから実行されているかを
		チェックします。

		.. note:: このメソッドは現在使用されている PHP SAPI の名前と
			``STDIN`` 定数が定義されている事をチェックします。
			これは通常、PHP がコマンドライン経由で実行されている事を確認する安全な方法です。

		::

			$this->input->is_cli_request()

		.. note:: このメソッドは推奨されておらず、
			現在は :func:`is_cli()` ファンクションのエイリアスです。

	.. php:method:: method([$upper = FALSE])

		:param	bool	$upper: リクエストメソッドの名前を大文字のみまたは小文字のみで返却するかどうか
		:returns:	    HTTP リクエストメソッド
		:rtype:	string

		設定したオプションにより
		大文字のみまたは小文字のみで ``$_SERVER['REQUEST_METHOD']`` の値を返却します。
		::

			echo $this->input->method(TRUE); // Outputs: POST
			echo $this->input->method(FALSE); // Outputs: post
			echo $this->input->method(); // Outputs: post
