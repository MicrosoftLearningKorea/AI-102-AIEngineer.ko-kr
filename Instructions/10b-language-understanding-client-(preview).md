---
lab:
  title: 대화 언어 이해 클라이언트 애플리케이션 만들기(미리 보기)
  module: Module 5 - Creating Language Understanding Solutions
---

# <a name="create-a-language-service-client-application"></a>언어 서비스 클라이언트 애플리케이션 만들기

The Conversational Language Understanding feature of the Azure Cognitive Service for Language enables you to define a conversational language model that client apps can use to interpret natural language input from users, predict the users <bpt id="p1">*</bpt>intent<ept id="p1">*</ept> (what they want to achieve), and identify any <bpt id="p2">*</bpt>entities<ept id="p2">*</ept> to which the intent should be applied. You can create client applications that consume conversational language understanding models directly through REST interfaces, or by using language-specific software development kits (SDKs).

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.

2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.

3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.

4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-language-service-resources"></a>언어 서비스 리소스 만들기

If you already have a Language service resource in your Azure subscription, you can use it in this exercise. Otherwise, follow these instructions to create one.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.

2. **&#65291;리소스 만들기** 단추를 선택하고, 언어 서비스를 검색하고, 다음 설정을 사용하여 **언어 서비스** 리소스를 만듭니다.

    - **기본 기능** 전체
    - **사용자 지정 기능**: 없음
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: *westus2*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: *S*
    - **약관**: 확인하려면 확인란 선택
    - **책임 있는 AI 알림**: 확인하려면 확인란 선택

3. Wait for the resources to be created. You can view your resource by navigating to the resource group where you created it.

## <a name="import-train-and-publish-a-conversational-language-understanding-model"></a>대화 언어 이해 모델 가져오기, 학습 및 게시

언어용 Azure Cognitive Service의 대화 언어 이해 기능을 사용하면 클라이언트 앱이 사용자의 자연어 입력을 해석하고, 사용자 의도(달성하려는 것)를 예측하고, 의도를 적용해야 하는 엔터티를 식별하는 데 사용할 수 있는 대화 언어 모델을 정의할 수 있습니다. 

1. 새 브라우저 탭에서 Language Studio - 미리 보기 포털(`https://language.cognitive.azure.com`)을 엽니다.

2. REST 인터페이스를 통해 직접 또는 언어별 SDK(소프트웨어 개발 키트)를 통해 대화 언어 이해 모델을 사용하는 클라이언트 애플리케이션을 만들 수 있습니다.

3. **대화 언어 이해** 페이지를 엽니다.

3. Next to <bpt id="p1">**</bpt>&amp;#65291;Create new project<ept id="p1">**</ept>, view the drop-down list and select <bpt id="p2">**</bpt>Import<ept id="p2">**</ept>. Click <bpt id="p1">**</bpt>Choose File<ept id="p1">**</ept> and then browse to the <bpt id="p2">**</bpt>10b-clu-client-(preview)<ept id="p2">**</ept> subfolder in the project folder containing the lab files for this exercise. Select <bpt id="p1">**</bpt>Clock.json<ept id="p1">**</ept>, click <bpt id="p2">**</bpt>Open<ept id="p2">**</ept>, and then click <bpt id="p3">**</bpt>Done<ept id="p3">**</ept>.

4. 효과적인 언어 서비스 앱을 만들 수 있는 팁이 포함된 패널이 표시되면 해당 패널을 닫습니다.

5. At the left of the Language Studio portal, select <bpt id="p1">**</bpt>Train model<ept id="p1">**</ept> to train the app. Click <bpt id="p1">**</bpt>Start a training job<ept id="p1">**</ept>, name the model <bpt id="p2">**</bpt>Clock<ept id="p2">**</ept> and ensure that evaluation with training is enabled. Training may take several minutes to complete.

    > **참고**: 모델 이름 **Clock**은 clock-client 코드(랩의 뒷부분에서 사용됨)로 하드 코딩되므로 이름은 설명된 대로 정확히 대소문자를 구분하고 철자를 표시합니다. 

