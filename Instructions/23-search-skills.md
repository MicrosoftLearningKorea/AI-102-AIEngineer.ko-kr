---
lab:
  title: Azure Cognitive Search용 사용자 지정 기술 만들기
  module: Module 12 - Creating a Knowledge Mining Solution
---

# <a name="create-a-custom-skill-for-azure-cognitive-search"></a>Azure Cognitive Search용 사용자 지정 기술 만들기

Azure Cognitive Search uses an enrichment pipeline of cognitive skills to extract AI-generated fields from documents and include them in a search index. There's a comprehensive set of built-in skills that you can use, but if you have a specific requirement that isn't met by these skills, you can create a custom skill.

이 연습에서는 사용자 지정 기술을 만듭니다. 이 기술은 문서의 개별 단어 빈도를 표로 만들어 가장 많이 사용되는 상위 5개 단어 목록을 생성한 다음 가상의 여행사인 Margie's Travel용 검색 솔루션에 추가합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If you have previously completed the <bpt id="p2">**</bpt><bpt id="p3">[</bpt>Create an Azure Cognitive Search solution<ept id="p3">](22-azure-search.md)</ept><ept id="p2">**</ept> exercise, and still have these Azure resources in your subscription, you can skip this section and start at the <bpt id="p4">**</bpt>Create a search solution<ept id="p4">**</ept> section. Otherwise, follow the steps below to provision the required Azure resources.

