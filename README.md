# voice-journal-recorder

`音声ジャーナル`(GAS Webアプリ)のマイク録音専用ページ。GASのHtmlServiceが生成するiframeにはマイク許可が伝播しない(Permissions Policyでmicrophone/cameraが除外される)ため、この別ドメインのページをポップアップで開いて録音し、結果を`postMessage`で親ウィンドウ(GAS側)に返す。

このリポジトリに機密情報(ID・APIキー・日記データ等)は一切含まない。GitHub Pagesで公開される前提。

## ファイル

- `recorder.html`: 録音本体。GAS側から`?origin=<GASのオリジン>&maxAudioBytes=<数値>&maxDurationSec=<数値>&chunkSize=<数値>`のクエリパラメータ付きでポップアップとして開かれる想定。
  - `origin`パラメータは`ALLOWED_ORIGINS`の**完全一致リスト**でのみ許可する(2026-09-23変更。以前は`script.google.com`/`n-<英数字とハイフン>-script.googleusercontent.com`という正規表現で「任意のGAS Webアプリ」を許可していたが、これだと攻撃者が自分のGASアプリのoriginを指定してこのページを開けば録音を横取りできてしまう。GAS WebアプリのHtmlServiceコンテンツはスクリプトごとに固有のサンドボックスドメインを割り当てられるため、実測した値だけを列挙する設計に変更した)。一致しない場合は固定文言で拒否し、渡された値自体は画面に表示せず`console.warn`にのみ出す(任意の文言を画面に表示させるフィッシング経路を塞ぐため)。
  - 録音完了後、`{type:'voiceJournalRecording', version:1, blob, mimeType, recordedAt, durationSec}`を`postMessage`で送信する。**送信後すぐには閉じない**。GAS側からの`{type:'voiceJournalRecordingAck', version:1}`を最大8秒待ち、届けば閉じる。届かない・`window.opener`が無い/閉じている場合は音声を保持したまま端末保存リンクを表示する(録音が黙って失われるのを防ぐため)。

## 送信元の検証(GAS側)

`hatauchi-tech/voice-journal-gas`(非公開)の`src/index.html`は、`event.origin`だけでなく`event.source`(`window.open`で取得したポップアップ参照そのもの)も照合してから録音データを受理する。オリジン一致だけでは、同一origin(`hatauchi-tech.github.io`)上の別ページやこのリポジトリへの侵害があった場合になりすましを防げないため。呼び出し側のコードもあわせて参照。

作成者：畑内雄希／レイテクソリューションズ株式会社
