####################
セキュリティヘルパー
####################

セキュリティヘルパーのファイルには、セキュリティに関連する関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('security');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: xss_clean($str[, $is_image = FALSE])

	:param	string	$str: 入力データ
	:param	bool	$is_image: 画像を扱うかどうか
	:returns:	XSS クリーンな文字列
	:rtype:	string

	クロスサイトスクリプティングハッキングのフィルタリングを提供します。

	この関数は、 ``CI_Input::xss_clean()`` のエイリアスです。より多くの情報を
	得るには、:doc:`入力クラス <../libraries/input>` を見てください。

.. php:function:: sanitize_filename($filename)

	:param	string	$filename: ファイル名
	:returns:	サニタイズされたファイル名
	:rtype:	string

	ディレクトリトラバーサルに対する保護を提供します。

	この関数は、 ``CI_Security::sanitize_filename()`` のエイリアスです。
	より多くの情報を得るには、:doc:`セキュリティクラス <../libraries/security>`
	を見てください。


.. php:function:: do_hash($str[, $type = 'sha1'])

	:param	string	$str: 入力文字列
	:param	string	$type: アルゴリズム
	:returns:	16進数表記されたハッシュ値
	:rtype:	string

	パスワードの暗号化に適した SHA1 の一方向ハッシュ値を生成できます。
	初期状態では SHA1 ハッシュ値を生成します。

	サポートしているアルゴリズムの完全なリストは、
	`hash_algos() <http://php.net/function.hash_algos>`_ を見てください。

	例::

		$str = do_hash($str); // SHA1
		$str = do_hash($str, 'md5'); // MD5

	.. note:: この関数は、以前は ``dohash()`` という名前でした。
		``do_hash()`` の使用が推奨され、変更されました。

	.. note:: この関数は廃止予定です。ネイティブの ``hash()`` を代わりに使用してください。


.. php:function:: strip_image_tags($str)

	:param	string	$str: 入力文字列
	:returns:	入力文字列からイメージタグを除いたもの
	:rtype:	string

	これは、文字列からイメージタグを除去するセキュリティ関数です。
	画像の URL はプレーンテキストとして残ります。

	例::

		$string = strip_image_tags($string);

	この関数は、 ``CI_Security::strip_image_tags()`` のエイリアスです。
	より多くの情報を得るには、:doc:`セキュリティクラス <../libraries/security>`
	を見てください。


.. php:function:: encode_php_tags($str)

	:param	string	$str: 入力文字列
	:returns:	安全な形式の文字列
	:rtype:	string

	これは、PHP タグを HTML エンティティに変換するセキュリティ関数です。

	.. note:: これを使用しても :php:func:`xss_clean()` は自動的に行われません。

	例::

		$string = encode_php_tags($string);