1. 웹 브라우저에서 `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. 구독의 **리소스 그룹**을 확인합니다.
3. If you are using a restricted subscription in which a resource group has been provided for you, select the resource group to view its properties. Otherwise, create a new resource group with a name of your choice, and go to it when it has been created.
4. Azure Cognitive Search에서는 인식 기술 보강 파이프라인을 사용하여 문서에서 AI 생성 필드를 추출한 다음 검색 인덱스에 포함합니다.
5. 포괄적인 기본 제공 기술 세트를 사용할 수 있습니다. 하지만 기본 제공 기술로는 특정 요구 사항을 충족할 수 없는 경우에는 사용자 지정 기술을 만들 수 있습니다.
6. **23-custom-search-skill** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
7. 터미널 창에서 다음 명령을 입력하여 Azure 구독에 대해 인증된 연결을 설정합니다.

    ```
    az login --output none
    ```

8. When prompted, sign into your Azure subscription. Then return to Visual Studio Code and wait for the sign-in process to complete.
9. 다음 명령을 실행하여 Azure 위치를 나열합니다.

    ```
    az account list-locations -o table
    ```

10. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus*임).
11. In the <bpt id="p1">**</bpt>setup.cmd<ept id="p1">**</ept> script, modify the <bpt id="p2">**</bpt>subscription_id<ept id="p2">**</ept>, <bpt id="p3">**</bpt>resource_group<ept id="p3">**</ept>, and <bpt id="p4">**</bpt>location<ept id="p4">**</ept> variable declarations with the appropriate values for your subscription ID, resource group name, and location name. Then save your changes.
12. **23-custom-search-skill** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

    ```
    setup
    ```

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The Search CLI module is in preview, and may get stuck in the <bpt id="p2">*</bpt>- Running ..<ept id="p2">*</ept> process. If this happens for over 2 minutes, press CTRL+C to cancel the long-running operation, and then select <bpt id="p1">**</bpt>N<ept id="p1">**</ept> when asked if you want to terminate the script. It should then complete successfully.
    >
    > 스크립트 실행이 실패하면 올바른 변수 이름을 적용하여 스크립트를 저장했는지 확인하고 다시 시도합니다.

13. 스크립트가 완료되면 표시되는 출력을 검토하고 Azure 리소스에 대한 다음 정보를 확인합니다(나중에 해당 값이 필요함).
    - 스토리지 계정 이름
    - 스토리지 연결 문자열
    - Cognitive Services 계정
    - Cognitive Services 키
    - 검색 서비스 엔드포인트
    - 검색 서비스 관리자 키
    - 검색 서비스 쿼리 키

14. Azure Portal에서 리소스 그룹을 새로 고쳐 Azure Storage 계정, Azure Cognitive Services 리소스 및 Azure Cognitive Search 리소스가 포함되어 있는지 확인합니다.

## <a name="create-a-search-solution"></a>검색 솔루션 만들기

이제 필요한 Azure 리소스가 준비되었으므로 다음 구성 요소로 이루어진 검색 솔루션을 만들 수 있습니다.

- Azure Storage 컨테이너의 문서를 참조하는 **데이터 원본**
- 문서에서 AI 생성 필드를 추출하는 기술 보강 파이프라인을 정의하는 **기술 세트**
- 문서 레코드의 검색 가능 세트를 정의하는 **인덱스**
- 데이터 원본에서 문서를 추출하여 기술 세트를 적용한 다음 인덱스를 채우는 **인덱서**

이 연습에서는 Azure Cognitive Search REST 인터페이스를 사용하여 JSON 요청을 제출해 이러한 구성 요소를 만듭니다.

1. In Visual Studio Code, in the <bpt id="p1">**</bpt>23-custom-search-skill<ept id="p1">**</ept> folder, expand the <bpt id="p2">**</bpt>create-search<ept id="p2">**</ept> folder and select <bpt id="p3">**</bpt>data_source.json<ept id="p3">**</ept>. This file contains a JSON definition for a data source named <bpt id="p1">**</bpt>margies-custom-data<ept id="p1">**</ept>.
2. **YOUR_CONNECTION_STRING** 자리 표시자는 Azure Storage 계정의 연결 문자열로 바꿉니다. 이 연결 문자열은 다음과 같습니다.

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    Azure Portal의 스토리지 계정 **액세스 키** 페이지에서 연결 문자열을 확인할 수 있습니다.

3. 업데이트된 JSON 파일을 저장하고 닫습니다.
4. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>skillset.json<ept id="p2">**</ept>. This file contains a JSON definition for a skillset named <bpt id="p1">**</bpt>margies-custom-skillset<ept id="p1">**</ept>.
5. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 요소에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 Cognitive Services 리소스의 키 중 하나로 바꿉니다.

    Azure Portal의 Cognitive Services 리소스에 대한 **키 및 엔드포인트** 페이지에서 키를 확인할 수 있습니다.

6. 업데이트된 JSON 파일을 저장하고 닫습니다.
7. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>index.json<ept id="p2">**</ept>. This file contains a JSON definition for an index named <bpt id="p1">**</bpt>margies-custom-index<ept id="p1">**</ept>.
8. 인덱스의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
9. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>indexer.json<ept id="p2">**</ept>. This file contains a JSON definition for an indexer named <bpt id="p1">**</bpt>margies-custom-indexer<ept id="p1">**</ept>.
10. 인덱서의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
11. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>create-search.cmd<ept id="p2">**</ept>. This batch script uses the cURL utility to submit the JSON definitions to the REST interface for your Azure Cognitive Search resource.
12. **YOUR_SEARCH_URL** 및 **YOUR_ADMIN_KEY** 변수 자리 표시자를 각각 **Url**, 그리고 Azure Cognitive Search 리소스의 **관리 키** 중 하나로 바꿉니다.

    Azure Portal의 Azure Cognitive Search 리소스 **개요** 및 **키** 페이지에서 이러한 값을 확인할 수 있습니다.

13. 업데이트된 배치 파일을 저장합니다.
14. **create-search** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
15. **create-search** 폴더의 터미널 창에서 다음 명령을 입력하여 배치 스크립트를 실행합니다.

    ```
    create-search
    ```

16. 스크립트 실행이 완료되면 Azure Portal의 Azure Cognitive Search 리소스 페이지에서 **인덱서** 페이지를 선택하고 인덱싱 프로세스가 완료될 때까지 기다립니다.

    **새로 고침**을 선택하여 인덱싱 작업 진행률을 추적할 수 있습니다. 인덱싱이 완료되려면 1분 정도 걸릴 수 있습니다.

## <a name="search-the-index"></a>인덱스 검색

이제 인덱스가 생성되었으므로 해당 인덱스를 검색할 수 있습니다.

1. Azure Cognitive Search 리소스 블레이드 위쪽에서 **검색 탐색기**를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 다음 쿼리 문자열을 입력한 다음 **검색**을 선택합니다.

    ```
    search=London&$select=url,sentiment,keyphrases&$filter=metadata_author eq 'Reviewer' and sentiment eq 'positive'
    ```

    이 쿼리는 *London*이 언급되어 있고, 작성자가 *Reviewer*이며, 긍정적인 **sentiment** 레이블이 있는 모든 문서(즉, 런던을 언급한 긍정적인 내용의 리뷰)에 대한 **url**, **sentiment** 및 **keyphrases**를 검색합니다.

## <a name="create-an-azure-function-for-a-custom-skill"></a>사용자 지정 기술용 Azure 함수 만들기

검색 솔루션에는 여러 기본 제공 인식 기술이 포함되어 있습니다. 이러한 기술은 감정 점수, 이전 작업에서 확인했던 핵심 구 목록과 같은 문서의 정보를 사용하여 인덱스를 보강합니다.

You can enhance the index further by creating custom skills. For example, it might be useful to identify the words that are used most frequently in each document, but no built-in skill offers this functionality.

여기서는 단어 개수 기능을 사용자 지정 기술로 구현하기 위해 원하는 언어로 Azure 함수를 만듭니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In this exercise, you'll create a simple Node.JS function using the code editing capabilities in the Azure portal. In a production solution, you would typically use a development environment such as Visual Studio Code to create a function app in your preferred language (for example C#, Python, Node.JS, or Java) and publish it to Azure as part of a DevOps process.

1. Azure Portal의 **홈** 페이지에서 다음 설정을 사용하여 새 **함수 앱** 리소스를 만듭니다.
    - **구독**: *사용자의 구독*
    - **리소스 그룹**: Azure Cognitive Search 리소스와 동일한 리소스 그룹
    - **함수 앱 이름**: 고유한 이름
    - **게시**: 코드
    - **런타임 스택**: Node.js
    - **버전**: 14 LTS
    - **지역**: Azure Cognitive Search 리소스와 동일한 지역

2. 배포가 완료될 때까지 기다렸다가 배포된 함수 앱 리소스로 이동합니다.
3. 함수 앱에 대한 블레이드의 왼쪽 창에서 **함수** 탭을 선택합니다. 그러고 나서, 다음 설정을 사용하여 새 함수를 만듭니다.
    - **개발 환경 설정**”
        - **개발 환경**: 포털에서 개발
    - **템플릿 선택**”
        - **템플릿**: HTTP 트리거
    - **템플릿 세부 정보**:
        - **새 함수**: wordcount
        - **권한 부여 수준**: 함수
4. **참고**: **[Create an Azure Cognitive Search solution](22-azure-search.md)** 연습을 이전에 완료했으며 구독에 해당 연습의 Azure 리소스가 아직 남아 있으면 이 섹션을 건너뛰고 **검색 솔루션 만들기** 섹션부터 시작하면 됩니다.
5. 기본 함수 코드를 다음 코드로 바꿉니다.

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.body && req.body.values) {

        vals = req.body.values;

        // Array of stop words to be ignored
        var stopwords = ['', 'i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', 
        "youre", "youve", "youll", "youd", 'your', 'yours', 'yourself', 
        'yourselves', 'he', 'him', 'his', 'himself', 'she', "shes", 'her', 
        'hers', 'herself', 'it', "its", 'itself', 'they', 'them', 
        'their', 'theirs', 'themselves', 'what', 'which', 'who', 'whom', 
        'this', 'that', "thatll", 'these', 'those', 'am', 'is', 'are', 'was',
        'were', 'be', 'been', 'being', 'have', 'has', 'had', 'having', 'do', 
        'does', 'did', 'doing', 'a', 'an', 'the', 'and', 'but', 'if', 'or', 
        'because', 'as', 'until', 'while', 'of', 'at', 'by', 'for', 'with', 
        'about', 'against', 'between', 'into', 'through', 'during', 'before', 
        'after', 'above', 'below', 'to', 'from', 'up', 'down', 'in', 'out', 
        'on', 'off', 'over', 'under', 'again', 'further', 'then', 'once', 'here', 
        'there', 'when', 'where', 'why', 'how', 'all', 'any', 'both', 'each', 
        'few', 'more', 'most', 'other', 'some', 'such', 'no', 'nor', 'not', 
        'only', 'own', 'same', 'so', 'than', 'too', 'very', 'can', 'will',
        'just', "dont", 'should', "shouldve", 'now', "arent", "couldnt", 
        "didnt", "doesnt", "hadnt", "hasnt", "havent", "isnt", "mightnt", "mustnt",
        "neednt", "shant", "shouldnt", "wasnt", "werent", "wont", "wouldnt"];

        res = {"values":[]};

        for (rec in vals)
        {
            // Get the record ID and text for this input
            resVal = {recordId:vals[rec].recordId, data:{}};
            txt = vals[rec].data.text;

            // remove punctuation and numerals
            txt = txt.replace(/[^ A-Za-z_]/g,"").toLowerCase();

            // Get an array of words
            words = txt.split(" ")

            // count instances of non-stopwords
            wordCounts = {}
            for(var i = 0; i < words.length; ++i) {
                word = words[i];
                if (stopwords.includes(word) == false )
                {
                    if (wordCounts[word])
                    {
                        wordCounts[word] ++;
                    }
                    else
                    {
                        wordCounts[word] = 1;
                    }
                }
            }

            // Convert wordcounts to an array
            var topWords = [];
            for (var word in wordCounts) {
                topWords.push([word, wordCounts[word]]);
            }

            // Sort in descending order of count
            topWords.sort(function(a, b) {
                return b[1] - a[1];
            });

            // Get the first ten words from the first array dimension
            resVal.data.text = topWords.slice(0,9)
              .map(function(value,index) { return value[0]; });

            res.values[rec] = resVal;
        };

        context.res = {
            body: JSON.stringify(res),
            headers: {
            'Content-Type': 'application/json'
        }

        };
    }
    else {
        context.res = {
            status: 400,
            body: {"errors":[{"message": "Invalid input"}]},
            headers: {
            'Content-Type': 'application/json'
        }

        };
    }
};
```

