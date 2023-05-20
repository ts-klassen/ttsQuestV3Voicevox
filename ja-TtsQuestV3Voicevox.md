# TtsQuestV3Voicevox.js

[English](./en-TtsQuestV3Voicevox.md) | 日本語

TtsQuestV3Voicevox.jsは、Voicevoxエンジンを使用してTTS Quest V3 APIを利用して音声を生成するためのJavaScriptクラスです。ブラウザで音声合成を簡素化し、再生することができます。

## 必要なもの

TtsQuestV3Voicevox.jsを使用する前に、TTS QuestのAPIキーを取得することをおすすめします。apiKeyパラメータは任意ですが、APIのパフォーマンスを向上させることができます。

## 使用方法

1. HTMLファイルに`TtsQuestV3Voicevox.js`のファイルをインクルードします：

   ```html
   <script src="./TtsQuestV3Voicevox.js"></script>
   ```

2. 必要なパラメータを指定して、`TtsQuestV3Voicevox`クラスのインスタンスを作成します：

   ```javascript
   var speakerId = 1; // VOICEVOX:ずんだもん（あまあま）
   var text = "こんにちは";
   var ttsQuestApiKey = ''; // 任意
   var audio = new TtsQuestV3Voicevox(speakerId, text, ttsQuestApiKey);
   ```

   注意：`ttsQuestApiKey`をご自身のAPIキーに置き換えるか、空白または未定義のままにしてください。

3. `audio`オブジェクトの`play`メソッドを呼び出すことで、合成された音声を再生します：

   ```javascript
   audio.play();
   ```

## サンプル

`TtsQuestV3Voicevox.js`を使用した実装の例については、提供された`sample.html`ファイルを参照してください。`TtsQuestV3Voicevox.js`と同じディレクトリに配置してください。

## ライセンス

このプロジェクトはTTS QUEST PUBLIC Licenseの下でライセンスされています。詳細については[LICENSE](./LICENSE)ファイルを参照してください。
