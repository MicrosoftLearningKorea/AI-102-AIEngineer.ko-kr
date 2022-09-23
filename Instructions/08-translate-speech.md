---
lab:
  title: 음성 번역
  module: Module 4 - Building Speech-Enabled Applications
---

# <a name="translate-speech"></a>음성 번역

The <bpt id="p1">**</bpt>Speech<ept id="p1">**</ept> service includes a <bpt id="p2">**</bpt>Speech translation<ept id="p2">**</ept> API that you can use to translate spoken language. For example, suppose you want to develop a translator application that people can use when traveling in places where they don't speak the local language. They would be able to say phrases such as "Where is the station?" or "I need to find a pharmacy" in their own language, and have it translate them to the local language.

<bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This exercise requires that you are using a computer with speakers/headphones. For the best experience, a microphone is also required. Some hosted virtual environments may be able to capture audio from your local microphone, but if this doesn't work (or you don't have a microphone at all), you can use a provided audio file for speech input. Follow the instructions carefully, as you'll need to choose different options depending on whether you are using a microphone or the audio file.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 **Cognitive Services** 리소스가 아직 없으면 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services*를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. When the resource has been deployed, go to it and view its <bpt id="p1">**</bpt>Keys and Endpoint<ept id="p1">**</ept> page. You will need one of the keys and the location in which the service is provisioned from this page in the next procedure.

## <a name="prepare-to-use-the-speech-translation-service"></a>Speech Translation 서비스 사용 준비

이 연습에서는 Speech SDK를 사용해 음성을 인식, 번역 및 합성하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **Speech** 서비스에 포함된 **Speech Translation** API를 사용하면 음성 언어를 번역할 수 있습니다.

1. Visual Studio Code의 **탐색기** 창에서 **08-speech-translation** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. 예를 들어 현지어를 모르는 곳을 여행할 때 사용할 수 있는 번역기 애플리케이션을 개발하려는 등의 경우에 이 API를 사용할 수 있습니다.

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. **translator** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    그들은 “역은 어디에 있습니까?” 와 같은 문구를 말할 수 있을 것입니다.
4. **translator** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: translator.py

    또는 자신의 언어로 “약국을 찾아야”하고 현지 언어로 번역해야 합니다.

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. In the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, note that code to load the cognitive services key and region from the configuration file has already been provided. You must use these variables to create a <bpt id="p1">**</bpt>SpeechTranslationConfig<ept id="p1">**</ept> for your cognitive services resource, which you will use to translate spoken input. Add the following code under the comment <bpt id="p1">**</bpt>Configure translation<ept id="p1">**</ept>:

    **C#**
    
    ```C#
    // Configure translation
    translationConfig = SpeechTranslationConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    translationConfig.SpeechRecognitionLanguage = "en-US";
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("es");
    translationConfig.AddTargetLanguage("hi");
    Console.WriteLine("Ready to translate from " + translationConfig.SpeechRecognitionLanguage);
    ```
    
    **Python**
    
    ```Python
    # Configure translation
    translation_config = speech_sdk.translation.SpeechTranslationConfig(cog_key, cog_region)
    translation_config.speech_recognition_language = 'en-US'
    translation_config.add_target_language('fr')
    translation_config.add_target_language('es')
    translation_config.add_target_language('hi')
    print('Ready to translate from',translation_config.speech_recognition_language)
    ```

6. **참고**: 이 연습을 진행하려면 스피커/헤드폰이 있는 컴퓨터를 사용해야 합니다.

    **C#**
    
    ```C#
    // Configure speech
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    ```
    
    **Python**
    
    ```Python
    # Configure speech
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    ```

7. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

8. 최상의 경험을 위해서는 마이크도 필요합니다.

## <a name="implement-speech-translation"></a>음성 번역 구현

Cognitive Services 리소스에서 Speech 서비스용 **SpeechTranslationConfig**를 만들었으므로 **Speech Translation** API를 사용하여 음성을 인식한 다음 번역할 수 있습니다.

### <a name="if-you-have-a-working-microphone"></a>작동하는 마이크가 있는 경우

