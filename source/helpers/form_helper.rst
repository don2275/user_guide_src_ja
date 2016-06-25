################
フォームヘルパー
################

フォームヘルパーのファイルは、フォームをを処理するのに
役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('form');

フィールド値のエスケープ
========================

フォーム要素の中に HTML やクォートのような文字列を使う必要が
あるかもしれません。安全に実行するために、
:doc:`共通関数 <../general/common_functions>`
:func:`html_escape()` を使用する必要があります。

次の例を考えてみましょう::

	$string = 'Here is a string containing "quoted" text.';

	<input type="text" name="myfield" value="<?php echo $string; ?>" />

上記の文字列はクォートのセットが含まれていますので、フォームが壊れてしまう
原因となります。 :php:func:`html_escape()` 関数は、 HTML 特殊文字を
安全に使用できるように変換します::

	<input type="text" name="myfield" value="<?php echo html_escape($string); ?>" />

.. note:: このページに記載されているフォームヘルパー関数の
	いずれかを使用している場合、フォームの値は自動的にエスケープされます。
	この関数を呼ぶ必要はありません。
	独自のフォーム要素を作成している場合にのみ、使用してください。

利用できる機能
==============

次の関数が利用できます:


.. php:function:: form_open([$action = ''[, $attributes = ''[, $hidden = array()]]])

	:param	string	$action: フォームの action/target である URI 文字列
	:param	array	$attributes: HTML 属性
	:param	array	$hidden: 隠しフィールドを定義した配列
	:returns:	HTML フォームの開始タグ
	:rtype:	string

	**設定ファイルに基づいて構築した** ベース URL を元にフォームの
	開始タグを作成します。 オプションで、form タグの属性と隠しフィールド
	を追加する事ができ、設定ファイルの文字コードの値にもとづき、
	常に `accept-charset` 属性が付与されます。

	HTML をハードコーディングせずにこのコードを使う主なメリットは、URL
	が変更になった時に、移植性が高まることです。

	シンプルな例です::

		echo form_open('email/send');

	上記の例では、次のように、ベース URL に "email/send" URI
	セグメントを追加したところを参照する form タグを生成します::

		<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send">

	**属性の追加**

		次のように、第2引数に連想配列を渡すことで、
		属性を追加できます::

			$attributes = array('class' => 'email', 'id' => 'myform');
			echo form_open('email/send', $attributes);

		かわりに、第2引数に文字列を指定することができます::

			echo form_openG'email/send', 'class="email" id="myform"');

		上記の例は、次のようなフォームを生成します::

			<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send" class="email" id="myform">

	**隠しフィールドの追加**

		次のように、第3引数に連想配列を渡すことで、
		隠しフィールドを追加できます::

			$hidden = array('username' => 'Joe', 'member_id' => '234');
			echo form_open('email/send', '', $hidden);

		false となるような値を渡すことで、第2引数をスキップすることができます。

		上記の例は、次のようなフォームを生成します::

			<form method="post" accept-charset="utf-8" action="http://example.com/index.php/email/send">
				<input type="hidden" name="username" value="Joe" />
				<input type="hidden" name="member_id" value="234" />


.. php:function:: form_open_multipart([$action = ''[, $attributes = array()[, $hidden = array()]]])

	:param	string	$action: フォームの action/target である URI 文字列
	:param	array	$attributes: HTML 属性
	:param	array	$hidden: 隠しフィールドを定義した配列
	:returns:	HTML マルチパートフォームの開始タグ
	:rtype:	string

	ファイルをアップロードする際に使う *マルチパート (multipart)*
	の指定を属性に追加する以外は、この関数は上記の :php:func:`form_open()`
	とまったく同じです。


