---
lab:
  title: Azure Cognitive Search 솔루션 만들기
  module: Module 12 - Creating a Knowledge Mining Solution
---

# <a name="create-an-azure-cognitive-search-solution"></a>Azure Cognitive Search 솔루션 만들기

All organizations rely on information to make decisions, answer questions, and function efficiently. The problem for most organizations is not a lack of information, but the challenge of finding and  extracting the information from the massive set of documents, databases, and other sources in which the information is stored.

For example, suppose <bpt id="p1">*</bpt>Margie's Travel<ept id="p1">*</ept> is a travel agency that specializes in organizing trips to cities around the world. Over time, the company has amassed a huge amount of information in documents such as brochures, as well as reviews of hotels submitted by customers. This data is a valuable source of insights for travel agents and customers as they plan trips, but the sheer volume of data can make it difficult to find relevant information to answer a specific customer question.

Margie's Travel은 이 문제를 해결하기 위해 Azure Cognitive Search를 사용하여 솔루션을 구현할 수 있습니다. 이 솔루션에서는 문서를 더 쉽게 검색할 수 있도록 AI 기반 인식 기술을 사용하여 문서를 인덱싱하고 보강합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

If you have not already cloned <bpt id="p1">**</bpt>AI-102-AIEngineer<ept id="p1">**</ept> code repository to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the cloned folder in Visual Studio Code.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

Margie’s Travel을 위해 만들 솔루션에는 Azure 구독의 다음 리소스가 필요합니다.

- 인덱싱 및 쿼리를 관리하는 **Azure Cognitive Search** 리소스.
- **Cognitive Services** 리소스는 검색 솔루션이 AI 생성 인사이트로 데이터 소스의 데이터를 보강하는 데 사용할 수 있는 기술에 대한 AI 서비스를 제공합니다.
- 검색할 문서가 저장되는 Blob 컨테이너가 포함된 **스토리지 계정**

> **중요**: Azure Cognitive Search 및 Cognitive Services 리소스는 동일한 위치에 있어야 합니다.

### <a name="create-an-azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스 만들기

