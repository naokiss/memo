# モジュールについて

## モジュールの種類

+ コアモジュール
    - `Node.js` に標準に組み込まれているモジュール
+ `npm` モジュール
    - サードパーティ製の外部ライブラリ
    - `npm` コマンドと `package.json` による自動管理
+ ファイルモジュール
    - 自作モジュール

## `CommonJS(CJS)` と ` ECMAScript Modules(ESM)`

* `CommonJS(CJS)`

    + Node.js でしか動かない
    + 厳格ではない
    + 同期
        - `require` は同期関数なので、ローディングは実行時に順次行われる
    + 静的解析が不可能
        - `require` 関数がファイルのローディング時に解析する
    + `require` 構文を使用する
        - `require` で `.js` ファイルを読み込むときは拡張子を省略できる
        - `require` で `.cjs` ファイルを読み込むときは拡張子を省略できない
    + 拡張子は `.cjs`
    + 拡張子 `.js` は `package.json` に `"type": "commonjs"` が指定されているときに、`CommonJS` として実行される（省略時は `CommonJS`）

* ` ECMAScript Modules(ESM)`

    + ブラウザ互換
    + 厳格
        - `JavaScript` はファイルの先頭に `'use strict';` をつけるとStrict モードになるが、ESMではデフォルトで有効になる
    + 非同期
        - モジュールのローディングとパースの処理が非同期で行われる
    + 静的解析可能
        - JSエンジンがモジュールを解析する
    + `import/export` 構文を使用する
        - `import` で拡張子は省略できない
        - ただし、`--es-module-specifier-resolution=node` フラグをつけて実行すると、拡張子が省略可能になる
    + 拡張子は `.mjs`
    + 拡張子 `.js` は `package.json` に `"type": "module"` が指定されているときに、`ESM` として実行される
    + `Node.js 13.2.0` 以前のバージョンでは、`--experimental-modules`フラグ付きで起動したときのみ、`ECMAScript Modules`をサポートしていたが、`13.2.0`からフラグなしで `import` が可能になった
    + `require`、`exports`、`module.exports`, `__filename`, `__dirname` といった `Node.js` 特有のグローバル変数や関数は ESM では利用できない
        - `module.createRequire` 関数を使うことで `require 関数`を生成できる

            ```
            import module from "module";
            const require = module.createRequire(import.meta.url);

            const packageJson = require("../package.json");
            ```

        - `fileURLToPath` 関数を使うことで `__filename` を生成できる

            ```
            import { fileURLToPath } from 'url';
            const __filename = fileURLToPath(import.meta.url);
            ```

        - `dirname` 関数を使うことで `__dirname` を生成できる

            ```
            import { dirname } from 'path';
            const __dirname = dirname(__filename);
            ```

## モジュールの理解

`Node.js` の `v14.15.1` で確認する

### `CJS` と `ESM` のどちらで動作しているのかの確認

* 拡張子による違い

    index.cjs
    ```JavaScript
    console.log(typeof module);
    ```

    index.mjs
    ```JavaScript
    console.log(typeof module);
    ```

    実行結果
    ```console
    $ node index.cjs
    object
    
    $ node index.mjs
    undefined
    ```

* `package.json` の `type` による違い (拡張子は `.js` )

    index.js
    ```JavaScript
    console.log(typeof module);
    ```

    `package.json` に `"type": "commonjs"` を設定する(省略は `commonjs` )
    ```console
    $ node index.js
    object
    ```

    `package.json` に `"type": "module"` を設定する
    ```console
    $ node index.js
    undefined
    ```

* 階層化されたディレクトリで `package.json` を設定

    同じディレクトリかあるいは一番近い上位ディレクトリの `package.json` の設定が使われている
    ```
    /
    ├── package.json             # "type": "module"
    ├── index.js
    └── sub
        ├── index.js
        └── sub
            ├── package.json     # "type": "commonjs"
            ├── index.js
            └── sub
                └ index.js
    ```

    実行結果
    ```console
    $ node index.js
    undefined
    $ node sub/index.js
    undefined
    $ node sub/sub/index.js
    object
    $ node sub/sub/sub/index.js
    object
    ```
### インポートとエクスポートの確認