.. php:function:: form_hidden($name[, $value = ''])

	:param	string	$name: フィールド名
	:param	string	$value: フィールドの値
	:returns:	HTML 隠しフィールドタグ
	:rtype:	string

	隠しフィールドを生成します。1つのフィールドの 名前 / 値
	の両方の文字列を渡すことができます::

		form_hidden('username', 'johndoe');
		// 次のようなタグを生成します: <input type="hidden" name="username" value="johndoe" />

	あるいは、複数のフィールドを作成するために、連想配列を渡すこともできます::

		$data = array(
			'name'	=> 'John Doe',
			'email'	=> 'john@example.com',
			'url'	=> 'http://example.com'
		);

		echo form_hidden($data);

		/*
			次のようなタグを生成します:
			<input type="hidden" name="name" value="John Doe" />
			<input type="hidden" name="email" value="john@example.com" />
			<input type="hidden" name="url" value="http://example.com" />
		*/

	値に連想配列を渡すことも可能です::

		$data = array(
			'name'	=> 'John Doe',
			'email'	=> 'john@example.com',
			'url'	=> 'http://example.com'
		);

		echo form_hidden('my_array', $data);

		/*
			次のようなタグを生成します:

			<input type="hidden" name="my_array[name]" value="John Doe" />
			<input type="hidden" name="my_array[email]" value="john@example.com" />
			<input type="hidden" name="my_array[url]" value="http://example.com" />
		*/

	属性を追加して隠しフィールドを作りたい場合::

		$data = array(
			'type'	=> 'hidden',
			'name'	=> 'email',
			'id'	=> 'hiddenemail',
			'value'	=> 'john@example.com',
			'class'	=> 'hiddenemail'
		);

		echo form_input($data);

		/*
			次のようなタグを生成します:

			<input type="hidden" name="email" value="john@example.com" id="hiddenemail" class="hiddenemail" />
		*/

