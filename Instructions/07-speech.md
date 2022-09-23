---
lab:
  title: 음성 인식 및 합성
  module: Module 4 - Building Speech-Enabled Applications
---

# <a name="recognize-and-synthesize-speech"></a>음성 인식 및 합성

Azure Cognitive Service인 **Speech** 서비스에서는 다음과 같은 음성 관련 기능을 제공합니다.

- *음성 텍스트 변환* API - 음성 인식(가청 음성 단어를 텍스트로 변환)을 구현할 수 있습니다.
- *텍스트 음성 변환* API - 음성 합성(텍스트를 가청 음성으로 변환)을 구현할 수 있습니다.

이 연습에서는 두 API를 모두 사용하여 음성으로 시간을 안내하는 시계 애플리케이션을 구현합니다.

<bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This exercise requires that you are using a computer with speakers/headphones. For the best experience, a microphone is also required. Some hosted virtual environments may be able to capture audio from your local microphone, but if this doesn't work (or you don't have a microphone at all), you can use a provided audio file for speech input. Follow the instructions carefully, as you'll need to choose different options depending on whether you are using a microphone or the audio file.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

If you have not already cloned <bpt id="p1">**</bpt>AI-102-AIEngineer<ept id="p1">**</ept> code repository to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the cloned folder in Visual Studio Code.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

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

## <a name="prepare-to-use-the-speech-service"></a>Speech 서비스 사용 준비

이 연습에서는 Speech SDK를 사용해 음성을 인식하고 합성하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

<bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **07-speech** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Right-click the <bpt id="p1">**</bpt>speaking-clock<ept id="p1">**</ept> folder and open an integrated terminal. Then install the Speech SDK package by running the appropriate command for your language preference:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. **speaking-clock** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to include an authentication <bpt id="p1">**</bpt>key<ept id="p1">**</ept> for your cognitive services resource, and the <bpt id="p2">**</bpt>location<ept id="p2">**</ept> where it is deployed. Save your changes.
4. **speaking-clock** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: speaking-clock.py

    Open the code file and at the top, under the existing namespace references, find the comment <bpt id="p1">**</bpt>Import namespaces<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Speech SDK:

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. **참고**: 이 연습을 진행하려면 스피커/헤드폰이 있는 컴퓨터를 사용해야 합니다.

    **C#**
    
    ```C#
    // Configure speech service
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    
    // Configure voice
    speechConfig.SpeechSynthesisVoiceName = "en-US-AriaNeural";
    ```
    
    **Python**
    
    ```Python
    # Configure speech service
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    print('Ready to use speech service in:', speech_config.region)
    ```

6. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

7. 최상의 경험을 위해서는 마이크도 필요합니다.

## <a name="recognize-speech"></a>음성 인식

Cognitive Services 리소스에서 Speech 서비스용 **SpeechConfig**를 만들었으므로 **Speech-to-text** API를 사용하여 음성을 인식한 다음 텍스트로 필사할 수 있습니다.

### <a name="if-you-have-a-working-microphone"></a>작동하는 마이크가 있는 경우

1. 프로그램의 **Main** 함수에서 코드가 **TranscribeCommand** 함수를 사용해 음성 입력을 수락함을 확인합니다.
2. **TranscribeCommand** 함수의 **음성 인식 구성** 주석 아래에 다음의 적절한 코드를 추가하여 **SpeechRecognizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 기본 시스템 마이크를 사용해 음성을 인식하고 필사할 수 있습니다.

    **C#**
    
    ```C#
    // Configure speech recognition
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    Console.WriteLine("Speak now...");
    ```
    
    **Python**
    
    ```Python
    # Configure speech recognition
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    print('Speak now...')
    ```

3. 이제 아래의 **필사된 명령을 처리하기 위한 코드 추가** 섹션으로 넘어갑니다.

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

3. 일부 호스트 가상 환경에서는 로컬 마이크로부터 오디오를 캡처할 수 있지만, 이 기능이 작동하지 않거나 마이크가 아예 없는 경우에는 제공된 오디오 파일을 음성 입력으로 사용할 수 있습니다.

    **C#**

    ```C#
    // Configure speech recognition
    string audioFile = "time.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech recognition
    audioFile = 'time.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

### <a name="add-code-to-process-the-transcribed-command"></a>필사된 명령을 처리하기 위한 코드 추가

1. **TranscribeCommand** 함수의 **음성 입력 처리** 주석 아래에 다음 코드를 추가하여 음성 입력을 청취합니다. 이때 명령을 반환하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

    **C#**
    
    ```C#
    // Process speech input
    SpeechRecognitionResult speech = await speechRecognizer.RecognizeOnceAsync();
    if (speech.Reason == ResultReason.RecognizedSpeech)
    {
        command = speech.Text;
        Console.WriteLine(command);
    }
    else
    {
        Console.WriteLine(speech.Reason);
        if (speech.Reason == ResultReason.Canceled)
        {
            var cancellation = CancellationDetails.FromResult(speech);
            Console.WriteLine(cancellation.Reason);
            Console.WriteLine(cancellation.ErrorDetails);
        }
    }
    ```

    **Python**
    
    ```Python
    # Process speech input
    speech = speech_recognizer.recognize_once_async().get()
    if speech.reason == speech_sdk.ResultReason.RecognizedSpeech:
        command = speech.text
        print(command)
    else:
        print(speech.reason)
        if speech.reason == speech_sdk.ResultReason.Canceled:
            cancellation = speech.cancellation_details
            print(cancellation.reason)
            print(cancellation.error_details)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. 마이크를 사용하는지 아니면 오디오 파일을 사용하는지에 따라 서로 다른 옵션을 선택해야 하므로 주의하여 지침을 따르세요.

    The SpeechRecognizer gives you around 5 seconds to speak. If it detects no spoken input, it produces a "No match" result.

    If the SpeechRecognizer encounters an error, it produces a result of "Cancelled". The code in the application will then display the error message. The most likely cause is an incorrect key or region in the configuration file.

