##############################
フォームバリデーション（検証）
##############################

CodeIgniter では包括的なフォーム検証とデータ準備のクラスを提供します。
あなたが書くつもりのコード量を最小限に抑えることができるでしょう。

.. contents:: Page Contents

****
概要
****

CodeIgniter のデータ検証に対するアプローチを説明する前に、
理想的なシナリオを説明してみましょう:

#. フォームが表示されます。
#. それを記入し、送信します。
#. 無効なものを送信したなら、つまりもしかしたら必須項目を逃した場合は、
   問題を説明するエラーメッセージとともに
   フォームはあなたのデータを再表示します。
#. 有効なフォームを送信するまで、このプロセスが継続します。

受信側では、スクリプトは次のことを満たす必要があります:

#. 必須データを確認します。
#. データが正しい型であり、正しく基準を満たしていることを確認します。
   例えばユーザ名が送信された場合、
   許可された文字だけを含むように検証する必要があります。
   これは最小長より大きく、最大長を超えてはなりません。
   ユーザ名は他の誰かの既存ユーザ名は使えず、またはおそらく予約語も使えません。
   など。
#. セキュリティのためにデータをサニタイズします。
#. 必要に応じてデータをプリフォーマットします
   （データはトリミングを必要とする？　HTML のエンコードは？　など）。
#. データベースに挿入するためのデータを準備します。

上記のプロセスはひどく複雑なものではありませんが、
通常はかなりの量コードを必要とします。エラーメッセージを表示するためには
通常、様々な制御構造がフォームの HTML 内に配置されます。
フォームバリデーションは作成は簡単ながら、
一般的には非常に面倒で退屈な実装作業になります。

************************************
フォームバリデーションチュートリアル
************************************

以下は CodeIgniter のフォームバリデーションを実装するための
「ハンズオン」チュートリアルです。

フォームバリデーションを実装するためには次の 3 つが必要になります:

#. フォームを持つ :doc:`ビュー <../general/views>` ファイル。
#. 送信成功の時に表示される「成功」メッセージを含む
   ビューファイル。
#. 送信されたデータを受信および処理する :doc:`コントローラ <../general/controllers>`
   メソッド。

それではサンプルとしてメンバー登録フォームを使い、
これらの 3 つを作成してみましょう。

入力フォーム
============

テキストエディタを使用して、 myform.php というフォームを作成します。
それには application/views/ フォルダにこのコードを配置して保存します::

	<html>
	<head>
	<title>私のフォーム</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>ユーザ名</h5>
	<input type="text" name="username" value="" size="50" />

	<h5>パスワード</h5>
	<input type="text" name="password" value="" size="50" />

	<h5>パスワード確認</h5>
	<input type="text" name="passconf" value="" size="50" />

	<h5>メールアドレス</h5>
	<input type="text" name="email" value="" size="50" />

	<div><input type="submit" value="Submit" /></div>

	</form>

	</body>
	</html>

成功ページ
==========

テキストエディタを使用して、 formsuccess.php というフォームを作成します。
それには application/views/ フォルダにこのコードを配置して保存します::

	<html>
	<head>
	<title>私のフォーム</title>
	</head>
	<body>

	<h3>あなたのフォームは送信成功しました！</h3>

	<p><?php echo anchor('form', 'もういっかい！'); ?></p>

	</body>
	</html>

コントローラ
============

テキストエディタを使用して、 Form.php というコントローラを作成します。
それには application/controllers フォルダにこのコードを配置して保存します::

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

フォームを試すには次のようなURLを使ってサイトを開いてください::

	example.com/index.php/form/

フォームを送信すると、単にフォームがリロードされるはずです。
それはまだ検証ルールを設定していないためです。

**フォームバリデーションクラスに何の検証も指示していないので、
デフォルトの FALSE（ブール偽）を返します。 ``run()`` メソッドはあなたのルールを適用でき、
1 つも失敗しなかった場合にのみ TRUE
を返します。**

解説
====

上記のページについて、いくつかのことに気付いたことでしょう。:

このフォーム（myform.php）は標準的な Web フォームですが、 2 つの例外があります:

#. フォームの開きタグを作成するために、フォームヘルパーを使用しています。
   技術的には、これは必須ではありません。標準の HTML を使用してフォームを作成することもできます。
   しかしながらヘルパーを使用する利点として、
   config ファイル内の URL に基づいてアクション URL を生成することができます。
   これはあなたのURLを変更する際に、アプリケーションをよりポータブルにしてくれます。
