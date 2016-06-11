##############################
フォームバリデーション（検証）
##############################

CodeIgniter は、最小限のコードで総合的なフォームバリデーションとデータ
の準備をするクラスを提供します。

.. contents:: Page Contents

****
概要
****

Before explaining CodeIgniter's approach to data validation, let's
describe the ideal scenario:

#. A form is displayed.
#. You fill it in and submit it.
#. If you submitted something invalid, or perhaps missed a required
   item, the form is redisplayed containing your data along with an
   error message describing the problem.
#. This process continues until you have submitted a valid form.

On the receiving end, the script must:

#. Check for required data.
#. Verify that the data is of the correct type, and meets the correct
   criteria. For example, if a username is submitted it must be
   validated to contain only permitted characters. It must be of a
   minimum length, and not exceed a maximum length. The username can't
   be someone else's existing username, or perhaps even a reserved word.
   Etc.
#. Sanitize the data for security.
#. Pre-format the data if needed (Does the data need to be trimmed? HTML
   encoded? Etc.)
#. Prep the data for insertion in the database.

Although there is nothing terribly complex about the above process, it
usually requires a significant amount of code, and to display error
messages, various control structures are usually placed within the form
HTML. Form validation, while simple to create, is generally very messy
and tedious to implement.

**************
チュートリアル
**************

What follows is a "hands on" tutorial for implementing CodeIgniters Form
Validation.

In order to implement form validation you'll need three things:

#. A :doc:`View <../general/views>` file containing a form.
#. A View file containing a "success" message to be displayed upon
   successful submission.
#. A :doc:`controller <../general/controllers>` method to receive and
   process the submitted data.

Let's create those three things, using a member sign-up form as the
example.

入力フォーム
============

Using a text editor, create a form called myform.php. In it, place this
code and save it to your application/views/ folder::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>Username</h5>
	<input type="text" name="username" value="" size="50" />

	<h5>Password</h5>
	<input type="text" name="password" value="" size="50" />

	<h5>Password Confirm</h5>
	<input type="text" name="passconf" value="" size="50" />

	<h5>Email Address</h5>
	<input type="text" name="email" value="" size="50" />

	<div><input type="submit" value="Submit" /></div>

	</form>

	</body>
	</html>

成功ページ
==========

Using a text editor, create a form called formsuccess.php. In it, place
this code and save it to your application/views/ folder::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<h3>Your form was successfully submitted!</h3>

	<p><?php echo anchor('form', 'Try it again!'); ?></p>

	</body>
	</html>

コントローラ
============

Using a text editor, create a controller called Form.php. In it, place
this code and save it to your application/controllers/ folder::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

動かしてみよう！
================

To try your form, visit your site using a URL similar to this one::

	example.com/index.php/form/

If you submit the form you should simply see the form reload. That's
because you haven't set up any validation rules yet.

**Since you haven't told the Form Validation class to validate anything
yet, it returns FALSE (boolean false) by default. ``The run()`` method
only returns TRUE if it has successfully applied your rules without any
of them failing.**

解説
====

You'll notice several things about the above pages:

The form (myform.php) is a standard web form with a couple exceptions:

#. It uses a form helper to create the form opening. Technically, this
   isn't necessary. You could create the form using standard HTML.
   However, the benefit of using the helper is that it generates the
   action URL for you, based on the URL in your config file. This makes
   your application more portable in the event your URLs change.
#. At the top of the form you'll notice the following function call:
   ::

	<?php echo validation_errors(); ?>

   This function will return any error messages sent back by the
   validator. If there are no messages it returns an empty string.

The controller (Form.php) has one method: ``index()``. This method
initializes the validation class and loads the form helper and URL
helper used by your view files. It also runs the validation routine.
Based on whether the validation was successful it either presents the
form or the success page.

.. _setting-validation-rules:

検証ルールを設定する
====================

CodeIgniter lets you set as many validation rules as you need for a
given field, cascading them in order, and it even lets you prep and
pre-process the field data at the same time. To set validation rules you
will use the ``set_rules()`` method::

	$this->form_validation->set_rules();

The above method takes **three** parameters as input:

#. The field name - the exact name you've given the form field.
#. A "human" name for this field, which will be inserted into the error
   message. For example, if your field is named "user" you might give it
   a human name of "Username".
#. The validation rules for this form field.
#. (optional) Set custom error messages on any rules given for current field. If not provided will use the default one.