* `ESM` から `ESM` をインポートする

    インポートするファイルを作成する

    sub.mjs
    ```JavaScript
    export const hoge = "Hello hoge";
    export default "Hello fuga";
    ```

    まず、拡張子を指定して、インポートしてみると、問題なくインポートできる

    index.mjs
    ```JavaScript
    // 拡張子 .mjs を指定する
    import fuga, { hoge } from './sub.mjs';

    console.log(hoge);
    console.log(fuga);
    ```

    実行結果
    ```console
    $ node index.mjs
    Hello hoge
    Hello fuga
    ```

    次に拡張子を省略してインポートしてみるとエラーになる

    index.mjs
    ```JavaScript
    // 拡張子 .mjs を省略する
    import fuga, { hoge } from './sub';

    console.log(hoge);
    console.log(fuga);
    ```

    実行結果
    ```console
    $ node index.mjs
    internal/process/esm_loader.js:74
        internalBinding('errors').triggerUncaughtException(
                                ^

    Error [ERR_MODULE_NOT_FOUND]: Cannot find module '/home/taro/sample-app/imp/sub' imported from /home/taro/sample-app/imp/index.mjs
        at finalizeResolution (internal/modules/esm/resolve.js:276:11)
        at moduleResolve (internal/modules/esm/resolve.js:699:10)
        at Loader.defaultResolve [as _resolve] (internal/modules/esm/resolve.js:810:11)
        at Loader.resolve (internal/modules/esm/loader.js:85:40)
        at Loader.getModuleJob (internal/modules/esm/loader.js:229:28)
        at ModuleWrap.<anonymous> (internal/modules/esm/module_job.js:51:40)
        at link (internal/modules/esm/module_job.js:50:36) {
      code: 'ERR_MODULE_NOT_FOUND'
    }
    ```
    
    続いて

    * 拡張子を `.js` にリネームする
    * 拡張子を `.js` のまま、`package.json` に `"type": "module"` を設定する
    * 拡張子を `.js` にリネームして、`package.json` に `"type": "module"` を設定する

    結果、同様のエラーになる

* `CJS` から `CJS` をインポートする

    インポートされる `CJS` ファイルを作成する

    sub.cjs
    ```JavaScript
    module.exports = {
        hoge: "Hello hoge"
    }
    ```

    まず、拡張子を指定して、インポートしてみると問題なくインポートできる

    index.cjs
    ```JavaScript
    // 拡張子 .mjs を指定する
    const hoge = require('./sub.cjs');

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    { hoge: 'Hello hoge' }
    ```

    次に拡張子を省略してインポートしてみるとエラーになる

    index.cjs
    ```JavaScript
    // 拡張子 .cjs を省略する
    const hoge = require('./sub');

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    internal/modules/cjs/loader.js:883
    throw err;
    ^

    Error: Cannot find module './sub'
    Require stack:
    - /home/taro/sample-app/imp/index.cjs
        at Function.Module._resolveFilename (internal/modules/cjs/loader.js:880:15)
        at Function.Module._load (internal/modules/cjs/loader.js:725:27)
        at Module.require (internal/modules/cjs/loader.js:952:19)
        at require (internal/modules/cjs/helpers.js:88:18)
        at Object.<anonymous> (/home/taro/sample-app/imp/index.cjs:1:14)
        at Module._compile (internal/modules/cjs/loader.js:1063:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
        at Module.load (internal/modules/cjs/loader.js:928:32)
        at Function.Module._load (internal/modules/cjs/loader.js:769:14)
        at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12) {
      code: 'MODULE_NOT_FOUND',
      requireStack: [ '/home/taro/sample-app/imp/index.cjs' ]
    }
    ```

    続いて、拡張子を `.js` にリネームして実行すると問題なく実行できる
    ```
    $ mv sub.cjs sub.js
    $ node index.cjs
    { hoge: 'Hello hoge' }
    ```

    `modules` ディレクトリを作って、そこに `index.js` ファイルを置いて、   
    ディレクトリを指定しインポートしてみると、問題なく実行できる
    ```
    mkdir modules
    cp sub.cjs modules/index.js
    ```

    index.cjs
    ```JavaScript
    // ディレクトリ modules を指定する
    const hoge = require('./modules');

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    { hoge: 'Hello hoge' }
    ```

* `ESM` から `CJS` をインポートする

    インポートされる `CJS` ファイルを作成する

    sub.cjs
    ```JavaScript
    module.exports = {
        hoge: "Hello hoge"
    }
    ```

    デフォルトインポートは、問題なく実行できる

    index.mjs
    ```JavaScript
    // デフォルトインポート
    import hoge from './sub.cjs';

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    { hoge: 'Hello hoge' }
    ```

    名前付きインポートはエラーになる

    index.mjs
    ```JavaScript
    // 名前付きインポート
    import { hoge } from './sub.cjs';

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.mjs
    file:///home/taro/sample-app/imp/mjs_cjs/index.mjs:1
    import { hoge } from './sub.cjs';
            ^^^^
    SyntaxError: Named export 'hoge' not found. The requested module './sub.cjs' is a CommonJS module, which may not support all module.exports as named exports.
    CommonJS modules can always be imported via the default export, for example using:

    import pkg from './sub.cjs';
    const { hoge } = pkg;

        at ModuleJob._instantiate (internal/modules/esm/module_job.js:98:21)
        at async ModuleJob.run (internal/modules/esm/module_job.js:143:5)
        at async Loader.import (internal/modules/esm/loader.js:165:24)
        at async Object.loadESM (internal/process/esm_loader.js:68:5)
    ```

    エイリアスをつけて、すべてのコンテンツをインポートする

    index.mjs
    ```JavaScript
    // すべてのコンテンツをインポート
    import * as hoge from './sub.cjs';

    console.log(hoge);
    console.log(hoge.default);
    console.log(hoge.default.hoge);
    ```

    実行結果
    ```console
    $ node index.mjs
    [Module] { default: { hoge: 'Hello hoge' } }
    { hoge: 'Hello hoge' }
    Hello hoge
    ```

    もちろん、`ESM` から `require` は利用できない

    index.mjs
    ```JavaScript
    // require でインポートする
    const hoge = require('./sub.cjs');

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.mjs
    file:///home/taro/sample-app/imp/mjs_cjs/index.mjs:1
    const hoge = require('./sub.cjs');
                ^

    ReferenceError: require is not defined
        at file:///home/taro/sample-app/imp/mjs_cjs/index.mjs:1:14
        at ModuleJob.run (internal/modules/esm/module_job.js:146:23)
        at async Loader.import (internal/modules/esm/loader.js:165:24)
        at async Object.loadESM (internal/process/esm_loader.js:68:5)
    ```