#. フォームの一番上のところで、次の関数呼び出しに気付くでしょう:
   ::

	<?php echo validation_errors(); ?>

   この関数は、バリデータによって戻されたすべてのエラーメッセージを返します。
   メッセージがない場合、空の文字列を返します。

コントローラ（Form.php）には 1 つのメソッドがあります: ``index()`` です。
このメソッドはバリデーションクラスを初期化し、ビューファイルで使用されるフォームヘルパーと URL ヘルパーをロードします。
また、バリデーションルーチンを実行します。
検証が成功したかどうかに基づいて、
フォームと成功ページのどちらかを表示します。

.. _setting-validation-rules:

検証ルールを設定する
====================

CodeIgniter は、与えられたフィールドに必要なだけの多くの検証ルールを設定することができ、
その順序でカスケード処理し、
さらには同時にフィールドデータの準備と前処理をすることができます。
検証ルールを設定するには ``set_rules()`` メソッドを使用します::

	$this->form_validation->set_rules();

上のメソッドは、入力として **3 つ** のパラメータを取ります:

#. フィールド名 - フォームフィールドを与えた正確な名前。
#. このフィールドの「人間向け」の名前。エラーメッセージに挿入されます。
   たとえば、フィールドに「user」と名付けた場合、
   人間向けの名前として「ユーザ名」と名付けるかもしれません。
#. このフォームフィールドの検証ルール。
#. （オプション）このフィールドに指定された任意のルールにカスタムエラーメッセージを設定します。指定されない場合、デフォルトのエラーメッセージを使用します。

.. note:: 言語ファイルに格納されているフィールド名をご希望の場合、
	:ref:`translating-field-names` を参照してください。

ここで一例を示しましょう。コントローラ（Form.php）で、
バリデーション初期化メソッドの直下にこのコードを追加します::

	$this->form_validation->set_rules('username', 'ユーザ名', 'required');
	$this->form_validation->set_rules('password', 'パスワード', 'required');
	$this->form_validation->set_rules('passconf', 'パスワード確認', 'required');
	$this->form_validation->set_rules('email', 'メールアドレス', 'required');