.. php:function:: form_input([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML テキストフィールドタグ
	:rtype:	string

	通常のテキストフィールドを生成します。
	最低限、第1、第2引数に、名前と値をそれぞれ渡します::

		echo form_input('username', 'johndoe');

	あるいは、フォームに含めたい任意のデータを含む連想配列を渡すことも
	できます::

		$data = array(
			'name'		=> 'username',
			'id'		=> 'username',
			'value'		=> 'johndoe',
			'maxlength'	=> '100',
			'size'		=> '50',
			'style'		=> 'width:50%'
		);

		echo form_input($data);

		/*
			次のようなタグを生成します:

			<input type="text" name="username" value="johndoe" id="username" maxlength="100" size="50" style="width:50%"  />
		*/

	フォームに JavaScript のようないくつかの追加データを含めたい場合、
	第3引数に文字列を渡すことができます::

		$js = 'onClick="some_function()"';
		echo form_input('username', 'johndoe', $js);

	あるいは、配列として渡すこともできます::

		$js = array('onClick' => 'some_function();');
		echo form_input('username', 'johndoe', $js);

.. php:function:: form_password([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML password フィールドタグ
	:rtype:	string

	この関数は、"password" タイプのフィールドをセットする以外は、上記の
	:php:func:`form_input()` 関数とあらゆる点で同じです。


.. php:function:: form_upload([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML ファイルアップロードフィールドタグ
	:rtype:	string

	この関数は、ファイルのアップロード時に使用できる "file"
	タイプのフィールドをセットする以外は、上記の :php:func:`form_input()`
	関数とあらゆる点で同じです。


.. php:function:: form_textarea([$data = ''[, $value = ''[, $extra = '']]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML textarea タグ
	:rtype:	string

	この関数は、"textarea" のフィールドをセットする以外は、上記の
	:php:func:`form_input()` 関数とあらゆる点で同じです。

	.. note:: 上記の例の *maxlength* と *size* 属性を指定するかわりに、
		*rows* と *cols* 属性を指定します。

.. php:function:: form_dropdown([$name = ''[, $options = array()[, $selected = array()[, $extra = '']]]])

	:param	string	$name: フィールド名
	:param	array	$options: 一覧にしたいオプションの連想配列
	:param	array	$selected: *selected* 属性をつけるためのフィールドのリスト
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML ドロップダウンフィールドタグ
	:rtype:	string

	通常のドロップダウンフィールドを生成します。第1引数にフィールド名を、
	第2引数に選択肢（option）の連想配列を、
	そして第3引数には、選択済み状態にしたい
	項目の値を設定します。
	第3引数に複数の項目の配列を渡すことで、CodeIgniter は複数選択を
	生成することができます。

	例::

		$options = array(
			'small'		=> 'Small Shirt',
			'med'		=> 'Medium Shirt',
			'large'		=> 'Large Shirt',
			'xlarge'	=> 'Extra Large Shirt',
		);

		$shirts_on_sale = array('small', 'large');
		echo form_dropdown('shirts', $options, 'large');

		/*
			次のようなタグを生成します:

			<select name="shirts">
				<option value="small">Small Shirt</option>
				<option value="med">Medium  Shirt</option>
				<option value="large" selected="selected">Large Shirt</option>
				<option value="xlarge">Extra Large Shirt</option>
			</select>
		*/

		echo form_dropdown('shirts', $options, $shirts_on_sale);

		/*
			次のようなタグを生成します:

			<select name="shirts" multiple="multiple">
				<option value="small" selected="selected">Small Shirt</option>
				<option value="med">Medium  Shirt</option>
				<option value="large" selected="selected">Large Shirt</option>
				<option value="xlarge">Extra Large Shirt</option>
			</select>
		*/

	<select> の開始タグで、 id 属性や JavaScript
	などの追加データを設定したい場合は、
	第4引数にそれを文字列として渡すことができます::

		$js = 'id="shirts" onChange="some_function();"';
		echo form_dropdown('shirts', $options, 'large', $js);

	あるいは、配列として渡すこともできます::

		$js = array(
			'id'       => 'shirts',
			'onChange' => 'some_function();'
		);
		echo form_dropdown('shirts', $options, 'large', $js);

	``$options`` に渡される配列が多次元配列である場合、
	``form_dropdown()`` は配列のキーをラベルとした
	<optgroup> を生成します。


.. php:function:: form_multiselect([$name = ''[, $options = array()[, $selected = array()[, $extra = '']]]])

	:param	string	$name: フィールド名
	:param	array	$options: 一覧にしたいオプションの連想配列
	:param	array	$selected: *selected* 属性をつけるためのフィールドのリスト
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns: HTML 複数選択フィールドタグ
	:rtype:	string

	標準的な複数選択フィールドを生成します。
	第1引数はフィールド名、
	第2引数はオプションの連想配列、
	第3引数は選択状態にしたい値です。

	フィールド名に、例として foo[] のような POST の配列を
	利用する必要がある、という点を除けば、
	引数の使い方は上記の :php:func:`form_dropdown()` と同じです。


.. php:function:: form_fieldset([$legend_text = ''[, $attributes = array()]])

	:param	string	$legend_text: <legend> タグの中に設定される文字列
	:param	array	$attributes: <fieldset> タグに設定される属性
	:returns:	HTML fieldset の開始タグ
	:rtype:	string

	fieldset/legend フィールドを生成します。

	例::

		echo form_fieldset('Address Information');
		echo "<p>fieldset content here</p>\n";
		echo form_fieldset_close();

		/*
			次のようなタグを生成します:

				<fieldset>
					<legend>Address Information</legend>
						<p>form content here</p>
				</fieldset>
		*/

	他の関数同様、各属性に値を設定したい場合は、第2引数に連想配列を渡すこ
	とができます::

		$attributes = array(
			'id'	=> 'address_info',
			'class'	=> 'address_info'
		);

		echo form_fieldset('Address Information', $attributes);
		echo "<p>fieldset content here</p>\n";
		echo form_fieldset_close();

		/*
			次のようなタグを生成します:

			<fieldset id="address_info" class="address_info">
				<legend>Address Information</legend>
				<p>form content here</p>
			</fieldset>
		*/


.. php:function:: form_fieldset_close([$extra = ''])

	:param	string	$extra: 終了タグの後に *そのまま* 追加するもの
	:returns:	HTML fieldset の閉じタグ
	:rtype:	string
	

	</fieldset> の閉じタグを生成します。
	タグの下に追加するデータを渡せるというのがこの関数を使う
	唯一の利点になります。例

	::

		$string = '</div></div>';
		echo form_fieldset_close($string);
		// 次のようなタグを生成します: </fieldset></div></div>


.. php:function:: form_checkbox([$data = ''[, $value = ''[, $checked = FALSE[, $extra = '']]]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	bool	$checked: *checked* としてチェックボックスにマークするかどうか
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML チェックボックスフィールドタグ
	:rtype:	string

	チェックボックスフィールドを生成します。簡単な例です::

		echo form_checkbox('newsletter', 'accept', TRUE);
		// 次のようなタグを生成します:  <input type="checkbox" name="newsletter" value="accept" checked="checked" />

	第3引数には、チェックボックスがチェック済みかそうでないかを決める
	ブール値の TRUE/FALSE を設定できます。

	このヘルパーも他のフォーム関数と同様に属性を
	連想配列で渡すことができます::

		$data = array(
			'name'		=> 'newsletter',
			'id'		=> 'newsletter',
			'value'		=> 'accept',
			'checked'	=> TRUE,
			'style'		=> 'margin:10px'
		);

		echo form_checkbox($data);
		// 次のようなタグを生成します: <input type="checkbox" name="newsletter" id="newsletter" value="accept" checked="checked" style="margin:10px" />

	他の関数のように、このタグに JavaScript のような
	追加データを設定したい場合は、
	第4引数にそれを文字列として渡すことができます::

		$js = 'onClick="some_function()"';
		echo form_checkbox('newsletter', 'accept', TRUE, $js);

	あるいは、配列として渡すこともできます::

		$js = array('onClick' => 'some_function();');
		echo form_checkbox('newsletter', 'accept', TRUE, $js);


.. php:function:: form_radio([$data = ''[, $value = ''[, $checked = FALSE[, $extra = '']]]])

	:param	array	$data: フィールドの属性データ
	:param	string	$value: フィールドの値
	:param	bool	$checked: *checked* としてラジオボタンにマークするかどうか
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML ラジオボタンタグ
	:rtype:	string

	この関数は、"radio" タイプのフィールドをセットする以外は、上記の
	:php:func:`form_checkbox()` 関数とあらゆる点で同じです。


.. php:function:: form_label([$label_text = ''[, $id = ''[, $attributes = array()]]])

	:param	string	$label_text: <label> タグの中に設定する文字列
	:param	string	$id: ラベルタグを生成するフォーム要素の ID
	:param	string	$attributes: HTML 属性
	:returns:	HTML ラベルタグ
	:rtype:	string

	<label> を生成します。 簡単な例です::

		echo form_label('What is your Name', 'username');
		// 次のようなタグを生成します:  <label for="username">What is your Name</label>

	他の関数同様、各属性に値を設定したい場合は、第3引数に連想配列を渡す
	ことができます。

	例::

		$attributes = array(
			'class' => 'mycustomclass',
			'style' => 'color: #000;'
		);

		echo form_label('What is your Name', 'username', $attributes);
		// 次のようなタグを生成します:  <label for="username" class="mycustomclass" style="color: #000;">What is your Name</label>


.. php:function:: form_submit([$data = ''[, $value = ''[, $extra = '']]])

	:param	string	$data: ボタン名
	:param	string	$value: ボタンの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML 送信ボタンタグ
	:rtype:	string

	通常の送信ボタンを生成します。簡単な例です::

		echo form_submit('mysubmit', 'Submit Post!');
		// 次のようなタグを生成します:  <input type="submit" name="mysubmit" value="Submit Post!" />

	他の関数同様、各属性に値を設定したい場合は、第1引数に、連想配列を渡す
	ことができます。 第3引数で、JavaScript のような
	追加データをフォームに設定できます。


.. php:function:: form_reset([$data = ''[, $value = ''[, $extra = '']]])

	:param	string	$data: ボタン名
	:param	string	$value: ボタンの値
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML リセットボタンタグ
	:rtype:	string

	通常のリセットボタンを生成します。
	使い方は :func:`form_submit()` と同様です。


.. php:function:: form_button([$data = ''[, $content = ''[, $extra = '']]])

	:param	string	$data: ボタン名
	:param	string	$content: ボタンラベル
	:param	mixed	$extra: 配列または文字列リテラルとしてタグに追加される追加属性
	:returns:	HTML ボタンタグ
	:rtype:	string

	通常のボタンを作成します。1つ目と2つ目の引数にボタンの名前とコンテンツ
	を渡すだけでもボタンを作ることができます::

		echo form_button('name','content');
		// 次のようなタグを生成します: <button name="name" type="button">Content</button>

	あるいは、フォームに含めたい任意のデータを連想配列で
	渡すことができます::

		$data = array(
			'name'		=> 'button',
			'id'		=> 'button',
			'value'		=> 'true',
			'type'		=> 'reset',
			'content'	=> 'Reset'
		);

		echo form_button($data);
		// 次のようなタグを生成します: <button name="button" id="button" value="true" type="reset">Reset</button>

	フォームに JavaScript のような追加のデータを含めたい場合、
	第3引数に文字列で渡すことができます::

		$js = 'onClick="some_function()"';
		echo form_button('mybutton', 'Click Me', $js);


.. php:function:: form_close([$extra = ''])

	:param	string	$extra: 終了タグの後に *そのまま* 追加するもの
	:returns:	HTML フォームの終了タグ
	:rtype:	string

	</form> の閉じタグを生成します。タグの下に追加するデータ
	を渡せるというのがこの関数を使う唯一の利点になります。
	例::

		$string = '</div></div>';
		echo form_close($string);
		// 次のようなタグを生成します:  </form> </div></div>


.. php:function:: set_value($field[, $default = ''[, $html_escape = TRUE]])

	:param	string	$field: フィールド名
	:param	string	$default: デフォルト値
	:param  bool	$html_escape: 値の HTML エスケース処理をオフにするかどうか
	:returns:	フィールドの値
	:rtype:	string

	入力フォームやテキストエリアの値を設定します。
	関数の第1引数でフィールド名を指定します。
	第2引数（オプション）では、フォームの初期値を指定できます。
	例えば :php:func:`form_input()` と組み合わせてこの関数を使って
	二重にエスケープされることを避ける必要がある場合、
	第3引数（オプション）では、値の HTML のエスケープ処理をオフにできます。

	例::

		<input type="text" name="quantity" value="<?php echo set_value('quantity', '0'); ?>" size="50" />

	上記のフォームは、最初に読み込まれた時には"0"を表示します。

	.. note:: :doc:`フォームバリデーション (検証) <../libraries/form_validation>` をロードし
		このヘルパーで使われているフィールド名の検証ルールを設定している場合、
		:doc:`フォームバリデーション (検証) <../libraries/form_validation>` 自身の
		``set_value()`` メソッドの呼び出しを行います。
		それ以外の場合、この関数はフィールドの値を設定するために ``$_POST`` を参照します。

.. php:function:: set_select($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: フィールド名
	:param	string	$value: チェックするための値
	:param	string	$default: 値がデフォルト値かどうか
	:returns:	'selected' 属性または空文字列
	:rtype:	string

	<select> メニューを利用している場合、この関数はメニューで選択された項
	目を表示します。

	第1引数には選択メニューの名前を指定します。
	第2引数ではそれぞれの項目の値を指定します。 第3引数（オプション）では、ブール値の
	TRUE/FALSE で項目の初期状態を指定できます。

	例::

		<select name="myselect">
			<option value="one" <?php echo  set_select('myselect', 'one', TRUE); ?> >One</option>
			<option value="two" <?php echo  set_select('myselect', 'two'); ?> >Two</option>
			<option value="three" <?php echo  set_select('myselect', 'three'); ?> >Three</option>
		</select>

.. php:function:: set_checkbox($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: フィールド名
	:param	string	$value: チェックするための値
	:param	string	$default: 値がデフォルト値かどうか
	:returns:	'checked' 属性または空文字列
	:rtype:	string

	送信された状態のチェックボックスを表示します。

	第1引数にはチェックボックスの名前を指定し、第2引数では値を指定します。
	第3引数（オプション）では、ブール値の TRUE/FALSE
	を使って項目の初期状態を指定できます。

	例::

		<input type="checkbox" name="mycheck" value="1" <?php echo set_checkbox('mycheck', '1'); ?> />
		<input type="checkbox" name="mycheck" value="2" <?php echo set_checkbox('mycheck', '2'); ?> />

.. php:function:: set_radio($field[, $value = ''[, $default = FALSE]])

	:param	string	$field: フィールド名
	:param	string	$value: チェックするための値
	:param	string	$default: 値がデフォルト値かどうか
	:returns:	'checked' 属性または空文字列
	:rtype:	string

	送信された状態のラジオボタンを表示します。
	この関数は上記の :php:func:`set_checkbox()` と同じ挙動です。

	例::

		<input type="radio" name="myradio" value="1" <?php echo  set_radio('myradio', '1', TRUE); ?> />
		<input type="radio" name="myradio" value="2" <?php echo  set_radio('myradio', '2'); ?> />

	.. note:: フォームバリデーション（検証）クラスを使っている場合、
		たとえ値が空であろうとも、``set_*()`` が動作するために、常にフィールドに
		ルールを指定する必要があります。その理由は、もしフォームバリデーション（検証）オブジェクトが
		定義されていれば ``set_*()`` のための制御は、一般的なヘルパー関数のかわりのクラスの
		メソッドに引き渡されれるからです。

.. php:function:: form_error([$field = ''[, $prefix = ''[, $suffix = '']]])

	:param	string	$field:	フィールド名
	:param	string	$prefix: エラーの開始タグ
	:param	string	$suffix: エラーの終了タグ
	:returns:	HTML フォーマットされたフォームのバリデーションエラーメッセージ（複数）
	:rtype:	string

	:doc:`フォームバリデーション（検証） <../libraries/form_validation>` より
	指定したフィールド名に関連付けらたバリデーション（検証）エラーメッセージを返します。
	必要に応じて、エラーメッセージの周りに置く開始タグと終了タグ（複数可）を
	指定することができます

	例::

		// Assuming that the 'username' field value was incorrect:
		echo form_error('myfield', '<div class="error">', '</div>');

		// 次のようなタグを生成します: <div class="error">Error message associated with the "username" field.</div>


.. php:function:: validation_errors([$prefix = ''[, $suffix = '']])

	:param	string	$prefix: エラーの開始タグ
	:param	string	$suffix: エラーの終了タグ
	:returns:	HTML フォーマットされたフォームのバリデーションエラーメッセージ（複数）
	:rtype:	string

	:php:func:`form_error()` 関数と同様に
	必要に応じて、それぞれのメッセージの周りに置く開始タグと終了タグを指定し
	:doc:`フォームバリデーション（検証） <../libraries/form_validation>`
	によって生成されたすべてのバリデーション（検証）エラーメッセージを返します。

	例::

		echo validation_errors('<span class="error">', '</span>');

		/*
			次のようなタグを生成します。例:

			<span class="error">The "email" field doesn't contain a valid e-mail address!</span>
			<span class="error">The "password" field doesn't match the "repeat_password" field!</span>

		 */

.. php:function:: form_prep($str)

	:param	string	$str: エスケープする値
	:returns:	エスケープされた値
	:rtype:	string

	フォームを壊すことなく HTML やフォーム要素内の引用符のような文字列を
	安全に使用することができます。

	.. note:: このページに記載されているフォームヘルパー関数のいずれかを使用している場合、
		フォームの値が自動的にエスケープされるため、この関数を呼び出す必要はありません。
		独自のフォーム要素を作成している場合にのみ、使用してください。

	.. note:: この関数は廃止予定で、
		:doc:`共通関数 <../general/common_functions>` :func:`html_escape()` のエイリアスです。
		かわりにそちらを使用してください。
