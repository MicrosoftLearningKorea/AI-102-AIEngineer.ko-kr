---
lab:
  title: Cognitive Services 모니터링
  module: Module 2 - Developing AI Apps with Cognitive Services
---

# <a name="monitor-cognitive-services"></a>Cognitive Services 모니터링

Azure Cognitive Services can be a critical part of an overall application infrastructure. It's important to be able to monitor activity and get alerted to issues that may need attention.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services*를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 체크박스를 선택하여 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. When the resource has been deployed, go to it and view its <bpt id="p1">**</bpt>Keys and Endpoint<ept id="p1">**</ept> page. Make a note of the endpoint URI - you will need it later.

## <a name="configure-an-alert"></a>경고 구성

Cognitive Services 리소스의 활동을 감지할 수 있도록 경고 규칙을 정의하는 것으로 모니터링을 시작해 보겠습니다.

1. Azure Portal에서 Cognitive Services 리소스로 이동하여 **경고** 페이지(**모니터링** 섹션에 있음)를 확인합니다.
2. **+ 새 경고 규칙**을 선택합니다.
3. **경고 규칙 만들기** 페이지의 **범위** 아래에 Cognitive Services 리소스가 나열되어 있는지 확인합니다.
4. **조건** 아래에서 **조건 추가**를 클릭하고 오른쪽에 표시되는 **신호 선택** 창을 확인합니다. 이 창에서 모니터링할 신호 유형을 선택할 수 있습니다.
5. **신호 유형** 목록에서 **활동 로그**를 선택하고 필터링된 목록에서 **키 나열**을 선택합니다.
6. 지난 6시간 동안의 활동을 검토합니다.
7. Select the <bpt id="p1">**</bpt>Actions<ept id="p1">**</ept> tab. Note that you can specify an <bpt id="p2">*</bpt>action group<ept id="p2">*</ept>. This enables you to configure automated actions when an alert is fired - for example, sending an email notification. We won't do that in this exercise; but it can be useful to do this in a production environment.
8. **세부 정보** 탭에서 **경고 규칙 이름**을 **키 목록 경고**로 설정합니다.
9. **검토 + 만들기**를 선택합니다. 
10. Azure Cognitive Services는 전체 애플리케이션 인프라에서 핵심적인 역할을 할 수 있습니다.
11. 그러므로 활동을 모니터링하여 확인이 필요할 수 있는 문제 관련 경고를 받을 수 있어야 합니다.

    ```
    az login
    ```

    A web browser tab will open and prompt you to sign into Azure. Do so, and then close the browser tab and return to Visual Studio Code.

    > <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: If you have multiple subscriptions, you'll need to ensure that you are working in the one that contains your cognitive services resource.  Use this command to determine your current subscription.
    >
    > ```
    > az account show
    > ```
    >
    > 구독을 변경해야 하는 경우 *&lt;subscriptionName&gt;* 을 올바른 구독 이름으로 변경하여 다음 명령을 실행합니다.
    >
    > ```
    > az account set --subscription <subscriptionName>
    > ```

10. 이제 다음 명령을 사용하여 Cognitive Services 키 목록을 가져올 수 있습니다. 명령에서 *&lt;resourceName&gt;* 은 Cognitive Services 리소스 이름으로, *&lt;resourceGroup&gt;* 은 해당 리소스를 만든 리소스 그룹 이름으로 바꾸세요.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

이 명령은 Cognitive Services 리소스의 키 목록을 반환합니다.

11. Switch back to the browser containing the Azure portal, and refresh your <bpt id="p1">**</bpt>Alerts page<ept id="p1">**</ept>. You should see a <bpt id="p1">**</bpt>Sev 4<ept id="p1">**</ept> alert listed in the table (if not, wait up to five minutes and refresh again).
12. 경고를 선택하여 세부 정보를 봅니다.

## <a name="visualize-a-metric"></a>메트릭 시각화

Cognitive Services에서는 경고를 정의할 수 있을 뿐 아니라 Cognitive Services 리소스의 메트릭을 확인하여 리소스 사용률을 모니터링할 수도 있습니다.

1. Azure Portal의 Cognitive Services 리소스 페이지에서 **메트릭**(**모니터링** 섹션에 있음)을 선택합니다.
2. If there is no existing chart, select <bpt id="p1">**</bpt>+ New chart<ept id="p1">**</ept>. Then in the <bpt id="p1">**</bpt>Metric<ept id="p1">**</ept> list, review the possible metrics you can visualize and select <bpt id="p2">**</bpt>Total Calls<ept id="p2">**</ept>.
3. In the <bpt id="p1">**</bpt>Aggregation<ept id="p1">**</ept> list, select <bpt id="p2">**</bpt>Count<ept id="p2">**</ept>.  This will enable you to monitor the total calls to you Cognitive Service resource; which is useful in determining how much the service is being used over a period of time.
4. To generate some requests to your cognitive service, you will use <bpt id="p1">**</bpt>curl<ept id="p1">**</ept> - a command line tool for HTTP requests. In Visual Studio Code, in the <bpt id="p1">**</bpt>03-monitor<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>rest-test.cmd<ept id="p2">**</ept> and edit the <bpt id="p3">**</bpt>curl<ept id="p3">**</ept> command it contains (shown below), replacing <bpt id="p4">*</bpt><ph id="ph1">&amp;lt;</ph>yourEndpoint<ph id="ph2">&amp;gt;</ph><ept id="p4">*</ept> and <bpt id="p5">*</bpt><ph id="ph3">&amp;lt;</ph>yourKey<ph id="ph4">&amp;gt;</ph><ept id="p5">*</ept> with your endpoint URI and <bpt id="p6">**</bpt>Key1<ept id="p6">**</ept> key to use the Text Analytics API in your cognitive services resource.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.1/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':           [{'id':1,'text':'hello'}]}"
    ```

5. 변경 내용을 저장하고 **03-monitor** 폴더의 통합 터미널에서 다음 명령을 실행합니다.

    ```
    rest-test
    ```

이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

6. **rest-test** 명령을 여러 번 다시 실행하여 호출 활동을 생성합니다( **^** 키를 사용하여 이전 명령을 차례로 다시 실행할 수 있음).
7. Return to the <bpt id="p1">**</bpt>Metrics<ept id="p1">**</ept> page in the Azure portal and refresh the <bpt id="p2">**</bpt>Total Calls<ept id="p2">**</ept> count chart. It may take a few minutes for the calls you made using <bpt id="p1">*</bpt>curl<ept id="p1">*</ept> to be reflected in the chart - keep refreshing the chart until it updates to include them.

## <a name="more-information"></a>추가 정보

One of the options for monitoring cognitive services is to use <bpt id="p1">*</bpt>diagnostic logging<ept id="p1">*</ept>. Once enabled, diagnostic logging captures rich information about your cognitive services resource usage, and can be a useful monitoring and debugging tool. It can take over an hour after setting up diagnostic logging to generate any information, which is why we haven't explored it in this exercise; but you can learn more about it in the <bpt id="p1">[</bpt>Cognitive Services documentation<ept id="p1">](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging)</ept>.
