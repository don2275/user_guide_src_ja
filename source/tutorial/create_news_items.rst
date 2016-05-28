##################
ニュース記事を作成
##################

あなたは CodeIgniter を使ってデータベースからデータを読み出す方法を学びましたが、
まだデータベースに何も情報を書き込む事をしていません。
この章では news コントローラと以前作ったモデルを拡張し、
書き込み機能を追加していきます。

フォームを作成する
------------------

データベースにデータを入力するには、情報を入力するためのフォームを作成
しなければいけません。 フォームには「題名」と「本文」を入力するための
２つのフィールドが必要です。slug は題名を元にモデル内で生成します。 
*application/views/news/create.php*
に新しいビューを作成してください。

::

    <h2><?php echo $title; ?></h2>

    <?php echo validation_errors(); ?>

    <?php echo form_open('news/create'); ?>

        <label for="title">Title</label> 
        <input type="input" name="title" /><br />

        <label for="text">Text</label>
        <textarea name="text"></textarea><br />

        <input type="submit" name="submit" value="Create news item" /> 

    </form>

この中で見慣れないものが２つあると思います： 
``form_open()`` 関数と ``validation_errors()`` 関数です。

最初の関数は :doc:`formヘルパー <../helpers/form_helper>` 
によって提供されており、form要素を描画すると共に、
:doc:`CSRF 保護フィールド <../libraries/security>` のような追加の
機能も利用可能になります。後者はフォームバリデーションに関する
エラーを出力するためのものです。

では news コントローラに戻ってください。ここで２つの事を行ないます：
フォームが送信されたのかを確認する事と、送信されたデータがバリデーション
のルールを満たすかどうか確認する事です。 これをするには 
:doc:`form validation <../libraries/form_validation>` ライブラリを使用します。

::

    public function create()
    {
        $this->load->helper('form');
        $this->load->library('form_validation');
        
        $data['title'] = 'Create a news item';
        
        $this->form_validation->set_rules('title', 'Title', 'required');
        $this->form_validation->set_rules('text', 'Text', 'required');
        
        if ($this->form_validation->run() === FALSE)
        {
            $this->load->view('templates/header', $data);   
            $this->load->view('news/create');
            $this->load->view('templates/footer');
            
        }
        else
        {
            $this->news_model->set_news();
            $this->load->view('news/success');
        }
    }

上記のコードは様々な処理を追加しています。まず最初の数行でformヘルパー
と form validation ライブラリをロードします。 次に、フォームバリデーション
のルールが定義されます。``set_rules()`` メソッドには３つの引数が渡されます； 
入力フィールドの名前、エラーメッセージに使用される名前、そして実際のルール。
この例では title と text フィールドは必須 (required) とします。

CodeIgniter には上記のような強力なフォームバリデーションのライブラリ
が備わっています。 このライブラリについては 
:doc:`ここで詳細を知ることができます <../libraries/form_validation>` 。

コードの続きを見ていくと、フォームバリデーションが正しく行なわれたかの
条件分岐があります。 もし不正だった場合はフォームのページが表示され、
送信され **なおかつ** 全てのルールを通過した場合はモデルが呼ばれます。
この後、成功時メッセージを表示するビューがロードされます。
*application/view/news/success.php* にビューを作成し、成功時メッセージを書いてください。

モデル
------

残るはデータベースにデータを書き込むメソッドを実装する事です。Query Builder
クラスを使用してデータを挿入し、input ライブラリを使用して post されてきたデータ
を取得します。以前作成したモデルを開き、
次のコードを追記してください：

::

    public function set_news()
    {
        $this->load->helper('url');
        
        $slug = url_title($this->input->post('title'), 'dash', TRUE);
        
        $data = array(
            'title' => $this->input->post('title'),
            'slug' => $slug,
            'text' => $this->input->post('text')
        );
        
        return $this->db->insert('news', $data);
    }

このメソッドはデータベースにニュース記事を挿入してくれます。
3行目には url\_title() という新しい関数があります。
:doc:`URL ヘルパー <../helpers/url_helper>` によって提供
されているこの関数は、渡した文字列を解析し、空白スペースを全て
ハイフン(-)に置換して全文字を小文字にしてくれます。
結果はURIを生成するのにぴったりの綺麗なslugです。

続けて、``$data`` 配列内に実際に挿入されるレコードを準備しましょう。
それぞれの要素は以前作成したデータベースのカラムと対応しています。
ここで post() という
:doc:`inputライブラリ <../libraries/input>`
の新しいメソッドに気づくと思います。このメソッドはあなたのデータが
正しくサニタイズされるようにして、悪意のある外部攻撃からあなたを
守ってくれます。input ライブラリはデフォルトで読み込まれるライブラリ
です。最後に ``$data`` 配列をデータベースに挿入しておしまいです。

ルーティング
------------

CodeIgniter アプリケーションにニュース記事を追加し始められる前に、
*config/routes.php* に新しいルールを追加しなければいけません。
あなたのファイルに下記が追加されている事を確認してください。
これによりCodeIgniterがcreateメソッドをニュース記事の slug ではなく、独自のメソッドである事を保証します。

::

    $route['news/create'] = 'news/create';
    $route['news/(:any)'] = 'news/view/$1';
    $route['news'] = 'news';
    $route['(:any)'] = 'pages/view/$1';
    $route['default_controller'] = 'pages/view';

ではブラウザ上であなたの CodeIgniter の開発環境を開き、
URLに index.php/news/create を入れてみてください。
おめでとうございます、これであなたは初めての CodeIgniter アプリケーションを完成させました！
いくつかニュースを追加してみて、それぞれのページを確認してみてください。
