---
lab:
  title: 질문 답변 솔루션 만들기
  module: Module 6 - Building a QnA Solution
---

# <a name="create-a-question-answering-solution"></a>질문 답변 솔루션 만들기

One of the most common conversational scenarios is providing support through a knowledge base of frequently asked questions (FAQs). Many organizations publish FAQs as documents or web pages, which works well for a small set of question and answer pairs, but large documents can be difficult and time-consuming to search.

**언어** 서비스는 자연어 입력을 사용하여 쿼리할 수 있는 질문과 대답 기술 자료를 만들 수 있는 질문 답변 기능을 포함하며, 사용자가 제출한 질문의 대답을 조회하기 위해 봇이 사용할 수 있는 리소스로 가장 많이 사용됩니다.

> **참고**: 언어 서비스의 질문 답변 기능은 별도의 서비스로 사용할 수 있는 새 버전의 QnA Maker 서비스입니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

If you have not already cloned <bpt id="p1">**</bpt>AI-102-AIEngineer<ept id="p1">**</ept> code repository to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the cloned folder in Visual Studio Code.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-a-language-resource"></a>Language 리소스 만들기

질문 답변에 대한 기술 자료를 만들고 호스팅하려면 Azure 구독에 **언어 서비스** 리소스가 필요합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고, 언어를 검색하고, **언어 서비스** 리소스를 만듭니다.
3. Click <bpt id="p1">**</bpt>Select<ept id="p1">**</ept> on the <bpt id="p2">**</bpt>Custom question answering<ept id="p2">**</ept> block. Then click <bpt id="p1">**</bpt>Continue to create your resource<ept id="p1">**</ept>. You will need to enter the following settings:
    
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 위치 선택
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S
    - **Azure Search 위치**\*: 언어 리소스와 같은 글로벌 지역에서 위치를 선택합니다.
    - **Azure Search 가격 책정 계층**: 무료(F)(이 계층을 사용할 수 없으면 기본(B) 선택)
    - **약관**: _동의_ 
    - **책임 있는 AI 알림**: _동의_
    
    \*맞춤형 질의응답은 Azure Search를 사용해 질문과 대답 기술 자료를 인덱싱 및 쿼리합니다.

4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

## <a name="create-a-question-answering-project"></a>질문 답변 프로젝트 만들기

가장 흔히 진행되는 대화 시나리오 중 하나는 FAQ(질문과 대답) 기술 자료를 통한 지원 제공입니다.

1. 새 브라우저 탭에서 `https://language.azure.com`의 *Language Studio* 포털로 이동하고 Azure 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
2. 언어 리소스를 선택하라는 메시지가 표시되면 다음 설정을 선택합니다.
    - **Azure Directory**: 구독이 포함된 Azure Directory입니다.
    - **Azure 구독**: Azure 구독입니다.
    - **언어 리소스**: 이전에 만든 언어 리소스입니다.
