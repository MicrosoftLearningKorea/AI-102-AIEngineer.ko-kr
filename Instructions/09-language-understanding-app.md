---
lab:
  title: Language Understanding 앱 만들기
  module: Module 5 - Creating Language Understanding Solutions
---

# <a name="create-a-language-understanding-app"></a>Language Understanding 앱 만들기

Language Understanding 서비스에서는 언어 모델을 캡슐화하는 앱을 정의할 수 있습니다. 애플리케이션은 이 모델을 통해 사용자의 자연어 입력을 해석하고, 사용자의 *의도*(사용자가 달성하려는 목표)를 예측하고, 해당 의도를 적용해야 하는 *엔터티*를 식별할 수 있습니다.

예를 들어 시계 애플리케이션용 Language Understanding 앱은 다음과 같은 입력을 처리할 수 있습니다.

*What is the time in London?*

이러한 입력 유형의 예가 발화(사용자가 말하거나 입력하는 내용)입니다. 이 발화에서 원하는 의도는 특정 위치(엔터티), 즉 여기서는 런던의 시간을 확인하는 것입니다.  

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The task of the language understanding app is to predict the user's intent, and identify any entities to which the intent applies. It is <bpt id="p1">&lt;u&gt;</bpt>not<ept id="p1">&lt;/u&gt;</ept> its job to actually perform the actions required to satisfy the intent. For example, the clock application can use a language app to discern that the user wants to know the time in London; but the client application itself must then implement the logic to determine the correct time and present it to the user.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

If you have not already cloned <bpt id="p1">**</bpt>AI-102-AIEngineer<ept id="p1">**</ept> code repository to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the cloned folder in Visual Studio Code.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-language-understanding-resources"></a>Language Understanding 리소스 만들기

Language Understanding 서비스를 사용하려면 다음과 같은 두 가지 종류의 리소스가 필요합니다.

- An <bpt id="p1">*</bpt>authoring<ept id="p1">*</ept> resource: used to define, train, and test the language understanding app. This must be a <bpt id="p1">**</bpt>Language Understanding - Authoring<ept id="p1">**</ept> resource in your Azure subscription.
- A <bpt id="p1">*</bpt>prediction<ept id="p1">*</ept> resource: used to publish your language understanding app and handle requests from client applications that use it. This can be either a <bpt id="p1">**</bpt>Language Understanding<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Cognitive Services<ept id="p2">**</ept> resource in your Azure subscription.

     > <bpt id="p1">**</bpt>Important<ept id="p1">**</ept>: Authoring resources must be created in one of three <bpt id="p2">*</bpt>regions<ept id="p2">*</ept> (Europe, Australia, or US). Language Understanding apps created in European or Australian authoring resources can only be deployed to prediction resources in Europe or Australia respectively; models created in US authoring resources can be deployed to prediction resources in any Azure location other than Europe and Australia. See the <bpt id="p1">[</bpt>authoring and publishing regions documentation<ept id="p1">](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)</ept> for details about matching authoring and prediction locations.

Language Understanding 작성 리소스와 예측 리소스가 아직 없으면 다음 작업을 수행합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *language understanding*을 검색한 후에 다음 설정을 사용하여 **Language Understanding** 리소스를 만듭니다.

    *Language Understanding(Azure Cognitive Services)* 이 <u>아닌</u> **Language Understanding**

    - **생성 옵션**: 둘 다
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **이름**: *고유 이름 입력*
    - **작성 위치**: *기본 위치 선택*
    - **가격 책정 계층 작성**: F0
    - **예측 위치**: 작성 위치와 같은 위치
    - **예측 가격 책정 계층**: F0
3. Wait for the resources to be created, and note that two Language Understanding resources are provisioned; one for authoring, and another for prediction. You can view both of these by navigating to the resource group where you created them. If you select <bpt id="p1">**</bpt>Go to resource<ept id="p1">**</ept>, it will open the <bpt id="p2">*</bpt>authoring<ept id="p2">*</ept> resource.