6. Language Studio 포털 왼쪽에서 **모델 배포**를 선택하고 **배포 추가**를 사용하여 **production**이라는 Clock 모델에 대한 배포를 만듭니다.

    > **참고**: 배포 이름 **production**은 clock-client 코드(랩의 뒷부분에서 사용됨)로 하드 코딩되므로 이름은 설명된 대로 정확히 대소문자를 구분하고 철자를 표시합니다. 

7. After the deployment is complete, select the <bpt id="p1">**</bpt>production<ept id="p1">**</ept> deployment, and then click <bpt id="p2">**</bpt>Get prediction URL<ept id="p2">**</ept>. Client applications need the endpoint URL to use your deployed model.

8. At the left of the Language Studio portal, select <bpt id="p1">**</bpt>Project Settings<ept id="p1">**</ept> and note the <bpt id="p2">**</bpt>Primary key<ept id="p2">**</ept>. Client applications need the primary key to use your deployed model.

9. 클라이언트 애플리케이션은 배포된 모델에 연결하고 인증을 받으려면 예측 URL 및 언어 서비스 키의 정보가 필요합니다.

## <a name="prepare-to-use-the-language-service-sdk"></a>언어 서비스 SDK 사용 준비

이 연습에서는 Clock 모델(게시된 대화 언어 이해 모델)을 사용하여 사용자 입력의 의도를 예측하고 적절하게 대응하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>.NET<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **10b-clu-client-(preview)** 폴더로 이동하고 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.

2. Right-click the <bpt id="p1">**</bpt>clock-client<ept id="p1">**</ept> folder and then select <bpt id="p2">**</bpt>Open in Integrated Terminal<ept id="p2">**</ept>. Then install the Conversational Language Service SDK package by running the appropriate command for your language preference:

    **C#**

    ```
    dotnet add package Azure.AI.Language.Conversations --version 1.0.0-beta.2
    ```

    **Python**

    ```
    pip install azure-ai-language-conversations --pre
    python -m pip install python-dotenv
    python -m pip install python-dateutil
    ```

3. **clock-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.

    - **C#** : appsettings.json
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to include the <bpt id="p1">**</bpt>Endpoint URL<ept id="p1">**</ept> and the <bpt id="p2">**</bpt>Primary key<ept id="p2">**</ept> for your Language resource. You can find the required values in the Azure portal or Language Studio as follows:

    - Azure portal: Open your Language resource. Under <bpt id="p1">**</bpt>Resource Management<ept id="p1">**</ept>, select <bpt id="p2">**</bpt>Keys and Endpoint<ept id="p2">**</ept>. Copy the <bpt id="p1">**</bpt>KEY 1<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Endpoint<ept id="p2">**</ept> values to your configuration settings file.
    - Language Studio: Open your <bpt id="p1">**</bpt>Clock<ept id="p1">**</ept> project. The Language service endpoint can be found on the <bpt id="p1">**</bpt>Deploy model<ept id="p1">**</ept> page under <bpt id="p2">**</bpt>Get prediction URL<ept id="p2">**</ept>, and the the <bpt id="p3">**</bpt>Primary key<ept id="p3">**</ept> can be found on the <bpt id="p4">**</bpt>Project settings<ept id="p4">**</ept> page. The Language service endpoint portion of the Prediction URL ends with <bpt id="p1">**</bpt>.cognitiveservices.azure.com/<ept id="p1">**</ept>. For example: <ph id="ph1">`https://ai102-langserv.cognitiveservices.azure.com/`</ph>.

4. **clock-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Azure 구독에 언어 서비스 리소스가 이미 있는 경우 이 연습에서 사용할 수 있습니다.

    **C#**

    ```C#
    // Import namespaces
    using Azure;
    using Azure.AI.Language.Conversations;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.language.conversations import ConversationAnalysisClient
    from azure.ai.language.conversations.models import ConversationAnalysisOptions
    ```

## <a name="get-a-prediction-from-the-conversational-language-model"></a>대화 언어 모델에서 예측 가져오기

이제 SDK를 사용하여 언어 이해 모델에서 예측을 가져오는 코드를 구현할 수 있습니다.

1. 그렇지 않은 경우에는 관련 지침에 따라 리소스를 만듭니다.

    **C#**

    ```C#
    // Create a client for the Language service model
    Uri lsEndpoint = new Uri(predictionEndpoint);
    AzureKeyCredential lsCredential = new AzureKeyCredential(predictionKey);

    ConversationAnalysisClient lsClient = new ConversationAnalysisClient(lsEndpoint, lsCredential);
    ```

    **Python**

    ```Python
    # Create a client for the Language service model
    client = ConversationAnalysisClient(
        ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    ```

