################################
CodeIgniter のドキュメントを書く
################################

CodeIgniter は、reStructuredText という形式を使うことで様々なフォーマット
でのドキュメントを生成できる Sphinx を使います。もし、Markdown や Textile
に慣れているなら、reStructuredText を素早く把握することができるでしょう。
焦点は、信頼性とユーザフレンドリーさです。
それらは、とても技術的である一方、常に人にとって読み易いものを書くことがきます。

以下のもののように、目次は常に含まれます。
以下を挿入することで自動的に作成されます。:

::

	.. contents::
		:local:

	.. raw:: html

  	<div class="custom-index container"></div>

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

HTML として挿入された <div> タグは、
現在のページ内のあらゆる関数やメソッド定義へのリンクを動的に追加するために
ドキュメントの JavaScript に仕掛けがされています。

************
必要なツール
************

HTML や ePub、PDFなど表示されたものを見るためには、Sphinx と一緒に
Shpinx のための PHP ドメイン拡張 をインストールする必要があります。
基本的には、Python をインストールする必要があります。
最後に、コードブロックが適切にハイライトされるために Pygments 用の CI Lexer をインストールします。

.. code-block:: bash

	easy_install "sphinx==1.2.3"
	easy_install sphinxcontrib-phpdomain

それから CI Lexer をインストールするためのドキュメントリポジトリの中にある
:samp:`cilexer` フォルダの README ファイルの指示に従ってください。



************************************
ページとセクションの見出し、小見出し
************************************

見出しは、ページ内のセクションや順序を提供するだけではありません。
それらは、また、ページとドキュメントの目次を両方自動的に生成します。
見出しは、ちょっとしたテキストに特定の文字をアンダーラインすることによって
形成されています。ページタイトルやセクションの見出しのような主な見出しは
オーバーラインも使用します。その他の見出しは、次のような階層でアンダーラインのみ使用します。::

	# ページタイトルでは、オーバーラインとともに使用します
	* 主なセクションでは、オーバーラインとともに使用します
	= サブセクション
	- サブセクション2階層目
	^ サブセクション3階層目
	" サブセクション4階層目 (!)

:download:`TextMate ELDocs バンドル <./ELDocs.tmbundle.zip>` は、
以下のタブトリガーでこれらを作成することができます。::

	title->

		##########
		Page Title
		##########

	sec->

		*************
		Major Section
		*************

	sub->

		Subsection
		==========

	sss->

		SubSubSection
		-------------

	ssss->

		SubSubSubSection
		^^^^^^^^^^^^^^^^

	sssss->

		SubSubSubSubSection (!)
		"""""""""""""""""""""""




**********************
メソッドのドキュメント
**********************

サードパーティの開発者のためにクラスメソッドのドキュメントを書く場合
Sphinx は物事をシンプルに保ち手助けするためのディレクティブを提供します。
例えば、次のような ReST が考えられます:

.. code-block:: rst

	.. php:class:: Some_class

		.. php:method:: some_method ( $foo [, $bar [, $bat]])

			この関数は、いくつかのアクションを実行します。``$bar`` 配列は
			何かと何か他のものが含まれている必要があります。そしてそれに加えて
			``$bat`` は任意のパラメータです。

			:param int $foo: 何かをする foo の ID
			:param mixed $bar: 何かと何か他のものが含まれている必要がある配列
			:param bool $bat: 何かを行うかどうか
			:returns: 失敗したら FALSE、 成功したら TRUE
			:rtype: bool

			::

				$this->load->library('some_class');

				$bar = array(
					'something'		=> 'Here is this parameter!',
					'something_else'	=> 42
				);

				$bat = $this->some_class->should_do_something();

				if ($this->some_class->some_method(4, $bar, $bat) === FALSE)
				{
					show_error('An Error Occurred Doing Some Method');
				}

			.. note:: ここは some_method() を使う際に知っておくべき何かです。
					実際の内容。

			:meth:`Some_class::should_do_something`  を見てください。


		.. php:method:: should_do_something()

			:returns: 何かが行われるべきかどうか
			:rtype: bool


以下のように表示されるものを生成します:

.. php:class:: Some_class


	.. php:method:: some_method ( $foo [, $bar [, $bat]])

		この関数は、いくつかのアクションを実行します。``$bar`` 配列は
		何かと何か他のものが含まれている必要があります。そしてそれに加えて
		``$bat`` は任意のパラメータです。
		
		:param int $foo: 何かをする foo の ID
		:param mixed $bar: 何かと何か他のものが含まれている必要がある配列
		:param bool $bat: 何かを行うかどうか
		:returns: 失敗したら FALSE、 成功したら TRUE
		:rtype: bool

		::

			$this->load->library('some_class');

			$bar = array(
				'something'		=> 'Here is this parameter!',
				'something_else'	=> 42
			);

			$bat = $this->some_class->should_do_something();

			if ($this->some_class->some_method(4, $bar, $bat) === FALSE)
			{
				show_error('An Error Occurred Doing Some Method');
			}

		.. note:: ここは some_method() を使う際に知っておくべき何かです。
				実際の内容。

		:meth:`Some_class::should_do_something` を見てください。


	.. php:method:: should_do_something()

		:returns: 何かが行われるべきかどうか
		:rtype: bool