## <a name="create-a-language-understanding-app"></a>Language Understanding 앱 만들기

이제 작성 리소스를 만들었으므로 해당 리소스를 사용하여 Language Understanding 앱을 만들 수 있습니다.

1. 새 브라우저 탭에서 Language Understanding 포털 `https://www.luis.ai`를 엽니다.
2. Sign in using the Microsoft account associated with your Azure subscription. If this is the first time you have signed into the Language Understanding portal, you may need to grant the app some permissions to access your account details. Then complete the <bpt id="p1">*</bpt>Welcome<ept id="p1">*</ept> steps by selecting your Azure subscription and the authoring resource you just created.

    > **참고**: 계정이 각기 다른 디렉터리의 여러 구독과 연결되어 있으면 Language Understanding 리소스를 프로비전한 구독이 포함되어 있는 디렉터리로 전환해야 할 수 있습니다.

3. **참고**: Language Understanding 앱은 사용자 의도를 예측하여 해당 의도가 적용되는 엔터티를 식별하는 작업을 수행합니다.
    - **이름**: Clock
    - **Culture**: 영어(*이 옵션을 사용할 수 없으면 공백으로 둠*)
    - **설명**: 자연어 시계
    - **예측 리소스**: *Language Understanding 예측 리소스*

    **Clock** 앱이 자동으로 열리지 않으면 앱을 엽니다.
    
    효과적인 Language Understanding 앱을 만들기 위한 팁이 포함된 패널이 표시되면 닫습니다.

## <a name="create-intents"></a>의도 만들기

새 앱에서는 먼저 몇 가지 의도를 정의합니다.

1. **의도** 페이지에서 **65291; 만들기**를 클릭하여 새 의도 **GetTime**을 만듭니다.
2. **GetTime** 의도에서 예제 사용자 입력으로 다음 발화를 추가합니다.

    *what is the time?*

    *what time is it?*

3. 이러한 발화를 추가한 후 **의도** 페이지로 돌아와서 다음 발화가 포함된 새 의도 **GetDay**를 더 추가합니다.

    *what is the day today?*

    *what day is it?*

4. 이러한 발화를 추가한 후 **의도** 페이지로 돌아와서 다음 발화가 포함된 새 의도 **GetDate**를 더 추가합니다.

    *what is the date today?*

    *what date is it?*

5. 하지만 의도를 충족하는 데 필요한 작업을 실제로 수행하지는 <u>않습니다</u>.
6. **None** 의도에 다음 발화를 추가합니다.

    *hello*

    *goodbye*

## <a name="train-and-test-the-app"></a>앱 학습 및 테스트

이제 몇 가지 의도를 추가했으므로 앱을 학습시켜 사용자 입력에서 정확하게 예측을 할 수 있는지 확인해 보겠습니다.

1. 포털 오른쪽 위에서 **학습**을 선택하여 앱 학습을 진행합니다.
2. 앱 학습이 완료되면 **테스트를** 선택하여 테스트 패널을 표시한 후에 다음 테스트 발화를 입력합니다.

    *what's the time now?*

    반환되는 결과를 검토하여 예측한 의도(**GetTime**이어야 함), 그리고 모델이 예측한 의도를 대상으로 계산한 가능성을 나타내는 신뢰도 점수가 포함되어 있음을 확인합니다.

3. 다음 테스트 발화를 사용해 봅니다.

    *tell me the time*

    이번에도 예측한 의도 및 신뢰도 점수를 검토합니다.

4. 다음 테스트 발화를 사용해 봅니다.

    *what's today?*

    이 경우 모델은 **GetDay** 의도를 예측해야 합니다.

5. 마지막으로 다음 테스트 발화를 사용해 봅니다.

    *hi*

    이번에는 **None** 의도가 반환되어야 합니다.

6. 테스트 패널을 닫습니다.

