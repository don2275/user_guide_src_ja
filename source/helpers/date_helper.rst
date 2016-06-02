############
日付ヘルパー
############

日付ヘルパーのファイルは、日付を処理するのに役立つ関数で構成されています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

ヘルパーのロード
================

このヘルパーは次のコードを使ってロードします::

	$this->load->helper('date');

利用できる機能
==============

次の関数が利用できます:


.. php:function:: now([$timezone = NULL])

	:param	string	$timezone: Timezone
	:returns:	UNIX timestamp
	:rtype:	int

	現在時刻をUNIXタイムスタンプで返します。設定ファイルの "time reference" の
	設定により、サーバのローカル時間または PHP でサポートされるタイムゾーンを
	指す時刻になります。
	マスターの時間を PHP でサポートされるタイムゾーンに設定しないつもりなら、
	(各ユーザが各々のタイムゾーンを設定することができるサイトであれば通常そうすると
	思います) PHP の time()関数を使わずに、この関数を利用する利点はありません。
	::

		echo now('Australia/Victoria');

	タイムゾーンを指定しない場合、 戻り値として **time_reference** の設定に基づく
	UNIXタイムスタンプの値を返却します。

.. php:function:: mdate([$datestr = ''[, $time = '']])

	:param	string	$datestr: 日付文字列
	:param	int	$time: UNIX タイムスタンプ
	:returns:	MySQL フォーマットされた日付
	:rtype:	string

	この関数は、%Y %m %d のように各コードの前に % がついている MySQLスタイルの
	日付コードを利用可能なこと以外は、 PHP の `date()<http://www.php.net/date>`_ 関数と
	同じです。

	日付をこの方法で扱う利点は、date()
	関数を使った時には通常しなければならない、 日付コードでない文字列の
	エスケープ処理について考慮する必要がないという点です。

	例::

		$datestring = 'Year: %Y Month: %m Day: %d - %h:%i %a';
		$time = time();
		echo mdate($datestring, $time);

	第2引数にタイムスタンプを指定しない場合は、
	現在時刻が使われます。

.. php:function:: standard_date([$fmt = 'DATE_RFC822'[, $time = NULL]])

	:param	string	$fmt: 日付文字列
	:param	int	$time: UNIX タイムスタンプ
	:returns:	フォーマットされた日付又は不正なフォーマットの場合に FALSE
	:rtype:	string

	複数の標準フォーマットのうち、ひとつの形式の日付文字列を生成できます。

	例::

		$format = 'DATE_RFC822';
		$time = time();
		echo standard_date($format, $time);

	.. 注:: この関数は廃止予定です。 代わりにPHP 標準の ``date()`` と
		`DateTime's format constants
		<http://php.net/manual/ja/class.datetime.php#datetime.constants.types>`_
		を使用してください。::

			echo date(DATE_RFC822, time());

	**Supported formats:**

	===============	=======================	======================================
	Constant        Description             Example
	===============	=======================	======================================
	DATE_ATOM       Atom                    2005-08-15T16:13:03+0000
	DATE_COOKIE     HTTP Cookies            Sun, 14 Aug 2005 16:13:03 UTC
	DATE_ISO8601    ISO-8601                2005-08-14T16:13:03+00:00
	DATE_RFC822     RFC 822                 Sun, 14 Aug 05 16:13:03 UTC
	DATE_RFC850     RFC 850                 Sunday, 14-Aug-05 16:13:03 UTC
	DATE_RFC1036    RFC 1036                Sunday, 14-Aug-05 16:13:03 UTC
	DATE_RFC1123    RFC 1123                Sun, 14 Aug 2005 16:13:03 UTC
	DATE_RFC2822    RFC 2822                Sun, 14 Aug 2005 16:13:03 +0000
	DATE_RSS        RSS                     Sun, 14 Aug 2005 16:13:03 UTC
	DATE_W3C        W3C                     2005-08-14T16:13:03+0000
	===============	=======================	======================================