.. note:: If you would like the field name to be stored in a language
	file, please see :ref:`translating-field-names`.

Here is an example. In your controller (Form.php), add this code just
below the validation initialization method::

	$this->form_validation->set_rules('username', 'Username', 'required');
	$this->form_validation->set_rules('password', 'Password', 'required');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
	$this->form_validation->set_rules('email', 'Email', 'required');

Your controller should now look like this::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			$this->form_validation->set_rules('username', 'Username', 'required');
			$this->form_validation->set_rules('password', 'Password', 'required',
				array('required' => 'You must provide a %s.')
			);
			$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required');
			$this->form_validation->set_rules('email', 'Email', 'required');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

Now submit the form with the fields blank and you should see the error
messages. If you submit the form with all the fields populated you'll
see your success page.

.. note:: The form fields are not yet being re-populated with the data
	when there is an error. We'll get to that shortly.

配列を使った検証ルールの指定
============================

Before moving on it should be noted that the rule setting method can
be passed an array if you prefer to set all your rules in one action. If
you use this approach, you must name your array keys as indicated::

	$config = array(
		array(
			'field' => 'username',
			'label' => 'ユーザ名',
			'rules' => 'required'
		),
		array(
			'field' => 'password',
			'label' => 'パスワード',
			'rules' => 'required',
			'errors' => array(
				'required' => '%s は必須です。',
			),
		),
		array(
			'field' => 'passconf',
			'label' => 'パスワードの確認',
			'rules' => 'required'
		),
		array(
			'field' => 'email',
			'label' => 'メールアドレス',
			'rules' => 'required'
		)
	);

	$this->form_validation->set_rules($config);

ルールの連結（カスケード）
==========================

CodeIgniter lets you pipe multiple rules together. Let's try it. Change
your rules in the third parameter of rule setting method, like this::

	$this->form_validation->set_rules(
		'username', 'Username',
		'required|min_length[5]|max_length[12]|is_unique[users.username]',
		array(
			'required'	=> 'You have not provided %s.',
			'is_unique'	=> 'This %s already exists.'
		)
	);
	$this->form_validation->set_rules('password', 'Password', 'required');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'required|matches[password]');
	$this->form_validation->set_rules('email', 'Email', 'required|valid_email|is_unique[users.email]');

The above code sets the following rules:

#. The username field be no shorter than 5 characters and no longer than
   12.
#. The password field must match the password confirmation field.
#. The email field must contain a valid email address.

Give it a try! Submit your form without the proper data and you'll see
new error messages that correspond to your new rules. There are numerous
rules available which you can read about in the validation reference.

.. note:: You can also pass an array of rules to ``set_rules()``,
	instead of a string. Example::

	$this->form_validation->set_rules('username', 'Username', array('required', 'min_length[5]'));

データの整形
============

In addition to the validation method like the ones we used above, you
can also prep your data in various ways. For example, you can set up
rules like this::

	$this->form_validation->set_rules('username', 'Username', 'trim|required|min_length[5]|max_length[12]');
	$this->form_validation->set_rules('password', 'Password', 'trim|required|min_length[8]');
	$this->form_validation->set_rules('passconf', 'Password Confirmation', 'trim|required|matches[password]');
	$this->form_validation->set_rules('email', 'Email', 'trim|required|valid_email');

In the above example, we are "trimming" the fields, checking for length
where necessary and making sure that both password fields match.

**Any native PHP function that accepts one parameter can be used as a
rule, like ``htmlspecialchars()``, ``trim()``, etc.**

.. note:: You will generally want to use the prepping functions
	**after** the validation rules so if there is an error, the
	original data will be shown in the form.

フォームの再表示（データの引き継ぎ）
====================================

Thus far we have only been dealing with errors. It's time to repopulate
the form field with the submitted data. CodeIgniter offers several
helper functions that permit you to do this. The one you will use most
commonly is::

	set_value('field name')

Open your myform.php view file and update the **value** in each field
using the :php:func:`set_value()` function:

**Don't forget to include each field name in the :php:func:`set_value()`
function calls!**

::

	<html>
	<head>
	<title>My Form</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>Username</h5>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>Password</h5>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>Password Confirm</h5>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>Email Address</h5>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

	<div><input type="submit" value="Submit" /></div>

	</form>

	</body>
	</html>

Now reload your page and submit the form so that it triggers an error.
Your form fields should now be re-populated

