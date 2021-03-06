# 01-Git Quiz
練習問題です。Gitはエンジニアにとって九九みたいなものなので、
（それほど詳しくない）デザイナーさんやコーダーさんから質問された時に答えられるようになっていると
ベターです。

## パッと出てきて欲しい問題集
### Q1

#### 基礎編
+ 1つのコミットに何が含まれているかを答えなさい
+ コミットxa23aad3の詳細を見るためのコマンドを答えなさい。
+ 2つのブランチbranch-Aとbranch-Bの差分を確認するコマンドを答えなさい
+ HEADとは何を現していますか？
+ branch-Aを削除するためのコマンドを書きなさい。
+ リモートリポジトリにあるブランチbranch-Bを削除するためのコマンドを書きなさい。

#### 実践編
+ リポジトリの状態を前の状態に戻すgitコマンドを3つあげなさい。
+ 上記について、それぞれに関して特徴を1行で記載しなさい。
+ 特定のファイルにバグが潜んでいることがわかりました。変更履歴を追うためにはどうしますか。
+ ある日、作業をしているとXさんに下記の質問をされました。  
「このgit pullで間違ったブランチをpullしてしまって...、元に戻したいんですけど...」
XさんのPCを触って、git pullで事故が起こる直前の状態に戻します。  
どういう操作をしますか？  
NOTE:適宜cpなどは使って良い。ただし、なるべくgitコマンドで  
NOTE:XさんはCSSやHTMLを色々と編集する作業をしている途中でした。  

+ 上記において、git pullで誤ったブランチをpullしてしまった場合、それをpushするとどのような危険性がありますか？  
NOTE:Fast-Forwardなpullである場合を考えてください。  

+ 自分しか作業していないブランチで、git log --onelineを打つと下記の用に見えるとします。  

```
06967a2 [tech-doc] fugafuga4
90bae5b [tech-doc] fugafuga3
0850c29 [tech-doc] fugafuga2
d72f797 [tech-doc] fugafuga1
f1a38b0 [tech-doc] fugafuga0
```

この際、0850c29の状態からやり直して、幾つかのファイルを改めて編集してcommitしてpushしたいとします  
このとき、どのようなコマンドを実行すればよいですか？
## 調べれば良い問題

+ 一般的にリリースに際して「切り戻す」とはどのような操作を刺すか答えなさい。
+ 上記について、Gitを用いて「切り戻す」場合はどのような操作が考えられるか答えなさい。
+ git rebaseをする際の注意点について述べなさい。コミットツリーを図示すること。
+ 上記について、どういう場合にgit rebaseをすると危険か答えなさい。
+ マージでコンフリクトが起きた場合、どのように対処すればよいですか？
+ git log --graph --pretty=onelineが何をやっているか、何の目的で実行するか答えなさい。