コントローラは次のようになります::

	<?php

	class Form extends CI_Controller {

		public function index()
		{
			$this->load->helper(array('form', 'url'));

			$this->load->library('form_validation');

			$this->form_validation->set_rules('username', 'ユーザ名', 'required');
			$this->form_validation->set_rules('password', 'パスワード', 'required',
				array('required' => '%s は必須です。')
			);
			$this->form_validation->set_rules('passconf', 'パスワード確認', 'required');
			$this->form_validation->set_rules('email', 'メールアドレス', 'required');

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

いま、フィールドが空欄のままフォームを送信すると、エラーメッセージが表示されるはずです。
すべてのフィールドを埋めてフォームを送信すると、
成功ページが表示されるでしょう。

.. note:: エラーが存在するとき、フォームフィールドはまだ入力データで埋めなおされず空欄のままです。
	すぐあとで説明します。

配列を使った検証ルールの指定
============================

先に進む前に、次のことに注意すべきです。
単一の操作ですべてのルールを設定したい場合は、配列を渡すことができます。
この方法を使う場合、つぎに示されているように、配列のキーに名前を付ける必要があります::

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
			'label' => 'パスワード確認',
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

CodeIgniter では複数のルールをパイプで一緒につなげることができます。試してみましょう。
ルール設定メソッドの第 3 パラメータに指定するルールを変更します。このように::

	$this->form_validation->set_rules(
		'username', 'ユーザ名',
		'required|min_length[5]|max_length[12]|is_unique[users.username]',
		array(
			'required'	=> '%s を入力していません。',
			'is_unique'	=> '%s はすでに存在します。'
		)
	);
	$this->form_validation->set_rules('password', 'パスワード', 'required');
	$this->form_validation->set_rules('passconf', 'パスワード確認', 'required|matches[password]');
	$this->form_validation->set_rules('email', 'メールアドレス', 'required|valid_email|is_unique[users.email]');

上のコードは次のルールを設定します:

#. ユーザ名フィールドは 5 文字未満または
   12 文字を超えることはありません。
#. パスワードフィールドは、パスワード確認フィールドと一致する必要があります。
#. メールアドレスフィールドは有効なメールアドレスを含める必要があります。

試してみましょう！　まちがったデータでフォームを送信すると、
新しいルールに対応する新しいエラーメッセージが表示されます。
利用可能なルールは多数あり、バリデーションリファレンスでそれらについて読むことができます。

.. note:: 文字列のかわりに配列で ``set_rules()`` にルールを渡すことができます。
	例::

	$this->form_validation->set_rules('username', 'ユーザ名', array('required', 'min_length[5]'));

データの整形
============

上記で使用しているようなバリデーションメソッドに加え、
様々な方法でデータを整形することもできます。
たとえば、次のようなルールを設定することができます::

	$this->form_validation->set_rules('username', 'ユーザ名', 'trim|required|min_length[5]|max_length[12]');
	$this->form_validation->set_rules('password', 'パスワード', 'trim|required|min_length[8]');
	$this->form_validation->set_rules('passconf', 'パスワード確認', 'trim|required|matches[password]');
	$this->form_validation->set_rules('email', 'メールアドレス', 'trim|required|valid_email');

上の例では、フィールドを「トリミング」し、必要なところでは文字列長をチェックし、
パスワードフィールドの両方が一致することを確認しています。

**あらゆる PHP ネイティブ関数のうちパラメータを 1 つ受けとるものは、ルールとして使用することができます。
``htmlspecialchars()`` 、 ``trim()`` などです。**

.. note:: 一般的には、バリデーションルールの
	**後で** データ整形機能を使用したいことでしょう。
	エラーがある場合にオリジナルのデータをフォームに表示させるためです。

フォームの再表示（データの引き継ぎ）
====================================

ここまではエラーのみを取り扱ってきました。
ここからは送信されたデータでフォームフィールドを埋めなおしていきましょう。CodeIgniter
ではそうするためのヘルパー関数をいくつか提供しています。
最も一般的に使用されるのは、次のものです::

	set_value('field name')

myform.php ビューファイルを開き、
:php:func:`set_value()` 関数を使用して各フィールドの **value** を変えていきましょう:

**:PHP:FUNC:`set_value()` 関数呼び出しに各フィールド名を含めることを
忘れないでください！**

::

	<html>
	<head>
	<title>私のフォーム</title>
	</head>
	<body>

	<?php echo validation_errors(); ?>

	<?php echo form_open('form'); ?>

	<h5>ユーザ名</h5>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>パスワード</h5>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>パスワード確認</h5>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>メールアドレス</h5>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

	<div><input type="submit" value="送信" /></div>

	</form>

	</body>
	</html>

さて、ページをリロードしてエラーを起こすようにフォームを送信します。
フォームフィールドはいま、埋めなおされたことでしょう。

.. note:: 下記の :ref:`class-reference` セクションには
	<select>メニュー、ラジオボタン、およびチェックボックスを埋めなおす
	メソッドがあります。

.. important:: フォームフィールドの name に配列を使用する場合は、
	関数に配列としてそれを指定する必要があります。例::

	<input type="text" name="colors[]" value="<?php echo set_value('colors[]'); ?>" size="50" />

詳細については下記の :ref:`using-arrays-as-field-names` セクションを参照してください。

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
場合、 ``callback_foo[bar]`` のようにメソッド名の後の角カッコの間にパラメータを
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
できます。匿名関数を使うこともできます::

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

匿名関数版::

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

標準のエラーメッセージはすべて、次の言語ファイルの中にあります:
**system/language/english/form_validation_lang.php**

あなた独自のグローバルなカスタムメッセージを
ルールに設定するには、
**application/language/english/form_validation_lang.php** で言語ファイル上書き/拡張するか（これについては
:doc:`言語クラス <language>` のドキュメントを読んでください）、
または次のメソッドを使用します::

	$this->form_validation->set_message('rule', 'エラーメッセージ');

もし特定のルールかつ特定のフィールド用のカスタムエラーメッセージを設定する必要がある場合は、
set_rules() メソッドを使用します::

	$this->form_validation->set_rules('field_name', 'フィールド名', 'rule1|rule2|rule3',
		array('rule2' => 'この field_name の rule2 に使用するエラーメッセージ')
	);

ルールのところはエラーを表示したい特定のルール名に対応し、
エラーメッセージのところは表示したいテキストです。

フィールドの「人間向け」の名前、
またはいくつかのルールが許可しているオプションパラメータ（max_length など）を含めたい場合は、
**{field}** タグと **{param}** タグをメッセージに追加することができます::

	$this->form_validation->set_message('min_length', '{field} は少なくとも {param} 文字必要です。');

人間向けの名前「ユーザ名」と min_length[5] のルールのフィールドでは、
次のエラーが表示されます: "ユーザ名 は少なくとも 5 文字必要です。"

.. note:: **%s** をエラーメッセージ内に使用する古い `sprintf()` の方法はまだ動作しますが、
	しかしそれは上記のタグを上書きします。
	どちらか一方だけを使用すべきです。

上記のコールバックルール例では、エラーメッセージはメソッドの名前を渡すことによって設定されます
（「callback\_」プレフィックスは不要です）::

	$this->form_validation->set_message('username_check')

.. _translating-field-names:

フィールド名の翻訳
==================

``set_rules()`` メソッドに渡される「人間向け」の名前を言語ファイルに保持したい場合、
つまり名前を翻訳できるようにしたい場合、
方法は次のとおりです:

まず、「人間向け」の名前をにプレフィックス **lang:** をつけます。この例のように:

	 $this->form_validation->set_rules('first_name', 'lang:first_name', 'required');

次に、言語ファイルの配列に名前を格納します
（プレフィックスなし）::

	$lang['first_name'] = '名前';

.. note:: CI によって自動的にロードされない言語ファイルで
	配列の項目を追加する場合は、
	コントローラでロードすることを忘れないでください::

	$this->lang->load('file_name');

言語ファイルに関する詳細情報については :doc:`言語クラス <language>`
のページを見てください。

.. _changing-delimiters:

エラーメッセージを囲む文字の変更
================================

デフォルトでは、フォームバリデーションクラスは表示する各エラーメッセージの周囲に段落タグ（<p>）を追加します。
これらの区切り文字はグローバルに、あるいは個別に、
はたまた設定ファイルでデフォルトを変更することができます。

#. **グローバルに区切り文字を変更**
   エラーの区切り文字をグローバルに変更するには、
   コントローラメソッドでフォームバリデーションクラスをロードしたすぐあと、これを追加します::

      $this->form_validation->set_error_delimiters('<div class="error">', '</div>');

   この例では div タグを使用するように切り替えました。

#. **個別に区切り文字を変更**
   このチュートリアルで示している 2 つのエラー文生成機能のそれぞれで、
   次のように独自の区切り文字を指定することができます::

      <?php echo form_error('field name', '<div class="error">', '</div>'); ?>

   または::

      <?php echo validation_errors('<div class="error">', '</div>'); ?>

#. **configファイルで区切り文字を設定**
   application/config/form_validation.php で次のようにエラー区切り文字を設定することができます::

      $config['error_prefix'] = '<div class="error_prefix">';
      $config['error_suffix'] = '</div>';

個別にエラーを表示する
======================

エラーメッセージをリストとしてではなく、
各フォームフィールドの横に表示したい場合は、:php:func:`form_error()` 関数を使用することができます。

やってみましょう！　次のようにフォームを変更します::

	<h5>ユーザ名</h5>
	<?php echo form_error('username'); ?>
	<input type="text" name="username" value="<?php echo set_value('username'); ?>" size="50" />

	<h5>パスワード</h5>
	<?php echo form_error('password'); ?>
	<input type="text" name="password" value="<?php echo set_value('password'); ?>" size="50" />

	<h5>パスワード確認</h5>
	<?php echo form_error('passconf'); ?>
	<input type="text" name="passconf" value="<?php echo set_value('passconf'); ?>" size="50" />

	<h5>メールアドレス</h5>
	<?php echo form_error('email'); ?>
	<input type="text" name="email" value="<?php echo set_value('email'); ?>" size="50" />

エラーがない場合は何も表示されません。エラーがある場合、
メッセージが表示されます。

.. important:: フォームフィールドの名前として配列を使用する場合は、
	関数に配列としてそれを指定する必要があります。例::

	<?php echo form_error('options[size]'); ?>
	<input type="text" name="options[size]" value="<?php echo set_value("options[size]"); ?>" size="50" />

詳細については下記の :ref:`using-arrays-as-field-names` セクションを参照してください。

配列（$_POST以外）のバリデーション
===================================

時には ``$_POST`` データではない配列をバリデーションしたいことでしょう。

その場合、バリデーションする配列を指定することができます::

	$data = array(
		'username' => '山田太郎',
		'password' => 'mypassword',
		'passconf' => 'mypassword'
	);

	$this->form_validation->set_data($data);

バリデーションルールを作成し、バリデーションを実行し、エラーメッセージを取得する一連の動作は
``$_POST`` データまたは設定した別の配列をバリデーションしているかどうかにかかわらず、
同じように動作します。

.. important:: あらゆるバリデーションルールを定義する *前に* ``set_data()`` メソッドを
	呼び出す必要があります。

.. important:: 1 回のプログラム実行内で複数の配列を検証したい場合は、
	ルールを設定して新しい配列をバリデーションする前に ``reset_validation()`` メソッドを
	呼び出す必要があります。

詳細については下記の :ref:`class-reference` セクションを参照してください。

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