.. note:: The :ref:`class-reference` section below
	contains methods that permit you to re-populate <select> menus,
	radio buttons, and checkboxes.

.. important:: If you use an array as the name of a form field, you
	must supply it as an array to the function. Example::

	<input type="text" name="colors[]" value="<?php echo set_value('colors[]'); ?>" size="50" />

For more info please see the :ref:`using-arrays-as-field-names` section below.

コールバック: ユーザ定義の検証メソッド
======================================

ユーザ定義の検証メソッドへのコールバックがシステムでサポートされていま
す。これを使えば、それぞれのニーズに合わせるため検証クラスを拡張するこ
とができます。 たとえば、選択したユーザが固有の名前かどうかを調べるた
めデータベースクエリを実行する必要があるとき、それを行うコールバックメ
ソッドを作成できます。 次に示す例のように作ってみましょう。

コントローラで、"username" ルールを次のように変更します::

	$this->form_validation->set_rules('username', 'ユーザ名', 'callback_username_check');

次に ``username_check()`` という名前のメソッドをコントローラに追加します。
コントローラは以下のようになっているはずです::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			$this->form_validation->set_rules('username', 'ユーザ名', 'callback_username_check');
			$this->form_validation->set_rules('password', 'パスワード', 'required');
			$this->form_validation->set_rules('passconf', 'パスワードの確認', 'required');
			$this->form_validation->set_rules('email', 'メールアドレス', 'required|is_unique[users.email]');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}

		public function username_check($str)
		{
			if ($str == 'test')
			{
				$this->form_validation->set_message('username_check', '{field} 欄に "test" は使えません');
				return FALSE;
			}
			else
			{
				return TRUE;
			}
		}

	}

フォームを再読み込みして、ユーザ名に "test" と入力して送信します。
フォームフィールドのデータがコールバックメソッドに渡され処理されたのが
わかります。

コールバックを呼び出すには、あるルールに従ってメソッド名を指定します。そのルール
とは、"callback\_" という **プリフィックス** をメソッド名に付け加えるという
ものです。もし、コールバックメソッドが追加のパラメータを受け取る必要がある
場合、"callback_foo**[bar]**" のようにメソッド名の後の角カッコの間にパラメータを
追加してください。そうすれば、第 2 引数としてコールバックメソッドに渡されます。

.. note:: また、コールバックに渡されたフォームデータを処理し、
	結果を返すことができます。
	コールバックが論理型の TRUE/FALSE 以外の値を返す場合、
	そのデータは新たに処理されたフォームデータであるとみなされます。

コーラブル: 何でもルールとして使う
==================================

もしコールバックルールが十分でないない場合（例えば、コールバックはコントローラ内に
制限されます）、がっかりしないでください。
もう 1 つ独自ルールを作成する方法があります。``is_callable()`` が TRUE を返すものをルールとする方法です。

次の例を検討してみましょう::

	$this->form_validation->set_rules(
		'username', 'ユーザ名',
		array(
			'required',
			array($this->users_model, 'valid_username')
		)
	);

上のコードは ``Users_model`` オブジェクトの ``valid_username()`` メソッド
を使っています。

もちろんこれは 1 つの例であり、コールバック関数はモデルに限定されません。
第 1 引数にフィールドの値を受け取るあらゆるオブジェクト/メソッドを使うことが
できます。また、もし PHP 5.3+ であれば、
匿名関数を使うこともできます::

	$this->form_validation->set_rules(
		'username', 'ユーザ名',
		array(
			'required',
			function($value)
			{
				// $value をチェックする
			}
		)
	);

もちろん、コーラブルルールは文字列ではなく、ルール名でもありません。
これは、エラーメッセージを設定したい場合、問題になります。
そのため、それらのルールの配列の第 1 要素にルール名、
第 2 要素にルールを記述できます::

	$this->form_validation->set_rules(
		'username', 'ユーザ名',
		array(
			'required',
			array('username_callable', array($this->users_model, 'valid_username'))
		)
	);

匿名関数（PHP 5.3+）版::

	$this->form_validation->set_rules(
		'username', 'ユーザ名',
		array(
			'required',
			array(
				'username_callable',
				function($str)
				{
					// $str を検証し TRUE または FALSE を返す
				}
			)
		)
	);

.. _setting-error-messages:

エラーメッセージの設定
======================

