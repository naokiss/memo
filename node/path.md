# `node` の `PATH` 確認

* `node_modules` のPATHを確認する

    ```
    $ node
    Welcome to Node.js v14.15.1
    Type ".help" for more information.
    > global.module.paths
    [
      '/home/taro/hoge/repl/node_modules',
      '/home/taro/hoge/node_modules',
      '/home/taro/node_modules',
      '/home/node_modules',
      '/node_modules',
      '/home/taro/.node_modules',
      '/home/taro/.node_libraries',
      '/home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node'
    ]
    ```

* `npm` の ディレクトリ `bin` のPATHを確認する方法

    ```
    $ npm bin -g
    ```

* モジュールがグローバルインストールされているパス確認

    ```
    $ npm root -g
    home/taro/.anyenv/envs/nodenv/versions/14.15.1/lib/node_modules
    ```