1. 웹 브라우저에서 `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *search*를 검색한 후에 다음 설정을 사용하여 **Azure Cognitive Search** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 새 리소스 그룹 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **서비스 이름**: 고유한 이름 입력
    - **위치**: 위치 선택(Azure Cognitive Search 및 Cognitive Services 리소스는 동일한 위치에 있어야 함)
    - **가격 책정 계층**: 기본

3. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
4. 모든 조직에서는 정보를 사용하여 의사 결정을 내리고, 질문에 답변하고, 효율적으로 업무를 수행합니다.

### <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

대부분의 조직에서 발생하는 문제는 정보 부족이 아니라 많은 문서, 데이터베이스 및 정보가 저장되는 기타 자료에서 정보를 찾고 추출하는 데 따르는 어려움입니다.

1. Azure Portal의 홈페이지로 돌아가서 **&#65291;리소스 만들기** 단추를 선택하고 *Cognitive Services*를 검색한 다음, 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Azure Cognitive Search 리소스와 동일한 리소스 그룹*
    - **지역**: *Azure Cognitive Search 리소스와 동일한 지역*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
2. 필요한 확인란을 선택하고 리소스를 만듭니다.
3. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

1. Azure Portal의 홈페이지로 돌아가서 **&#65291;리소스 만들기** 단추를 선택하고 *스토리지 계정*를 검색한 다음, 다음 설정을 사용하여 **스토리지 계정** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Azure Cognitive Search 리소스 및 Cognitive Services와 동일한 리소스 그룹
    - **스토리지 계정 이름**: 고유한 이름 입력
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **성능**: 표준
    - **복제**: LRS(로컬 중복 스토리지)
2. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
3. **개요** 페이지에서 **구독 ID**를 확인합니다. 이 ID를 사용하여 스토리지 계정이 프로비전된 구독을 식별합니다.
4. On the <bpt id="p1">**</bpt>Access keys<ept id="p1">**</ept> page, note that two keys have been generated for your storage account. Then select <bpt id="p1">**</bpt>Show keys<ept id="p1">**</ept> to view the keys.

    > **팁**: 다음 절차에서 구독 ID와 키 중 하나가 필요하므로 **스토리지 계정** 블레이드는 열어 두세요.

## <a name="upload-documents-to-azure-storage"></a>Azure Storage에 문서 업로드

이제 필요한 리소스가 준비되었으므로 Azure Storage 계정에 문서 몇 개를 업로드할 수 있습니다.

1. Visual Studio Code의 **탐색기** 창에서 **22-create-a-search-solution** 폴더를 확장하고 **UploadDocs.cmd**를 선택합니다.
2. 배치 파일을 편집하여 **YOUR_SUBSCRIPTION_ID**, **YOUR_AZURE_STORAGE_ACCOUNT_NAME** 및 **YOUR_AZURE_STORAGE_KEY** 자리 표시자를 각각 해당하는 구독 ID, Azure Storage 계정 이름, 그리고 앞에서 만든 스토리지 계정의 Azure Storage 계정 키 값으로 바꿉니다.
3. 변경 내용을 저장한 후 **22-create-a-search-solution** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
4. 다음 명령을 입력하여 Azure CLI를 통해 Azure 구독에 로그인합니다.

    ```
    az login
    ```

예를 들어 *Margie's Travel*은 전 세계 도시 여행 정보를 체계적으로 제공하는 데 특화된 여행사입니다.

5. 시간이 지남에 따라 회사는 고객이 제출한 호텔 리뷰뿐만 아니라 브로슈어 등의 문서로 된 상당한 양의 정보를 축적했습니다.

    ```
    UploadDocs
    ```

## <a name="index-the-documents"></a>문서 인덱싱

이제 문서를 업로드했으므로 해당 문서를 인덱싱하여 검색 솔루션을 만들 수 있습니다.

1. 이 데이터는 여행사와 고객이 여행을 계획할 때 유용한 소스가 되지만, 엄청난 양의 데이터로 인해 특정 고객의 질문에 답변할 때 관련 정보를 찾기가 어려워질 수 있습니다.
2. On the <bpt id="p1">**</bpt>Connect to your data<ept id="p1">**</ept> page, in the <bpt id="p2">**</bpt>Data Source<ept id="p2">**</ept> list, select <bpt id="p3">**</bpt>Azure Blob Storage<ept id="p3">**</ept>. Then complete the data store details with the following values:
    - **데이터 원본**: Azure Blob Storage
    - **데이터 원본 이름**: margies-data
    - **추출할 데이터**: 콘텐츠 및 메타데이터
    - **구문 분석 모드**: 기본값
    - **연결 문자열**: 기존 연결 **선택**을 선택합니다. 그런 다음 스토리지 계정을 선택하고 마지막으로 UploadDocs.cmp 스크립트를 실행하여 만든 **margies** 컨테이너를 선택합니다.
    - **관리 ID 인증**: 없음
    - **컨테이너 이름**: margies
    - **Blob 폴더**: *비워둡니다.*
    - **설명**: Margie's Travel 웹 사이트의 브로셔 및 리뷰
3. 다음 단계(인식 기술 추가)를 진행합니다.
4. **Cognitive Services 연결** 섹션에서 Cognitive Services 리소스를 선택합니다.
5. **보강 추가** 섹션에서 다음을 수행합니다.
    - **기술 세트 이름**을 **margies-skillset**로 변경합니다.
    - **OCR 활성화 및 모든 텍스트를 merged_content 필드로 병합** 옵션을 선택합니다.
    - **원본 데이터 필드**가 **merged_content** 설정되었는지 확인합니다.
    - **보강 세분성 수준**은 **원본 필드**로 유지합니다. 원본 필드는 인덱싱하는 문서의 전체 콘텐츠로 설정되어 있습니다. 페이지, 문장 등의 더 세부적인 수준에서 문서를 추출하도록 이 수준을 변경할 수도 있습니다.
    - 다음 보강 필드를 선택합니다.

        | 인지 기술 | 매개 변수 | 필드 이름 |
        | --------------- | ---------- | ---------- |
        | 위치 이름 추출 | | 위치 |
        | 핵심 구 추출 | | keyphrases |
        | 언어 검색 | | 언어 |
        | 이미지에서 태그 생성 | | imageTags |
        | 이미지에서 캡션 생성 | | imageCaption |

6. Double-check your selections (it can be difficult to change them later). Then proceed to the next step (<bpt id="p1">*</bpt>Customize target index<ept id="p1">*</ept>).
7. **인덱스 이름**을 **margies-index**로 변경합니다.
8. **키**가 **metadata_storage_path**로 설정되었는지 확인하고 **제안기 이름**을 공백으로 두고 **검색 모드**가 기본적으로 채워졌는지 확인합니다.
9. 인덱스 필드에서 다음 정보를 변경하고 나머지 필드는 모두 기본 설정으로 유지합니다(**중요**: 전체 표를 표시하려면 오른쪽으로 스크롤해야 할 수도 있음).

    | 필드 이름 | 조회 가능 | 필터 가능 | 정렬 가능 | 패싯 가능 | 검색 가능 |
    | ---------- | ----------- | ---------- | -------- | --------- | ---------- |
    | metadata_storage_size | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_last_modified | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_name | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | metadata_author | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | 위치 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | keyphrases | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | 언어 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | |

11. Double-check your selections, paying particular attention to ensure that the correct <bpt id="p1">**</bpt>Retrievable<ept id="p1">**</ept>, <bpt id="p2">**</bpt>Filterable<ept id="p2">**</ept>, <bpt id="p3">**</bpt>Sortable<ept id="p3">**</ept>, <bpt id="p4">**</bpt>Facetable<ept id="p4">**</ept>, and <bpt id="p5">**</bpt>Searchable<ept id="p5">**</ept> options are selected for each field  (it can be difficult to change them later). Then proceed to the next step (<bpt id="p1">*</bpt>Create an indexer<ept id="p1">*</ept>).
12. **인덱서 이름**을 **margies-indexer**로 변경합니다.
13. **일정**을 **한 번**으로 설정된 상태로 둡니다.
14. **고급** 옵션을 확장하고 **Base-64 인코딩 키** 옵션이 선택되어 있는지 확인합니다(일반적으로 인코딩 키를 사용하면 인덱스의 효율성이 향상됨).
15. 이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다.
    1. 데이터 원본에서 문서 메타데이터 필드 및 콘텐츠 추출
    2. 인식 기술의 기술 세트를 실행하여 추가 보강 필드 생성
    3. 추출된 필드를 인덱스에 매핑
16. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

## <a name="search-the-index"></a>인덱스 검색

이제 인덱스가 생성되었으므로 해당 인덱스를 검색할 수 있습니다.

1. Azure Cognitive Search 리소스의 **개요** 페이지 위쪽에서 **검색 탐색기**를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 `*`(별표 하나)를 입력한 다음 **검색**을 선택합니다.

    This query retrieves all documents in the index in JSON format. Examine the results and note the fields for each document, which contain document content, metadata, and enriched data extracted by the cognitive skills you selected.

3. `search=*&$count=true`에 대한 쿼리 문자열을 수정하고 검색을 제출합니다.

    이번에는 결과 맨 위에 **@odata.count** 필드가 포함되어 있습니다. 이 필드는 검색에서 반환된 문서 수를 나타냅니다.

4. 다음 쿼리 문자열을 사용해 봅니다.

    ```
    search=*&$count=true&$select=metadata_storage_name,metadata_author,locations
    ```

    This time the results include only the file name, author, and any locations mentioned in the document content. The file name and author are in the <bpt id="p1">**</bpt>metadata_storage_name<ept id="p1">**</ept> and <bpt id="p2">**</bpt>metadata_author<ept id="p2">**</ept> fields, which were extracted from the source document. The <bpt id="p1">**</bpt>locations<ept id="p1">**</ept> field was generated by a cognitive skill.

5. 이번에는 다음 쿼리 문자열을 사용해 봅니다.

    ```
    search="New York"&$count=true&$select=metadata_storage_name,keyphrases
    ```

    이 검색에서는 검색 가능한 필드에 "New York"이 언급된 문서를 찾은 다음 해당 문서의 파일 이름과 핵심 구를 반환합니다.

6. 쿼리 문자열을 하나 더 사용해 봅니다.

    ```
    search="New York"&$count=true&$select=metadata_storage_name&$filter=metadata_author eq 'Reviewer'
    ```

    이 쿼리는 *Reviewer*가 작성했으며 "New York"이 언급된 모든 문서의 파일 이름을 반환합니다.

## <a name="explore-and-modify-definitions-of-search-components"></a>검색 구성 요소의 정의 살펴보기 및 수정

검색 솔루션의 구성 요소는 JSON 정의를 기반으로 합니다. 이 정의는 Azure Portal에서 확인하고 편집할 수 있습니다.

Azure Portal을 사용하여 검색 솔루션을 만들고 수정할 수도 있지만, JSON에서 검색 개체를 정의한 다음 Azure Cognitive Service REST 인터페이스를 사용하여 검색 솔루션을 만들고 수정하는 방식이 더 효율적인 경우가 많습니다.

### <a name="get-the-endpoint-and-key-for-your-azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스용 엔드포인트 및 키 가져오기

1. Azure Portal에서 Azure Cognitive Search 리소스의 **개요** 페이지로 돌아온 후 페이지 맨 위 섹션에서 리소스의 **Url**( **https://resource_name.search.windows.net** 형식)을 찾아 클립보드에 복사합니다.
2. In Visual Studio Code, in the Explorer pane, expand the <bpt id="p1">**</bpt>22-create-a-search-solution<ept id="p1">**</ept> folder and its <bpt id="p2">**</bpt>modify-search<ept id="p2">**</ept> subfolder, and select <bpt id="p3">**</bpt>modify-search.cmd<ept id="p3">**</ept> to open it. You will use this script file to run <bpt id="p1">*</bpt>cURL<ept id="p1">*</ept> commands that submit JSON to the Azure Cognitive Service REST interface.
3. **modify-search.cmd**에서 **YOUR_SEARCH_URL** 자리 표시자를 클립보드에 복사한 URL로 바꿉니다.
4. Azure Portal에서 Azure Cognitive Search 리소스의 **키** 페이지를 표시한 다음 클립보드에 **기본 관리자 키**를 복사합니다.
5. Visual Studio Code에서 **YOUR_ADMIN_KEY** 자리 표시자를 클립보드에 복사한 키로 바꿉니다.
6. **modify-search.cmd**의 변경 내용을 저장합니다(파일을 아직 실행하지는 마세요).

### <a name="review-and-modify-the-skillset"></a>기술 세트 검토 및 수정

1. In Visual studio Code, in the <bpt id="p1">**</bpt>modify-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>skillset.json<ept id="p2">**</ept>. This shows a JSON definition for <bpt id="p1">**</bpt>margies-skillset<ept id="p1">**</ept>.
2. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 개체를 확인합니다. 이 개체는 Cognitive Services 리소스를 기술 세트에 연결하는 데 사용됩니다.
3. In the Azure portal, open your Cognitive Services resource (<bpt id="p1">&lt;u&gt;</bpt>not<ept id="p1">&lt;/u&gt;</ept> your Azure Cognitive Search resource!) and view its <bpt id="p2">**</bpt>Keys<ept id="p2">**</ept> page. Then copy <bpt id="p1">**</bpt>Key 1<ept id="p1">**</ept> to the clipboard.
4. Visual Studio Code의 **skillset.json**에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 클립보드에 복사한 Cognitive Services 키로 바꿉니다.
5. Scroll through the JSON file, noting that it includes definitions for the skills you created using the Azure Cognitive Search user interface in the Azure portal. At the bottom of the list of skills, an additional skill has been added with the following definition:

    ```
    {
        "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
        "defaultLanguageCode": "en",
        "name": "get-sentiment",
        "description": "New skill to evaluate sentiment",
        "context": "/document",
        "inputs": [
            {
                "name": "text",
                "source": "/document/merged_content"
            },
            {
                "name": "languageCode",
                "source": "/document/language"
            }
        ],
        "outputs": [
            {
                "name": "sentiment",
                "targetName": "sentimentLabel"
            }
        ]
    }
    ```

The new skill is named <bpt id="p1">**</bpt>get-sentiment<ept id="p1">**</ept>, and for each <bpt id="p2">**</bpt>document<ept id="p2">**</ept> level in a document, it, will evaluate the text found in the <bpt id="p3">**</bpt>merged_content<ept id="p3">**</ept> field of the document being indexed (which includes the source content as well as any text extracted from images in the content). It uses the extracted <bpt id="p1">**</bpt>language<ept id="p1">**</ept> of the document (with a default of English), and evaluates a label for the sentiment of the content. Values for the sentiment label can be "positive", "negative", "neutral", or "mixed". This label is then output as a new field named <bpt id="p1">**</bpt>sentimentLabel<ept id="p1">**</ept>.

6. **skillset.json**의 변경 내용을 저장합니다.

### <a name="review-and-modify-the-index"></a>인덱스 검토 및 수정

1. In Visual studio Code, in the <bpt id="p1">**</bpt>modify-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>index.json<ept id="p2">**</ept>. This shows a JSON definition for <bpt id="p1">**</bpt>margies-index<ept id="p1">**</ept>.
2. Scroll through the index and view the field definitions. Some fields are based on metadata and content in the source document, and others are the results of skills in the skillset.
3. Azure Portal에서 정의한 필드 목록 끝부분에 필드 2개가 더 추가되었습니다.

    ```
    {
        "name": "sentiment",
        "type": "Edm.String",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "sortable": true
    },
    {
        "name": "url",
        "type": "Edm.String",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "searchable": false,
        "sortable": false
    }
    ```

4. The <bpt id="p1">**</bpt>sentiment<ept id="p1">**</ept> field will be used to add the output from the <bpt id="p2">**</bpt>get-sentiment<ept id="p2">**</ept> skill that was added the skillset. The <bpt id="p1">**</bpt>url<ept id="p1">**</ept> field will be used to add the URL for each indexed document to the index, based on the <bpt id="p2">**</bpt>metadata_storage_path<ept id="p2">**</ept> value extracted from the data source. Note that index already includes the <bpt id="p1">**</bpt>metadata_storage_path<ept id="p1">**</ept> field, but it's used as the index key and Base-64 encoded, making it efficient as a key but requiring client applications to decode it if they want to use the actual URL value as a field. Adding a second field for the unencoded value resolves this problem.

### <a name="review-and-modify-the-indexer"></a>인덱서 검토 및 수정

1. In Visual studio Code, in the <bpt id="p1">**</bpt>modify-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>indexer.json<ept id="p2">**</ept>. This shows a JSON definition for <bpt id="p1">**</bpt>margies-indexer<ept id="p1">**</ept>, which maps fields extracted from document content and metadata (in the <bpt id="p2">**</bpt>fieldMappings<ept id="p2">**</ept> section), and values extracted by skills in the skillset (in the <bpt id="p3">**</bpt>outputFieldMappings<ept id="p3">**</ept> section), to fields in the index.
3. In the <bpt id="p1">**</bpt>fieldMappings<ept id="p1">**</ept> list, note the mapping for the <bpt id="p2">**</bpt>metadata_storage_path<ept id="p2">**</ept> value to the base-64 encoded key field. This was created when you assigned the <bpt id="p1">**</bpt>metadata_storage_path<ept id="p1">**</ept> as the key and selected the option to encode the key in the Azure portal. Additionally, a new mapping explicitly maps the same value to the <bpt id="p1">**</bpt>url<ept id="p1">**</ept> field, but without the Base-64 encoding:

    ```
    {
        "sourceFieldName" : "metadata_storage_path",
        "targetFieldName" : "url"
    }
    
    ```

원본 문서의 기타 모든 메타데이터 및 콘텐츠 필드는 인덱스에서 이름이 같은 필드에 암시적으로 매핑됩니다.

4. Review the <bpt id="p1">**</bpt>ouputFieldMappings<ept id="p1">**</ept> section, which maps outputs from the skills in the skillset to index fields. Most of these reflect the choices you made in the user interface, but the following mapping has been added to map the <bpt id="p1">**</bpt>sentimentLabel<ept id="p1">**</ept> value extracted by your sentiment skill to the <bpt id="p2">**</bpt>sentiment<ept id="p2">**</ept> field you added to the index:

    ```
    {
        "sourceFieldName": "/document/sentimentLabel",
        "targetFieldName": "sentiment"
    }
    ```

### <a name="use-the-rest-api-to-update-the-search-solution"></a>REST API를 사용하여 검색 솔루션 업데이트

1. **modify-search** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
2. **modify-search** 폴더의 터미널 창에서 다음 명령을 입력하여 **modify-search.cmd** 스크립트를 실행합니다. 이 스크립트는 REST 인터페이스에 JSON 정의를 제출하고 인덱싱을 시작합니다.

    ```
    modify-search
    ```

3. When the script has finished, return to the <bpt id="p1">**</bpt>Overview<ept id="p1">**</ept> page for your Azure Cognitive Search resource in the Azure portal and view the <bpt id="p2">**</bpt>Indexers<ept id="p2">**</ept> page. The periodically select <bpt id="p1">**</bpt>Refresh<ept id="p1">**</ept> to track the progress of the indexing operation. It may take a minute or so to complete.

    너무 커서 감정을 평가할 수 없는 일부 문서의 경우 경고가 표시될 수 있습니다. 감정 분석은 전체 문서가 아닌 페이지 또는 문장 수준에서 수행되는 경우가 많습니다. 하지만 이 사례에 해당하는 시나리오에서는 대다수 문서(특히 호텔 리뷰)가 짧으므로 유용한 문서 수준 감정 점수를 평가할 수 있습니다.

### <a name="query-the-modified-index"></a>수정된 인덱스 쿼리

1. Azure Cognitive Search 리소스 블레이드 위쪽에서 **검색 탐색기**를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 다음 쿼리 문자열을 입력한 다음 **검색**을 선택합니다.

    ```
    search=London&$select=url,sentiment,keyphrases&$filter=metadata_author eq 'Reviewer' and sentiment eq 'positive'
    ```

    이 쿼리는 *London*이 언급되어 있고, 작성자가 *Reviewer*이며, **sentiment** 레이블이 양수인 모든 문서(즉, 런던을 언급한 긍정적인 내용의 리뷰)의 **url**, **sentiment** 및 **keyphrases**를 검색합니다.

3. **검색 탐색기** 페이지를 닫고 **개요** 페이지로 돌아옵니다.

## <a name="create-a-search-client-application"></a>검색 클라이언트 애플리케이션 만들기

Now that you have a useful index, you can use it from a client application. You can do this by consuming the REST interface, submitting requests and receiving responses in JSON format over HTTP; or you can use the software development kit (SDK) for your preferred programming language. In this exercise, we'll use the SDK.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

### <a name="get-the-endpoint-and-keys-for-your-search-resource"></a>검색 리소스용 엔드포인트 및 키 가져오기

1. In the Azure portal, on the <bpt id="p1">**</bpt>Overview<ept id="p1">**</ept> page for your Azure Cognitive Search resource, note the <bpt id="p2">**</bpt>Url<ept id="p2">**</ept> value, which should be similar to <bpt id="p3">**</bpt>https://<bpt id="p4">*</bpt>your_resource_name<ept id="p4">*</ept>.search.windows.net<ept id="p3">**</ept>. This is the endpoint for your search resource.
2. On the <bpt id="p1">**</bpt>Keys<ept id="p1">**</ept> page, note that there are two <bpt id="p2">**</bpt>admin<ept id="p2">**</ept> keys, and a single <bpt id="p3">**</bpt>query<ept id="p3">**</ept> key. An <bpt id="p1">*</bpt>admin<ept id="p1">*</ept> key is used to create and manage search resources; a <bpt id="p2">*</bpt>query<ept id="p2">*</ept> key is used by client applications that only need to perform search queries.

    여기서는 클라이언트 애플리케이션 엔드포인트 및 쿼리 키가 필요합니다.

### <a name="prepare-to-use-the-azure-cognitive-search-sdk"></a>Azure Cognitive Search SDK 사용 준비

1. Visual Studio Code의 **탐색기** 창에서 **22-create-a-search-solution** 폴더로 이동한 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Right-click the <bpt id="p1">**</bpt>margies-travel<ept id="p1">**</ept> folder and open an integrated terminal. Then install the Azure Cognitive Search SDK package by running the appropriate command for your language preference:

    **C#**
    
    ```
    dotnet add package Azure.Search.Documents --version 11.1.1
    ```
    
    **Python**
    
    ```
    pip install azure-search-documents==11.0.0
    ```
    
3. **margies-travel** 폴더의 내용을 보고 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the <bpt id="p1">**</bpt>endpoint<ept id="p1">**</ept> and <bpt id="p2">**</bpt>query key<ept id="p2">**</ept> for your Azure Cognitive Search resource. Save your changes.

### <a name="explore-code-to-search-an-index"></a>인덱스를 검색하는 코드 살펴보기

**margies-travel** 폴더에는 검색 기능이 포함된 웹 애플리케이션(Microsoft C# *ASP.NET Razor* 웹 애플리케이션 또는 Python *Flask* 애플리케이션)용 코드 파일이 들어 있습니다.

1. 선택한 프로그래밍 언어에 따라 웹 애플리케이션에서 다음 코드 파일을 엽니다.
    - **C#** :Pages/Index.cshtml.cs
    - **Python**: app.py
2. 코드 파일 맨 위에서 **검색 네임스페이스 가져오기** 주석을 찾은 다음 Azure Cognitive Search SDK 사용을 위해 가져온 네임스페이스를 확인합니다.
3. **search_query** 함수에서 **검색 클라이언트 만들기** 주석을 찾은 다음 코드가 Azure Cognitive Search 리소스용 엔드포인트와 쿼리 키를 사용하여 **SearchClient** 개체를 만든다는 것을 확인합니다.
4. **search_query** 함수에서 **검색 쿼리 제출** 주석을 찾은 다음 코드가 다음 옵션을 사용하여 지정된 텍스트의 검색을 제출하는 방식을 검토합니다.
    - 검색 텍스트의 개별 단어를 **모두** 찾아야 하는 *검색 모드*
    - 검색에서 확인되는 총 문서 수를 결과에 포함
    - 제공된 필터 식과 일치하는 문서만 포함되도록 결과 필터링
    - 지정한 정렬 순서로 결과 정렬
    - **metadata_author** 필드의 각 고유 값을 *패싯*으로 반환. 이 패싯을 사용하여 필터링을 위해 미리 정의된 값을 표시할 수 있습니다.
    - **merged_content** 및 **imageCaption** 필드에서 추출하여 검색 용어를 강조 표시한 항목을 최대 3개까지 결과에 포함
    - 지정된 필드만 결과에 포함

### <a name="explore-code-to-render-search-results"></a>검색 결과를 렌더링하는 코드 살펴보기

웹앱에는 검색 결과를 처리 및 렌더링하는 코드가 이미 포함되어 있습니다.

1. 선택한 프로그래밍 언어에 따라 웹 애플리케이션에서 다음 코드 파일을 엽니다.
    - **C#** :Pages/Index.cshtml
    - **Python**: templates/search.html
2. Examine the code, which renders the page on which the search results are displayed. Observe that:
    - 페이지의 시작 부분에는 검색 양식이 표시됩니다. 사용자는 이 검색 양식을 통해 새 검색을 제출할 수 있습니다(Python 버전 애플리케이션에서는 **base.html** 템플릿에 이 양식이 정의되어 있음). 그러면 페이지 시작 부분에서 새로 제출된 검색을 참조합니다.
    - Azure Portal에서 Azure Cognitive Search 리소스 블레이드의 **개요** 페이지를 검토합니다.
        - 검색 결과에서 문서 개수를 검색하여 표시합니다.
        - **metadata_author** 필드의 패싯 값을 검색하여 필터링용 옵션 목록으로 표시합니다.
        - 결과에 사용 가능한 정렬 옵션의 드롭다운 목록을 만듭니다.
    - 그런 다음 코드는 검색 결과에서 반복 실행되어 각 결과를 다음과 같이 렌더링합니다.
        - **metadata_storage_name**(파일 이름) 필드를 **url** 필드의 주소로 이동할 수 있는 링크로 표시합니다.
        - **merged_content** 및 **imageCaption** 필드에서 확인된 검색 용어를 *강조 표시*하면 관련 컨텍스에서 검색 용어를 표시할 수 있습니다.
        - **metadata_author**, **metadata_storage_size**, **metadata_storage_last_modified** 및 **language** 필드를 표시합니다.
        - 이 페이지에서 시각적 인터페이스를 사용하여 데이터 원본, 인덱스, 인덱서, 기술 세트를 비롯한 검색 솔루션의 다양한 구성 요소 만들기, 테스트, 관리, 모니터링을 수행할 수 있습니다.
        - 처음 5개 **keyphrases**(있는 경우)를 표시합니다.
        - 처음 5개 **locations**(있는 경우)를 표시합니다.
        - 처음 5개 **imageTags**(있는 경우)를 표시합니다.

### <a name="run-the-web-app"></a>웹앱 실행

 1. **margies-travel** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    flask run
    ```

