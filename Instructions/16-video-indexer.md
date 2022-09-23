---
lab:
  title: Video Analyzer를 사용하여 비디오 분석
  module: Module 8 - Getting Started with Computer Vision
---

# <a name="analyze-video-with-video-analyzer"></a>Video Analyzer를 사용하여 비디오 분석

A large proportion of the data created and consumed today is in the format of video. <bpt id="p1">**</bpt>Video Analyzer for Media<ept id="p1">**</ept> is an AI-powered service that you can use to index videos and extract insights from them.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: From June 21st 2022, capabilities of cognitive services that return personally identifiable information are restricted to customers who have been granted <bpt id="p2">[</bpt>limited access<ept id="p2">](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access)</ept>. Without getting limited access approval, recognizing people and celebrities with Video Analyzer for this lab is not available. For more details about the changes Microsoft has made, and why - see <bpt id="p1">[</bpt>Responsible AI investments and safeguards for facial recognition<ept id="p1">](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/)</ept>.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="upload-a-video-to-video-analyzer"></a>Video Analyzer에 비디오 업로드

먼저 Video Analyzer 포털에 로그인하여 비디오를 업로드해야 합니다.

> <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: If the Video Analyzer page is slow to load in the hosted lab environment, use your locally installed browser. You can switch back to the hosted VM for the later tasks.

1. 브라우저에서 Video Analyzer 포털 `https://www.videoindexer.ai`를 엽니다.
2. 오늘날 생성 및 사용되고 있는 데이터 중 대부분은 비디오 형식입니다.
3. AI 지원 서비스인 **Video Analyzer for Media**를 사용하면 비디오를 인덱싱하고 비디오에서 인사이트를 추출할 수 있습니다.
4. 파일이 업로드되면 Video Analyzer에서 파일을 자동으로 인덱싱하는 동안 잠시 기다립니다.

> **참고**: 이 연습에서는 이 비디오를 사용해 Video Analyzer 기능을 살펴봅니다. 이 비디오에는 AI 지원 애플리케이션을 책임감 있는 방식으로 개발하는 방법과 관련된 유용한 정보 및 지침이 포함되어 있으므로, 연습을 마친 후 비디오를 끝까지 시청해야 합니다. 

## <a name="review-video-insights"></a>비디오 인사이트 검토

인덱싱 프로세스에서는 비디오의 인사이트가 추출됩니다. 추출된 인사이트는 포털에서 확인할 수 있습니다.

1. In the Video Analyzer portal, when the video is indexed, select it to view it. You'll see the video player alongside a pane that shows insights extracted from the video.

![비디오 플레이어와 인사이트 창이 있는 Video Analyzer](./images/video-indexer-insights.png)

2. 비디오가 재생되면 **타임라인** 탭을 선택하여 비디오 오디오의 대본을 확인합니다.

![비디오 대본이 표시된 타임라인 창과 비디오 플레이어가 있는 Video Analyzer](./images/video-indexer-transcript.png)

