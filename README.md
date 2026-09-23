# voice-journal-recorder

`音声ジャーナル`(GAS Webアプリ)のマイク録音専用ページ。GASのHtmlServiceが生成するiframeにはマイク許可が伝播しないため、この別ドメインのページをポップアップで開いて録音し、結果を`postMessage`で親ウィンドウ(GAS側)に返す。

このリポジトリに機密情報(ID・APIキー・日記データ等)は一切含まない。GitHub Pagesで公開される前提。

作成者：畑内雄希／レイテクソリューションズ株式会社