.. php:function:: local_to_gmt([$time = ''])

	:param	int	$time: UNIX タイムスタンプ
	:returns:	UNIX タイムスタンプ
	:rtype:	int

	UNIX タイムスタンプを入力として、その時刻をGMT(グリニッジ標準時)として返します。

	例::

		$gmt = local_to_gmt(time());

.. php:function:: gmt_to_local([$time = ''[, $timezone = 'UTC'[, $dst = FALSE]]])

	:param	int	$time: UNIX タイムスタンプ
	:param	string	$timezone: タイムゾーン
	:param	bool	$dst: サマータイムが有効かどうか
	:returns:	UNIX タイムスタンプ
	:rtype:	int

	UNIX タイムスタンプ (グリニッジ標準時を指します) を入力として、
	渡されたタイムゾーンとサマータイム適用区分にもとづいて、その地域の時刻に
	変換します。

	例::

		$timestamp = 1140153693;
		$timezone  = 'UM8';
		$daylight_saving = TRUE;
		echo gmt_to_local($timestamp, $timezone, $daylight_saving);


	.. note:: タイムゾーンのリストは、このページの一番下のリファレンスをご覧ください。

.. php:function:: mysql_to_unix([$time = ''])

	:param	string	$time: MySQL タイムスタンプ
	:returns:	UNIX タイムスタンプ
	:rtype:	int

	MySQL タイムスタンプを入力として、その時刻を UNIX タイムスタンプとして返します。

	例::

		$unix = mysql_to_unix('20061124092345');

.. php:function:: unix_to_human([$time = ''[, $seconds = FALSE[, $fmt = 'us']]])

	:param	int	$time: UNIX タイムスタンプ
	:param	bool	$seconds: 秒を表示するかどうか
	:param	string	$fmt: フォーマット (us 又は euro)
	:returns:	フォーマットされた日付
	:rtype:	string

	UNIXタイムスタンプを入力として、次の例のように、人間が読める形式で
	返します::

		YYYY-MM-DD HH:MM:SS AM/PM

	これは、フォームの送信のために、フォームフィールドに表示したい場合に
	役立ちます。

	時間は、秒の部分をつける形式とつけない形式にフォーマットでき、
	ヨーロッパ形式またはアメリカ形式にセットできます。 タイムスタンプだけが
	渡された時は、秒の部分がない形式で、アメリカ形式にフォーマットされます。

	例::

		$now = time();
		echo unix_to_human($now); // 秒なしのアメリカ形式
		echo unix_to_human($now, TRUE, 'us'); // 秒ありのアメリカ形式
		echo unix_to_human($now, TRUE, 'eu'); // 秒ありのヨーロッパ形式

.. php:function:: human_to_unix([$datestr = ''])

	:param	int	$datestr: 日付文字列
	:returns:	UNIX タイムスタンプ又は失敗した場合は FALSE
	:rtype:	int

	:php:func:`unix_to_time()` 関数の反対です。"人" の時間を入力として、
	UNIXタイムスタンプを返します。 これは、フォームから "人"が読める形式に
	フォーマットされた日付を受け取る時に役立ちます。渡された文字列が、
	上で示したようなフォーマットでない場合、ブール値の FALSE を返します。

	Example::

		$now = time();
		$human = unix_to_human($now);
		$unix = human_to_unix($human);

.. php:function:: nice_date([$bad_date = ''[, $format = FALSE]])

	:param	int	$bad_date: 不完全な日付フォーマット
	:param	string	$format: 返却される日付フォーマット ( PHP の ``date()`` 関数と同様)
	:returns:	フォーマットされた日付
	:rtype:	string

	この関数は不完全な日付フォーマットの数字を引数に取り、有用な形式に変換
	します。正しい日付フォーマットを引数に取ることもできます。

	デフォルトでは UNIX タイムスタンプを返します。オプションとして、
	第2引数にフォーマット文字列( PHP の date 関数が引き受けるものと同じ)
	を渡すことができます。

	例::

		$bad_date = '199605';
		// 次の日付を生成: 1996-05-01
		$better_date = nice_date($bad_date, 'Y-m-d');

		$bad_date = '9-11-2001';
		// 次の日付を生成: 2001-09-11
		$better_date = nice_date($bad_date, 'Y-m-d');