2. Note that the code in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function prompts for user input until the user enters "quit". Within this loop, find the comment <bpt id="p1">**</bpt>Call the Language service model to get intent and entities<ept id="p1">**</ept> and add the following code:

    **C#**

    ```C#
    // Call the Language service model to get intent and entities
    var lsProject = "Clock";
    var lsSlot = "production";

    ConversationsProject conversationsProject = new ConversationsProject(lsProject, lsSlot);
    Response<AnalyzeConversationResult> response = await lsClient.AnalyzeConversationAsync(userText, conversationsProject);

    ConversationPrediction conversationPrediction = response.Value.Prediction as ConversationPrediction;

    Console.WriteLine(JsonConvert.SerializeObject(conversationPrediction, Formatting.Indented));
    Console.WriteLine("--------------------\n");
    Console.WriteLine(userText);
    var topIntent = "";
    if (conversationPrediction.Intents[0].Confidence > 0.5)
    {
        topIntent = conversationPrediction.TopIntent;
    }

    var entities = conversationPrediction.Entities;
    ```

    **Python**

    ```Python
    # Call the Language service model to get intent and entities
    convInput = ConversationAnalysisOptions(
        query = userText
        )
    
    cls_project = 'Clock'
    deployment_slot = 'production'

    with client:
        result = client.analyze_conversations(
            convInput, 
            project_name=cls_project, 
            deployment_name=deployment_slot
            )

    # list the prediction results
    top_intent = result.prediction.top_intent
    entities = result.prediction.entities

    print("view top intent:")
    print("\ttop intent: {}".format(result.prediction.top_intent))
    print("\tcategory: {}".format(result.prediction.intents[0].category))
    print("\tconfidence score: {}\n".format(result.prediction.intents[0].confidence_score))

    print("view entities:")
    for entity in entities:
        print("\tcategory: {}".format(entity.category))
        print("\ttext: {}".format(entity.text))
        print("\tconfidence score: {}".format(entity.confidence_score))

    print("query: {}".format(result.query))
    ```

    The call to the Language service model returns a prediction/result, which includes the top (most likely) intent as well as any entities that were detected in the input utterance. Your client application must now use that prediction to determine and perform the appropriate action.

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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Location")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        location = entity.Text;
                    }
                    
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Date")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        date = entity.Text;
                    }
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Weekday")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        day = entity.Text;
                    }
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
            for entity in entities:
                if 'Location' == entity.category:
                    # ML entities are strings, get the first one
                    location = entity.text
        # Get the time for the specified location
        print(GetTime(location))

    elif top_intent == 'GetDay':
        date_string = date.today().strftime("%m/%d/%Y")
        # Check for entities
        if len(entities) > 0:
            # Check for a Date entity
            for entity in entities:
                if 'Date' == entity.category:
                    # Regex entities are strings, get the first one
                    date_string = entity.text
        # Get the day for the specified date
        print(GetDay(date_string))

    elif top_intent == 'GetDate':
        day = 'today'
        # Check for entities
        if len(entities) > 0:
            # Check for a Weekday entity
            for entity in entities:
                if 'Weekday' == entity.category:
                # List entities are lists
                    day = entity.text
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

5. When prompted, enter utterances to test the application. For example, try:

    *Hello*
    
    *What time is it?*

    *What's the time in London?*

    *What's the date?*

    *What date is Sunday?*

    *What day is it?*

    *What day is 01/01/2025?*

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The logic in the application is deliberately simple, and has a number of limitations. For example, when getting the time, only a restricted set of cities is supported and daylight savings time is ignored. The goal is to see an example of a typical pattern for using Language Service in which your application must:
>
>   1. 예측 엔드포인트에 연결
>   2. 발화를 제출하여 예측 가져오기
>   3. 예측된 의도와 엔터티에 적절하게 응답하는 논리 구현

6. 테스트를 완료한 후 *quit*을 입력합니다.

## <a name="more-information"></a>자세한 정보

언어 서비스 클라이언트 만들기에 대해 자세히 알아보려면 [개발자 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)를 참조하세요.
