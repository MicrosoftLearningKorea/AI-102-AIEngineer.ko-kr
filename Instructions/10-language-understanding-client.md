---
lab:
  title: Language Understanding 클라이언트 애플리케이션 만들기
  module: Module 5 - Creating Language Understanding Solutions
---

# <a name="create-a-language-understanding-client-application"></a>Language Understanding 클라이언트 애플리케이션 만들기

The Language Understanding service enables you to define an app that encapsulates a language model that applications can use to interpret natural language input from users,  predict the users <bpt id="p1">*</bpt>intent<ept id="p1">*</ept> (what they want to achieve), and identify any <bpt id="p2">*</bpt>entities<ept id="p2">*</ept> to which the intent should be applied. You can create client applications that consume Language Understanding apps directly through REST interfaces, or by using language -specific software development kits (SDKs).

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

## <a name="import-train-and-publish-a-language-understanding-app"></a>Language Understanding 앱 가져오기, 학습 및 게시

Language Understanding 서비스에서는 언어 모델을 캡슐화하는 앱을 정의할 수 있습니다. 애플리케이션은 이 모델을 통해 사용자의 자연어 입력을 해석하고, 사용자의 *의도*(사용자가 달성하려는 목표)를 예측하고, 해당 의도를 적용해야 하는 *엔터티*를 식별할 수 있습니다.

1. 새 브라우저 탭에서 Language Understanding 포털 `https://www.luis.ai`를 엽니다.
2. REST 인터페이스를 통해 Language Understanding 앱을 직접 사용하거나 언어별 SDK(소프트웨어 개발 키트)를 통해 앱을 사용하는 클라이언트 애플리케이션을 만들 수 있습니다.
3. Open the <bpt id="p1">**</bpt>Conversation Apps<ept id="p1">**</ept> page, next to <bpt id="p2">**</bpt>&amp;#65291;New app<ept id="p2">**</ept>, view the drop-down list and select <bpt id="p3">**</bpt>Import As LU<ept id="p3">**</ept>.
Browse to the <bpt id="p1">**</bpt>10-luis-client<ept id="p1">**</ept> subfolder in the project folder containing the lab files for this exercise, and select <bpt id="p2">**</bpt>Clock.lu<ept id="p2">**</ept>. Then specify a unique name for the clock app.
4. 효과적인 Language Understanding 앱을 만들기 위한 팁이 포함된 패널이 표시되면 닫습니다.
5. Language Understanding 포털 위쪽에서 **학습**을 선택하여 앱을 학습시킵니다.
6. Language Understanding 포털 오른쪽 위에서 **게시**를 선택하여 **프로덕션 슬롯**에 앱을 게시합니다.
7. 게시가 완료되면 Language Understanding 포털 위쪽에서 **관리**를 선택합니다.
8. On the <bpt id="p1">**</bpt>Settings<ept id="p1">**</ept> page, note the <bpt id="p2">**</bpt>App ID<ept id="p2">**</ept>. Client applications need this to use your app.
9. **Azure 리소스** 페이지의 **예측 리소스** 아래에 예측 리소스가 나열되어 있지 않으면 Azure 구독의 예측 리소스를 추가합니다.
10. Note the <bpt id="p1">**</bpt>Primary Key<ept id="p1">**</ept>, <bpt id="p2">**</bpt>Secondary Key<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>Endpoint URL<ept id="p3">**</ept> for the prediction resource. Client applications need the endpoint and one of the keys to connect to the prediction resource and be authenticated.

## <a name="prepare-to-use-the-language-understanding-sdk"></a>Language Understanding SDK 사용 준비

이 연습에서는 Clock Language Understanding 앱을 사용하여 사용자 입력에서 의도를 예측하고 적절하게 응답을 하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **10-luis-client** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Right-click the <bpt id="p1">**</bpt>clock-client<ept id="p1">**</ept> folder and open an integrated terminal. Then install the Language Understanding SDK package by running the appropriate command for your language preference:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

**Runtime** (예측) 패키지 외에 **Authoring** 패키지도 있습니다. 이 패키지를 사용하면 Language Understanding 모델 작성 및 관리용 코드를 작성할 수 있습니다.

**Python**

```
pip install azure-cognitiveservices-language-luis==0.7.0
```
    
Python SDK 패키지에는 **prediction** 및 **authoring**용 클래스가 모두 포함되어 있습니다.

3. **clock-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 파일에 포함되어 있는 구성 값을 업데이트하여 Language Understanding 앱의 **앱 ID** 및 예측 리소스의 **엔드포인트 URL**과 **키** 중 하나를 포함합니다(Language Understanding 포털의 앱 **관리** 페이지에서 확인 가능).

4. **clock-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Open the code file and at the top, under the existing namespace references, find the comment <bpt id="p1">**</bpt>Import namespaces<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Language Understanding prediction SDK:

**C#**

```C#
// Import namespaces
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

**Python**

```Python
# Import namespaces
from azure.cognitiveservices.language.luis.runtime import LUISRuntimeClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="get-a-prediction-from-the-language-understanding-app"></a>Language Understanding 앱에서 예측 가져오기

