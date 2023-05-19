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
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/02.wav
```

mp3ファイルは
```bash
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/0.mp3
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/1.mp3
wget https://audio1.tts.quest/v1/data/e8ffdebfc288edb743c0bf0cf4b28834a53b26a78eeeca3586c857825ab0ae7a/02.mp3
```

のように、合成が完了しているファイルをダウンロードすることができます。合成が完了していないファイルは404エラーを返す他、ファイルの総数が判明するまでの間の`audioCount`は`0`です。
