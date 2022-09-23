---
lab:
  title: 언어 서비스를 사용하여 언어 이해 모델 만들기(미리 보기)
  module: Module 5 - Creating Language Understanding Solutions
---

# <a name="create-a-language-understanding-model-with-the-language-service-preview"></a>언어 서비스를 사용하여 언어 이해 모델 만들기(미리 보기)

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The conversational language understanding feature of the Language service is currently in preview, and subject to change. In some cases, model training may fail - if this happens, try again.  

언어 서비스를 사용하면 애플리케이션이 사용자의 자연어 입력을 해석하고, 사용자 의도(달성하려는 것)를 예측하고, 의도를 적용해야 하는 엔터티를 식별하는 데 사용할 수 있는 대화 언어 이해 모델을 정의할 수 있습니다.  

예를 들어, 시계 애플리케이션용 대화 언어 모델은 다음과 같은 입력을 처리해야 할 수 있습니다.

*What is the time in London?*

이러한 입력 유형의 예가 발화(사용자가 말하거나 입력하는 내용)입니다. 이 발화에서 원하는 의도는 특정 위치(엔터티), 즉 여기서는 런던의 시간을 확인하는 것입니다.  

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The task of a conversational language model is to predict the user's intent and identify any entities to which the intent applies. It is <bpt id="p1">&lt;u&gt;</bpt>not<ept id="p1">&lt;/u&gt;</ept> the job of a conversational language model to actually perform the actions required to satisfy the intent. For example, a clock application can use a conversational language model to discern that the user wants to know the time in London; but the client application itself must then implement the logic to determine the correct time and present it to the user.

## <a name="create-a-language-service-resource"></a>언어 서비스 리소스 만들기

To create a conversational language model, you need a <bpt id="p1">**</bpt>Language service<ept id="p1">**</ept> resource in a supported region. At the time of writing, only the West US 2 and West Europe regions are supported.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고, 언어를 검색하고, 다음 설정을 사용하여 **언어 서비스** 리소스를 만듭니다.

    - **기능**: 대화 언어 이해를 포함하는 기본 기능 사용(미리 보기) 
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 미국 서부 2 또는 서유럽
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준(S) 계층을 선택합니다(대화 언어 이해는 현재 무료 계층에서 지원되지 않음).
    - **약관**: 동의 
    - **책임 있는 AI 알림**: 동의
3. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

## <a name="create-a-conversational-language-understanding-project"></a>대화 언어 이해 프로젝트 만들기

이제 작성 리소스를 만들었으므로 이 리소스를 사용하여 대화 언어 이해 프로젝트를 만들 수 있습니다.

1. 새 브라우저 탭에서 `https://language.cognitive.azure.com/`의 Language Studio 포털을 열고 Azure 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.

2. 언어 리소스를 선택하라는 메시지가 표시되면 다음 설정을 선택합니다.

    - **Azure Directory**: 구독이 포함된 Azure Directory입니다.
    - **Azure 구독**: Azure 구독입니다.
    - **언어 리소스**: 이전에 만든 언어 리소스입니다.

