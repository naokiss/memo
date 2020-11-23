# モジュール化

## モジュールの種類

+ コアモジュール
    - `Node.js` に標準に組み込まれているモジュール
+ `npm` モジュール
    - サードパーティ製の外部ライブラリ
    - `npm` コマンドと `package.json` による自動管理
+ ファイルモジュール
    - 自作モジュール

## `require` と `import/export` について

* require

    + `CommonJS` の仕様で `Node.js` がサポート
    + Node.js(サーバーサイド)で実行可能
    + ブラウザで実行するなら、モジュールバンドルツールを利用する必要がある

* import/export

    + `es2015(ES6)` で決められた仕様で、JavaScriptにおけるモジュール機能
    + `Chrome` や `Firefox` など ES6対応ブラウザで利用可能
    + 対応していないブラウザで利用するには モジュールバンドルツールなどで変換する
    + 拡張子は `.mjs`
    + `Node.js 13.2.0` 以前のバージョンでは、`--experimental-modules`フラグ付きで起動したときのみ、`ECMAScript Modules`をサポートしていたが、`13.2.0`からフラグなしで `import` が可能になった

## モジュールバンドルツール

* Babel
* Webpack
* Laravel Mix

## require（CJS）

* `exports` オブジェクト

    `exports` オブジェクトが `require` 関数で呼ばれた時の戻り値になる

    以下のような書き方ができる
    * `global.module.exports`
    * `module.exports`
    * `exports`
    

* 関数をエクスポート

    sub.js
    ```JavaScript
    module.exports = function(name) {
        console.log('Hello ' + name);
    }
    ```

    main.js
    ```JavaScript
    const sub = require('./sub');
    
    sub('太郎');    
    ```


    実行結果
    ```
    $ node main.js
    Hello 太郎
    ```
* `Date` インスタンスをエクスポート

    sub.js
    ```JavaScript
    module.exports = new Date();
    ```

    main.js
    ```JavaScript
    const sub = require('./sub');

    console.log(sub.getFullYear());
    ```

    実行結果
    ```
    $ node main.js
    2020
    ```

* 複数インスタンスをエクスポート

    変数 `name` と関数 `getName`、オブジェクト `car` をエクスポートする

    sub.js
    ```JavaScript
    var name = 'スカイライン';
    var getName = function() {
        return name + "です";
    }
    var car = {
        name: 'GT-R',
        power: 570
    }

    exports.name = name;
    exports.getName = getName;
    exports.car = car;
    ```

    main.js
    ```JavaScript
    const sub = require('./sub');

    console.log(sub.name);
    console.log(sub.getName());
    console.log(sub.car);
    ```

    実行結果
    ```
    $ node main.js
    スカイライン
    スカイラインです
    { name: 'GT-R', power: 570 }
    ```

* クラスをエクスポート

    sub.js
    ```JavaScript
    class Person {
        constructor(name) {
            this.name = name;
        }

        hello() {
            console.log(`Hello, ${this.name}!`);
        }
    }

    module.exports = Person;
    ```

    main.js
    ```JavaScript
    const Person = require('./sub');

    const p = new Person('太郎')
    p.hello();
    ```

    実行結果
    ```
    $ node main.js
    Hello, 太郎!
    ```

## import（ESM）