## <a name="add-entities"></a>엔터티 추가

예를 들어 시계 애플리케이션은 언어 앱을 사용해 사용자가 런던의 시간을 확인하려 한다는 것을 파악할 수는 있습니다. 그러나 이처럼 의도가 파악되고 나면 클라이언트 애플리케이션 자체가 논리를 구현해 정확한 시간을 확인하여 사용자에게 표시해야 합니다.

### <a name="add-a-machine-learned-entity"></a>*기계 학습된* 엔터티 추가

가장 흔히 사용되는 엔터티 유형은 *기계 학습된* 엔터티입니다. 앱은 이 엔터티에서 예제를 기준으로 엔터티 값을 식별합니다.

1. **엔터티** 페이지에서 **&#65291; 만들기**를 클릭하여 새 엔터티를 만듭니다.
2. **엔터티 만들기** 대화 상자에서 **기계 학습된** 엔터티인 **Location**을 만듭니다.
3. **Location** 엔터티가 작성되면 **의도** 페이지로 돌아와서 **GetTime** 의도를 선택합니다.
4. 다음과 같은 새 예제 발화를 입력합니다.

    *what time is it in London?*

5. 이 발화를 추가한 후 단어 ***london***을 선택합니다. 그러면 표시되는 드롭다운 목록에서 **Location**을 선택하여 "london"이 위치의 예임을 지정합니다.
6. 다른 예제 발화를 추가합니다.

    *what is the current time in New York?*

7. 이 발화를 추가한 후 단어 ***new york***을 선택하여 **Location** 엔터티에 매핑합니다.

### <a name="add-a-list-entity"></a>*list* 엔터티 추가

엔터티의 유효한 값을 특정 단어 및 동의어 목록으로 제한할 수 있는 경우도 있습니다. 이렇게 하면 앱이 발화의 엔터티 인스턴스를 쉽게 식별할 수 있습니다.

1. **엔터티** 페이지에서 **&#65291; 만들기**를 클릭하여 새 엔터티를 만듭니다.
2. **엔터티 만들기** 대화 상자에서 **List** 엔터티인 **Weekday**를 만듭니다.
3. 다음의 **정규화된 값** 및 **동의어**를 추가합니다.

    | 정규화된 값 | 동의어|
    |-------------------|---------|
    | sunday | sun |
    | monday | mon |
    | tuesday | tue |
    | wednesday | wed |
    | thursday | thu |
    | friday | fri |
    | saturday | sat |

3. **Weekday** 엔터티가 작성되면 **의도** 페이지로 돌아와서 **GetDate** 의도를 선택합니다.
4. 다음과 같은 새 예제 발화를 입력합니다.

    *what date was it on Saturday?*

5. When the utterance has been added, verify that <bpt id="p1">**</bpt>saturday<ept id="p1">**</ept> has been automatically mapped to the <bpt id="p2">**</bpt>Weekday<ept id="p2">**</ept> entity. If not, select the word <bpt id="p1">***</bpt>saturday<ept id="p1">***</ept>, and in the drop-down list that appears, select <bpt id="p2">**</bpt>Weekday<ept id="p2">**</ept>.
6. 다른 예제 발화를 추가합니다.

    *what date will it be on Friday?*

7. 이 발화를 추가한 후 **friday**가 **Weekday** 엔터티에 매핑되는지 확인합니다.

### <a name="add-a-regex-entity"></a>*Regex* 엔터티 추가

Sometimes, entities have a specific format, such as a serial number, form code, or date. You can define a regular expression (<bpt id="p1">*</bpt>regex<ept id="p1">*</ept>) that describes an expected format to help your app identify matching entity values.

1. **엔터티** 페이지에서 **&#65291; 만들기**를 클릭하여 새 엔터티를 만듭니다.
2. **엔터티 만들기** 대화 상자에서 다음 정규식을 사용하여 **Regex** 엔터티인 **Date**를 만듭니다.

    ```
    [0-9]{2}/[0-9]{2}/[0-9]{4}
    ```

    > 이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다.

