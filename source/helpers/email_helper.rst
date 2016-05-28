##############
Email ヘルパー
##############

Email ヘルパーは Email 処理を支援する関数を提供します。より堅牢な
Email の処理方法については CodeIgniter の :doc:`Email クラス
<../libraries/email>` を参照してください。

.. important:: The Email helper is DEPRECATED and is currently
	only kept for backwards compatibility.

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

	:param	string	$email: E-mail address
	:returns:	TRUE if a valid email is supplied, FALSE otherwise
	:rtype:	bool

	Checks if the input is a correctly formatted e-mail address. Note that is
	doesn't actually prove that the address will be able recieve mail, but
	simply that it is a validly formed address.

	Example::

		if (valid_email('email@somesite.com'))
		{
			echo 'email is valid';
		}
		else
		{
			echo 'email is not valid';
		}

	.. note:: All that this function does is to use PHP's native ``filter_var()``::

		(bool) filter_var($email, FILTER_VALIDATE_EMAIL);

.. php:function:: send_email($recipient, $subject, $message)

	:param	string	$recipient: E-mail address
	:param	string	$subject: Mail subject
	:param	string	$message: Message body
	:returns:	TRUE if the mail was successfully sent, FALSE in case of an error
	:rtype:	bool

	PHP の `mail() <http://www.php.net/function.mail>`_
	関数を使ってメールを送信します。

	.. note:: All that this function does is to use PHP's native ``mail``

		::

			mail($recipient, $subject, $message);

	より堅牢なメールの処理方法については、CodeIgniter の :doc:`Email
	クラス <../libraries/email>` を参照してください。