3. 언어 리소스를 선택하라는 메시지가 표시되지 않으면 구독에 여러 언어 리소스가 있기 때문일 수 있습니다. 이 경우 다음을 수행합니다.<u></u>
    1. 페이지의 위쪽에 있는 막대에서 **설정(&#9881;)** 단추를 클릭합니다.
    2. **설정** 페이지에서 **리소스** 탭을 봅니다.
    3. 방금 만든 언어 리소스를 선택하고 **리소스 전환**을 클릭합니다.
    4. 페이지 맨 위에서 **Language Studio**를 클릭하여 Language Studio 홈페이지로 돌아갑니다.
3. 포털의 위쪽에 있는 **새로 만들기** 메뉴에서 **사용자 지정 질문 답변**을 선택합니다.
4. 대다수 조직은 FAQ를 문서나 웹 페이지로 게시합니다. 질문과 대답 쌍의 수가 적은 경우에는 이러한 방식에 문제가 없지만, 문서가 크면 검색이 어려우며 시간이 많이 걸릴 수 있습니다.
5. **기본 정보 입력** 페이지에서 다음 세부 정보를 입력하고 **다음**을 클릭합니다.
    - **이름** LearnFAQ
    - **설명**: Microsoft Learn에 대한 FAQ
    - **답변이 반환되지 않는 경우의 기본 답변**: 죄송합니다. 질문을 이해하지 못했습니다.
6. 검토 및 완료 페이지에서 **만들기**를 클릭합니다.

## <a name="add-a-sources-to-the-knowledge-base"></a>기술 자료에 원본 추가

You can create a knowledge base from scratch, but it's common to start by importing questions and answers from an existing FAQ page or document. In this case, you'll import data from an existing FAQ web page for Microsoft learn, and you'll also import some pre-defined "chit chat" questions and answers to support common conversational exchanges.

1. On the <bpt id="p1">**</bpt>Manage sources<ept id="p1">**</ept> page for your question answering project, in the <bpt id="p2">**</bpt>&amp;#9547; Add source<ept id="p2">**</ept> list, select <bpt id="p3">**</bpt>URLs<ept id="p3">**</ept>. Then in the <bpt id="p1">**</bpt>Add URLs<ept id="p1">**</ept> dialog box, click <bpt id="p2">**</bpt>&amp;#9547; Add url<ept id="p2">**</ept> and add the following URL, and then click <bpt id="p3">**</bpt>Add all<ept id="p3">**</ept> to add it to the knowledge base:
    - **이름**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
2. On the <bpt id="p1">**</bpt>Manage sources<ept id="p1">**</ept> page for your question answering project, in the <bpt id="p2">**</bpt>&amp;#9547; Add source<ept id="p2">**</ept> list, select <bpt id="p3">**</bpt>Chitchat<ept id="p3">**</ept>. The in the <bpt id="p1">**</bpt>Add chit chat<ept id="p1">**</ept> dialog box, select <bpt id="p2">**</bpt>Friendly<ept id="p2">**</ept> and click <bpt id="p3">**</bpt>Add chit chat<ept id="p3">**</ept>.

## <a name="edit-the-knowledge-base"></a>지식 베이스 편집

Your knowledge base has been populated with question and answer pairs from the Microsoft Learn FAQ, supplemented with a set of conversational <bpt id="p1">*</bpt>chit-chat<ept id="p1">*</ept> question  and answer pairs. You can extend the knowledge base by adding additional question and answer pairs.

1. Language Studio의 **LearnFAQ** 프로젝트에서 **기술 자료 편집** 페이지를 선택하여 기존 질문 및 답변 쌍을 확인합니다(일부 팁이 표시되는 경우 팁을 읽고 **확인**을 클릭하여 해제하거나, **모두 건너뛰기** 클릭).
2. 기술 자료에서 **&#65291; 질문 쌍 추가**를 선택합니다.
3. **질문** 상자에 `What is Microsoft certification?`를 입력하고 **Enter****를 누릅니다.
4. **&#65291; 대체 구문 추가**를 선택하고, `How can I demonstrate my Microsoft technology skills?`를 입력하고, **Enter** 키를 누릅니다.
5. **대답** 상자에 `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.`를 입력합니다. 그런 다음 **Enter** 키를 눌러 질문(대체 구문 포함)과 답변을 기술 자료에 추가합니다.

    사용자가 대답을 확인한 후 추가 작업으로 멀티 턴 대화를 작성하면 효율적인 경우도 있습니다. 그러면 사용자가 질문을 여러 번 구체화하여 필요한 대답을 확인할 수 있습니다.

6. 인증 관련 질문에 입력한 대답 아래에서 **&#65291; 후속 프롬프트 추가**를 선택합니다.
7. **후속 프롬프트** 대화 상자에서 다음 설정을 입력한 다음 **프롬프트 추가**를 클릭합니다.
    - **사용자에게 프롬프트에 표시되는 텍스트**: `Learn more about certification`.
    - **새 쌍에 대한 링크 만들기**를 선택하고 다음 텍스트를 입력합니다. `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - 이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다.

## <a name="train-and-test-the-knowledge-base"></a>기술 자료 학습 및 테스트

이제 기술 자료가 있으므로, Language Studio에서 이것을 테스트할 수 있습니다.

1. 페이지의 오른쪽 위에서 **변경 내용 저장**을 클릭합니다.
2. 변경 내용을 저장한 후 **테스트**를 클릭하여 테스트 창을 엽니다.
3. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.
4. In the test pane, at the bottom enter the message <ph id="ph1">`What is Microsoft Learn?`</ph>. An appropriate response from the FAQ should be returned.
5. `Thanks!` 메시지를 입력합니다. 적절한 잡담 응답이 반환되어야 합니다.
6. Enter the message <ph id="ph1">`Tell me about certification`</ph>. The answer you created should be returned along with a follow-up prompt link.
7. Select the <bpt id="p1">**</bpt>Learn more about certification<ept id="p1">**</ept> follow-up link. The follow-up answer with a link to the certification page should be returned.
8. 기술 자료 테스트를 마쳤으면 테스트 창을 닫습니다.

## <a name="deploy-and-test-the-knowledge-base"></a>기술 자료 배포 및 테스트

The knowledge base provides a back-end service that client applications can use to answer questions. Now you are ready to publish your knowledge base and access its REST interface from a client.

1. Language Studio의 **LearnFAQ** 프로젝트에서 **기술 자료 배포** 페이지를 선택합니다.
2. At the top of the page, click <bpt id="p1">**</bpt>Deploy<ept id="p1">**</ept>. Then click <bpt id="p1">**</bpt>Deploy<ept id="p1">**</ept> to confirm you want to deploy the knowledge base.
3. 배포가 완료되면 **예측 URL 가져오기**를 클릭하여 기술 자료에 대한 REST 엔드포인트를 보고 클립보드에 복사합니다(대화 상자를 아직 닫지 않음).
4. In Visual Studio Code, in the <bpt id="p1">**</bpt>12-qna<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>ask-question.cmd<ept id="p2">**</ept>. This script uses <bpt id="p1">*</bpt>Curl<ept id="p1">*</ept> to call the REST interface of a question answering endpoint.
5. 스크립트에서 *YOUR_PREDICTION_ENDPOINT*를 복사한 예측 엔드포인트로 바꿉니다(따옴표로 묶여 있어야 함).
6. Return to the browser and in the <bpt id="p1">**</bpt>Get prediction URL<ept id="p1">**</ept> dialog box, note that the sample request includes a value for the <bpt id="p2">**</bpt>Ocp-Apim-Subscription-Key<ept id="p2">**</ept> parameter, which looks similar to <bpt id="p3">*</bpt>ab12c345de678fg9hijk01lmno2pqrs34<ept id="p3">*</ept>. This is the authorization key for your resource. Copy it to the clipboard, and then click <bpt id="p1">**</bpt>Got it<ept id="p1">**</ept> to close the dialog box.
7. Visual Studio Code로 돌아가서 **ask-question.cmd** 스크립트에서 *YOUR_KEY*를 복사한 키로 바꿉니다(따옴표로 묶어야 함).
8. 스크립트의 Curl 명령은 **학습 경로란?** 값으로 **question** 매개 변수를 제출합니다.
9. 전체 스크립트가 다음 코드와 유사한지 확인한 다음 파일을 저장합니다.

    ```
    @echo off
    SETLOCAL ENABLEDELAYEDEXPANSION

    rem Set variables
    set prediction_url="https://some-name.cognitiveservices.azure.com/language/......."
    set key="ab12c345de678fg9hijk01lmno2pqrs34"

    curl -X POST !prediction_url! -H "Ocp-Apim-Subscription-Key: !key!" -H "Content-Type: application/json" -d "{'question': 'What is a learning Path?' }"
    ```

10. Visual Studio Code의 탐색기 창에서 **ask-question.cmd** 스크립트를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
11. 터미널 창에서 `ask-question.cmd` 명령을 입력하여 스크립트를 실행하고 서비스에서 반환하는 JSON 응답을 확인합니다. 이 응답은 학습 경로란? 질문에 적절한 답을 포함해야 합니다.

## <a name="create-a-bot-for-the-knowledge-base"></a>기술 자료용 봇 만들기

기술 자료에서 대답을 검색하는 데 사용되는 클라이언트 애플리케이션으로는 봇이 가장 흔히 사용됩니다.

1. Return to Language Studio in the browser, and in the <bpt id="p1">**</bpt>Deploy knowledge base<ept id="p1">**</ept> page, select <bpt id="p2">**</bpt>Create Bot<ept id="p2">**</ept>. This opens the Azure portal in a new browser tab so you can create a Web App Bot in your Azure subscription (if prompted, sign in).
2. Azure Portal에서 다음 설정을 사용하여 Web App Bot을 만듭니다(대부분의 경우 이는 사용자를 위해 미리 입력됨).

    누락된 값이 있으면 브라우저를 새로 고치세요.  

  - **봇 핸들**: *봇의 고유한 이름*
  - **구독**: ‘Azure 구독’
  - **리소스 그룹**: 언어 리소스가 포함된 리소스 그룹
  - **위치**: *Text Analytics 서비스와 같은 위치*
  - **가격 책정 계층**: F0
  - **앱 이름**: *고유 ID와 *.azurewebsites.net*이 자동으로 추가된 **봇 핸들**과 동일
  - **SDK 언어**: *C# 또는 Node.js 중 하나 선택*
  - **QnA 인증 키**: *QnA 기술 자료에 대한 인증 키로 자동 설정되어야 함*
  - **App Service 계획/위치**: 적절한 계획과 위치가 있는 경우 자동 설정될 수 있습니다. 그렇지 않으면 새로 계획을 만듭니다.
  - **Application Insights**: 해제
  - **Microsoft 앱 ID 및 암호**: 앱 ID 및 암호를 자동으로 만듭니다.
3. Wait for your bot to be created . Then click <bpt id="p1">**</bpt>Go to resource<ept id="p1">**</ept> (or alternatively, on the home page, click <bpt id="p2">**</bpt>Resource groups<ept id="p2">**</ept>, open the resource group where you created the web app bot, and click it.)
4. In the blade for your bot, view the <bpt id="p1">**</bpt>Test in Web Chat<ept id="p1">**</ept> page, and wait until the bot displays the message <bpt id="p2">**</bpt>Hello and welcome!<ept id="p2">**</ept> (it may take a few seconds to initialize).
5. **사용자 지정 질문 답변** 블록에서 **선택**을 클릭합니다.

## <a name="more-information"></a>추가 정보

언어 서비스에서 질문 답변에 대해 자세히 알아보려면 [언어 서비스 설명서](https://docs.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview)를 참조하세요.