All of the native error messages are located in the following language
file: **system/language/english/form_validation_lang.php**

To set your own global custom message for a rule, you can either 
extend/override the language file by creating your own in
**application/language/english/form_validation_lang.php** (read more
about this in the :doc:`Language Class <language>` documentation),
or use the following method::

	$this->form_validation->set_message('rule', 'Error Message');

If you need to set a custom error message for a particular field on 
some particular rule, use the set_rules() method::

	$this->form_validation->set_rules('field_name', 'Field Label', 'rule1|rule2|rule3',
		array('rule2' => 'Error Message on rule2 for this field_name')
	);

Where rule corresponds to the name of a particular rule, and Error
Message is the text you would like displayed.

If you'd like to include a field's "human" name, or the optional
parameter some rules allow for (such as max_length), you can add the
**{field}** and **{param}** tags to your message, respectively::

	$this->form_validation->set_message('min_length', '{field} must have at least {param} characters.');

On a field with the human name Username and a rule of min_length[5], an
error would display: "Username must have at least 5 characters."

.. note:: The old `sprintf()` method of using **%s** in your error messages
	will still work, however it will override the tags above. You should
	use one or the other.

In the callback rule example above, the error message was set by passing
the name of the method (without the "callback\_" prefix)::

	$this->form_validation->set_message('username_check')

.. _translating-field-names:

フィールド名の変換
==================

If you would like to store the "human" name you passed to the
``set_rules()`` method in a language file, and therefore make the name
able to be translated, here's how:

First, prefix your "human" name with **lang:**, as in this example::

	 $this->form_validation->set_rules('first_name', 'lang:first_name', 'required');

Then, store the name in one of your language file arrays (without the
prefix)::

	$lang['first_name'] = 'First Name';

.. note:: If you store your array item in a language file that is not
	loaded automatically by CI, you'll need to remember to load it in your
	controller using::

	$this->lang->load('file_name');

See the :doc:`Language Class <language>` page for more info regarding
language files.

.. _changing-delimiters:

エラーメッセージを囲む文字の変更
================================

By default, the Form Validation class adds a paragraph tag (<p>) around
each error message shown. You can either change these delimiters
globally, individually, or change the defaults in a config file.

#. **Changing delimiters Globally**
   To globally change the error delimiters, in your controller method,
   just after loading the Form Validation class, add this::

      $this->form_validation->set_error_delimiters('<div class="error">', '</div>');

   In this example, we've switched to using div tags.

#. **Changing delimiters Individually**
   Each of the two error generating functions shown in this tutorial can
   be supplied their own delimiters as follows::

      <?php echo form_error('field name', '<div class="error">', '</div>'); ?>

   Or::

      <?php echo validation_errors('<div class="error">', '</div>'); ?>

#. **Set delimiters in a config file**
   You can add your error delimiters in application/config/form_validation.php as follows::

      $config['error_prefix'] = '<div class="error_prefix">';
      $config['error_suffix'] = '</div>';

個別にエラーを表示する
======================

If you prefer to show an error message next to each form field, rather
than as a list, you can use the :php:func:`form_error()` function.

Try it! Change your form so that it looks like this::

	<h5>Username</h5>
	<?php echo form_error('username'); ?>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>Password</h5>
	<?php echo form_error('password'); ?>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>Password Confirm</h5>
	<?php echo form_error('passconf'); ?>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>Email Address</h5>
	<?php echo form_error('email'); ?>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

If there are no errors, nothing will be shown. If there is an error, the
message will appear.

.. important:: If you use an array as the name of a form field, you
	must supply it as an array to the function. Example::

	<?php echo form_error('options[size]'); ?>
	<input type="text" name="options[size]" value="<?php echo set_value("options[size]"); ?>" size="50" />

For more info please see the :ref:`using-arrays-as-field-names` section below.

配列（$_POST 以外）のバリデーション
===================================

Sometimes you may want to validate an array that does not originate from ``$_POST`` data.

In this case, you can specify the array to be validated::

	$data = array(
		'username' => 'johndoe',
		'password' => 'mypassword',
		'passconf' => 'mypassword'
	);

	$this->form_validation->set_data($data);

Creating validation rules, running the validation, and retrieving error
messages works the same whether you are validating ``$_POST`` data or
another array of your choice.

.. important:: You have to call the ``set_data()`` method *before* defining
	any validation rules.

