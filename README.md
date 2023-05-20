# TTS QUEST V3 VOICEVOX API

VOICEVOXで逐次合成した音声をストリーミング再生することにより、合成完了を待たずに音声を再生できるWEB APIです。

## 例（JavaScript）
Audioクラスを継承したTtsQuestV3Voicevoxクラスを使うことで、簡単にブラウザ上でストリーミング再生ができます。
```javascript
var audio = new TtsQuestV3Voicevox(3, "「VOICEVOX:ずんだもん」なのだ");
audio.play();
```
詳細は[こちら](./ja-TtsQuestV3Voicevox.md)。

## 例（curl）
例えば、話者ID `3` に「あ」と言わせたい場合、`speaker`に`3`、`text`に「あ」をUTF-8でURLエンコードした`%E3%81%82`が対応します。
```bash
curl 'https://api.tts.quest/v3/voicevox/synthesis?speaker=3&text=%E3%81%82'
```
リクエストが成功した場合、
```
{
	"success": true,
	"isApiKeyValid": false,
	"speakerName": "VOICEVOX:ずんだもん（ノーマル）",
	"audioStatusUrl": "https://audio2.tts.quest/v1/data/a712c96d35b19275c29e93c6267581f1bf0950072e242470d098b44770bdfad2/status.json",
	"wavDownloadUrl": "https://audio2.tts.quest/v1/data/a712c96d35b19275c29e93c6267581f1bf0950072e242470d098b44770bdfad2/audio.wav",
	"mp3DownloadUrl": "https://audio2.tts.quest/v1/data/a712c96d35b19275c29e93c6267581f1bf0950072e242470d098b44770bdfad2/audio.mp3",
	"mp3StreamingUrl": "https://audio2.tts.quest/v1/data/a712c96d35b19275c29e93c6267581f1bf0950072e242470d098b44770bdfad2/audio.mp3s"
}
```
このようなJSONレスポンスが帰ってきます。（見やすいように一部書き換えています。改行、順序、全角文字や記号の扱いは実際と異なることがあります。）
mp3形式でダウンロードしたい場合は、`mp3DownloadUrl`にリクエストします。
```bash
wget 'https://audio2.tts.quest/v1/data/a712c96d35b19275c29e93c6267581f1bf0950072e242470d098b44770bdfad2/audio.mp3'
```
`wavDownloadUrl`と`mp3DownloadUrl`は、合成が完了していないと404エラーを返し、完了するとステータスコード200を返します。
`audioStatusUrl`と`mp3StreamingUrl`は直ちに利用可能で、ステータスコードは200です。

また、ストリーミングの代わりに分割された音声ファイルをダウンロードすることもできます。例えば、「あ、い、う」という音声を合成するリクエストを送信した場合、レスポンスと`audioStatusUrl`のJSONデータは次のようになります。

api.tts.questレスポンス
```json
{
	"success": true,
	"isApiKeyValid": false,
	"speakerName": "VOICEVOX:ずんだもん（ノーマル）",
	"audioStatusUrl": "https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/status.json",
	"wavDownloadUrl": "https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/audio.wav",
	"mp3DownloadUrl": "https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/audio.mp3",
	"mp3StreamingUrl": "https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/audio.mp3s"
}
```

audio1.tts.questの`audioStatusUrl`レスポンス
```json
{
	"success": true,
	"isAudioReady": true,
	"isAudioError": false,
	"status": "done",
	"speaker": 3,
	"audioCount": 3,
	"updatedTime": 1684528788
}
```
この例では、`audioStatusUrl`が3となっているので、

wavファイルは
```bash
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/0.wav
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/1.wav
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/2.wav
```

mp3ファイルは
```bash
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/0.mp3
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/1.mp3
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/2.mp3
```

のように、合成が完了しているファイルをダウンロードすることができます。合成が完了していないファイルは404エラーを返す他、ファイルの総数が判明するまでの間の`audioCount`は`0`です。

## Web Api 仕様
各エンドポイントでは、パラメーターをGETパラメーターとPOSTフォームパラメーターで渡すことができます。

URLエンコードしたパラメーターのキーと値を`=`で区切り、それらを`&`で区切ります。同じキーが複数ある場合、どれが優先されるかは未定義です。

