---
lab:
  title: Speech 및 Language Understanding 서비스 사용
  module: Module 5 - Creating Language Understanding Solutions
---

# <a name="use-the-speech-and-language-understanding-services"></a>Speech 및 Language Understanding 서비스 사용

Speech 서비스와 Language Understanding 서비스를 통합하면 음성 입력에서 사용자 의도를 지능형으로 확인할 수 있는 애플리케이션을 만들 수 있습니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This exercise works best if you have a microphone. Some hosted virtual environments may be able to capture audio from your local microphone, but if this doesn't work (or you don't have a microphone at all), you can use a provided audio file for speech input. Follow the instructions carefully, as you'll need to choose different options depending on whether you are using a microphone or the audio file.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-language-understanding-resources"></a>Language Understanding 리소스 만들기

If you already have Language Understanding authoring and prediction resources in your Azure subscription, you can use them in this exercise. Otherwise, follow these instructions to create them.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&amp;#65291;리소스 생성** 단추를 선택하고, *언어 이해*를 감지하며, 다음 설정을 사용하여 **Language Understanding** 리소스를 생성합니다.
    - **생성 옵션**: 둘 다
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **이름**: *고유 이름 입력*
    - **작성 위치**: *기본 위치 선택*
    - **가격 책정 계층 작성**: F0
    - **예측 위치**: *작성 위치와 <u>동일한 위치</u> 선택* .
    - **예측 가격 책정 계층**: F0(F0을 사용할 수 없으면 S0 선택)

3. Wait for the resources to be created, and note that two Language Understanding resources are provisioned; one for authoring, and another for prediction. You can view both of these by navigating to the resource group where you created them.

## <a name="prepare-a-language-understanding-app"></a>Language Understanding 앱 준비

If you already have a <bpt id="p1">**</bpt>Clock<ept id="p1">**</ept> app from a previous exercise, open it in the Language Understanding portal at <ph id="ph1">`https://www.luis.ai`</ph>. Otherwise, follow these instructions to create it.

1. 새 브라우저 탭에서 Language Understanding 포털 `https://www.luis.ai`를 엽니다.
2. **참고**: 이 연습은 마이크가 있는 경우에 가장 잘 작동합니다.
3. 일부 호스트 가상 환경에서는 로컬 마이크로부터 오디오를 캡처할 수 있지만, 이 기능이 작동하지 않거나 마이크가 아예 없는 경우에는 제공된 오디오 파일을 음성 입력으로 사용할 수 있습니다.
4. 효과적인 Language Understanding 앱을 만들기 위한 팁이 포함된 패널이 표시되면 닫습니다.

## <a name="train-and-publish-the-app-with-speech-priming"></a>*음성 프라이밍*을 사용하여 앱 학습 및 게시

1. 앱을 아직 학습시키지 않았으면 Language Understanding 포털 위쪽에서 **학습**을 선택하여 앱을 학습시킵니다.
2. 마이크를 사용하는지 아니면 오디오 파일을 사용하는지에 따라 서로 다른 옵션을 선택해야 하므로 주의하여 지침을 따르세요.
3. 게시가 완료되면 Language Understanding 포털 위쪽에서 **관리**를 선택합니다.
4. On the <bpt id="p1">**</bpt>Settings<ept id="p1">**</ept> page, note the <bpt id="p2">**</bpt>App ID<ept id="p2">**</ept>. Client applications need this to use your app.
5. **Azure 리소스** 페이지의 **예측 리소스** 아래에 예측 리소스가 나열되어 있지 않으면 Azure 구독의 예측 리소스를 추가합니다.
6. Note the <bpt id="p1">**</bpt>Primary Key<ept id="p1">**</ept>, <bpt id="p2">**</bpt>Secondary Key<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>Location<ept id="p3">**</ept> (<bpt id="p4">&lt;u&gt;</bpt>not<ept id="p4">&lt;/u&gt;</ept> endpoint!) for the prediction resource. Speech SDK client applications need the location and one of the keys to connect to the prediction resource and be authenticated.

## <a name="configure-a-client-application-for-language-understanding"></a>Language Understanding용 클라이언트 애플리케이션 구성

이 연습에서는 음성 입력을 받아들이고 Language Understanding 앱을 사용하여 사용자의 의도를 예측하는 클라이언트 애플리케이션을 만듭니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept> in this exercise. In the steps that follow, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **11-luis-speech** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **speaking-clock-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 파일에 포함되어 있는 구성 값을 업데이트하여 Language Understanding 앱의 **앱 ID**, **위치**(예: *eastus*. 전체 엔드포인트 <u>아님</u>), 그리고 예측 리소스의 **키** 중 하나를 포함합니다(Language Understanding 포털의 앱 **관리** 페이지에서 확인 가능).

## <a name="install-sdk-packages"></a>SDK 패키지 설치