.. important:: If you want to validate more than one array during a single
	execution, then you should call the ``reset_validation()`` method
	before setting up rules and validating the new array.

For more info please see the :ref:`class-reference` section below.

.. _saving-groups:

******************************
検証ルールを設定ファイルに保存
******************************

A nice feature of the Form Validation class is that it permits you to
store all your validation rules for your entire application in a config
file. You can organize these rules into "groups". These groups can
either be loaded automatically when a matching controller/method is
called, or you can manually call each set as needed.

ルールの保存方法
================

To store your validation rules, simply create a file named
form_validation.php in your application/config/ folder. In that file
you will place an array named $config with your rules. As shown earlier,
the validation array will have this prototype::

	$config = array(
		array(
			'field' => 'username',
			'label' => 'Username',
			'rules' => 'required'
		),
		array(
			'field' => 'password',
			'label' => 'Password',
			'rules' => 'required'
		),
		array(
			'field' => 'passconf',
			'label' => 'Password Confirmation',
			'rules' => 'required'
		),
		array(
			'field' => 'email',
			'label' => 'Email',
			'rules' => 'required'
		)
	);

Your validation rule file will be loaded automatically and used when you
call the ``run()`` method.

Please note that you MUST name your ``$config`` array.

検証ルールのセットを作る
========================

In order to organize your rules into "sets" requires that you place them
into "sub arrays". Consider the following example, showing two sets of
rules. We've arbitrarily called these two rules "signup" and "email".
You can name your rules anything you want::

	$config = array(
		'signup' => array(
			array(
				'field' => 'username',
				'label' => 'Username',
				'rules' => 'required'
			),
			array(
				'field' => 'password',
				'label' => 'Password',
				'rules' => 'required'
			),
			array(
				'field' => 'passconf',
				'label' => 'Password Confirmation',
				'rules' => 'required'
			),
			array(
				'field' => 'email',
				'label' => 'Email',
				'rules' => 'required'
			)
		),
		'email' => array(
			array(
				'field' => 'emailaddress',
				'label' => 'EmailAddress',
				'rules' => 'required|valid_email'
			),
			array(
				'field' => 'name',
				'label' => 'Name',
				'rules' => 'required|alpha'
			),
			array(
				'field' => 'title',
				'label' => 'Title',
				'rules' => 'required'
			),
			array(
				'field' => 'message',
				'label' => 'MessageBody',
				'rules' => 'required'
			)
		)
	);

特定のルールグループを呼び出す
==============================

In order to call a specific group, you will pass its name to the ``run()``
method. For example, to call the signup rule you will do this::

	if ($this->form_validation->run('signup') == FALSE)
	{
		$this->load->view('myform');
	}
	else
	{
		$this->load->view('formsuccess');
	}

コントローラー内のメソッドにルールグループを関連づける
======================================================

An alternate (and more automatic) method of calling a rule group is to
name it according to the controller class/method you intend to use it
with. For example, let's say you have a controller named Member and a
method named signup. Here's what your class might look like::

	<?php

	class Member extends CI_Controller {

		public function signup()
		{
			$this->load->library('form_validation');

			if ($this->form_validation->run() == FALSE)
			{
				$this->load->view('myform');
			}
			else
			{
				$this->load->view('formsuccess');
			}
		}
	}

In your validation config file, you will name your rule group
member/signup::

	$config = array(
		'member/signup' => array(
			array(
				'field' => 'username',
				'label' => 'Username',
				'rules' => 'required'
			),
			array(
				'field' => 'password',
				'label' => 'Password',
				'rules' => 'required'
			),
			array(
				'field' => 'passconf',
				'label' => 'PasswordConfirmation',
				'rules' => 'required'
			),
			array(
				'field' => 'email',
				'label' => 'Email',
				'rules' => 'required'
			)
		)
	);

When a rule group is named identically to a controller class/method it
will be used automatically when the ``run()`` method is invoked from that
class/method.

.. _using-arrays-as-field-names:

******************************
フィールド名の指定に配列を使う
******************************

The Form Validation class supports the use of arrays as field names.
Consider this example::

	<input type="text" name="options[]" value="" size="50" />

If you do use an array as a field name, you must use the EXACT array
name in the :ref:`Helper Functions <helper-functions>` that require the
field name, and as your Validation Rule field name.

For example, to set a rule for the above field you would use::

	$this->form_validation->set_rules('options[]', 'Options', 'required');