3. **Date** 엔터티가 작성되면 **의도** 페이지로 돌아와서 **GetDay** 의도를 선택합니다.
4. 다음과 같은 새 예제 발화를 입력합니다.

    *what day was 01/01/1901?*

5. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.
6. 다른 예제 발화를 추가합니다.

    *what day will it be on 12/12/2099?*

7. 이 발화를 추가한 후 **12/12/2099**가 **Date** 엔터티에 매핑되는지 확인합니다.

### <a name="retrain-the-app"></a>앱 다시 학습시키기

이제 언어 모델을 수정했으므로 앱을 다시 학습시켜 테스트를 다시 진행해야 합니다.

1. 포털 오른쪽 위에서 **학습**을 선택하여 앱을 다시 학습시킵니다.
2. 앱 학습이 완료되면 **테스트를** 선택하여 테스트 패널을 표시한 후에 다음 테스트 발화를 입력합니다.

    *what's the time in Edinburgh?*

3. Review the result that is returned, which should hopefully predict the <bpt id="p1">**</bpt>GetTime<ept id="p1">**</ept> intent. Then select <bpt id="p1">**</bpt>Inspect<ept id="p1">**</ept> and in the additional inspection panel that is displayed, examine the <bpt id="p2">**</bpt>ML entities<ept id="p2">**</ept> section. The model should have predicted that "edinburgh" is an instance of a <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> entity.
4. 다음 발화를 테스트해 봅니다.

    *what date is it on Friday?*

    *what's the date on Thu?*

    *what was the day on 01/01/2020?*

5. 테스트를 완료한 후 검사 패널을 닫되 테스트 패널은 열어 둡니다.

## <a name="perform-batch-testing"></a>일괄 처리 테스트 수행

테스트 창을 사용해 개별 발화를 대화형으로 테스트할 수 있습니다. 그러나 더욱 복잡한 언어 모델의 경우에는 대개 *일괄 처리 테스트*를 수행하는 것이 더 효율적입니다.

1. In Visual Studio Code, open the <bpt id="p1">**</bpt>batch-test.json<ept id="p1">**</ept> file in the <bpt id="p2">**</bpt>09-luis-app<ept id="p2">**</ept> folder. This file consists of a JSON document that contains multiple test cases for the clock language model you created.
2. In the Language Understanding portal, in the Test panel, select <bpt id="p1">**</bpt>Batch testing panel<ept id="p1">**</ept>. Then select <bpt id="p1">**</bpt>&amp;#65291; Import<ept id="p1">**</ept> and import the <bpt id="p2">**</bpt>batch-test.json<ept id="p2">**</ept> file, assigning the name <bpt id="p3">**</bpt>clock-test<ept id="p3">**</ept>.
3. 일괄 처리 테스트 패널에서 **clock-test** 테스트를 실행합니다.
4. 테스트가 완료되면 **결과 보기**를 선택합니다.
5. On the results page, view the confusion matrix that represents the prediction results. It shows true positive, false positive, true negative, and false negative predictions for the intent or entity that is selected in the list on the right.

    ![Language Understanding 일괄 처리 테스트 결과에 해당하는 오차 행렬](./images/luis-confusion-matrix.jpg)

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Each utterance is scored as <bpt id="p2">*</bpt>positive<ept id="p2">*</ept> or <bpt id="p3">*</bpt>negative<ept id="p3">*</ept> for each intent - so for example "what time is it?" should be scored as <bpt id="p1">*</bpt>positive<ept id="p1">*</ept> for the <bpt id="p2">**</bpt>GetTime<ept id="p2">**</ept> intent, and <bpt id="p3">*</bpt>negative<ept id="p3">*</ept> for the <bpt id="p4">**</bpt>GetDate<ept id="p4">**</ept> intent. The points on the confusion matrix show which utterances were predicted correctly (<bpt id="p1">*</bpt>true<ept id="p1">*</ept>) and incorrectly (<bpt id="p2">*</bpt>false<ept id="p2">*</ept>) as <bpt id="p3">*</bpt>positive<ept id="p3">*</ept> and <bpt id="p4">*</bpt>negative<ept id="p4">*</ept> for the selected intent.

