# voice-journal-recorder

`音声ジャーナル`(GAS Webアプリ)のマイク録音専用ページ。GASのHtmlServiceが生成するiframeにはマイク許可が伝播しない(Permissions Policyでmicrophone/cameraが除外される)ため、この別ドメインのページをポップアップで開いて録音し、結果を`postMessage`で親ウィンドウ(GAS側)に返す。

このリポジトリに機密情報(ID・APIキー・日記データ等)は一切含まない。GitHub Pagesで公開される前提。

## ファイル

- `recorder.html`: 録音本体。GAS側から`?origin=<GASのオリジン>&maxAudioBytes=<数値>&maxDurationSec=<数値>&chunkSize=<数値>`のクエリパラメータ付きでポップアップとして開かれる想定。
  - `origin`パラメータが`https://script.google.com`または`https://n-<英数字とハイフン>-script.googleusercontent.com`のパターンに一致しない場合は録音を拒否する(任意のリンク経由でこのページが音声送信先として悪用されるのを防ぐため)。
  - 録音完了後、`{type:'voiceJournalRecording', version:1, blob, mimeType, recordedAt, durationSec}`を`postMessage`で送信し、ウィンドウを閉じる。

## 呼び出し元

`hatauchi-tech/voice-journal-gas`(非公開)の`src/index.html`から`window.open`で呼ばれる。呼び出し側のコードもあわせて参照。

作成者：畑内雄希／レイテクソリューションズ株式会社