GETパラメーターとして渡す場合は、値をURLエンコードしてURLの末尾に記載します。
```
?paramA=valueA&paramB=valueB
```

POSTで送信する場合は、ヘッダーの`content-type`を`application/x-www-form-urlencoded`に指定し、値をURLエンコードしてボディーに記載します。
```
paramA=valueA&paramB=valueB
```

レスポンスボディーはJSON形式で、`success`はステータスコードが200のとき`true`となります。
`success`が`false`のときは、`errorMessage`に理由がありますが、単にステータスコードが書かれているだけのことが多いです。
ステータスコードが429のときは、`retryAfter`の秒数待ってから再送してください。

### エンドポイント一覧
- `https://api.tts.quest/v3/voicevox/synthesis`
- `https://api.tts.quest/v3/voicevox/speakers_array`
- `https://audio*.tts.quest/v1/data/*/status.json`
- `https://api.tts.quest/v3/key/points`
- `https://api.tts.quest/v3/key/generate`

### v3 voicevox synthesis
URL: `https://api.tts.quest/v3/voicevox/synthesis`

音声の合成をリクエストする。

パラメーター
- `speaker`: 話者を表す整数値。範囲外の場合はinvalidSpeakerエラーを返す。
- `text`: 合成する文章。UTF-8。文字数が0の場合はtextRequiredエラー、長すぎる場合はtextTooLongエラーを返す。
- `key`: apiKey。apiKeyが有効な場合は`isApiKeyValid`が`true`になり、高速で合成される。

レスポンス
- `isApiKeyValid`: `true`のとき、apiKeyのポイントを1文字あたり1ポイント消費して高速で合成する。
- `speakerName`: 話者名
- `audioStatusUrl`: 合成の状況を取得するためのURL。
- `wavDownloadUrl`: 合成が完了したときのwavファイルURL。
- `mp3DownloadUrl`: 合成が完了したときのmp3ファイルURL。
- `mp3StreamingUrl`: ストリーミング用URL。

### v3 voicevox speakers_array
URL: `https://api.tts.quest/v3/voicevox/speakers_array`

話者の一覧を取得する。配列のキーは0で始まり、話者IDに対応する。利用できない話者IDの値は`null`。

パラメーター
- `key`: apiKey。apiKeyが有効な場合は`isApiKeyValid`が`true`になり、高速で処理される。

レスポンス
- `isApiKeyValid`: `true`のとき、apiKeyのポイントを1消費して高速に処理する。
- `speakers`: 話者名の配列

### v3 voicevox status
URL: `https://audio*.tts.quest/v1/data/*/status.json`

`isAudioReady`と`isAudioError`の組み合わせで合成の状況を確認する。

レスポンス
- `isAudioReady`: `true`の場合、全ての合成が完了している。
- `isAudioError`: `true`の場合、合成に失敗している。
- `status`: 完了したら`done`になる他、失敗した際には原因が書かれることがある。
- `speaker`: 話者ID。
- `audioCount`: 分割された音声ファイルの総数。
- `updatedTime`: 更新されたUNIX時間。

### v3 key points
URL: `https://api.tts.quest/v3/key/points`

apiKeyの残りポイント数を確認する。

パラメーター
- `key`: apiKey。

レスポンス
- `isApiKeyValid`: apiKeyが有効であれば`true`であるが、ポイントが残っていない場合は`false`になる。
- `points`: 残りポイント数。毎日朝9時（日本時間）にリセットされる。

### v3 key generate
URL: `https://api.tts.quest/v3/key/generate`

最大24時間で、時間制限付きの使い捨てSubKeyを生成する。使用ポイント上限も設定可能。
subKeyのポイント消費は、生成に用いたapiKeyに紐付けられる。

パラメーター
- `key`: apiKey。
- `time`: subKeyが有効な秒数。指定がない場合は最大値に指定される。
- `points`: ポイント上限。指定がない場合は無制限。保有するポイントの総数を超えて指定することが可能。

レスポンス
- `isApiKeyValid`: apiKeyが有効であれば`true`であるが、ポイントが残っていない場合は`false`になる。
- `cost`: subKeyの生成に消費したポイント。subKeyが有効な秒数に連動し、1分であれば1ポイント、24時間であれば1000ポイント消費する。
- `key`: 生成されたsubKey。