6. 함수를 저장한 다음, **테스트/실행** 창을 엽니다.
7. **테스트/실행** 창에서 기존 **본문**을 다음 JSON으로 바꿉니다. 여기에는 Azure Cognitive Search 기술에 필요한 스키마가 반영되어 있으며, 하나 이상의 문서에 대한 데이터를 포함하는 레코드가 처리를 위해 제출됩니다.

    ```
    {
        "values": [
            {
                "recordId": "a1",
                "data":
                {
                "text":  "Tiger, tiger burning bright in the darkness of the night.",
                "language": "en"
                }
            },
            {
                "recordId": "a2",
                "data":
                {
                "text":  "The rain in spain stays mainly in the plains! That's where you'll find the rain!",
                "language": "en"
                }
            }
        ]
    }
    ```
    
8. 그렇지 않은 경우에는 아래 단계에 따라 필요한 Azure 리소스를 프로비전합니다.

    ```
    {
    "values": [
        {
        "recordId": "a1",
        "data": {
            "text": [
            "tiger",
            "burning",
            "bright",
            "darkness",
            "night"
            ]
        },
        "errors": null,
        "warnings": null
        },
        {
        "recordId": "a2",
        "data": {
            "text": [
            "rain",
            "spain",
            "stays",
            "mainly",
            "plains",
            "thats",
            "youll",
            "find"
            ]
        },
        "errors": null,
        "warnings": null
        }
    ]
    }
    ```