* 名前付きエクスポート ( named export )

    + 変数、関数、オブジェクト、クラスの宣言文の先頭に `export` をつけると外部から利用できるようになる
    + インポートするためにはエクスポートされた定義された名前を知っている必要がある。

        sub.mjs
        ```JavaScript
        export const name = '太郎！';
        export const getName = name => `Hello ${name}`;
        export const date = new Date();
        export const car = {
            name: 'GT-R',
            power: 570
        }
        export class Person {
            constructor(name) {
                this.name = name;
            }

            hello() {
                console.log(`Hello, ${this.name}!`);
            }
        }    
        ```

    + 以下のように後からまとめてエクスポートすることもできる

        sub.mjs
        ```JavaScript
        const name = '太郎！';
        const getName = name => `Hello ${name}`;
        const date = new Date();
        const car = {
            name: 'GT-R',
            power: 570
        }
        class Person {
            constructor(name) {
                this.name = name;
            }

            hello() {
                console.log(`Hello, ${this.name}!`);
            }
        }
        export {name, getName, date, car, Person};        
        ```

        `export` 文の中かっこの中では、`as` キーワードを使うことで、名前を変更することができる

        ```JavaScript
        export {name as n, getName as func, date as d,
                car as c, Person as cls};        
        ```

    + `export` で定義した変数名や関数名を指定してインポートする

        main.mjs
        ```JavaScript
        import {name, getName, date, car, Person} from "./sub.mjs";

        console.log(name);
        getName();
        console.log(date.getFullYear());
        console.log(car);
        const p = new Person('太郎');
        p.hello();
        ```

    + インポートする変数名を指定

        `import` 文の中かっこの中では、`as` キーワードを使うことで、名前を変更することができる

        main.mjs
        ```JavaScript
        import {name as n, getName as func, date as d,
                car as c, Person as cls} from "./sub.mjs";

        console.log(n);
        func();
        console.log(d.getFullYear());
        console.log(c);
        const p = new cls('太郎');
        p.hello();
        ```

    + モジュールをまとめてインポート

        main.mjs
        ```JavaScript
        import * as module from "./sub.mjs";

        console.log(module.name);
        module.getName();
        console.log(module.date.getFullYear());
        console.log(module.car);
        const p = new module.Person('太郎');
        p.hello();
        ```

    実行結果
    ```
    $ node main.mjs
    太郎！
    2020
    { name: 'GT-R', power: 570 }
    Hello, 太郎!
    ```
    * Node.js 13.2.0 より前のバージョンは --experimental-modules をつける

        ```node --experimental-modules main.mjs```

* デフォルトエクスポート ( Default exports )

    + １つのモジュールに対して１つのだけ `default`を付与できる
    + `default` の `export` では名前を付けない関数や配列・オブジェクトなどをモジュール化できる
    + インポートするためにはエクスポートされた定義された名前を知らなくてもよい

    sub_name.mjs
    ```JavaScript
    export default '太郎！';
    ```

    sub_get_name.mjs
    ```JavaScript
    export default function(name) { 
        return `Hello ${name}`;
    }
    ```

    sub_date.mjs
    ```JavaScript
    export default new Date();
    ```

    sub_car.mjs
    ```JavaScript
    export default {
        name: 'GT-R',
        power: 570
    }
    ```

    sub_person.mjs
    ```JavaScript
    export default class {
        constructor(name) {
            this.name = name;
        }

        hello() {
            console.log(`Hello, ${this.name}!`);
        }
    }    
    ```

    main.mjs
    ```JavaScript
    import a from "./sub_name.mjs";
    import b from "./sub_date.mjs";
    import c from "./sub_get_name.mjs";
    import d from "./sub_car.mjs";
    import e from "./sub_person.mjs";

    console.log(a);
    b();
    console.log(c.getFullYear());
    console.log(d);
    const p = new e('太郎');
    p.hello();
    ```

    `import` 文は以下のように書くこともできる
    ```JavaScript
    import { default as a } from "./sub_name.mjs";
    import { default as b } from "./sub_date.mjs";
    import { default as c } from "./sub_get_name.mjs";
    import { default as d } from "./sub_car.mjs";
    import { default as e } from "./sub_person.mjs";
    ```

    実行結果
    ```
    $ node main.mjs
    太郎！
    2020
    { name: 'GT-R', power: 570 }
    Hello, 太郎!
    ```


* 名前付きインポート + デフォルトインポート

    sub.mjs
    ```JavaScript
    export function hoge() {
        console.log("hoge");
    }

    export default function () {
        console.log("fuga");
    }
    ```

    main.js
    ```JavaScript
    import fuga, { hoge } from './sub.mjs'

    fuga();
    hoge();
    ```

    `import` 文は以下のように書くこともできる
    ```JavaScript
    import { default as fuga, hoge } from './sub.mjs'
    ```

    実行結果
    ```
    $ node main.mjs
    fuga
    hoge
    ```

## 補足

+ `import`/`export` 文は {...} の中では動作しない

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