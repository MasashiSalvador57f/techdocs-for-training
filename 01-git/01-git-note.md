# Gitの基礎

## 目次

+ Gitとは
+ Git周りの諸概念
  + コミット / コミットツリー
  + ワークツリー / インデックス
  + マージ
    + Fast forward / Non Fastforward
  + リモート(トラッキング)ブランチ
+ 具体的な使い方
  + commit / push / pull / fetch
  + diffの見方
  + 戻し方
  + 一般に禁じられている行為
+ Git周りのツール
  + GitHub
  + tig

## Gitとは

> git（ギット[2][3][4]）は、プログラムのソースコードなどの変更履歴を記録・追跡するための分散型バージョン管理システムである
(wikipedia)

Gitは分散バージョン管理システムです。  
何が「分散」なのかは追っていくと見えてくると思います。 
バージョン管理ツール(or方法論)は色々あり、

+ Mercurial
+ subversion
+ Dropboxでリアルタイム同期〜（！？）
+ ファイル名に日付と時刻とverを...（アレ)
+ 漢は黙ってノーバージョン管理(ｶｺ(・∀・)ｲｲ!!)

などが挙げられます。  
[Git 対 Subversion：長引く争い](http://readwrite.jp/archives/4492)  
まず、最近の開発スタイルと分散型のバージョン管理が合っているということで分散型のバージョン管理ツールが伸びています。
その中でも、Gitの使い勝手の良さ、GitHubという使いやすいサービスの普及もあってかGitが圧倒的なシェアを誇っています。  
大体日本のweb業界では殆どの会社がGitを使っていると思って間違いないです。  
バージョン管理ツールは、チームで開発、様々な職種の人が分業してサービス開発を行っていくために無くてはならないツールなので、
デザイナー/コーダー/プログラマなど、職種を問わずGitを使いこなすスキルは必要とされています。

## Git周りの諸概念

### コミット/コミットツリー
#### コミット
gitにおける1つのコミットには下記が含まれます。

+ リビジョン (ex: 8f5877860825a43daa1b79da933a19f5f2262fbc)
+ Author(作成者)
+ Commiter(コミット反映者)
+ ファイルのスナップショット
+ 1つ前のコミットのリビジョン

リビジョンはSHA1ハッシュなので、基本的に1つのコミットと1つのハッシュが対応します。
つまりSHA1ハッシュ=ハッシュ文字列を用いて、各コミットを特定できるということです。  
リビジョンはコミットハッシュとも呼ばれます。  

「コミットには1つ前のコミットのコミットハッシュが含まれる」  
これが、gitのコミットの本質を表現している性質です。  
現在のコミットから前のコミットを追うことができるということです。

以下[A],[B], .. [1], [2]など[hoge]でコミットを表すことにします。  

#### コミットツリー

HEAD = 現在  
[A] -> [B] -> [C] ... 最古  
順にたどることが出来ます

[A] -> [B] -> [C] -> [D]  
この一連のコミット全体をコミットツリーと呼びます。  

#### ブランチ
？？？「最新のコミットは8f5877860825a43daa1b79da933a19f5f2262fbc
こいつだよ〜」  
ボブ「何の機能開発しているか全くわからん..」  
となってしまうので、開発者がどの機能がどのコミットツリーに入っているかを
呼びやすくする仕組みがブランチです。

```
(master) [A1] ------------> [B-1] -> [C]
(develop)[A2-1] -> [A2-2] ---|  
```

[A1]のコミットハッシュ   = hogeogefuga......  
[A2-1]のコミットハッシュ = fugafugafoobar...

となっているかもしれませんが、これでは人間にはよくわからないので別名をつけます。  

### ワークツリー / インデックス
Git管理下にある作業ディレクトリをワークツリーと言います。  
作業してファイルを編集しただけではコミットはされない=リポジトリに入りません。
作業結果を作業結果をリポジトリにコミットする準備をする場所として、インデックスが存在しています。

````
リポジトリ:  a.html  b.html 
インデックス b.html # 編集後コミットされようとしている
ワークツリー c.html # 新規ファイル(コミット対象ではない)
```

### マージ
さて、ブランチごとに色々と開発を進めていくわけですが、機能をまとめて行ってテストやリリースを行うためには
上手いこと各人の作業を合わせなければいけません。各人の作業は各人が作ったブランチに記録されていますから
、これらのブランチが含んでいるコミットを合わせることになります。これをマージといいます。
  
例えば、

```
(hotfix) [H1]---------|
(master)            [A1] ------------> [B-1] -> [C]
(develop)           [A2-1] -> [A2-2] ---|        |
(comment)           [A3-1] -> [A3-2] ------------|
```

このような感じでブランチが作成されていたとしましょう。  
この時のマージの挙動を見てみましょう。

#### Non-Fast-Forwardなマージ
developブランチでアリスが開発をしていましたが、masterブランチが実は少し先に進んでいたようです。
developで開発した内容をmasterに入れるため、masterにdevelopをマージするとどうなるでしょうか

```
(master)            [A1] ------------> [B-1] -> [C]
(develop)           [A2-1] -> [A2-2] ---|       
```

さて、合わせたいのですが...developには[A1]のコミットがないのでどうしましょう。ここで、Gitは
[A2-2]の修正内容と[A2-1]の修正内容をmasterブランチに加えようとします。
このマージに際して生じる変更は1つのコミットとして扱われ、『マージコミット」と呼ばれます。  

マージ後
```
(master)       [M1] --->[A1] ------------> [B-1] -> [C]
(develop)               [A2-1] -> [A2-2] ---|      
[M1] = [A2-1]+[A2-2] 
```
さて、この[M1]が上手く作れないときがあります。この時はどうなるのでしょうか...。
例えば、[A1]と[A2-1]が同じファイルを触っていた場合などです。

この時は、コンフリクトといって、マージコミットを作る際に競合している部分を手動で
解消する必要があります。

マージコミットが作られることで、何と何を結合したものが何であるかが、リポジトリに記録されていくのです。

#### Fast-Forwardなマージ
ボブがバグ修正でhotfixブランチを作りました。バグ修正が確認できたので、masterブランチにマージして
リリースを行おうとしています。  
この時はどういうマージになるのでしょう。

マージ前  

```
(hotfix) [H1]---------|
(master)            [A1] ------------> [B-1] -> [C]
```

マージ後


```
(hotfix) 
(master) [H1]-------->[A1] ------------> [B-1] -> [C]
```

hotfixブランチは、masterブランチに1つコミットを足したものです。    
この時、[H1]からたどるとmasterブランチの[A1]までたどり着きます。  
マージ後に、hotfixブランチとmasterブランチは全く同じものになるのです。  
こういったFast-Forwardなマージでは、マージコミットが作成されません。  

さて、これはマージの記録がリポジトリに残らない事になってしまって、ある意味不便といえます。
確実に記録するために、マージコミットを強制的につくるようにマージする方法が存在しています。

上記にあるcommentをdevelopにマージした後、developをhotfixにマージしたらどうなるか、
手元でコミットツリーを書いて試してみましょう。

### リモート(トラッキング)ブランチ
さて、開発者同士が同じリポジトリで共同作業するわけですが、これを
各人のローカル環境にあるリポジトリと、リモートにあるリポジトリを同期させることで実現しています。

下記は、リモートにはtopic/commentブランチがあるが、ローカルにはない状態を現しています。

```
<remote>    (master)
            (develop)
            (hotfix/hoge)
            (topic/comemnt)

<local>    (master)
           (origin/master)
           (develop)
           (origin/develop)
           (hotfix/hoge)
           (origin/hotfix/hoge)
```

さて、ここで<local>環境にoriginで始まるブランチがあることに気づくと思います。
これらを<local>にある、リモートトラッキングブランチと呼びます。
リモートトラッキングブランチは最後に<remote>環境と同期した時のブランチの状態を(おそらくコミットハッシュ)
を保持しています。リモートブランチは、（設定は出来ますが)大抵<remote>環境にあるorigin/以降の名前のブランチと同期指定しています。

## 具体的な使い方

### 状態の確認

```
git branch    # ブランチ一覧表示
git branch -r # リオーとトラッキングブランチ一覧表示
git status    # ワークツリー/インデックスの状態を確認
```

##### statusの見方

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   app/controllers/homes_controller.rb # インデックス

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   app/views/app_templates/index.html.erb # ワークツリー

Untracked files: #まだリポジトリにないからgitからはよくわからないファイル(git管理外のファイル)
  (use "git add <file>..." to include in what will be committed)

	fuga
	hoge
```

```
git status -s # だとこんな感じ
```

```
M  app/controllers/homes_controller.rb
 M app/views/app_templates/index.html.erb
?? fuga
?? hoge
```

色付きで出ます。

### コミット

```
git add hoge # ファイルhogeをステージに追加
git comit    # コミットを作成
git commit -m "foo bar" # コメントと共にcommitを作成
git commit -a # 変更があるファイルを全部コミット... (danger)
```

### ブランチ

```
git checkout $topic            # ワークツリーをtopicブランチに変更
git branch $topic $branch-name # $branch-nameのブランチから$topicという名前のブランチを作成
git checkout -b $topic         # 現在のブランチからtopicというブランチを作ってcheckout
git branch -d $topic           # ブランチの削除(1)
git vbranh -D $topic           # ブランチの削除(2)
git branch --merged $commit-hach # commit-hashがマージされているブランチ一覧を取得
```

### 差分確認

```
git diff #ワークツリーとリポジトリの差分を確認
git diff --cached # インデックスとリポジトリの差分を確認
git diff HEAD~    # 1つ前のリビジョンとの差分を確認
git diff HEAD~~   # 2つ前..
git diff --name-status # M .. modified D ... deleted A ... Addedなどで差分のあるファイルの一覧と状態を取得
git diff --diff-filter=M # 変更のあるファイル、削除されたファイルの一覧などを取得
```

### merge

```
git merge topic # 今のブランチにtopicブランチをマージ
git merge --no-ff topic # 今のブランチにtopicブランチをマージ(Fast-Forwardしない)
```

### コミットの確認

```
git show $commit-hash # $commit-hashのコミットの内容確認
git log  #コミットツリーに含まれるコミットの内容確認
git log -p file # fileのコミットを確認
git blame $file # fileのどの行を誰が変更したか調査する
```

### 戻し方

```
git checkout $file # fileをHEADの状態に戻す git checkout HEAD $fileと同じ
git revert $commit-hash $file # fileの状態を$commit-hashまで戻す
git reset $commit-hash # インデックスに乗っているファイルを$commit-hashまで戻す
git reset --hard $commit-hash # リポジトリ/インデックス/ワークツリーを$commit-hashまで戻す
git revert $commit-hash # $commit-hashのコミットを打ち消すコミット作る
```

### push  / pull

```
git push origin topic # topicブランチをリモートにpush
git pull origin topic # リモートのトピックブランチとリモートトラッキングブランチを同期したあと、topicブランチをローカルブランチにマージ
git pull              # ローカルブランチと同名のブランチをpull
git fetch             # リモートブランチとリモートトラッキングブランチを同期
```

### 一般的な禁じ手(危険度高い)

```
git push -f # 死
git rebase -i # 仕組みを知らないうちは危険
git clean -f  # 同上
```

## Git周りのツール

### GitHub
### tig