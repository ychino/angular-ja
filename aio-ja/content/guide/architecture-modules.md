# モジュールのイントロダクション

<img src="generated/images/guide/architecture/module.png" alt="モジュール" class="left">

Angularアプリケーションはモジュール型のアプリケーションで、 _NgModules_ という独自のモジュール方式を備えています。NgModuleは、アプリケーションドメイン、ワークフロー、または密接に関連する一連の機能をまとめたコードブロックのコンテナです。コンポーネント、サービスプロバイダー、およびNgModuleを含むスコープをもつ他のコードファイルを含めることができます。他のNgModuleからエクスポートされた機能をインポートしたり、他のNgModuleで使用するために選択した機能をエクスポートすることができます。

すべてのAngularアプリケーションには少なくとも1つのNgModuleクラスがあり、[_ルートモジュール_](guide/bootstrapping)は通常`AppModule`と呼ばれ、`app.module.ts`という名前のファイルにあります。ルートのNgModuleを*ブートストラップする*ことでアプリを起動します。

小さなアプリケーションには1つのNgModuleしかないかもしれませんが、ほとんどのアプリケーションにはより多くの機能があります。アプリの _ルート_ NgModuleは、任意の深さの階層に子NgModuleを含めることができるので、その名前が付けられています。

## NgModule メタデータ

NgModuleは`@NgModule`で装飾されたクラスとして定義されています。`@NgModule`デコレーターは、モジュールを記述するプロパティーをもつ単一のメタデータ・オブジェクトを取得する機能です。もっとも重要なプロパティは次のとおりです。

* `declarations`&mdash;このNgModuleに属する[コンポーネント](guide/architecture-components)、_ディレクティブ_、および _パイプ_

* `exports`&mdash;他のNgModuleの _コンポーネントテンプレート_ で可視で使用可能な宣言のサブセット。

* `imports`&mdash;エクスポートされたクラスが _この_ NgModuleで宣言されたコンポーネントテンプレートによって必要とされる他のモジュール。

* `providers`&mdash;このNgModuleが[サービス](guide/architecture-services)のグローバルなコレクションに貢献するサービスの作成元。それらはアプリのすべての部分でアクセス可能になります。 （コンポーネントレベルでプロバイダーを指定することもでき、しばしば好まれます）

* `bootstrap`&mdash; _ルートコンポーネント_ と呼ばれるメインアプリケーションビューで、他のすべてのアプリケーションビューをホストします。_ルートNgModule_ だけがこの`bootstrap`プロパティを設定する必要があります。

単純なルートNgModuleの定義は次のとおりです。

<code-example path="architecture/src/app/mini-app.ts" region="module" title="src/app/app.module.ts" linenums="false"></code-example>

<div class="alert is-helpful">

  `AppComponent`の`export`はエクスポートする方法を示しています。 この例では実際には必要ありません。他のモジュールはルートNgModuleを _インポート_ する必要がないため、ルートNgModuleは何も _エクスポート_ する必要はありません。

</div>

## NgModules とコンポーネント

NgModuleは、そのコンポーネントの _コンパイルコンテキスト_ を提供します。ルートNgModuleには常にブートストラップ時に作成されるルートコンポーネントがありますが、任意のNgModuleに追加のコンポーネントをいくつでも含めることができます。これらのコンポーネントは、ルーター経由でロードするか、テンプレートから作成することができます。NgModuleに属するコンポーネントは、コンパイルコンテキストを共有します。

<figure>

<img src="generated/images/guide/architecture/compilation-context.png" alt="コンポーネントコンパイルコンテキスト" class="left">

</figure>

<br class="clear">

コンポーネントとそのテンプレートは一緒に _ビュー_ を定義します。コンポーネントには、_ビュー階層_ を含めることができます。これにより、画面の任意の複雑な領域を定義し、ユニットとして作成、変更、および破棄することができます。ビュー階層は、異なるNgModuleに属するコンポーネントで定義されたビューを混在させることができます。これは多くの場合、特にUIライブラリの場合に当てはまります。

<figure>

<img src="generated/images/guide/architecture/view-hierarchy.png" alt="ビュー階層" class="left">

</figure>

<br class="clear">

コンポーネントを作成すると、そのコンポーネントは _ホストビュー_ という単一のビューに直接関連付けられます。ホストビューは、他のコンポーネントのホストビューである _埋め込みビュー_ を含むことができるビュー階層のルートにすることができます。これらのコンポーネントは、同じNgModuleに存在することも、他のNgModuleからインポートすることもできます。 ツリー内のビューは、任意の深さにネストすることができます。

<div class="alert is-helpful">
    ビューの階層構造は、AngularがDOMおよびアプリケーションデータの変更を検出して対応する方法における重要な要素です。
</div>

## NgModules と JavaScript モジュール

NgModuleシステムは、JavaScriptオブジェクトのコレクションを管理するJavaScript（ES2015）モジュールシステムとは異なり、関連しません。これら2つは、 _相補的な_ モジュールシステムです。両方を使ってアプリを書くことができます。

JavaScriptでは、各ファイルはモジュールであり、ファイルに定義されているすべてのオブジェクトはそのモジュールに属します。
モジュールは、いくつかのオブジェクトを`export`キーワードでマークすることによって、それらを公開することを宣言します。
他のJavaScriptモジュールはimportステートメントを使用して、他のモジュールのパブリックオブジェクトにアクセスします。

<code-example path="architecture/src/app/app.module.ts" region="imports" linenums="false"></code-example>

<code-example path="architecture/src/app/app.module.ts" region="export" linenums="false"></code-example>

<div class="alert is-helpful">
  <a href="http://exploringjs.com/es6/ch_modules.html">ウェブ上のJavaScriptモジュールシステムの詳細をご覧ください。</a>
</div>

## Angular ライブラリ

<img src="generated/images/guide/architecture/library-module.png" alt="コンポーネント" class="left">

Angularは、JavaScriptモジュールのコレクションとして提供されます。それらをライブラリモジュールと考えることができるでしょう。Angularライブラリの各名前は、`@angular`プレフィックスで始まります。それらを`npm`パッケージマネージャーでインストールし、JavaScriptの`import`ステートメントでそれらの一部をインポートします。

<br class="clear">

たとえば、Angularの`Component`デコレーターを`@angular/core`ライブラリから次のようにインポートします：

<code-example path="architecture/src/app/app.component.ts" region="import" linenums="false"></code-example>

また、JavaScriptのimportステートメントを使用してAngular _ライブラリ_ からNgModuleをインポートします。
たとえば、次のコードは `platformModule` NgModuleを ` platform-browser` ライブラリからインポートします：

<code-example path="architecture/src/app/mini-app.ts" region="import-browser-module" linenums="false"></code-example>

上の単純なルートモジュールの例では、アプリケーションモジュールは `BrowserModule`内のものを必要とします。そのものにアクセスするには、このように`@NgModule`メタデータの`imports`に追加します。

<code-example path="architecture/src/app/mini-app.ts" region="ngmodule-imports" linenums="false"></code-example>

このように、AngularとJavaScriptの _両方の_ モジュールシステムを使用しています。2つのシステムはいずれも"imports"と"exports"という語彙を共通して持っており混乱しやすいですが、使用するにつれてコンテキストの違いに慣れるでしょう。

<div class="alert is-helpful">

  詳細については[NgModules](guide/ngmodules)ページから確認してください。

</div>

<hr/>