9. Close the <bpt id="p1">**</bpt>Test/Run<ept id="p1">**</ept> pane and in the <bpt id="p2">**</bpt>wordcount<ept id="p2">**</ept> function blade, click <bpt id="p3">**</bpt>Get function URL<ept id="p3">**</ept>. Then copy the URL for the default key to the clipboard. You'll need this in the next procedure.

## <a name="add-the-custom-skill-to-the-search-solution"></a>검색 솔루션에 사용자 지정 기술 추가

이제검색 솔루션 기술 세트에 함수를 사용자 지정 기술로 포함하고 이 함수가 생성하는 결과를 인덱스의 필드에 매핑해야 합니다. 

1. In Visual Studio Code, in the <bpt id="p1">**</bpt>23-custom-search-skill/update-search<ept id="p1">**</ept> folder, open the <bpt id="p2">**</bpt>update-skillset.json<ept id="p2">**</ept> file. This contains the JSON definition of a skillset.
2. Review the skillset definition. It includes the same skills as before, as well as a new <bpt id="p1">**</bpt>WebApiSkill<ept id="p1">**</ept> skill named <bpt id="p2">**</bpt>get-top-words<ept id="p2">**</ept>.
3. **get-top-words** 기술 정의를 편집하여 **uri** 값을 Azure 함수의 URL(이전 절차에서 클립보드에 복사한 URL)로 설정합니다. **YOUR-FUNCTION-APP-URL**을 해당 URL로 바꾸면 됩니다.
4. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 요소에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 Cognitive Services 리소스의 키 중 하나로 바꿉니다.

    Azure Portal의 Cognitive Services 리소스에 대한 **키 및 엔드포인트** 페이지에서 키를 확인할 수 있습니다.