* `CJS` から `ESM` をインポートする

    インポートされる `ESM` ファイルを作成する

    sub.mjs
    ```JavaScript
    export const hoge = "Hello hoge";
    export default "Hello fuga";
    ```

    `ESM` ファイルを `require` をするとエラーになる

    index.cjs
    ```JavaScript
    const hoge = require('./sub.mjs');

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    internal/modules/cjs/loader.js:926
        throw new ERR_REQUIRE_ESM(filename);
        ^

    Error [ERR_REQUIRE_ESM]: Must use import to load ES Module: /home/taro/sample-app/imp/cjs_mjs/sub.mjs
        at Module.load (internal/modules/cjs/loader.js:926:11)
        at Function.Module._load (internal/modules/cjs/loader.js:769:14)
        at Module.require (internal/modules/cjs/loader.js:952:19)
        at require (internal/modules/cjs/helpers.js:88:18)
        at Object.<anonymous> (/home/taro/sample-app/imp/cjs_mjs/index.cjs:1:14)
        at Module._compile (internal/modules/cjs/loader.js:1063:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
        at Module.load (internal/modules/cjs/loader.js:928:32)
        at Function.Module._load (internal/modules/cjs/loader.js:769:14)
        at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12) {
      code: 'ERR_REQUIRE_ESM'
    }
    ```

    もちろん、`CJS` から `import` は利用できない

    index.cjs
    ```JavaScript
    // import でインポートする
    import hoge from './sub.mjs';

    console.log(hoge);
    ```

    実行結果
    ```console
    $ node index.cjs
    (node:14433) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
    (Use `node --trace-warnings ...` to show where the warning was created)
    /home/taro/sample-app/imp/cjs_mjs/index.cjs:1
    import hoge from './sub.mjs';
    ^^^^^^

    SyntaxError: Cannot use import statement outside a module
        at wrapSafe (internal/modules/cjs/loader.js:979:16)
        at Module._compile (internal/modules/cjs/loader.js:1027:27)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
        at Module.load (internal/modules/cjs/loader.js:928:32)
        at Function.Module._load (internal/modules/cjs/loader.js:769:14)
        at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
        at internal/main/run_main_module.js:17:47
    ```

    Dynamic import は実行可能なので、`import`を使って `ESM` ファイルを読み込むことができる。

    index.cjs
    ```JavaScript
    import('./sub.mjs').then(res => {
        console.log(res);
        console.log(res.default);
        console.log(res.hoge);
    });
    ```

    実行結果
    ```console
    $ node index.cjs
    [Module] { default: 'Hello fuga', hoge: 'Hello hoge' }
    Hello fuga
    Hello hoge
    ```

* `esm` パッケージ を利用して、`CJS` から `ESM` をインポートする
    
    `esm` パッケージ は `CommonJS` から `import/export` モジュールを有効にすることができる

    まず、`esm` パッケージ をインストールする
    ```
    $ npm install esm
    ```

    インポートされる `CJS` ファイルを作成する

    sub.cjs
    ```JavaScript
    module.exports = function(name) {
        return `Hello ${name}`;
    }
    ```

    インポートされる `ESM` ファイルを作成する

    sub.mjs
    ```JavaScript
    export default function(name) {
        return `Hello ${name}`;
    }
    ```

    `CJS` ファイルを `require` で、`ESM` ファイルを `import` で読み込む。   
    ※ インポートするファイルの拡張子は `.js` でないとエラーになる

    index.js
    ```JavaScript
    const hoge = require('./sub.cjs');
    import fuga from './sub.mjs'

    console.log(hoge('taro'));
    console.log(fuga('hanako'));
    ```

    実行結果   
    `-r esm` で `esm` モジュールをプリロードする
    ```console
    $ node -r esm index.js
    Hello taro
    Hello hanako
    ```

## 補足

* Conditional Exports

    + `Conditional Exports` を利用して、`ES Modules` と `CommonJS` 両方に対応した package を作ることができる。( `Dual Package` )   
    説明は省略する

* `import`/`export` 文は {...} の中では動作しない

    例えば、以下のような条件付きのインポートは動作しない
    ```
    if (something) {
        import {sayHi} from "./say.js";
    }
    ```

* 変数や関数の宣言と同様に、`import` 文は巻き上げ（ホイスティング: hoisting）が発生する

    例えば、`import` した変数を `import` 文より前で使うことができる。
    ```
    console.log(xxx);
    import xxx from "./hoge";
    ```