## <a name="synthesize-speech"></a>음성 합성

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. 프로그램의 **Main** 함수에서 코드가 **TellTime** 함수를 사용하여 사용자에게 현재 시간을 알려 줌을 확인합니다.
2. **TellTime** 함수의 **음성 합성 구성** 주석 아래에 다음 코드를 추가하여 **SpeechSynthesizer** 클라이언트를 만듭니다. 이 클라이언트를 사용하면 음성 출력을 생성할 수 있습니다.

    **C#**
    
    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```
    
    > **참고**: 기본 오디오 구성에서는 출력에 기본 시스템 오디오 디바이스를 사용하므로 **AudioConfig**는 명시적으로 제공하지 않아도 됩니다. 오디오 출력을 파일로 리디렉션해야 하는 경우에는 파일 경로가 포함된 **AudioConfig**를 사용하면 됩니다.

3. **TellTime** 함수의 **음성 출력 합성** 주석 아래에 다음 코드를 추가하여 음성 출력을 생성합니다. 이때 응답을 인쇄하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

    **C#**
    
    ```C#
    // Synthesize spoken output
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize spoken output
    speak = speech_synthesizer.speak_text_async(response_text).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

4. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

5. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

## <a name="use-a-different-voice"></a>다른 음성 사용

Your speaking clock application uses a default voice, which you can change. The Speech service supports a range of <bpt id="p1">*</bpt>standard<ept id="p1">*</ept> voices as well as more human-like <bpt id="p2">*</bpt>neural<ept id="p2">*</ept> voices. You can also create <bpt id="p1">*</bpt>custom<ept id="p1">*</ept> voices.

> **참고**: 신경망 음성과 표준 음성 목록을 확인하려면 Speech 서비스 설명서의 [언어 및 음성 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)을 참조하세요.

1. **TellTime** 함수의 **음성 합성 구성** 주석 아래에서 코드를 다음과 같이 수정하여 **SpeechSynthesizer** 클라이언트를 만들기 전에 대체 음성을 지정합니다.

   **C#**

    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-LibbyNeural"; // change this
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = 'en-GB-LibbyNeural' # change this
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. When prompted, speak clearly into the microphone and say "what time is it?". The program should speak in the specified voice, telling you the time.

## <a name="use-speech-synthesis-markup-language"></a>Speech Synthesis Markup Language 사용

SSML(Speech Synthesis Markup Language)을 사용하면 XML 기반 형식을 통해 음성 합성 방식을 사용자 지정할 수 있습니다.

1. **TellTime** 함수에서 **음성 출력 합성** 현재 주석 아래에 있는 모든 코드를 다음 코드로 바꿉니다(**응답 인쇄** 주석 아래의 코드는 그대로 유지).

   **C#**

    ```C#
    // Synthesize spoken output
    string responseSsml = $@"
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
            <voice name='en-GB-LibbyNeural'>
                {responseText}
                <break strength='weak'/>
                Time to end this lab!
            </voice>
        </speak>";
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakSsmlAsync(responseSsml);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**
    
    ```Python
    # Synthesize spoken output
    responseSsml = " \
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'> \
            <voice name='en-GB-LibbyNeural'> \
                {} \
                <break strength='weak'/> \
                Time to end this lab! \
            </voice> \
        </speak>".format(response_text)
    speak = speech_synthesizer.speak_ssml_async(responseSsml).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. 변경 내용을 저장하고 **speaking-clock** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. When prompted, speak clearly into the microphone and say "what time is it?". The program should speak in the voice that is specified in the SSML (overriding the voice specified in the SpeechConfig), telling you the time, and then after a pause telling you it's time to end this lab - which it is!

## <a name="more-information"></a>추가 정보

**음성 텍스트 변환** 및 **텍스트 음성 변환** API 사용에 대한 자세한 내용은 [음성 텍스트 변환 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) 및 [텍스트 음성 변환 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech)를 참조하세요.