Or, to show an error for the above field you would use::

	<?php echo form_error('options[]'); ?>

Or to re-populate the field you would use::

	<input type="text" name="options[]" value="<?php echo set_value('options[]'); ?>" size="50" />

You can use multidimensional arrays as field names as well. For example::

	<input type="text" name="options[size]" value="" size="50" />

Or even::

	<input type="text" name="sports[nba][basketball]" value="" size="50" />

As with our first example, you must use the exact array name in the
helper functions::

	<?php echo form_error('sports[nba][basketball]'); ?>

If you are using checkboxes (or other fields) that have multiple
options, don't forget to leave an empty bracket after each option, so
that all selections will be added to the POST array::

	<input type="checkbox" name="options[]" value="red" />
	<input type="checkbox" name="options[]" value="blue" />
	<input type="checkbox" name="options[]" value="green" />

Or if you use a multidimensional array::

	<input type="checkbox" name="options[color][]" value="red" />
	<input type="checkbox" name="options[color][]" value="blue" />
	<input type="checkbox" name="options[color][]" value="green" />

When you use a helper function you'll include the bracket as well::

	<?php echo form_error('options[color][]'); ?>


******************
ルールリファレンス
******************

The following is a list of all the native rules that are available to
use:

========================= ========== ============================================================================================= =======================
ルール                     Parameter  説明                                                                                           例
========================= ========== ============================================================================================= =======================
**required**              No         空き要素の場合はFALSEを返す
**matches**               Yes        formの要素が一致しない時はFALSEを返す											                   matches[form_item]
**regex_match**           Yes        Returns FALSE if the form element does not match the regular expression.                      regex_match[/regex/]
**differs**               Yes        Returns FALSE if the form element does not differ from the one in the parameter.              differs[form_item]
**is_unique**             Yes        Returns FALSE if the form element is not unique to the table and field name in the            is_unique[table.field]
                                     parameter. Note: This rule requires :doc:`Query Builder <../database/query_builder>` to be
                                     enabled in order to work.
**min_length**            Yes        指定する文字数より少ない場合はFALSEを返します。								 	                   min_length[3]
**max_length**            Yes        指定する文字数を超えた場合はFALSEを返します。	             		 							   max_length[12]
**exact_length**          Yes        指定する文字数と一致しない場合はFALSEを返します。								                       exact_length[8]
**greater_than**          Yes        指定した値よりも（数字的に）小さいか、数字でない時にFALSEを							       		   greater_than[8]
                                     返します。
**greater_than_equal_to** Yes        指定した値よりも（数字的に）等しいもしくは小さいか、数字でない時にFALSEを                           	   greater_than_equal_to[8]
                                     返します。
**less_than**             Yes        指定した値よりも（数字的に）大きいか、数字でない時にFALSEを         								   less_than[8]
                                     返します。
**less_than_equal_to**    Yes        指定した値よりも（数字的に）等しいもしくは大きいか、数字でない時にFALSEを                        	   less_than_equal_to[8]
                                     返します。
**in_list**               Yes        Returns FALSE if the form element is not within a predetermined list.                         in_list[red,blue,green]
**alpha**                 No         アルファベット以外の文字を含む場合、FALSEを返します。
**alpha_numeric**         No         アルファベット・数字以外の文字を含む場合、FALSEを返します。
**alpha_numeric_spaces**  No         Returns FALSE if the form element contains anything other than alpha-numeric characters
                                     or spaces.  Should be used after trim to avoid spaces at the beginning or end.
**alpha_dash**            No         アルファベット、下線、dashesの以外の時にFALSEを返します。
                                     数字を含む場合はFALSEを返します。
**numeric**               No         数字以外の文字を含む場合FALSEを返します。
**integer**               No         整数以外の数字・文字列の場合はFALSEを返します。
**decimal**               No         小数点を含む数字以外の場合は、FALSEを返します。
**is_natural**            No         自然数以外の場合は、FALSEを返します。
                                     0, 1, 2, 3, etc.
**is_natural_no_zero**    No         0を除く自然数の場合以外の場合は、FALSEを返します。
                                     number, but not zero: 1, 2, 3, etc.
**valid_url**             No         URL以外の値の場合は、FALSEを返します。
**valid_email**           No         email以外の値の場合は、FALSEを返します。
**valid_emails**          No         コンマを挟んだ複数のemail以外の値の場合は、FALSEを返します。
**valid_ip**              No         IPアドレス以外の場合は、FALSEを返します。
                                     'ipv4' or 'ipv6' の形式をサポートしています。