1. 프로그램의 **Main** 함수에서 코드가 **Translate** 함수를 사용해 음성 입력을 번역함을 확인합니다.
2. **Translate** 함수의 **음성 번역** 주석 아래에 다음 코드를 추가하여 **TranslationRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 입력용 기본 시스템 마이크를 사용해 음성을 인식하고 번역할 수 있습니다.

    **C#**
    
    ```C#
    // Translate speech
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Speak now...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Speak now...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > 일부 호스트 가상 환경에서는 로컬 마이크로부터 오디오를 캡처할 수 있지만, 이 기능이 작동하지 않거나 마이크가 아예 없는 경우에는 제공된 오디오 파일을 음성 입력으로 사용할 수 있습니다.

3. 이제 아래의 **프로그램 실행** 섹션으로 넘어갑니다.

### <a name="alternatively-use-audio-input-from-a-file"></a>또는 파일에서 오디오 입력 사용

1. 터미널 차에서 다음 명령을 입력하여 오디오 파일을 재생하는 데 사용할 수 있는 라이브러리를 설치합니다.

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

2. 프로그램의 코드 파일에서, 기존 네임스페이스 가져오기 아래에 다음 코드를 추가하여 방금 설치한 라이브러리를 가져옵니다.

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

3. 마이크를 사용하는지 아니면 오디오 파일을 사용하는지에 따라 서로 다른 옵션을 선택해야 하므로 주의하여 지침을 따르세요.

    **C#**
    
    ```C#
    // Translate speech
    string audioFile = "station.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Getting speech from file...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audioFile = 'station.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Getting speech from file...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The code in your application translates the input to all three languages in a single call. Only the translation for the specific language is displayed, but you could retrieve any of the translations by specifying the target language code in the <bpt id="p1">**</bpt>translations<ept id="p1">**</ept> collection of the result.

### <a name="run-the-program"></a>프로그램 실행

1. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

2. When prompted, enter a valid language code (<bpt id="p1">*</bpt>fr<ept id="p1">*</ept>, <bpt id="p2">*</bpt>es<ept id="p2">*</ept>, or <bpt id="p3">*</bpt>hi<ept id="p3">*</ept>), and then, if using a microphone, speak clearly and say "where is the station?" or some other phrase you might use when traveling abroad. The program should transcribe your spoken input and translate it to the language you specified (French, Spanish, or Hindi). Repeat this process, trying each language supported by the application. When you're finished, press ENTER to end the program.

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The TranslationRecognizer gives you around 5 seconds to speak. If it detects no spoken input, it produces a "No match" result.
    >
    > 언어 인코딩 문제로 인해 힌디어 번역이 콘솔 창에 올바르게 표시되지 않는 경우도 있습니다.

## <a name="synthesize-the-translation-to-speech"></a>번역을 음성으로 합성

So far, your application translates spoken input to text; which might be sufficient if you need to ask someone for help while traveling. However, it would be better to have the translation spoken aloud in a suitable voice.

1. **Translate** 함수의 **번역 합성** 주석 아래에 다음 코드를 추가합니다. 이 코드는 **SpeechSynthesizer** 클라이언트를 사용해 번역을 음성으로 합성하여 기본 스피커에서 재생합니다.

    **C#**
    
    ```C#
    // Synthesize translation
    var voices = new Dictionary<string, string>
                    {
                        ["fr"] = "fr-FR-HenriNeural",
                        ["es"] = "es-ES-ElviraNeural",
                        ["hi"] = "hi-IN-MadhurNeural"
                    };
    speechConfig.SpeechSynthesisVoiceName = voices[targetLanguage];
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(translation);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize translation
    voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
    }
    speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    speak = speech_synthesizer.speak_text_async(translation).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. 변경 내용을 저장하고 **translator** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

3. When prompted, enter a valid language code (<bpt id="p1">*</bpt>fr<ept id="p1">*</ept>, <bpt id="p2">*</bpt>es<ept id="p2">*</ept>, or <bpt id="p3">*</bpt>hi<ept id="p3">*</ept>), and then speak clearly into the microphone and say a phrase you might use when traveling abroad. The program should transcribe your spoken input and respond with a spoken translation. Repeat this process, trying each language supported by the application. When you're finished, press ENTER to end the program.

    > **참고** 이 예제에서는 **SpeechTranslationConfig**를 사용하여 음성을 텍스트로 번역한 다음 **SpeechConfig**를 사용하여 번역을 음성으로 합성했습니다. 실제로는 **SpeechTranslationConfig**를 사용해 번역을 직접 합성할 수 있습니다. 하지만 이 방식은 한 가지 언어로 번역할 때만 사용 가능하며, 음성 번역은 오디오 스트림으로 생성되어 스피커에서 바로 재생되는 대신 대개 파일로 저장됩니다.

## <a name="more-information"></a>추가 정보

**Speech Translation** API를 사용하는 방법에 대한 자세한 내용은 [Speech Translation 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-translation)를 참조하세요.
