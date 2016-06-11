###################
キャプチャ ヘルパー
###################

CAPTCHA ヘルパーのファイルは、CAPTCHA
画像を作成するのに役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

このヘルパーをロードする
========================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('captcha');

キャプチャ ヘルパーを使う
=========================

一旦ロードすれば、このようなキャプチャを生成できます::

	$vals = array(
		'word'		=> 'Random word',
		'img_path'	=> './captcha/',
		'img_url'	=> 'http://example.com/captcha/',
		'font_path'	=> './path/to/fonts/texb.ttf',
		'img_width'	=> '150',
		'img_height'	=> 30,
		'expiration'	=> 7200,
		'word_length'	=> 8,
		'font_size'	=> 16,
		'img_id'	=> 'Imageid',
		'pool'		=> '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ',

		// White background and border, black text and red grid
		'colors'	=> array(
			'background' => array(255, 255, 255),
			'border' => array(255, 255, 255),
			'text' => array(0, 0, 0),
			'grid' => array(255, 40, 40)
		)
	);

	$cap = create_captcha($vals);
	echo $cap['image'];

-  captcha 関数は GD 画像ライブラリを必要とします。
-  **img_path** と **img_url** は必須です。
-  **word** が指定されない場合、
   ランダムな ASCII 文字列が生成されます。
   自前の辞書を使用しても良いでしょう。
-  TRUE TYPE フォントのパスが指定されない場合、標準の見苦しい GD
   フォントが使用されます。
-  "captcha" ディレクトリは書込可能でなければなりません。
-  **expiration** （単位: 秒）は有効期限で、 captcha
   ディレクトリから削除されるまでの時間です。
   デフォルトでは2時間です。
-  **word_length** のデフォルトは8, **pool** のデフォルトは '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ' です。
-  **font_size** のデフォルトは16, 標準のGDフォントにはサイズ制限があります。 より大きいサイズを使用する場合は "true type" を指定してください。
-  **img_id** はキャプチャ画像の "id" として設定されます。
-  **colors** 要素のいずれかを指定しない場合は, デフォルトが指定されます。

データベースの追加
------------------

第三者に送信されるのを防ぐために、 ``create_captcha()``
関数が返す情報をデータベースに格納します。
そして、利用者によりフォームからデータが送信されると、
データが存在することと、 
期限が切れていないことを検証します。

 テーブルの定義::

	CREATE TABLE captcha (  
		captcha_id bigint(13) unsigned NOT NULL auto_increment,  
		captcha_time int(10) unsigned NOT NULL,  
		ip_address varchar(45) NOT NULL,  
		word varchar(20) NOT NULL,  
		PRIMARY KEY `captcha_id` (`captcha_id`),  
		KEY `word` (`word`)
	);

データベースと組み合わせた際の例です。
キャプチャを表示するページの例::

	$this->load->helper('captcha');
	$vals = array(     
		'img_path'	=> './captcha/',     
		'img_url'	=> 'http://example.com/captcha/'     
	);

	$cap = create_captcha($vals);
	$data = array(     
		'captcha_time'	=> $cap['time'],     
		'ip_address'	=> $this->input->ip_address(),     
		'word'		=> $cap['word']     
	);

	$query = $this->db->insert_string('captcha', $data);
	$this->db->query($query);

	echo 'Submit the word you see below:';
	echo $cap['image'];
	echo '<input type="text" name="captcha" value="" />';

送信を受け付けるページの
例::

	// 期限切れのキャプチャを削除
	$expiration = time() - 7200; // 有効期間は2時間です
	$this->db->where('captcha_time < ', $expiration)
		->delete('captcha');

	// キャプチャが存在するか確認:
	$sql = 'SELECT COUNT(*) AS count FROM captcha WHERE word = ? AND ip_address = ? AND captcha_time > ?';
	$binds = array($_POST['captcha'], $this->input->ip_address(), $expiration);
	$query = $this->db->query($sql, $binds);
	$row = $query->row();

	if ($row->count == 0)
	{     
		echo 'You must submit the word that appears in the image.';
	}

利用できる機能
==============

次の関数が利用できます:

.. php:function:: create_captcha([$data = ''[, $img_path = ''[, $img_url = ''[, $font_path = '']]]])

	:param	array	$data: キャプチャ生成のための配列
	:param	string	$img_path: 生成する画像があるパス
	:param	string	$img_url: キャプチャ画像フォルダの URL
	:param	string	$font_path: フォントのサーバパス
	:returns:	array('word' => $word, 'time' => $now, 'image' => $img)
	:rtype:	array

	入力として引数に CAPTCHA 生成のための情報を配列で受け取り、
	指定された画像を生成し、生成された画像に関するデータの
	連想配列を返します。

	::

		array(
			'image'	=> IMAGE TAG
			'time'	=> TIMESTAMP（マイクロ秒まで含む）
			'word'	=> CAPTCHA WORD
		)

	**image** は実際の image タグです::

		<img src="http://example.com/captcha/12345.jpg" width="140" height="50" />

	**time** はマイクロ秒でのタイプスタンプで、拡張子を除いた部分の画像の
	ファイル名として使われます。 このような数字になります: 1139612155.3422

	**word** はキャプチャ画像に表示される単語で、指定されない場合は
	ランダムな文字列になります。
