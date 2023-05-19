# TtsQuestV3Voicevox.js

English | [日本語](./ja-TtsQuestV3Voicevox.md)

TtsQuestV3Voicevox.js is a JavaScript class that allows you to generate speech using the TTS Quest V3 API with the Voicevox engine. It simplifies the process of synthesizing speech and playing it back in the browser.

## Prerequisites

Before using TtsQuestV3Voicevox.js, it is recommended to obtain an API key for TTS Quest. The apiKey parameter is optional but can improve the performance of the API.

## Usage

1. Include the `TtsQuestV3Voicevox.js` file in your HTML:

   ```html
   <script src="./TtsQuestV3Voicevox.js"></script>
   ```

2. Create an instance of the `TtsQuestV3Voicevox` class by providing the required parameters:

   ```javascript
   var speakerId = 1; // VOICEVOX:ずんだもん（あまあま）
   var text = "こんにちは";
   var ttsQuestApiKey = ''; // optional
   var audio = new TtsQuestV3Voicevox(speakerId, text, ttsQuestApiKey);
   ```

   Note: Replace `ttsQuestApiKey` with your own API key. If you don't have an API key or want to skip it, leave it empty or undefined.

3. Play the synthesized speech by calling the `play` method on the `audio` object:

   ```javascript
   audio.play();
   ```

## Example

See the provided `sample.html` file for an example implementation using `TtsQuestV3Voicevox.js`. Make sure to place it in the same directory as `TtsQuestV3Voicevox.js`.

## License

This project is licensed under the TTS QUEST PUBLIC License. See the [LICENSE](./LICENSE) file for details.