3. 포털 오른쪽 위에서 **보기** 기호(&#128455; 모양)를 선택하고 인사이트 목록에서 **대본** 외에 **OCR** 및 **발표자**도 선택합니다.

![대본, OCR 및 발표자를 선택한 Video Analyzer 보기 메뉴](./images/video-indexer-view-menu.png)

4. 이제 **타임라인** 창에는 다음 항목이 포함되어 있습니다.
    - 오디오 내레이션 대본
    - 비디오에 표시되는 텍스트
    - **참고**: 2022년 6월 21일부터 개인 식별 정보를 반환하는 Cognitive Service의 기능은 [제한된 액세스 권한](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access)이 부여된 고객으로 제한됩니다.
5. 제한된 액세스 승인을 받지 않으면 이 랩에서 Video Analyzer로 사용자 및 유명인사를 인식하는 기능을 사용할 수 없습니다.
    - 비디오에 나오는 각 사람
    - 비디오에서 논의하는 토픽
    - 비디오에 나오는 물체의 레이블
    - 비디오에 나오는 사람, 브랜드 등의 명명된 엔터티
    - 주요 장면
6. **인사이트** 창을 표시한 상태로 **보기** 기호를 다시 선택하고 인사이트 목록에서 **키워드** 및 **감정**을 창에 추가합니다.

    Microsoft가 변경한 내용 및 그 이유에 대한 자세한 내용은 [얼굴 인식에 대한 책임 있는 AI 투자 및 보호 조치](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/)를 참조하세요.

## <a name="search-for-insights"></a>인사이트 검색

Video Analyzer를 사용하여 비디오에서 인사이트를 검색할 수 있습니다.

1. In the <bpt id="p1">**</bpt>Insights<ept id="p1">**</ept> pane, in the <bpt id="p2">**</bpt>Search<ept id="p2">**</ept> box, enter <bpt id="p3">*</bpt>Bee<ept id="p3">*</ept>. You may need to scroll down in the Insights pane to see results for all types of insight.
2. 일치 *레이블* 1개가 검색되며, 비디오에서 해당 레이블의 위치가 결과 아래에 표시됩니다.
3. Bee(벌)가 나오는 섹션 시작 부분을 선택하고 해당 시점부터 비디오를 시청합니다(벌은 아주 잠깐만 나오므로 비디오를 일시 중지하고 시작 부분을 정확하게 선택해야 할 수 있음).
4. **검색** 상자의 내용을 지워 비디오의 모든 인사이트를 표시합니다.

![Video Analyzer에 표시된 Bee의 검색 결과](./images/video-indexer-search.png)

## <a name="use-video-analyzer-widgets"></a>Video Analyzer 위젯 사용

The Video Analyzer portal is a useful interface to manage video indexing projects. However, there may be occasions when you want to make the video and its insights available to people who don't have access to your Video Analyzer account. Video Analyzer provides widgets that you can embed in a web page for this purpose.

1. In Visual Studio Code, in the <bpt id="p1">**</bpt>16-video-indexer<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>analyze-video.html<ept id="p2">**</ept>. This is a basic HTML page to which you will add the Video Analyzer <bpt id="p1">**</bpt>Player<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Insights<ept id="p2">**</ept> widgets. Note the reference to the <bpt id="p1">**</bpt>vb.widgets.mediator.js<ept id="p1">**</ept> script in the header - this script enables multiple Video Analyzer widgets on the page to interact with one another.
2. Video Analyzer 포털에서 **미디어 파일** 페이지로 돌아와 **책임있는 AI** 비디오를 엽니다.
3. 비디오 플레이어 아래에서 **&lt;/&gt; 포함**을 선택하여 위젯을 포함할 HTML iframe 코드를 표시합니다.
4. **퍼가기 및 공유** 대화 상자에서 **플레이어** 위젯을 선택하고 비디오 크기를 560 x 315로 설정한 다음 클립보드에 embed 태그를 복사합니다.
5. Visual Studio Code의 **analyze-video.html** 파일에서 **&lt;-- Player widget goes here -- &gt;** 주석 아래에 복사한 코드를 붙여넣습니다.
6. Back in the <bpt id="p1">**</bpt>Share and Embed<ept id="p1">**</ept> dialog box, select the <bpt id="p2">**</bpt>Insights<ept id="p2">**</ept> widget and then copy the embed code to the clipboard. Then close the <bpt id="p1">**</bpt>Share and Embed<ept id="p1">**</ept> dialog box, switch back to Visual Studio Code, and paste the copied code under the comment <bpt id="p2">**</bpt><ph id="ph1">&amp;lt;</ph>-- Insights widget goes here -- <ph id="ph2">&amp;gt;</ph><ept id="p2">**</ept>.
7. Save the file. Then in the <bpt id="p1">**</bpt>Explorer<ept id="p1">**</ept> pane, right-click <bpt id="p2">**</bpt>analyze-video.html<ept id="p2">**</ept> and select <bpt id="p3">**</bpt>Reveal in File Explorer<ept id="p3">**</ept>.
8. 파일 탐색기에서 **analyze-video.html**을 브라우저에서 열어 웹 페이지를 표시합니다.
9. 위젯을 사용해 봅니다. 예를 들어 **인사이트** 위젯을 사용해 인사이트를 검색한 다음 비디오에서 해당 인사이트가 나오는 위치로 이동합니다.

![웹 페이지의 Video Analyzer 위젯](./images/video-indexer-widgets.png)

## <a name="use-the-video-analyzer-rest-api"></a>Video Analyzer REST API 사용

Video Analyzer는 계정에서 비디오를 업로드하고 관리하는 데 사용할 수 있는 REST API를 제공합니다.

### <a name="get-your-api-details"></a>API 세부 정보 가져오기

Video Analyzer API를 사용하려면 요청 인증을 위한 몇 가지 정보가 필요합니다.

1. Video Analyzer 포털에서 메뉴(≡)를 확장하고 **계정 설정** 페이지를 선택합니다.
2. 나중에 필요하므로 이 페이지의 **계정 ID**를 적어 둡니다.
3. 새 브라우저 탭을 열고 Video Analyzer 개발자 포털 `https://api-portal.videoindexer.ai`로 이동하여 Video Analyzer 계정의 자격 증명을 사용하여 로그인합니다.
4. **프로필** 페이지에서 프로필과 연관된 **구독**을 봅니다.
5. On the page with your subscription(s), observe that you have been assigned two keys (primary and secondary) for each subscription. Then select <bpt id="p1">**</bpt>Show<ept id="p1">**</ept> for any of the keys to see it. You will need this key shortly.

### <a name="use-the-rest-api"></a>REST API 사용

Now that you have the account ID and an API key, you can use the REST API to work with videos in your account. In this procedure, you'll use a PowerShell script to make REST calls; but the same principles apply with HTTP utilities such as cURL or Postman, or any programming language capable of sending and receiving JSON over HTTP.

Video Analyzer REST API와의 모든 상호 작용에서는 동일한 패턴이 사용됩니다.

- 헤더에서 API 키가 포함된 **AccessToken** 메서드로의 초기 요청을 사용하여 액세스 토큰을 가져옵니다.
- 후속 요청에서는 비디오 작업을 위해 REST 메서드를 호출할 때 이 액세스 토큰을 사용해 인증을 합니다.

1. Visual Studio Code의 **16-video-indexer** 폴더에서 **get-videos.ps1**을 엽니다.
2. PowerShell 스크립트에서 **YOUR_ACCOUNT_ID** 및 **YOUR_API_KEY** 자리 표시자를 앞에서 확인한 계정 ID 및 API 키 값으로 바꿉니다.
3. Observe that the <bpt id="p1">*</bpt>location<ept id="p1">*</ept> for a free account is "trial". If you have created an unrestricted Video Analyzer account (with an associated Azure resource), you can change this to the location where your Azure resource is provisioned (for example "eastus").
4. 스크립트의 코드를 검토합니다. 코드에서는 REST 메서드 2개가 호출됩니다. 하나는 액세스 토큰을 가져오는 메서드이고, 다른 하나는 계정에 비디오를 나열하는 메서드입니다.
5. 변경 내용을 저장하고 스크립트 창 오른쪽 위에서 **&#9655;** 단추를 사용하여 스크립트를 실행합니다.
6. REST 서비스의 JSON 응답을 확인합니다. 앞에서 인덱싱한 **책임있는 AI** 비디오의 세부 정보가 응답에 포함되어 있습니다.

## <a name="more-information"></a>추가 정보

Recognition of people and celebrities is still available, but following the <bpt id="p1">[</bpt>Responsible AI Standard<ept id="p1">](https://aka.ms/aah91ff)</ept> those are restricted behind a Limited Access policy. These features include facial identification and celebrity recognition. To learn more and apply for access, see the <bpt id="p1">[</bpt>Limited Access for Cognitive Services<ept id="p1">](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access)</ept>.

**Video Analyzer**에 대한 자세한 내용은 [Video Analyzer 설명서](https://docs.microsoft.com/azure/azure-video-analyzer/video-analyzer-for-media-docs/)를 참조하세요.
