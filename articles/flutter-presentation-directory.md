---
title: "FlutterアプリのPresentation層構成方針"
emoji: "🏗"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: [flutter]
published: true
---

当方針は [@naipaka](https://zenn.dev/naipaka) さんにより起案・骨子考案いただきました！ありがとうございます👏

[@keimiya_325](https://twitter.com/keimiya_325) さん、ディスカッションへの参加ありがとうございます🙌

# Flutterアプリ開発におけるWidgetの構成方針

チームでFlutterアプリ開発に参画することが増えて、人ぞれぞれWidgetの粒度や整理の仕方が違うことを実感しました。
何にでも適用できる優れた正解と呼べる構成はおそらく存在しませんが、会社やチームで統一された方針があれば良い指針となりそうです。

## 構成についてチームで方針が決まっているメリット

- 実装の際に `Widget` の構成や命名、分離の仕方に迷いにくくなる
- 画面や実装者によって実装がバラバラにならないので、プルリクエストがレビューしやすくなる
- 責務が明確に分けらやすいので、テストが書きやすくなる

などなど…

## サンプルリポジトリ
方針を実際に適用したサンプルとなるリポジトリです。

https://github.com/altive/flutter_app_template/tree/main/packages/flutter_app/lib/presentation/

主にUIを担当する `presentation` ディレクトリ以下に適用してあります。

（ `riverpod_examples` など、一部本のサンプルページとして使用しているページは適用外としています）

---

## 構成の概要

モバイルアプリでは画面、Webではページを表す `Page` とその他の画面構成要素でクラス及びファイルを分ける。

### Page
- 各画面（ページ）と1対1となるWidget
- 画面を構成する枠組み部分として定義
- `Scaffold` を配置し、 `appBar` や `body` 、 `floatingActionButton` などには分離したクラスを指定する。
    - `Page` からは `appBar` のタイトルや `body` の中身、 `floatingActionButton` がどう動くかなどは知らなくて良い状態とする。

#### 具体例
home_page.dart
```dart
class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: HomePageAppBar(),
      body: const SafeArea(
        child: HomePageBody(),
      ),
      floatingActionButton: const HomePageFloatingActionButton(),
    );
  }
}
```

### Body
- 各画面（ページ）と1対1となるWidget
- 画面を構成する、メインのコンテンツ部分として定義
- `Scaffold` の `body`部分を管理
- `Page` の第一構成要素であることを表すため、 `XxxPageBody` のようにページ名と `Page` を含む命名とする
- さらなる各コンポーネントを参照する
  - `Body` からは各要素がどのように働くかを知らない状態とする

#### 具体例
home_page_body.dart
```dart
class HomePageBody extends StatelessWidget {
  const HomePageBody({super.key});

  @override
  Widget build(BuildContext context) {
    return SingleChildScrollView(
      padding: const EdgeInsets.symmetric(
        horizontal: 16,
      ),
      child: Column(
        children: const [
          HomeHeader(),
          HomeTodoListView(),
          HomeFooter(),
        ],
      ),
    );
  }
}
```

### AppBar , FloatingActionButton
- 各画面（ページ）と1対1となるWidget
- `Scaffold` の `appBar` や `floatingActionButton` 部分を担当
- `Page` の第一構成要素であることを表すため、 `XxxPageAppBar` や `XxxPageFloatingActionButton` のようにページ名と `Page` を含む命名とする
- さらなる各コンポーネントを参照する

### その他画面構成要素（components）
- `Body` や `AppBar` 等でから呼び出して使用するWidget
- まとまりとして認識できる部品単位で分ける
- `Page` の第一構成要素ではないので、命名に `Page` は含めない

## ディレクトリ構成例

アプリのホーム画面（ページ）にTODOリストを表示するような画面をイメージしています。

```
.
├── main.dart
└── presentation
    ├── app.dart
    ├── components // <- アプリ全体や複数の画面で使用するようなコンポーネントWidget
    ├── pages
    │   └── home
    │       ├── components
    │       │   ├── home_page_app_bar.dart
    │       │   ├── home_page_body.dart
    │       │   ├── home_page_floating_action_button.dart
    │       │   ├── home_footer.dart
    │       │   ├── home_header.dart
    │       │   ├── home_todo_list_view.dart
    │       │   └── home_todo_list_item.dart
    │       └── home_page.dart
    ├── router
    └── style
```

※ `presentation` 外と`page`/`components`以外は省略して表記しています。