.. php:function:: timespan([$seconds = 1[, $time = ''[, $units = '']]])

	:param	int	$seconds: 秒数
	:param	string	$time: UNIX タイムスタンプ
	:param	int	$units: 表示する時間の単位
	:returns:	フォーマットされた時刻の差
	:rtype:	string

	UNIX タイムスタンプを次の例で示したようにフォーマットします::

		1 Year, 10 Months, 2 Weeks, 5 Days, 10 Hours, 16 Minutes

	第1引数には、 UNIX タイムスタンプを指定する必要があります。
	第2引数には、第1引数で渡したタイムスタンプよりも大きい(後の時間の)
	UNIX タイムスタンプを指定する必要があります。
	第3引数は、オプションです。表示する時間の単位を数値で制限します。

	もし第2引数が空だった場合は現在時刻が使用されます。

	この関数の主要な目的は、過去のある時点から現在までの経過時間を
	表示するというものです。

	例::

		$post_date = '1079621429';
		$now = time();
		$units = 2;
		echo timespan($post_date, $now, $units);

	.. 注:: この関数が生成するテキストは、次の言語ファイルの中にあります
		file: language/<あなたの言語>/date_lang.php

.. php:function:: days_in_month([$month = 0[, $year = '']])

	:param	int	$month: 月数
	:param	int	$year: 年数
	:returns:	指定された月の日数
	:rtype:	int

	指定された年月の日数を返します。
	うるう年が考慮されます。

	例::

		echo days_in_month(06, 2005);

	第2引数が空の時、現在の年が使われます。

	.. 注:: この関数は ``cal_days_in_month()`` が利用できる場合は
		エイリアスになります。

.. php:function:: date_range([$unix_start = ''[, $mixed = ''[, $is_unix = TRUE[, $format = 'Y-m-d']]]])

	:param	int	$unix_start: 開始日の UNIX タイムスタンプ
	:param	int	$mixed: 終了日の UNIX タイムスタンプ又は日数
	:param	bool	$is_unix: 第2引数がタイムスタンプでない場合は FALSE を設定する
	:param	string	$format: 出力する日付フォーマット。 ``date()`` 関数と同様。
	:returns:	日付の配列
	:rtype:	array

	指定した期間で日付のリストが返却されます。

	例::

		$range = date_range('2012-01-01', '2012-01-15');
		echo "First 15 days of 2012:";
		foreach ($range as $date)
		{
			echo $date."\n";
		}

.. php:function:: timezones([$tz = ''])

	:param	string	$tz: 数値のタイムゾーン
	:returns:	UTCからの時差
	:rtype:	int

	タイムゾーンリファレンス(有効なタイムゾーンのリストは、下の
	"タイムゾーンリファレンス" を参照してください)を引数にとり、UTC
	からの時差を数字で返します。

	例::

		echo timezones('UM5');


	このメソッドは、:php:func:`timezone_menu()` とともに使うと役立ちます。