3. 언어 리소스를 선택하라는 메시지가 표시되지 <u>않으면</u> 이미 다른 언어 리소스를 할당했기 때문일 수 있습니다. 이 경우 다음을 수행합니다.

    1. 페이지의 위쪽에 있는 막대에서 **설정(&#9881;)** 단추를 클릭합니다.
    2. **설정** 페이지에서 **리소스** 탭을 봅니다.
    3. 방금 만든 언어 리소스를 선택하고 **리소스 전환**을 클릭합니다.
    4. 페이지 맨 위에서 **Language Studio**를 클릭하여 Language Studio 홈페이지로 돌아갑니다.

4. 포털 위쪽의 **새로 만들기** 메뉴에서 **대화형 언어 이해**을 선택합니다.

5. **기본 정보 입력** 페이지의 **프로젝트 만들기** 대화 상자에 다음 세부 정보를 입력하고 **다음**을 클릭합니다.
    - **이름**: `Clock`
    - **설명**: `Natural language clock`
    - **발화 기본 언어**: 영어
    - **프로젝트에서 여러 언어 사용**: 선택 안 함

7. 검토 및 완료 페이지에서 **만들기**를 클릭합니다.

## <a name="create-intents"></a>의도 만들기

새 프로젝트에서 가장 먼저 수행할 작업은 일부 의도를 정의하는 것입니다.

> **팁**: 프로젝트에서 작업할 때 일부 팁이 표시되는 경우 해당 팁을 읽고 **확인**을 클릭하여 해제하거나 **모두 건너뛰기**를 클릭합니다.

1. **스키마 빌드** 페이지의 **의도** 탭에서 **&#65291; 추가**를 선택하여 **GetTime**이라는 새 의도를 추가합니다.

2. 새 **GetTime** 의도를 클릭하여 편집하고 다음 발화를 예제 사용자 입력으로 추가합니다.

    `what is the time?`

    `what's the time?`

    `what time is it?`

    `tell me the time`

3. 이러한 발화를 추가한 후 **변경 내용 저장**을 클릭하고 **스키마 빌드** 페이지로 돌아갑니다.

4. 다음 발화를 사용하여 **GetDay**라는 또 다른 새 의도를 추가합니다.

    `what day is it?`

    `what's the day?`

    `what is the day today?`

    `what day of the week is it?`

5. 이러한 발화를 추가하고 저장한 후 **스키마 빌드** 페이지로 돌아가고 다음 발화를 사용하여 **GetDate**라는 또 다른 새 의도를 추가합니다.

    `what date is it?`

    `what's the date?`

    `what is the date today?`

    `what's today's date?`

6. 이러한 발화를 추가한 후 저장하고 발화 페이지에서 **GetDate** 필터를 지우면 모든 의도에 대한 모든 발화를 확인할 수 있습니다.

## <a name="train-and-test-the-model"></a>모델 학습 및 테스트

이제 일부 의도를 추가했으므로 언어 모델을 학습시키고 사용자 입력에서 올바르게 예측할 수 있는지 확인해 보겠습니다.

1. 왼쪽 창에서 **모델 학습** 페이지를 선택한 다음, **학습 작업 시작**을 선택합니다.

2. **학습 작업 시작** 대화 상자에서 새 모델을 학습시키는 옵션을 선택하고, 이름을 **Clock**으로 지정하고, 학습 시 평가를 실행하는 옵션이 사용되는지 확인합니다. 

3. 모델 학습 프로세스를 시작하려면 **학습**을 클릭합니다.

4. 학습이 완료되면(몇 분 정도 걸릴 수 있음) 작업 **상태**가 **학습 성공**으로 변경됩니다.

5. **참고**: 언어 서비스의 대화 언어 이해 기능은 현재 미리 보기 상태이며 변경될 수 있습니다.

    >**참고**: 평가 메트릭에 대한 자세한 내용은 [설명서](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/concepts/evaluation-metrics)를 참조하세요.

5. **모델 배포** 페이지에서 **배포 추가**를 선택합니다.

5. **배포 추가** 대화 상자에서 **새 배포 이름 만들기**를 선택한 다음, **production**을 입력합니다.

5. 경우에 따라 모델 학습이 실패할 수 있습니다. 이 경우 다시 시도하세요.

6. 모델이 배포된 경우 **모델 테스트** 페이지에서 **Clock** 모델을 선택합니다.

6. **모델 테스트: Clock** 페이지의 **배포 이름** 드롭다운에서 **production**을 선택합니다.  

7. 다음 텍스트를 입력한 다음, **테스트 실행**을 클릭합니다.

    `what's the time now?`

    Review the result that is returned, noting that it includes the predicted intent (which should be <bpt id="p1">**</bpt>GetTime<ept id="p1">**</ept>) and a confidence score that indicates the probability the model calculated for the predicted intent. The JSON tab shows the comparative confidence for each potential intent (the one with the highest confidence score is the predicted intent)

8. 텍스트 상자를 지우고 다음 텍스트로 다른 테스트를 실행합니다.

    `tell me the time`

    이번에도 예측한 의도 및 신뢰도 점수를 검토합니다.

9. 다음 텍스트를 시도합니다.

    `what's the day today?`

    이 경우 모델은 **GetDay** 의도를 예측해야 합니다.

## <a name="add-entities"></a>엔터티 추가

So far you've defined some simple utterances that map to intents. Most real applications include more complex utterances from which specific data entities must be extracted to get more context for the intent.

### <a name="add-a-learned-entity"></a>학습된 엔터티를 추가합니다.

가장 일반적인 엔터티 종류는 모델이 예제를 기반으로 엔터티 값을 식별하기 위해 학습하는 학습된 엔터티입니다.

1. Language Studio에서 **스키마 빌드** 페이지로 돌아가고 **엔터티** 탭에서 **&#65291; 추가**를 선택하여 새 엔터티를 추가합니다.

2. In the <bpt id="p1">**</bpt>Add an entity<ept id="p1">**</ept> dialog box, enter the entity name <bpt id="p2">**</bpt>Location<ept id="p2">**</ept> and ensure that <bpt id="p3">**</bpt>Learned<ept id="p3">**</ept> is selected. Then click <bpt id="p1">**</bpt>Add entity<ept id="p1">**</ept>.

3. **Location** 엔터티를 만든 후 **스키마 빌드** 페이지로 돌아가고 **의도** 탭에서 **GetTime** 의도를 선택합니다.

4. 다음과 같은 새 예제 발화를 입력합니다.

    `what time is it in London?`

5. 이 발화를 추가한 후 단어 ***London***을 선택합니다. 그러면 표시되는 드롭다운 목록에서 **Location**을 선택하여 “London”이 위치의 예임을 지정합니다.

6. 다른 예제 발화를 추가합니다.

    `Tell me the time in Paris?`

7. 발화가 추가된 경우 단어 ***Paris***를 선택하고 **Location** 엔터티에 매핑합니다.

8. 다른 예제 발화를 추가합니다.

    `what's the time in New York?`

9. 이 발화를 추가한 후 단어 ***New York***을 선택하여 **Location** 엔터티에 매핑합니다.

10. **변경 내용 저장**을 클릭하여 새 발화를 저장합니다.

### <a name="add-a-list-entity"></a>*list* 엔터티 추가

엔터티의 유효한 값을 특정 단어 및 동의어 목록으로 제한할 수 있는 경우도 있습니다. 이렇게 하면 앱이 발화의 엔터티 인스턴스를 쉽게 식별할 수 있습니다.

1. Language Studio에서 **스키마 빌드** 페이지로 돌아가고 **엔터티** 탭에서 **&#65291; 추가**를 선택하여 새 엔터티를 추가합니다.

2. In the <bpt id="p1">**</bpt>Add an entity<ept id="p1">**</ept> dialog box, enter the entity name <bpt id="p2">**</bpt>Weekday<ept id="p2">**</ept> and select the <bpt id="p3">**</bpt>List<ept id="p3">**</ept> entity type. Then click <bpt id="p1">**</bpt>Add entity<ept id="p1">**</ept>.

3. On the page for the <bpt id="p1">**</bpt>Weekday<ept id="p1">**</ept> entity, in the <bpt id="p2">**</bpt>List<ept id="p2">**</ept> section, click <bpt id="p3">**</bpt>&amp;#65291; Add new list<ept id="p3">**</ept>. Then enter the following value and synonym and click <bpt id="p1">**</bpt>Save<ept id="p1">**</ept>:

    | 목록 키 | 동의어|
    |-------------------|---------|
    | 일요일 | Sun |

4. 이전 단계를 반복하여 다음 목록 구성 요소를 추가합니다.

    | 값 | 동의어|
    |-------------------|---------|
    | 월요일 | Mon |
    | 화요일 | Tue, Tues |
    | 수요일 | Wed, Weds |
    | 목요일 | Thur, Thurs |
    | 금요일 | Fri |
    | 토요일 | Sat |

5. **스키마 빌드** 페이지로 돌아가고 **의도** 탭에서 **GetDate** 의도를 선택합니다.

6. 다음과 같은 새 예제 발화를 입력합니다.

    `what date was it on Saturday?`

7. 발화가 추가된 경우 단어 ***Saturday***를 선택하고 표시되는 드롭다운 목록에서 **Weekday**를 선택합니다.

8. 다른 예제 발화를 추가합니다.

    `what date will it be on Friday?`

9. 발화가 추가된 경우 **금요일**을 **Weekday** 엔터티에 매핑합니다.

10. 다른 예제 발화를 추가합니다.

    `what will the date be on Thurs?`

11. 발화가 추가된 경우 **Thurs**를 **Weekday** 엔터티에 매핑합니다.

12. **변경 내용 저장**을 클릭하여 새 발화를 저장합니다.

### <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

언어 서비스는 대화 애플리케이션에서 일반적으로 사용되는 미리 빌드된 엔터티 세트를 제공합니다.

1. Language Studio에서 **스키마 빌드** 페이지로 돌아가고 **엔터티** 탭에서 **&#65291; 추가**를 선택하여 새 엔터티를 추가합니다.

2. **참고**: 대화 언어 모델의 작업은 사용자 의도를 예측하고 해당 의도가 적용되는 엔터티를 식별하는 것입니다.

3. **Date** 엔터티 페이지의 **Prebuilt** 섹션에서 **&#65291; 새 미리 빌드된 항목 추가**를 클릭합니다.

4. **미리 빌드된 항목 선택** 목록에서 **DateTime**을 선택한 다음, **저장**을 클릭합니다.

5. **스키마 빌드** 페이지로 돌아가고 **의도** 탭에서 **GetDay** 의도를 선택합니다.

6. 다음과 같은 새 예제 발화를 입력합니다.

    `what day was 01/01/1901?`

7. 발화가 추가된 경우 단어 ***01/01/1901***을 선택하고 표시되는 드롭다운 목록에서 **Date**를 선택합니다.

8. 다른 예제 발화를 추가합니다.

    `what day will it be on Dec 31st 2099?`

9. 발화가 추가된 경우 **2099년 12월 31일**을 **Date** 엔터티에 매핑합니다.

10. **변경 내용 저장**을 클릭하여 새 발화를 저장합니다.

### <a name="retrain-the-model"></a>모델 재교육

이제 스키마를 수정했으므로 모델을 다시 학습시키고 다시 테스트해야 합니다.

1. **모델 학습** 페이지에서 **학습 작업 시작**을 선택합니다.

1. 의도를 충족하는 데 필요한 작업을 실제로 수행하는 것은 대화 언어 모델의 작업이 <u>아닙니다</u>.

2. 학습이 완료되면 작업 **상태**가 **학습 성공**으로 업데이트됩니다. 

2. 예를 들어, 시계 애플리케이션은 대화 언어 모델을 사용하여 사용자가 런던의 시간을 확인하려 한다는 것을 파악할 수는 있습니다. 그러나 이처럼 의도가 파악되고 나면 클라이언트 애플리케이션 자체가 논리를 구현해 정확한 시간을 확인하여 사용자에게 표시해야 합니다.

3. **모델 배포** 페이지에서 **배포 추가**를 선택합니다.

3. **모델 배포** 대화 상자에서 **기존 배포 이름 재정의**를 선택한 다음, **production**을 선택합니다.

3. Select the <bpt id="p1">**</bpt>Clock<ept id="p1">**</ept> model and then click <bpt id="p2">**</bpt>Submit<ept id="p2">**</ept> to deploy it. This may take some time.

4. 모델이 배포되면 **모델 테스트** 페이지에서 **Clock** 모델을 선택하고, **production** 배포를 선택한 후, 다음 텍스트로 테스트합니다.

    `what's the time in Edinburgh?`

5. 반환되는 결과를 검토합니다. 여기에서 **GetTime** 의도 및 텍스트 값이 “Edinburgh”인 **Location** 엔터티를 예측할 수 있어야 합니다.

6. 다음 발화를 테스트해 봅니다.

    `what time is it in Tokyo?`
    
    `what date is it on Friday?`

    `what's the date on Weds?`

    `what day was 01/01/2020?`

    `what day will Mar 7th 2030 be?`

## <a name="use-the-model-from-a-client-app"></a>클라이언트 앱에서 모델 사용

In a real project, you'd iteratively refine intents and entities, retrain, and retest until you are satisfied with the predictive performance. Then, when you've tested it and are satisfied with its predictive performance, you can use it in a client app by calling its REST interface. In this exercise, you'll use the <bpt id="p1">*</bpt>curl<ept id="p1">*</ept> utility to call the REST endpoint for your model.

1. 대화 언어 모델을 만들려면 지원되는 지역에서 **언어 서비스** 리소스가 필요합니다.

2. **예측 URL 가져오기** 대화 상자에서 예측 엔드포인트의 URL은 엔드포인트에 HTTP POST 요청을 제출하여 헤더에 언어 리소스의 키를 지정하고 요청 데이터에 쿼리와 언어를 포함하는 **curl** 명령으로 구성된 샘플 요청과 함께 표시됩니다.

3. 샘플 요청을 복사하고 원하는 텍스트 편집기(예: 메모장)에 붙여넣습니다.

4. 다음 자리 표시자를 바꿉니다.
    - **YOUR_QUERY_HERE**: *What's the time in Sydney*
    - **QUERY_LANGUAGE_HERE**: *EN*

    이 명령은 다음 코드와 유사해야 합니다.

    ```
    curl -X POST "https://some-name.cognitiveservices.azure.com/language/:analyze-conversations?projectName=Clock&deploymentName=production&api-version=2021-11-01-preview" -H "Ocp-Apim-Subscription-Key: 0ab1c23de4f56..."  -H "Apim-Request-Id: 9zy8x76wv5u43...." -H "Content-Type: application/json" -d "{\"verbose\":true,\"query\":\"What's the time in Sydney?\",\"language\":\"EN\"}"
    ```

5. 명령 프롬프트(Windows) 또는 bash 셸(Linux/Mac)을 엽니다.

6. 편집된 curl 명령을 복사하여 명령줄 인터페이스에 붙여넣고 실행합니다.

7. 결과 JSON을 확인합니다. 여기에는 다음과 같이 예측된 의도 및 엔터티가 포함되어야 합니다.

    ```
    {"query":"What's the time in Sydney?","prediction":{"topIntent":"GetTime","projectKind":"conversation","intents":[{"category":"GetTime","confidenceScore":0.9998859},{"category":"GetDate","confidenceScore":9.8372206E-05},{"category":"GetDay","confidenceScore":1.5763446E-05}],"entities":[{"category":"Location","text":"Sydney","offset":19,"length":6,"confidenceScore":1}]}}
    ```

8. 모델에서 반환한 JSON 응답을 검토하여 예측된 최고 채점 의도가 **GetTime**인지 확인합니다.

9. curl 명령에서 쿼리를 `What's today's date?`로 변경한 다음, 명령을 실행하고, 결과 JSON을 검토합니다.

10. 다음 쿼리를 시도합니다.

    `What day will Jan 1st 2050 be?`

    `What time is it in Glasgow?`

    `What date will next Monday be?`

## <a name="export-the-project"></a>프로젝트 내보내기

작성 당시에는 미국 서부 2 및 서유럽 지역만 지원됩니다.

1. In Language Studio, on the <bpt id="p1">**</bpt>Projects<ept id="p1">**</ept> page, select the <bpt id="p2">**</bpt>Clock<ept id="p2">**</ept> project. Don't click <bpt id="p1">**</bpt>Clock<ept id="p1">**</ept>, select the circle icon to select the Clock project.

2. **&#x2913; 내보내기** 단추를 클릭합니다.

3. 생성된 **Clock.json** 파일을 원하는 위치에 저장합니다.

4. 원하는 코드 편집기(예: Visual Studio Code)에서 다운로드한 파일을 열어 프로젝트의 JSON 정의를 검토합니다.

## <a name="more-information"></a>추가 정보

**언어** 서비스를 사용하여 대화 언어 이해 솔루션을 만드는 방법에 대한 자세한 내용은 [언어 서비스 설명서](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/overview)를 참조하세요.
