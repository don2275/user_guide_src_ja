###############
Cookie ヘルパー
###############

クッキーヘルパーのファイルは、クッキーを処理するのに役立つ関数で
構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーをロードする
====================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('cookie');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: set_cookie($name[, $value = ''[, $expire = ''[, $domain = ''[, $path = '/'[, $prefix = ''[, $secure = FALSE[, $httponly = FALSE]]]]]]])

	:param	mixed	$name: クッキー名 *又は* この関数で利用可能なパラメータの全ての連想配列
	:param	string	$value: クッキーの値
	:param	int	$expire: 有効期限までの秒数
	:param	string	$domain: クッキーのドメイン（通例: .yourdomain.com）
	:param	string	$path: クッキーのパス
	:param	string	$prefix: クッキー名の接頭辞
	:param	bool	$secure: HTTPS でのみクッキーを送信するかどうか
	:param	bool	$httponly: JavaScript からクッキーを隠すかどうか
	:rtype:	void

	このヘルパー関数はブラウザのクッキーをセットするのに便利な
	関数です。 この関数は ``CI_Input::set_cookie()`` のエイリアスですので、
	詳細は :doc:`入力およびセキュリティクラス<../libraries/input>`
	を参照してください。

.. php:function:: get_cookie($index[, $xss_clean = NULL])

	:param	string	$index: クッキー名
	:param	bool	$xss_clean: 返却される値に XSS フィルタを適用するかどうか
	:returns:	クッキーの値又は見つからない場合は NULL
	:rtype:	mixed

	このヘルパー関数はブラウザのクッキーをセットするのに便利な
	関数です。 この関数は *application/config/config.php* ファイルに
        ``$config['cookie_prefix']`` を指定した場合に接頭辞が付与される事を除けば
	``CI_Input::cookie()`` とほとんど一緒です。
	詳細は  :doc:`入力およびセキュリティクラス<../libraries/input>` を
	参照してください。

.. php:function:: delete_cookie($name[, $domain = ''[, $path = '/'[, $prefix = '']]])

	:param	string	$name: クッキー名
	:param	string	$domain: クッキーのドメイン（通例: .yourdomain.com）
	:param	string	$path: クッキーのパス
	:param	string	$prefix: クッキー名の接頭辞
	:rtype:	void

	クッキーを削除できます。 カスタムパスやその他の値は必要なく、次のように
	クッキー名のみ指定してください。
	::

		delete_cookie('name');

	この関数は、値や有効期限のパラメータがない以外は、 ``set_cookie()`` と
	同じものです。
	第1引数に配列で渡すか、
	個別パラメータで値を渡すことができます。
	::

		delete_cookie($name, $domain, $path, $prefix);