Speech SDK를 Language Understanding 서비스와 함께 사용하려면 프로그래밍 언어에 맞는 Speech SDK 패키지를 설치해야 합니다.

1. In Visual Studio, right-click the <bpt id="p1">**</bpt>speaking-clock-client<ept id="p1">**</ept> folder and open an integrated terminal. Then install the Language Understanding SDK package by running the appropriate command for your language preference:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.14.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-speech==1.14.0
    ```

2. Additionally, if your system does <bpt id="p1">&lt;u&gt;</bpt>not<ept id="p1">&lt;/u&gt;</ept> have a working microphone, you will need to use an audio file to provide spoken input for your application. In this case, use the following commands to install an additional package so your program can play the audio file (you can skip this if you intend to use a microphone):

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

3. **speaking-clock-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: speaking-clock-client.py

4. Open the code file and at the top, under the existing namespace references, find the comment <bpt id="p1">**</bpt>Import namespaces<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Speech SDK:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Intent;
    ```

    **Python**

    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. 또한 시스템에 작동하는 마이크가 <u>없는</u> 경우에는 기존 네임스페이스 가져오기 아래에서 다음 코드를 추가하여 오디오 파일을 재생하는 데 사용할 라이브러리를 가져옵니다.

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

## <a name="create-an-intentrecognizer"></a>*IntentRecognizer* 만들기

**IntentRecognizer** 클래스는 음성 입력으로부터 Language Understanding 예측을 가져오는 데 사용할 수 있는 클라이언트 개체를 제공합니다.

1. In the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, note that code to load the App ID, prediction region, and key from the configuration file has already been provided. Then find the comment <bpt id="p1">**</bpt>Configure speech service and get intent recognizer<ept id="p1">**</ept>, and add the following code depending on whether you will use a microphone or an audio file for speech input:

    ### <a name="if-you-have-a-working-microphone"></a>**작동하는 마이크가 있는 경우:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

    ### <a name="if-you-need-to-use-an-audio-file"></a>**오디오 파일을 사용해야 하는 경우:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    string audioFile = "time-in-london.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    System.Threading.Thread.Sleep(2000);
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    audioFile = 'time-in-london.wav'
    playsound(audioFile)
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

## <a name="get-a-predicted-intent-from-spoken-input"></a>음성 입력에서 예측한 의도 가져오기

이제 Speech SDK를 사용하여 음성 입력에서 예측한 의도를 가져오는 코드를 구현할 수 있습니다.

1. **Main** 함수의 방금 추가한 코드 바로 아래에서 **AppID에서 모델을 가져온 다음 사용할 의도 추가** 주석을 찾은 후 다음 코드를 추가하여 Language Understanding 모델(앱 ID 기준)을 가져오고 인식기가 식별하도록 할 의도를 지정합니다.

    **C#**

    ```C#
    // Get the model from the AppID and add the intents we want to use
    var model = LanguageUnderstandingModel.FromAppId(luAppId);
    recognizer.AddIntent(model, "GetTime", "time");
    recognizer.AddIntent(model, "GetDate", "date");
    recognizer.AddIntent(model, "GetDay", "day");
    recognizer.AddIntent(model, "None", "none");
    ```

    각 의도에는 문자열 기반 ID를 지정할 수 있음

    **Python**

    ```Python
    # Get the model from the AppID and add the intents we want to use
    model = speech_sdk.intent.LanguageUnderstandingModel(app_id=lu_app_id)
    intents = [
        (model, "GetTime"),
        (model, "GetDate"),
        (model, "GetDay"),
        (model, "None")
    ]
    recognizer.add_intents(intents)
    ```

2. Under the comment <bpt id="p1">**</bpt>Process speech input<ept id="p1">**</ept>, add the following code, which uses the recognizer to asynchronously call the Language Understanding service with spoken input, and retrieve response. If the response includes a predicted intent, the spoken query, predicted intent, and full JSON response are displayed. Otherwise the code handles the response based on the reason returned.

**C#**

```C
// Process speech input
string intent = "";
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
if (result.Reason == ResultReason.RecognizedIntent)
{
    // Intent was identified
    intent = result.IntentId;
    Console.WriteLine($"Query: {result.Text}");
    Console.WriteLine($"Intent Id: {intent}.");
    string jsonResponse = result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
    Console.WriteLine($"JSON Response:\n{jsonResponse}\n");
    
    // Get the first entity (if any)

    // Apply the appropriate action
    
}
else if (result.Reason == ResultReason.RecognizedSpeech)
{
    // Speech was recognized, but no intent was identified.
    intent = result.Text;
    Console.Write($"I don't know what {intent} means.");
}
else if (result.Reason == ResultReason.NoMatch)
{
    // Speech wasn't recognized
    Console.WriteLine($"Sorry. I didn't understand that.");
}
else if (result.Reason == ResultReason.Canceled)
{
    // Something went wrong
    var cancellation = CancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
    }
}
```