**valid_base64**          No         Base64の形式以外の場合は、FALSEを返します。
========================= ========== ============================================================================================= =======================

.. note:: These rules can also be called as discrete methods. For
	example::

		$this->form_validation->required($string);

.. note:: You can also use any native PHP functions that permit up
	to two parameters, where at least one is required (to pass
	the field data).

**********************
整形処理のリファレンス
**********************

The following is a list of all the prepping methods that are available
to use:

==================== ========== =======================================================================================================
名前                 パラメータ 説明
==================== ========== =======================================================================================================
**prep_for_form**    No         Converts special characters so that HTML data can be shown in a form field without breaking it.
**prep_url**         No         Adds "\http://" to URLs if missing.
**strip_image_tags** No         Strips the HTML from image tags leaving the raw URL.
**encode_php_tags**  No         Converts PHP tags to entities.
==================== ========== =======================================================================================================

.. note:: You can also use any native PHP functions that permits one
	parameter, like ``trim()``, ``htmlspecialchars()``, ``urldecode()``,
	etc.

.. _class-reference:

******************
クラスリファレンス
******************

.. php:class:: CI_Form_validation

	.. php:method:: set_rules($field[, $label = ''[, $rules = '']])

		:param	string	$field: Field name
		:param	string	$label: Field label
		:param	mixed	$rules: Validation rules, as a string list separated by a pipe "|", or as an array or rules
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		Permits you to set validation rules, as described in the tutorial
		sections above:

		-  :ref:`setting-validation-rules`
		-  :ref:`saving-groups`

	.. php:method:: run([$group = ''])

		:param	string	$group: The name of the validation group to run
		:returns:	    TRUE on success, FALSE if validation failed
		:rtype:	bool

		Runs the validation routines. Returns boolean TRUE on success and FALSE
		on failure. You can optionally pass the name of the validation group via
		the method, as described in: :ref:`saving-groups`

	.. php:method:: set_message($lang[, $val = ''])

		:param	string	$lang: The rule the message is for
		:param	string	$val: The message
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		Permits you to set custom error messages. See :ref:`setting-error-messages`

	.. php:method:: set_error_delimiters([$prefix = '<p>'[, $suffix = '</p>']])

		:param	string	$prefix: Error message prefix
		:param	string	$suffix: Error message suffix
		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		Sets the default prefix and suffix for error messages.

	.. php:method:: set_data($data)

		:param	array	$data: Array of data validate
		:returns:  	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		Permits you to set an array for validation, instead of using the default
		``$_POST`` array.

	.. php:method:: reset_validation()

		:returns:	CI_Form_validation instance (method chaining)
		:rtype:	CI_Form_validation

		Permits you to reset the validation when you validate more than one array.
		This method should be called before validating each new array.

	.. php:method:: error_array()

		:returns:	Array of error messages
		:rtype:	array

		Returns the error messages as an array.

	.. php:method:: error_string([$prefix = ''[, $suffix = '']])

		:param	string	$prefix: Error message prefix
		:param	string	$suffix: Error message suffix
		:returns:	Error messages as a string
		:rtype:	string

		Returns all error messages (as returned from error_array()) formatted as a
		string and separated by a newline character.

	.. php:method:: error($field[, $prefix = ''[, $suffix = '']])

		:param	string $field: Field name
		:param	string $prefix: Optional prefix
		:param	string $suffix: Optional suffix
		:returns:	Error message string
		:rtype:	string

		Returns the error message for a specific field, optionally adding a
		prefix and/or suffix to it (usually HTML tags).

	.. php:method:: has_rule($field)

		:param	string	$field: Field name
		:returns:	TRUE if the field has rules set, FALSE if not
		:rtype:	bool

		Checks to see if there is a rule set for the specified field.

.. _helper-functions:

********************
ヘルパーリファレンス
********************

Please refer to the :doc:`Form Helper <../helpers/form_helper>` manual for
the following functions:

-  :php:func:`form_error()`
-  :php:func:`validation_errors()`
-  :php:func:`set_value()`
-  :php:func:`set_select()`
-  :php:func:`set_checkbox()`
-  :php:func:`set_radio()`

Note that these are procedural functions, so they **do not** require you
to prepend them with ``$this->form_validation``.