.. php:function:: timezone_menu([$default = 'UTC'[, $class = ''[, $name = 'timezones'[, $attributes = '']]]])

	:param	string	$default: タイムゾーン
	:param	string	$class: クラス名
	:param	string	$name: メニュー名
	:param	mixed	$attributes: HTML 属性
	:returns:	タイムゾーンの HTML プルダウンメニュー
	:rtype:	string

	次のようなタイムゾーンのプルダウンメニューを生成します:

	.. raw:: html

		<form action="#">
			<select name="timezones">
				<option value='UM12'>(UTC -12:00) Baker/Howland Island</option>
				<option value='UM11'>(UTC -11:00) Samoa Time Zone, Niue</option>
				<option value='UM10'>(UTC -10:00) Hawaii-Aleutian Standard Time, Cook Islands, Tahiti</option>
				<option value='UM95'>(UTC -9:30) Marquesas Islands</option>
				<option value='UM9'>(UTC -9:00) Alaska Standard Time, Gambier Islands</option>
				<option value='UM8'>(UTC -8:00) Pacific Standard Time, Clipperton Island</option>
				<option value='UM7'>(UTC -7:00) Mountain Standard Time</option>
				<option value='UM6'>(UTC -6:00) Central Standard Time</option>
				<option value='UM5'>(UTC -5:00) Eastern Standard Time, Western Caribbean Standard Time</option>
				<option value='UM45'>(UTC -4:30) Venezuelan Standard Time</option>
				<option value='UM4'>(UTC -4:00) Atlantic Standard Time, Eastern Caribbean Standard Time</option>
				<option value='UM35'>(UTC -3:30) Newfoundland Standard Time</option>
				<option value='UM3'>(UTC -3:00) Argentina, Brazil, French Guiana, Uruguay</option>
				<option value='UM2'>(UTC -2:00) South Georgia/South Sandwich Islands</option>
				<option value='UM1'>(UTC -1:00) Azores, Cape Verde Islands</option>
				<option value='UTC' selected='selected'>(UTC) Greenwich Mean Time, Western European Time</option>
				<option value='UP1'>(UTC +1:00) Central European Time, West Africa Time</option>
				<option value='UP2'>(UTC +2:00) Central Africa Time, Eastern European Time, Kaliningrad Time</option>
				<option value='UP3'>(UTC +3:00) Moscow Time, East Africa Time</option>
				<option value='UP35'>(UTC +3:30) Iran Standard Time</option>
				<option value='UP4'>(UTC +4:00) Azerbaijan Standard Time, Samara Time</option>
				<option value='UP45'>(UTC +4:30) Afghanistan</option>
				<option value='UP5'>(UTC +5:00) Pakistan Standard Time, Yekaterinburg Time</option>
				<option value='UP55'>(UTC +5:30) Indian Standard Time, Sri Lanka Time</option>
				<option value='UP575'>(UTC +5:45) Nepal Time</option>
				<option value='UP6'>(UTC +6:00) Bangladesh Standard Time, Bhutan Time, Omsk Time</option>
				<option value='UP65'>(UTC +6:30) Cocos Islands, Myanmar</option>
				<option value='UP7'>(UTC +7:00) Krasnoyarsk Time, Cambodia, Laos, Thailand, Vietnam</option>
				<option value='UP8'>(UTC +8:00) Australian Western Standard Time, Beijing Time, Irkutsk Time</option>
				<option value='UP875'>(UTC +8:45) Australian Central Western Standard Time</option>
				<option value='UP9'>(UTC +9:00) Japan Standard Time, Korea Standard Time, Yakutsk Time</option>
				<option value='UP95'>(UTC +9:30) Australian Central Standard Time</option>
				<option value='UP10'>(UTC +10:00) Australian Eastern Standard Time, Vladivostok Time</option>
				<option value='UP105'>(UTC +10:30) Lord Howe Island</option>
				<option value='UP11'>(UTC +11:00) Srednekolymsk Time, Solomon Islands, Vanuatu</option>
				<option value='UP115'>(UTC +11:30) Norfolk Island</option>
				<option value='UP12'>(UTC +12:00) Fiji, Gilbert Islands, Kamchatka Time, New Zealand Standard Time</option>
				<option value='UP1275'>(UTC +12:45) Chatham Islands Standard Time</option>
				<option value='UP13'>(UTC +13:00) Phoenix Islands Time, Tonga</option>
				<option value='UP14'>(UTC +14:00) Line Islands</option>
			</select>
		</form>


	このメニューは、ユーザごとのローカル時間ををセットできる会員制サイトの
	場合に使えます。

	第1引数で、メニューの "選択(selected)" 状態 を指定します。たとえば、
	太平洋標準時をデフォルト値にセットしたい場合は、次のようにします::

		echo timezone_menu('UM8');

	メニューに指定する値を調べるには、下記のタイムゾーンリファレンスをご覧ください

	第2引数では、メニューの CSS クラスの名前を指定できます。

	第4引数は生成されるプルダウンメニューに一つ以上の HTML 属性を設定できます。

	.. note:: このメニューに含まれるテキストは、次の言語ファイルの中にあります:
		language/<あなたの言語>/date_lang.php