6. With the <bpt id="p1">**</bpt>GetDate<ept id="p1">**</ept> intent selected, select any of the points on the confusion matrix to see the details of the prediction - including the utterance and the confidence score for the prediction. Then select the <bpt id="p1">**</bpt>GetDay<ept id="p1">**</ept>, <bpt id="p2">**</bpt>GetTime<ept id="p2">**</ept> and <bpt id="p3">**</bpt>None<ept id="p3">**</ept> intents and view their prediction results. The app should have done well at predicting the intents correctly.

    > **참고**: 사용자 인터페이스에서 이전에 선택한 점수가 지워지지 않을 수도 있습니다.

7. Select the <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> entity and view the prediction results in the confusion matrix. In particular, note the predictions that were <bpt id="p1">*</bpt>false negatives<ept id="p1">*</ept> - these were cases where the app failed to detect the specified location in the utterance, indicating that you may need to add more sample utterances to the intents and retrain the model.
8. 일괄 처리 테스트 패널을 닫습니다.

## <a name="publish-the-app"></a>앱 게시

In a real project, you'd iteratively refine intents and entities, retrain, and retest until you are satisfied with the predictive performance. Then, you can publish the app for client applications to use.

1. Language Understanding 포털의 오른쪽 위에서 **게시**를 선택합니다.
2. **프로덕션 슬롯**을 선택하고 앱을 게시합니다.
3. 게시가 완료되면 Language Understanding 포털 위쪽에서 **관리**를 선택합니다.
4. *작성* 리소스: Language Understanding 앱을 정의하고 학습시킨 후 테스트하는 데 사용됩니다.
5. Azure 구독에서 **Language Understanding - Authoring** 리소스여야 합니다.
6. In Visual Studio Code, in the <bpt id="p1">**</bpt>09-luis-app<ept id="p1">**</ept> folder, select the <bpt id="p2">**</bpt>GetIntent.cmd<ept id="p2">**</ept> batch file and view the code it contains. This command-line script uses cURL to call the Language Understanding REST API for the specified application and prediction endpoint.
7. 스크립트의 자리 표시자 값을 Language Understanding 앱의 **앱 ID**, **엔드포인트 URL** 및 **기본 키** 또는 **보조 키** 중 하나로 바꾸고 업데이트한 파일을 저장합니다.
8. *예측* 리소스: Language Understanding 앱을 게시하고 이 앱을 사용하는 클라이언트 애플리케이션을 요청을 처리하는 데 사용됩니다.

    ```
    GetIntent "What's the time?"
    ```

9. 앱이 반환하는 JSON 응답을 검토합니다. 입력한 명령에 대해 예측된 점수가 가장 높은 의도(**GetTime**이어야 함)가 표시됩니다.
10. 다음 명령을 시도합니다.

    ```
    GetIntent "What's today's date?"
    ```

11. 응답에서 **GetDate** 의도가 예측되었는지를 확인합니다.
12. 다음 명령을 시도합니다.

    ```
    GetIntent "What time is it in Sydney?"
    ```

13. 응답에 **Location** 엔터티가 포함되어 있는지 확인합니다.

14. 다음 명령을 실행하여 응답을 살펴봅니다.

    ```
    GetIntent "What time is it in Glasgow?"
    ```

    ```
    GetIntent "What's the time in Nairobi?"
    ```

    ```
    GetIntent "What's the UK time?"
    ```
