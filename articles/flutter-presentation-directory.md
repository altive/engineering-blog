---
title: "FlutterアプリのPresentation層構成"
emoji: "🏗"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: [flutter]
published: false
---

@naipaka さん起案ありがとうございます👏

# Flutterアプリ開発におけるWidgetの構成方針

Widgetの構成の方針をチームとして決めたいです🙋‍♂️

具体的には`XxxPage`/`XxxView`/`XxxButton`などの各コンポーネントの分け方を定義したいです。

## 構成についてチームで方針が決まっているメリット

- 実装の際にWidgetの分離に迷いにくくなる
- レビューしやすくなる
- 責務が明確に分けられていてテストが書きやすくなる
などなど…

みなさんは普段どんな構成で考えて実装していますでしょうか？👀
自分はまだこれといった形が定められていないので、みなさんの意見を聞きながら方針が決められたら嬉しいです！

以下Altiveの[flutter_app_template](https://github.com/altive/flutter_app_template)をもとに、自分が普段考えている構成を記載しましたので、「こうした方がよさそう」や「自分ならこういう構成にする」などあれば、コメントいただきたいです！🙏
（そもそもここまで決めなくても良いなどの意見も歓迎です！🙇‍♂️）

---

## 構成の概要

`Page`とその他画面構成要素`でクラス及びファイルを分ける。

### Page
- 各画面と1対1の部品
- 画面を構成する枠組み部分として定義
- Scaffoldを配置し、appBarやbody、floatingActionButtonなどには分離したクラスを設定
    - PageからはappBarのタイトルやbodyの中身、floatingActionButtonがどう動くかなどは知らなくて良い状態とする

#### 具体例
home_page.dart
```dart
class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: HomeAppBar(),
      body: const SafeArea(
        child: HomeBody(),
      ),
      floatingActionButton: const HomeFloatingActionButton(),
    );
  }
}
```

### Body
- 各画面と1対1の部品
- 画面を構成する中身部分として定義
- Scaffoldのbody部分を管理
- 基本各要素を参照するだけ
    - Bodyからは各要素がどのように働くかを知らない状態とする

#### 具体例
home_body.dart
```dart
class HomeBody extends StatelessWidget {
  const HomeBody({super.key});

  @override
  Widget build(BuildContext context) {
    return SingleChildScrollView(
      child: Padding(
        padding: const EdgeInsets.symmetric(
          horizontal: 16,
        ),
        child: Column(
          children: const [
            HomeHeader(),
            HomeTodoList(),
            HomeFooter(),
          ],
        ),
      ),
    );
  }
}
```

### その他画面構成要素
- BodyやAppBar等にのせる画面要素
- まとまりとして認識できるコンポーネント単位で分ける

## ディレクトリ構成例

ホーム画面にTODOリストを表示するような画面をイメージしています。

```
.
├── main.dart
└── presentation
    ├── app.dart
    ├── components
    │   ├── link_text.dart
    │   └── sign_in_button.dart
    ├── pages
    │   └── home
    │       ├── components
    │       │   ├── home_app_bar.dart
    │       │   ├── home_body.dart
    │       │   ├── home_floating_action_button.dart
    │       │   ├── home_footer.dart
    │       │   ├── home_header.dart
    │       │   ├── home_todo_list.dart
    │       │   └── home_todo_list_item.dart
    │       └── home_page.dart
    ├── router
    └── theme
```

※ presentation以外と`page`/`components`以外は省略しています。

