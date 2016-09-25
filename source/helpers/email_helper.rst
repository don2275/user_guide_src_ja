###################
イーメールヘルパー
###################

イーメールヘルパーはイーメールの処理を支援する関数を提供します。より堅牢な
イーメールの処理方法については CodeIgniter の :doc:`Email クラス
<../libraries/email>` を参照してください。

.. important:: イーメールヘルパーは非推奨です。
	現在は、後方互換のためのみに維持されているものです。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('email');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: valid_email($email)

	:param	string	$email: メールアドレス
	:returns:	有効なメールアドレスであれば TRUE そうではない場合は FALSE
	:rtype:	bool

	入力値が正しくフォーマットされたメールアドレスかどうかを確認します。そのアドレスで
	メールの受信ができることを保障するものではく、単純に有効なメールアドレスの形式である
	ことを確認します。

	例::

		if (valid_email('email@somesite.com'))
		{
			echo 'email is valid';
		}
		else
		{
			echo 'email is not valid';
		}

	.. note:: この関数で行うことはPHP標準の ``filter_var()`` でもできます::

		(bool) filter_var($email, FILTER_VALIDATE_EMAIL);

.. php:function:: send_email($recipient, $subject, $message)

	:param	string	$recipient: メールアドレス
	:param	string	$subject: メールの件名
	:param	string	$message: メールの内容
	:returns:	メールの送信に成功すれば TRUE エラーの場合は FALSE 
	:rtype:	bool

	PHP の `mail() <http://www.php.net/function.mail>`_
	関数を使ってメールを送信します。

	.. note:: この関数で行うことはPHP標準の ``mail`` でもできます

		::

			mail($recipient, $subject, $message);

	より堅牢なメールの処理方法については、CodeIgniter の :doc:`Email
	クラス <../libraries/email>` を参照してください。
