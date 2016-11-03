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

.. important:: The 'global_xss_filtering' setting is DEPRECATED and kept
	solely for backwards-compatibility purposes. XSS escaping should
	be performed on *output*, not *input*!

*******************
Accessing form data
*******************

==========================================

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

If you want to utilize the PUT, DELETE, PATCH or other exotic request
methods, they can only be accessed via a special input stream, that
can only be read once. This isn't as easy as just reading from e.g.
the ``$_POST`` array, because it will always exist and you can try
and access multiple variables without caring that you might only have
one shot at all of the POST data.

CodeIgniter will take care of that for you, and you can read the data
from the **php://input** stream at any time, just by using the
``$raw_input_stream`` property::

	$this->input->raw_input_stream;

Additionally if the input stream is form-encoded like $_POST you can 
access its values by calling the
``input_stream()`` method::

	$this->input->input_stream('key');

Similar to other methods such as ``get()`` and ``post()``, if the
requested data is not found, it will return NULL and you can also
decide whether to run the data through ``xss_clean()`` by passing
a boolean value as the second parameter::

	$this->input->input_stream('key', TRUE); // XSS Clean
	$this->input->input_stream('key', FALSE); // No XSS filter

.. note:: You can utilize ``method()`` in order to know if you're reading
	PUT, DELETE or PATCH data.

******************
クラスリファレンス
******************

