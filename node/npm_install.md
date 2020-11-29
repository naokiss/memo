# `npm` パッケージをインストールする

## パッケージバージョン

* セマンティックバージョン

   `npm` のバージョンは x.y.z の3桁の数字であらわされている
   + x : メジャーバージョン。後方互換性のない機能追加・変更
   + y : マイナーバージョン。後方互換性のある機能追加・変更
   + z : パッチバージョン。後方互換性のあるバグフィックスなど

* キャレット表記(`^`)

   一番左側にある、ゼロでないバージョニングは変えない更新
   + `^1.2.3` := `1.2.3` <= {version} < `2.0.0`
   + `^0.2.3` := `0.2.3` <= {version} < `0.3.0`
   + `^0.0.3` := `0.0.3` <= {version} < `0.0.4`

   ※ 注意：マイナーバージョン以降の変更ことではない

* チルダ表記(`~`)

   マイナーバージョンが指定されている場合はパッチバージョンの更新、   
   そうでない場合はマイナーバージョンの更新
   + `~1.2.3` := `1.2.3` <= {version} < `1.3.0`
   + `~1.2`   := `1.2.0` <= {version} < `1.3.0` := `1.2.x`
   + `~1`     := `1.0.0` <= {version} < `2.0.0` := `1.x`
   + `~0.2.3` := `0.2.3` <= {version} < `0.3.0`

* save-prefix

   `package.json` にデフォルトで `^` がつくように設定されている
   ```console
   # プレフィックスの確認
   $ npm config get save-prefix
   ^

   # デフォルトを ~ に変更したい場合
   # ~/.npmrc に保存されるが、プロジェクト配下に置くことも可能
   $ npm config set save-prefix "~"
   $ npm config get save-prefix
   ~
   ```
* save-exact

   `package.json` にプレフィックスをつけるかつけないが設定されている   
   デフォルトでは `false` (プレフィックスをつける)
   ```
   # プレフィックスをつけるつけないの確認
   $ npm config get save-exact
   false

   # デフォルトでバージョンを固定したい場合
   # ~/.npmrc に保存されるが、プロジェクト配下に置くことも可能
   $ npm config set save-exact true
   $ npm config get save-exact
   true
   ```

## package-lock.json とは

* `package.json` でキャレットやチルダで指定すると、インストールする時期によって異なるバージョンがインストールされる。   
それを防ぐため（バージョンを固定にするため）の `package-lock.json` の役割
* npm install の実行時に自動的に作成される。
* 実際にインストールしたパッケージのバージョンが記載される
   + インストール時点で指定範囲に収まる最新バージョンが記載される
* 直接編集してはだめ😆
* gitリポジトリなどに含めることで、パッケージのバージョンを固定にして、全ての開発者で同じパッケージの環境を共有できる
* インポート時の挙動
   + `package-lock.json` が存在しないとき

      `package.json` を参照して、dependency がインストールされ、
      実際にインストールされたバージョンが `package-lock.json` に書かれる。

   + `package-lock.json` が存在するとき

      `package-lock.json` を参照してインストールされるが、
      `package.json` 指定されたバージョンとの矛盾があれば、
      `package.json` が優先され、実際にインストールされたバージョンが `package-lock.json` に書かれる。

## インストールとアンインストール

* `package.json` の作成

   カレントディレクトリにに `package.json` を作成する
   ```console
   # 対話形式でpackage.jsonを生成する
   $ npm init

   # デフォルト値でpackage.jsonを生成する
   $ npm init -y
   $ npm init -yes
   ```

* パッケージのインストール

   ローカルインストール
   パッケージをカレントディレクトリ配下（プロジェクト）の `node_modules` ディレクトリにインストールする
   ```console
   $ npm install {package}
   $ npm i {package}
   ```

   グローバルインストール

   パッケージを npm のルート配下の `node_modules` ディレクトリにインストールする
   ```console
   $ npm install -g {package}
   $ npm install --global {package}
   ```

  バージョンを指定する
   ```console
   $ npm install {package}@{version}
   ```

   `package.json` に記述されているパッケージ
   ```console
   $ npm install

   # package.json の devDependencies のパッケージはインストールしない
   $ npm install --production
   ```

  インストール時に、`package.json` の `Dependencies` に追記する
   ```console
   $ npm install --save {package}
   $ npm install -S {package}
   ```

   `package-lock.json` を優先したインストール (v5.7 以降)
   ```console
   $ npm ci
   ```

  インストール時に、`package.json` の `devDependencies` に追記する
   ```console
   $ npm install --save-dev {package}
   $ npm install -D {package}
   ```

   バージョンを固定にしたインストール

   `package.json` の `Dependencies` や `devDependencies` チルダやキャレットをつけないようにする   
   ※ デフォルトで `npm config` の `save-prefix` で設定されたプレフィックスがつく

   ```
   $ npm install --save --exact {package}
   $ npm install --save -E {package}
   $ npm install -SE {package}
   $ npm install --save-dev --exact {package}
   $ npm install --save-dev -E {package}
   $ npm install -DE {package}
   ```

* パッケージのアンインストール

   ローカルからパッケージをアンインストールする
   ```console
   $ npm uninstall {package}
   $ npm un {package}
   ```

   グローバルからパッケージをアンインストールする
   ```console
   $ npm uninstall -g {package}
   ```

  アンインストール時に、`package.json` の `Dependencies` からも削除する
   ```console
   $ npm uninstall --save {package}
   $ npm uninstall -S {package}
   ```

  アンインストール時に、`package.json` の `devDependencies` からも削除する
   ```console
   $ npm uninstall --save-dev {package}
   $ npm uninstall -D {package}
   ```

* アップデート

   `npm` 自体をアップデート
   ```console
   $ npm update -g npm
   ```

   パッケージをアップデート
   ```console
   $ npm update
   ```

   `npm-check-updates` でpackageを更新する  
   更新可能なパッケージを確認、一括で `package.json` の依存パッケージを変更できる

   ```
   # npm-check-update をインストール
   $ npm install npm-check-update

   # ncu コマンドでパッケージ更新の確認
   $ npx ncu
   Checking /home/naoki/fua/package.json
   [====================] 2/2 100%

   hello  0.3.0  →  0.3.2

   # package.json を更新する
   $ npx ncu -u
   Upgrading /home/naoki/fua/package.json
   [====================] 2/2 100%

   hello  0.3.0  →  0.3.2

   Run npm install to install new versions. 

   # package.json が更新されたのでパッケージを更新する
   $ npm install
   ```
   更新が完了すると `package-lock.json` も更新される

* インストール済みのパッケージを表示

   ```console
   # プロジェクトにインストールされているモジュール一覧
   $ npm list

   # グローバルにインストールされているモジュール一覧
   $ npm list -g
   $ npm list --global
   ```

   `-depth=n` で階層を指定