5. 업데이트된 JSON 파일을 저장하고 닫습니다.
6. 제한된 구독을 사용 중이어서 리소스 그룹이 제공되어 있다면 해당 리소스 그룹을 선택하여 속성을 확인합니다.
7. 인덱스의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
8. 그렇지 않은 경우에는 원하는 이름으로 새 리소스 그룹을 만들고 그룹이 만들어지면 해당 그룹으로 이동합니다.
9. 인덱서의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
10. In the <bpt id="p1">**</bpt>update-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>update-search.cmd<ept id="p2">**</ept>. This batch script uses the cURL utility to submit the updated JSON definitions to the REST interface for your Azure Cognitive Search resource.
11. **YOUR_SEARCH_URL** 및 **YOUR_ADMIN_KEY** 변수 자리 표시자를 각각 **Url**, 그리고 Azure Cognitive Search 리소스의 **관리 키** 중 하나로 바꿉니다.

    Azure Portal의 Azure Cognitive Search 리소스 **개요** 및 **키** 페이지에서 이러한 값을 확인할 수 있습니다.

12. 업데이트된 배치 파일을 저장합니다.
13. **update-search** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
14. **update-search** 폴더의 터미널 창에서 다음 명령을 입력하여 배치 스크립트를 실행합니다.

    ```
    update-search
    ```

15. 스크립트 실행이 완료되면 Azure Portal의 Azure Cognitive Search 리소스 페이지에서 **인덱서** 페이지를 선택하고 인덱싱 프로세스가 완료될 때까지 기다립니다.

    **새로 고침**을 선택하여 인덱싱 작업 진행률을 추적할 수 있습니다. 인덱싱이 완료되려면 1분 정도 걸릴 수 있습니다.

## <a name="search-the-index"></a>인덱스 검색

이제 인덱스가 생성되었으므로 해당 인덱스를 검색할 수 있습니다.

1. Azure Cognitive Search 리소스 블레이드 위쪽에서 **검색 탐색기**를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 다음 쿼리 문자열을 입력한 다음 **검색**을 선택합니다.

    ```
    search=Las Vegas&$select=url,top_words
    ```

    이 쿼리는 *Las Vegas*를 언급하는 모든 문서의 **url** 및 **top_words** 필드를 검색합니다.

## <a name="more-information"></a>추가 정보

Azure Cognitive Search용 사용자 지정 기술을 만드는 방법에 대한 자세한 내용은 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/cognitive-search-custom-skill-interface)를 참조하세요.