タイムゾーンリファレンス
========================

次の表は、地域ごとの各タイムゾーンを示したものです。

いくつかの地域のリストは見易さとフォーマットの都合で要約されています。

===========     =====================================================================
Time Zone       Location
===========     =====================================================================
UM12            (UTC - 12:00) Baker/Howland Island
UM11            (UTC - 11:00) Samoa Time Zone, Niue
UM10            (UTC - 10:00) Hawaii-Aleutian Standard Time, Cook Islands
UM95            (UTC - 09:30) Marquesas Islands
UM9             (UTC - 09:00) Alaska Standard Time, Gambier Islands
UM8             (UTC - 08:00) Pacific Standard Time, Clipperton Island
UM7             (UTC - 07:00) Mountain Standard Time
UM6             (UTC - 06:00) Central Standard Time
UM5             (UTC - 05:00) Eastern Standard Time, Western Caribbean
UM45            (UTC - 04:30) Venezuelan Standard Time
UM4             (UTC - 04:00) Atlantic Standard Time, Eastern Caribbean
UM35            (UTC - 03:30) Newfoundland Standard Time
UM3             (UTC - 03:00) Argentina, Brazil, French Guiana, Uruguay
UM2             (UTC - 02:00) South Georgia/South Sandwich Islands
UM1             (UTC -1:00) Azores, Cape Verde Islands
UTC             (UTC) Greenwich Mean Time, Western European Time
UP1             (UTC +1:00) Central European Time, West Africa Time
UP2             (UTC +2:00) Central Africa Time, Eastern European Time
UP3             (UTC +3:00) Moscow Time, East Africa Time
UP35            (UTC +3:30) Iran Standard Time
UP4             (UTC +4:00) Azerbaijan Standard Time, Samara Time
UP45            (UTC +4:30) Afghanistan
UP5             (UTC +5:00) Pakistan Standard Time, Yekaterinburg Time
UP55            (UTC +5:30) Indian Standard Time, Sri Lanka Time
UP575           (UTC +5:45) Nepal Time
UP6             (UTC +6:00) Bangladesh Standard Time, Bhutan Time, Omsk Time
UP65            (UTC +6:30) Cocos Islands, Myanmar
UP7             (UTC +7:00) Krasnoyarsk Time, Cambodia, Laos, Thailand, Vietnam
UP8             (UTC +8:00) Australian Western Standard Time, Beijing Time
UP875           (UTC +8:45) Australian Central Western Standard Time
UP9             (UTC +9:00) Japan Standard Time, Korea Standard Time, Yakutsk
UP95            (UTC +9:30) Australian Central Standard Time
UP10            (UTC +10:00) Australian Eastern Standard Time, Vladivostok Time
UP105           (UTC +10:30) Lord Howe Island
UP11            (UTC +11:00) Srednekolymsk Time, Solomon Islands, Vanuatu
UP115           (UTC +11:30) Norfolk Island
UP12            (UTC +12:00) Fiji, Gilbert Islands, Kamchatka, New Zealand
UP1275          (UTC +12:45) Chatham Islands Standard Time
UP13            (UTC +13:00) Phoenix Islands Time, Tonga
UP14            (UTC +14:00) Line Islands
===========	=====================================================================
