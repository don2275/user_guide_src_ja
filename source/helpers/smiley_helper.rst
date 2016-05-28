##################
スマイリーヘルパー
##################

スマイリーヘルパーのファイルは、スマイリー (顔文字)
を管理できる関数で構成されています。

.. important:: スマイリーヘルパーは非推奨で、利用されるべきではありません。
	現在は、後方互換のためのみに維持されているものです。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('smiley');

概要
====

スマイリーヘルパーは、 :-) [ 訳注: 日本の顔文字の（＾＾）に相当 ]
のようなプレーンテキストの顔文字をもとに、|smile!| のような画像の表現に変換して描画します。

また、クリックしたときにフォームフィールドに挿入できる顔文字画像のセッ
トも表示できます。 たとえば、ユーザがコメントをつけられるブログを作成
しているとき、顔文字のセットをコメントフォームの下に表示させることがで
きます。 ユーザはセットしたい顔文字をクリックでき、 JavaScript
で選択した顔文字がフォームフィールドに置かれます。

クリッカブルスマイリーのチュートリアル
======================================

以下は、フォームフィールドの下にクッリクできる顔文字を作成する方法を説
明した例です。この例では、 まず、顔文字の画像をダウンロードして
インストールする必要があります。そのあと、説明通りにコントローラとビュ
ーを作成します。

.. important:: 始める前に、 `顔文字をダウンロード
	<https://ellislab.com/asset/ci_download_files/smileys.zip>`_ して、 サーバの
	パブリックにアクセスできる場所に設置してください。また、このヘルパーは、
	`application/config/smileys.php`
	にある顔文字置き換え設定の配列が必要になります

コントローラ
------------

**application/controllers/** フォルダの中に、Smileys.php
という名前のファイルを作成し、その中に下記のコードを書いて下さい。

.. important:: :php:func:`get_clickable_smileys()` 関数で、URL を smiley
	フォルダの場所を指すように変更します。

スマイリーヘルパーのほか、 :doc:`HTML テーブルクラス <../libraries/table>`
を使っているのがわかると思います。::

	<?php

	class Smileys extends CI_Controller {

		public function index()
		{
			$this->load->helper('smiley');
			$this->load->library('table');

			$image_array = get_clickable_smileys('http://example.com/images/smileys/', 'comments');
			$col_array = $this->table->make_columns($image_array, 8);

			$data['smiley_table'] = $this->table->generate($col_array);
			$this->load->view('smiley_view', $data);
		}

	}

**application/views/** フォルダの中に、 **smiley_view.php**
という名前のファイルを作成し、その中に下記のコードを書いてください::

	<html>
		<head>
			<title>Smileys</title>
			<?php echo smiley_js(); ?>
		</head>
		<body>
			<form name="blog">
				<textarea name="comments" id="comments" cols="40" rows="4"></textarea>
			</form>
			<p>Click to insert a smiley!</p>
			<?php echo $smiley_table; ?> </body> </html>
			When you have created the above controller and view, load it by visiting http://www.example.com/index.php/smileys/
		</body>
	</html>

フィールドの別名
~~~~~~~~~~~~~~~~

ビューへの変更を行う際に、コントローラにフィールド ID を持たせていては扱いにくい
場合があります。このような問題に対処するため、ビューの中の特定の ID に
関連づけようとしているスマイリーリンクに、一般的な名前を付けることができます。

::

	$image_array = get_smiley_links("http://example.com/images/smileys/", "comment_textarea_alias");

別名とフィールド ID を関連付けるため、:func:`smiley_js()` 関数に
それら両方を渡します。::

	$image_array = smiley_js("comment_textarea_alias", "comments");

利用できる機能
===================

.. php:function:: get_clickable_smileys($image_url[, $alias = ''[, $smileys = NULL]])

	:param	string	$image_url: 顔文字画像フォルダの URL パス
	:param	string	$alias: フィールドの別名
	:returns:	スマイリーをすぐに使うことができる配列
	:rtype:	array

	クッリク可能なリンクで囲まれた顔文字画像の配列を返します。
	顔文字画像のフォルダの URL と、 フィールドの ID か別名を
	指定する必要があります。

	例::

		$image_array = get_clickable_smileys('http://example.com/images/smileys/', 'comment');

.. php:function:: smiley_js([$alias = ''[, $field_id = ''[, $inline = TRUE]]])

	:param	string	$alias: フィールドの別名
	:param	string	$field_id: フィールド ID
	:param	bool	$inline: インラインでスマイリーを挿入するかどうか
	:returns: スマイリーが表示可能なの JavaScript のコード
	:rtype:	string

	顔文字画像をクリックし、フォームフィールドに挿入可能にする JavaScript
	を生成します。 スマイリーリンクを生成する際に、ID
	の代わりに別名を指定する場合、 関数に、別名とそれに関連するフォーム ID
	を渡す必要があります。 この関数は、Web ページの <head>
	領域に設置するように設計されています。

	例::

		<?php echo smiley_js(); ?>

.. php:function:: parse_smileys([$str = ''[, $image_url = ''[, $smileys = NULL]]])

	:param	string	$str: スマイリーコードを含む文字列
	:param	string	$image_url: 顔文字画像フォルダの URL パス
	:param	array	$smileys: スマイリーの配列
	:returns:	パースされたスマイリー
	:rtype:	string

	テキストを入力として、プレーンテキストの顔文字を同じ種類の画像の顔文字
	に変換します。 第1引数には文字列を、第2引数には顔文字画像フォルダの
	URL を指定する必要があります。

	例::

		$str = 'Here are some smileys: :-)  ;-)';
		$str = parse_smileys($str, 'http://example.com/images/smileys/');
		echo $str;

.. |smile!| image:: ../images/smile.gif