이제 SDK를 사용하여 Language Understanding 앱에서 예측을 가져오는 코드를 구현할 수 있습니다.

1. In the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, note that code to load the App ID, prediction endpoint, and key from the configuration file has already been provided. Then find the comment <bpt id="p1">**</bpt>Create a client for the LU app<ept id="p1">**</ept> and add the following code to create a prediction client for your Language Understanding app:

**C#**

```C#
// Create a client for the LU app
var credentials = new Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.ApiKeyServiceClientCredentials(predictionKey);
var luClient = new LUISRuntimeClient(credentials) { Endpoint = predictionEndpoint };
```

**Python**

```Python
# Create a client for the LU app
credentials = CognitiveServicesCredentials(lu_prediction_key)
lu_client = LUISRuntimeClient(lu_prediction_endpoint, credentials)
```

2. Note that the code in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function prompts for user input until the user enters "quit". Within this loop, find the comment <bpt id="p1">**</bpt>Call the LU app to get intent and entities<ept id="p1">**</ept> and add the following code:

**C#**

```C#
// Call the LU app to get intent and entities
var slot = "Production";
var request = new PredictionRequest { Query = userText };
PredictionResponse predictionResponse = await luClient.Prediction.GetSlotPredictionAsync(luAppId, slot, request);
Console.WriteLine(JsonConvert.SerializeObject(predictionResponse, Formatting.Indented));
Console.WriteLine("--------------------\n");
Console.WriteLine(predictionResponse.Query);
var topIntent = predictionResponse.Prediction.TopIntent;
var entities = predictionResponse.Prediction.Entities;
```

**Python**

```Python
# Call the LU app to get intent and entities
request = { "query" : userText }
slot = 'Production'
prediction_response = lu_client.prediction.get_slot_prediction(lu_app_id, slot, request)
top_intent = prediction_response.prediction.top_intent
entities = prediction_response.prediction.entities
print('Top Intent: {}'.format(top_intent))
print('Entities: {}'.format (entities))
print('-----------------\n{}'.format(prediction_response.query))
```

The call to the Language Understanding app returns a prediction, which includes the top (most likely) intent as well as any entities that were detected in the input utterance. Your client application must now use that prediction to determine and perform the appropriate action.

3. **적절한 작업 적용** 주석을 찾은 후 다음 코드를 추가합니다. 이 코드는 애플리케이션에서 지원하는 의도(**GetTime**, **GetDate** 및 **GetDay**)를 확인한 다음 관련 엔터티가 감지되었는지를 확인합니다. 그런 후에 기존 함수를 호출하여 적절한 응답을 생성합니다.

**C#**

```C#
// Apply the appropriate action
switch (topIntent)
{
    case "GetTime":
        var location = "local";
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a location entity
            if (entities.ContainsKey("Location"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Location"].ToString());
                // ML entities are strings, get the first one
                location = entityJson[0].ToString();
            }
        }

        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;

    case "GetDay":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Date entity
            if (entities.ContainsKey("Date"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Date"].ToString());
                // Regex entities are strings, get the first one
                date = entityJson[0].ToString();
            }
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;

    case "GetDate":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Weekday entity
            if (entities.ContainsKey("Weekday"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Weekday"].ToString());
                // List entities are lists
                day = entityJson[0][0].ToString();
            }
        }
        // Get the date for the specified day
        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;

    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("Try asking me for the time, the day, or the date.");
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if top_intent == 'GetTime':
    location = 'local'
    # Check for entities
    if len(entities) > 0:
        # Check for a location entity
        if 'Location' in entities:
            # ML entities are strings, get the first one
            location = entities['Location'][0]
    # Get the time for the specified location
    print(GetTime(location))

elif top_intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if len(entities) > 0:
        # Check for a Date entity
        if 'Date' in entities:
            # Regex entities are strings, get the first one
            date_string = entities['Date'][0]
    # Get the day for the specified date
    print(GetDay(date_string))

elif top_intent == 'GetDate':
    day = 'today'
    # Check for entities
    if len(entities) > 0:
        # Check for a Weekday entity
        if 'Weekday' in entities:
            # List entities are lists
            day = entities['Weekday'][0][0]
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('Try asking me for the time, the day, or the date.')
```
    
4. 변경 내용을 저장하고 **clock-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python clock-client.py
```

5. Azure 구독에 Language Understanding 작성 및 예측 리소스가 이미 포함되어 있으면 이 연습에서 해당 리소스를 사용할 수 있습니다.

    *Hello*
    
    *What time is it?*

    *What's the time in London?*

    *What's the date?*

    *What date is Sunday?*

    *What day is it?*

    *What day is 01/01/2025?*

> 그렇지 않은 경우에는 다음 지침에 따라 해당 리소스를 만듭니다.
>
>   1. 예측 엔드포인트에 연결
>   2. 발화를 제출하여 예측 가져오기
>   3. 예측된 의도와 엔터티에 적절하게 응답하는 논리 구현

6. 테스트를 완료한 후 *quit*을 입력합니다.

## <a name="more-information"></a>추가 정보

Language Understanding 클라이언트 만들기에 대해 자세히 알아보려면 [개발자 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)를 참조하세요.