**Python**

```Python
# Process speech input
intent = ''
result = recognizer.recognize_once_async().get()
if result.reason == speech_sdk.ResultReason.RecognizedIntent:
    intent = result.intent_id
    print("Query: {}".format(result.text))
    print("Intent: {}".format(intent))
    json_response = json.loads(result.intent_json)
    print("JSON Response:\n{}\n".format(json.dumps(json_response, indent=2)))
    
    # Get the first entity (if any)
    
    # Apply the appropriate action
    
elif result.reason == speech_sdk.ResultReason.RecognizedSpeech:
    # Speech was recognized, but no intent was identified.
    intent = result.text
    print("I don't know what {} means.".format(intent))
elif result.reason == speech_sdk.ResultReason.NoMatch:
    # Speech wasn't recognized
    print("Sorry. I didn't understand that.")
elif result.reason == speech_sdk.ResultReason.Canceled:
    # Something went wrong
    print("Intent recognition canceled: {}".format(result.cancellation_details.reason))
    if result.cancellation_details.reason == speech_sdk.CancellationReason.Error:
        print("Error details: {}".format(result.cancellation_details.error_details))
```

지금까지 추가한 코드는 *의도*는 식별하지만 일부 의도는 *엔터티*를 참조할 수도 있으므로, 서비스에서 반환된 JSON에서 엔터티 정보를 추출하는 코드를 추가해야 합니다.

3. 방금 추가한 코드에서 **첫 번째 엔터티 가져오기(있는 경우)** 주석을 찾아 그 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get the first entity (if any)
JObject jsonResults = JObject.Parse(jsonResponse);
string entityType = "";
string entityValue = "";
if (jsonResults["entities"].HasValues)
{
    JArray entities = new JArray(jsonResults["entities"][0]);
    entityType = entities[0]["type"].ToString();
    entityValue = entities[0]["entity"].ToString();
    Console.WriteLine(entityType + ": " + entityValue);
}
```

**Python**

```Python
# Get the first entity (if any)
entity_type = ''
entity_value = ''
if len(json_response["entities"]) > 0:
    entity_type = json_response["entities"][0]["type"]
    entity_value = json_response["entities"][0]["entity"]
    print(entity_type + ': ' + entity_value)
```
    
Your code now uses the Language Understanding app to predict an intent as well as any entities that were detected in the input utterance. Your client application must now use that prediction to determine and perform the appropriate action.

4. 방금 추가한 코드 아래에서 **적절한 작업 적용** 주석을 찾은 후 다음 코드를 추가합니다. 이 코드는 애플리케이션에서 지원하는 의도(**GetTime**, **GetDate** 및 **GetDay**)를 확인한 다음 관련 엔터티가 감지되었는지를 확인합니다. 그런 후에 기존 함수를 호출하여 적절한 응답을 생성합니다.

**C#**

```C#
// Apply the appropriate action
switch (intent)
{
    case "time":
        var location = "local";
        // Check for entities
        if (entityType == "Location")
        {
            location = entityValue;
        }
        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;
    case "day":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entityType == "Date")
        {
            date = entityValue;
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;
    case "date":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entityType == "Weekday")
        {
            day = entityValue;
        }

        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;
    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("You said " + result.Text.ToLower());
        if (result.Text.ToLower().Replace(".", "") == "stop")
        {
            intent = result.Text;
        }
        else
        {
            Console.WriteLine("Try asking me for the time, the day, or the date.");
        }
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if intent == 'GetTime':
    location = 'local'
    # Check for entities
    if entity_type == 'Location':
        location = entity_value
    # Get the time for the specified location
    print(GetTime(location))

elif intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if entity_type == 'Date':
        date_string = entity_value
    # Get the day for the specified date
    print(GetDay(date_string))

elif intent == 'GetDate':
    day = 'today'
    # Check for entities
    if entity_type == 'Weekday':
        # List entities are lists
        day = entity_value
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('You said {}'.format(result.text))
    if result.text.lower().replace('.', '') == 'stop':
        intent = result.text
    else:
        print('Try asking me for the time, the day, or the date.')
```

## <a name="run-the-client-application"></a>클라이언트 애플리케이션 실행

1. 변경 내용을 저장하고 **speaking-clock-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock-client.py
    ```

2. Azure 구독에 Language Understanding 작성 및 예측 리소스가 이미 포함되어 있으면 이 연습에서 해당 리소스를 사용할 수 있습니다.

    *What's the time?*
    
    *What time is it?*

    *What day is it?*

    *What is the time in London?*

    *What's the date?*

    *What date is Sunday?*

> 그렇지 않은 경우에는 다음 지침에 따라 해당 리소스를 만듭니다.

## <a name="more-information"></a>추가 정보

Speech와 Language Understanding의 통합에 대해 자세히 알아보려면 [Speech 설명서](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition)를 참조하세요.