15. 몇 가지 변형 명령을 더 실행해 봅니다. **GetTime** 의도는 올바르게 예측하지만 **Location** 엔터티는 검색하지 못하는 응답을 몇 개 생성해야 합니다.

    Azure 구독에서 **Language Understanding** 또는 **Cognitive Services** 리소스여야 합니다.

## <a name="apply-active-learning"></a>*활성 학습* 적용

You can improve a Language Understanding app based on historical utterances submitted to the endpoint. This practice is called <bpt id="p1">*</bpt>active learning<ept id="p1">*</ept>.

**중요**: 작성 리소스는 유럽, 오스트레일리아, 미국의 세 *지역* 중 하나에서 생성되어야 합니다.

1. 유럽 또는 오스트레일리아 작성 리소스에서 만드는 Language Understanding 앱은 각각 유럽 또는 오스트레일리아의 예측 리소스에만 배포할 수 있습니다. 미국 작성 리소스에서 만드는 모델은 유럽 및 오스트레일리아를 제외한 모든 Azure 위치의 예측 리소스에 배포할 수 있습니다.
2. 의도와 새 위치 엔터티(원래 학습 발화에는 포함되지 않았던 엔터티)가 정확하게 예측된 발화의 경우 **&#10003;** 을 선택하여 엔터티를 확인한 다음 **&#10514;** 아이콘을 사용하여 해당 발화를 학습 예제로 의도에 추가합니다.
3. 작성 위치와 예측 위치 일치에 대한 자세한 내용은 [작성 및 게시 지역 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)를 참조하세요.
4. **의도** 페이지로 이동하여 **GetTime** 의도를 열고 제안 발화가 추가되었음을 확인합니다.
5. Language Understanding 포털 위쪽에서 **학습**을 선택하여 앱을 다시 학습시킵니다.
6. Language Understanding 포털 오른쪽 위에서 **게시**를 선택하여 **프로덕션 슬롯**에 앱을 다시 게시합니다.
7. **09-luis-app** 폴더의 터미널로 돌아와서 **GetIntent** 명령을 사용해 활성 학습 중에 추가 및 수정한 발화를 제출합니다.
8. Verify that the result now includes the <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> entity. Then try another utterance that uses the same phrasing but specifies a different location (for example, <bpt id="p1">*</bpt>Berlin<ept id="p1">*</ept>).

## <a name="export-the-app"></a>앱 내보내기

You can use the Language Understanding portal to develop and test your language app, but in a software development process for DevOps, you should maintain a source controlled definition of the app that can be included in continuous integration and delivery (CI/CD) pipelines. While you <bpt id="p1">*</bpt>can<ept id="p1">*</ept> use the Language Understanding SDK or REST API in code scripts to create and train the app, a simpler way is to use the portal to create the app, and export it as a <bpt id="p2">*</bpt>.lu<ept id="p2">*</ept> file that can be imported and retrained in another Language Understanding instance. This approach enables you to make use of the productivity benefits of the portal while maintaining portability and reproducibility for the app.

1. Language Understanding 포털에서 **관리**를 선택합니다.
2. **버전** 페이지에서 앱의 현재 버전을 선택합니다(버전은 하나뿐임).
3. In the <bpt id="p1">**</bpt>Export<ept id="p1">**</ept> drop-down list, select <bpt id="p2">**</bpt>Export as LU<ept id="p2">**</ept>. Then, when prompted by your browser, save the file in the <bpt id="p1">**</bpt>09-luis-app<ept id="p1">**</ept> folder.
4. In Visual Studio Code, open the <bpt id="p1">**</bpt>.lu<ept id="p1">**</ept> file you just exported and downloaded (if you are prompted to search the marketplace for an extension that can read it, dismiss the prompt). Note that the LU format is human-readable, making it an effective way to document the definition of your Language Understanding app in a team development environment.

## <a name="more-information"></a>추가 정보

**Language Understanding** 서비스를 사용하는 방법에 대한 자세한 내용은 [Language Understanding 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/)를 참조하세요.