.. php:class:: CI_Input

	.. attribute:: $raw_input_stream
		
		Read only property that will return php://input data as is.
		
		The property can be read multiple times.

	.. php:method:: post([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: POST parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_POST if no parameters supplied, otherwise the POST value if found or NULL if not
		:rtype:	mixed

		第1引数は、コレクションの中から探し出す POST
		された項目の名前になります::

			$this->input->post('some_data');

		このメソッドは、取り出そうとして見つからなかった場合、 NULL
		を返します。

		The second optional parameter lets you run the data through the XSS
		filter. It's enabled by setting the second parameter to boolean TRUE
		or by setting your ``$config['global_xss_filtering']`` to TRUE.
		::

			$this->input->post('some_data', TRUE);

		引数を指定せずに呼び出すことで、 POST されたすべての値を連想配列で返します。

		第1引数を NULL 、第2引数にブール値の TRUE を指定することで、POST
		されたすべての値を XSS フィルタに通すことができます。
		::

			$this->input->post(NULL, TRUE); // POST された値を XSS フィルタを通して返します
			$this->input->post(NULL, FALSE); // POST された値を XSS フィルタを通さずに返します

		To return an array of multiple POST parameters, pass all the required keys
		as an array.
		::

			$this->input->post(array('field1', 'field2'));

		Same rule applied here, to retrive the parameters with XSS filtering enabled, set the
		second parameter to boolean TRUE.
		::

			$this->input->post(array('field1', 'field2'), TRUE);

	.. php:method:: get([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: GET parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_GET if no parameters supplied, otherwise the GET value if found or NULL if not
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

		To return an array of multiple GET parameters, pass all the required keys
		as an array.
		::

			$this->input->get(array('field1', 'field2'));

		Same rule applied here, to retrive the parameters with XSS filtering enabled, set the
		second parameter to boolean TRUE.
		::

			$this->input->get(array('field1', 'field2'), TRUE);

	.. php:method:: post_get($index[, $xss_clean = NULL])

		:param	string	$index: POST/GET parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	POST/GET value if found, NULL if not
		:rtype:	mixed

		This method works pretty much the same way as ``post()`` and ``get()``,
		only combined. It will search through both POST and GET streams for data,
		looking in POST first, and then in GET::

			$this->input->post_get('some_data', TRUE);

	.. php:method:: get_post($index[, $xss_clean = NULL])

		:param	string	$index: GET/POST parameter name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	GET/POST value if found, NULL if not
		:rtype:	mixed

		This method works the same way as ``post_get()`` only it looks for GET
		data first.

			$this->input->get_post('some_data', TRUE);

		.. note:: This method used to act EXACTLY like ``post_get()``, but it's
			behavior has changed in CodeIgniter 3.0.

	.. php:method:: cookie([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: COOKIE name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_COOKIE if no parameters supplied, otherwise the COOKIE value if found or NULL if not
		:rtype:	mixed

		このメソッドは、クッキーデータを取り出すということ以外は、
		``post()`` や ``get()`` メソッドと同じです::

			$this->input->cookie('some_cookie');
			$this->input->cookie('some_cookie', TRUE); // with XSS filter

		To return an array of multiple cookie values, pass all the required keys
		as an array.
		::

			$this->input->cookie(array('some_cookie', 'some_cookie2'));

		.. note:: Unlike the :doc:`Cookie Helper <../helpers/cookie_helper>`
			function :php:func:`get_cookie()`, this method does NOT prepend
			your configured ``$config['cookie_prefix']`` value.

	.. php:method:: server($index[, $xss_clean = NULL])

		:param	mixed	$index: Value name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	$_SERVER item value if found, NULL if not
		:rtype:	mixed

		このメソッドは、 SERVER データ(``$_SERVER``)を取り出すということ以外は、
		``post()`` 、 ``get()`` や ``cookie()`` と同じです:: 

			$this->input->server('some_data');

		To return an array of multiple ``$_SERVER`` values, pass all the required keys
		as an array.
		::

			$this->input->server(array('SERVER_PROTOCOL', 'REQUEST_URI'));

	.. php:method:: input_stream([$index = NULL[, $xss_clean = NULL]])

		:param	mixed	$index: Key name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	Input stream array if no parameters supplied, otherwise the specified value if found or NULL if not
		:rtype:	mixed

		This method is identical to ``get()``, ``post()`` and ``cookie()``,
		only it fetches the *php://input* stream data.

	.. php:method:: set_cookie($name = ''[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = FALSE[, $httponly = FALSE]]]]]]])

		:param	mixed	$name: Cookie name or an array of parameters
		:param	string	$value: Cookie value
		:param	int	$expire: Cookie expiration time in seconds
		:param	string	$domain: Cookie domain
		:param	string	$path: Cookie path
		:param	string	$prefix: Cookie name prefix
		:param	bool	$secure: Whether to only transfer the cookie through HTTPS
		:param	bool	$httponly: Whether to only make the cookie accessible for HTTP requests (no JavaScript)
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

		:returns:	Visitor's IP address or '0.0.0.0' if not valid
		:rtype:	string

		現在のユーザの IP アドレスを返します。
		アドレスが正しくない場合、このメソッドは、 '0.0.0.0' を返却します::

			echo $this->input->ip_address();

		.. important:: This method takes into account the ``$config['proxy_ips']``
			setting and will return the reported HTTP_X_FORWARDED_FOR,
			HTTP_CLIENT_IP, HTTP_X_CLIENT_IP or HTTP_X_CLUSTER_CLIENT_IP
			address for the allowed IP addresses.

	.. php:method:: valid_ip($ip[, $which = ''])

		:param	string	$ip: IP address
		:param	string	$which: IP protocol ('ipv4' or 'ipv6')
		:returns:	TRUE if the address is valid, FALSE if not
		:rtype:	bool

		Takes an IP address as input and returns TRUE or FALSE (boolean) depending
		on whether it is valid or not.

		.. note:: The $this->input->ip_address() method above automatically
			validates the IP address.

		::

			if ( ! $this->input->valid_ip($ip))
			{
				echo 'Not Valid';
			}
			else
			{
				echo 'Valid';
			}

		Accepts an optional second string parameter of 'ipv4' or 'ipv6' to specify
		an IP format. The default checks for both formats.

	.. php:method:: user_agent([$xss_clean = NULL])

		:returns:	User agent string or NULL if not set
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:rtype:	mixed

		現在のユーザが使用しているユーザエージェント(Webブラウザ)を返します。
		利用できないときは NULL を返します。
		::

			echo $this->input->user_agent();

		ユーザエージェントの文字列から情報を抽出する方法は
		:doc:`ユーザエージェントクラス <user_agent>` を参照してください。

	.. php:method:: request_headers([$xss_clean = FALSE])

		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	An array of HTTP request headers
		:rtype:	array

		HTTP リクエストヘッダの配列を返します。
		Apache 以外 の( `apache_request_headers()
		<http://php.net/apache_request_headers>`_ をサポートしない)
		環境で有効です。
		::

			$headers = $this->input->request_headers();

	.. php:method:: get_request_header($index[, $xss_clean = FALSE])

		:param	string	$index: HTTP request header name
		:param	bool	$xss_clean: Whether to apply XSS filtering
		:returns:	    An HTTP request header or NULL if not found
		:rtype:	string

		Returns a single member of the request headers array or NULL
		if the searched header is not found.
		::

			$this->input->get_request_header('some-header', TRUE);

	.. php:method:: is_ajax_request()

		:returns:	TRUE if it is an Ajax request, FALSE if not
		:rtype:	bool

		サーバのヘッダに HTTP_X_REQUESTED_WITH がセットされているかチェックし、

	.. php:method:: is_cli_request()

		:returns:	TRUE if it is a CLI request, FALSE if not
		:rtype:	bool

		Checks to see if the application was run from the command-line
		interface.

		.. note:: This method checks both the PHP SAPI name currently in use
			and if the ``STDIN`` constant is defined, which is usually a
			failsafe way to see if PHP is being run via the command line.

		::

			$this->input->is_cli_request()

		.. note:: This method is DEPRECATED and is now just an alias for the
			:func:`is_cli()` function.

	.. php:method:: method([$upper = FALSE])

		:param	bool	$upper: Whether to return the request method name in upper or lower case
		:returns:	    HTTP request method
		:rtype:	string

		Returns the ``$_SERVER['REQUEST_METHOD']``, with the option to set it
		in uppercase or lowercase.
		::

			echo $this->input->method(TRUE); // Outputs: POST
			echo $this->input->method(FALSE); // Outputs: post
			echo $this->input->method(); // Outputs: post