2. 앱이 정상적으로 시작되면 표시되는 메시지에서 실행 중인 웹 애플리케이션의 링크( *http://localhost:5000/* 또는 *http://127.0.0.1:5000/* )를 따라 이동하여 웹 브라우저에서 Margie's Travel 사이트를 엽니다.
3. Margie's Travel 웹 사이트에서 검색 상자에 **London hotel**을 입력하고 **검색**을 클릭합니다.
4. Review the search results. They include the file name (with a hyperlink to the file URL), an extract of the file content with the search terms (<bpt id="p1">*</bpt>London<ept id="p1">*</ept> and <bpt id="p2">*</bpt>hotel<ept id="p2">*</ept>) emphasized, and other attributes of the file from the index fields.
5. Observe that the results page includes some user interface elements that enable you to refine the results. These include:
    - 구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.
    - 검색 솔루션은 이를 사용하여 AI에서 생성된 인사이트를 사용하여 데이터 저장소의 데이터를 보강합니다.
6. **검토자** 필터와 **긍정적인 내용부터** 정렬 옵션을 선택하고 **결과 구체화**를 선택합니다.
7. 결과가 리뷰만 포함하도록 필터링되고 감정 레이블에 따라 정렬되는지 확인합니다.
8. **검색** 상자에 **quiet hotel in New York**을 새 검색으로 입력하고 결과를 검토합니다.
9. 다음 검색 용어를 사용해 봅니다.
    - **Tower of London**(일부 문서에서 이 용어는 *핵심 구*로 식별됨)
    - **skyscraper**(이 단어는 어떤 문서의 실제 콘텐츠에도 표시되지는 않지만 일부 문서의 이미지용으로 생성된 *이미지 캡션* 및 *이미지 태그*에서 확인됨)
    - **Mojave desert**(일부 문서에서 이 용어는 *위치*로 식별됨)
10. Close the browser tab containing the Margie's Travel web site and return to Visual Studio Code. Then in the Python terminal for the <bpt id="p1">**</bpt>margies-travel<ept id="p1">**</ept> folder (where the dotnet or flask application is running), enter Ctrl+C to stop the app.

## <a name="more-information"></a>추가 정보

Azure Cognitive Search에 대해 자세히 알아보려면 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/search-what-is-azure-search)를 참조하세요.
