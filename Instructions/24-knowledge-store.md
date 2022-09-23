---
lab:
  title: Azure Cognitive Search를 사용하여 지식 저장소 만들기
  module: Module 12 - Creating a Knowledge Mining Solution
---

# <a name="create-a-knowledge-store-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용하여 지식 저장소 만들기

Azure Cognitive Search uses an enrichment pipeline of cognitive skills to extract AI-generated fields from documents and include them in a search index. While the index might be considered the primary output from an indexing process, the enriched data it contains might also be useful in other ways. For example:

- 인덱스는 기본적으로 인덱싱된 레코드를 나타내는 JSON 개체의 컬렉션이므로 데이터 오케스트레이션 프로세스에 통합하는 경우 Azure Data Factory와 같은 도구를 사용하여 개체를 JSON 파일로 내보내는 것이 유용할 수 있습니다.
- 분석 및 보고를 위해 Microsoft Power BI와 같은 도구를 사용하여 인덱스 레코드를 테이블의 관계형 스키마로 정규화할 수도 있습니다.
- 인덱싱 프로세스 중에 문서에서 포함된 이미지를 추출하면 해당 이미지를 파일로 저장할 수 있습니다.

이 연습에서는 브로셔 및 호텔 리뷰의 정보를 사용해 고객의 여행 계획을 지원하는 가상의 여행사인 *Margie's Travel*용으로 지식 저장소를 구현합니다.

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
5. 인덱스를 인덱싱 프로세스의 기본 출력으로 고려할 수 있지만 인덱스에 포함된 보강 데이터는 다른 방식으로 유용할 수도 있습니다.
6. **24-knowledge-store** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
7. 터미널 창에서 다음 명령을 입력하여 Azure 구독에 대해 인증된 연결을 설정합니다.

    ```
    az login --output none
    ```

8. 예를 들면 다음과 같습니다.
9. 다음 명령을 실행하여 Azure 위치를 나열합니다.

    ```
    az account list-locations -o table
    ```

10. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus*임).
11. In the <bpt id="p1">**</bpt>setup.cmd<ept id="p1">**</ept> script, modify the <bpt id="p2">**</bpt>subscription_id<ept id="p2">**</ept>, <bpt id="p3">**</bpt>resource_group<ept id="p3">**</ept>, and <bpt id="p4">**</bpt>location<ept id="p4">**</ept> variable declarations with the appropriate values for your subscription ID, resource group name, and location name. Then save your changes.
12. **24-knowledge-store** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

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
- A <bpt id="p1">**</bpt>skillset<ept id="p1">**</ept> that defines an enrichment pipeline of skills to extract AI-generated fields from the documents. The skillset also defines the <bpt id="p1">*</bpt>projections<ept id="p1">*</ept> that will be generated in your <bpt id="p2">*</bpt>knowledge store<ept id="p2">*</ept>.
- 문서 레코드의 검색 가능 세트를 정의하는 **인덱스**
- An <bpt id="p1">**</bpt>indexer<ept id="p1">**</ept> that extracts the documents from the data source, applies the skillset, and populates the index. The process of indexing also persists the projections defined in the skillset in the knowledge store.

이 연습에서는 Azure Cognitive Search REST 인터페이스를 사용하여 JSON 요청을 제출해 이러한 구성 요소를 만듭니다.

### <a name="prepare-json-for-rest-operations"></a>REST 작업용 JSON 준비

이 연습에서는 REST 인터페이스를 사용해 Azure Cognitive Search 구성 요소의 JSON 정의를 제출합니다.

1. In Visual Studio Code, in the <bpt id="p1">**</bpt>24-knowledge-store<ept id="p1">**</ept> folder, expand the <bpt id="p2">**</bpt>create-search<ept id="p2">**</ept> folder and select <bpt id="p3">**</bpt>data_source.json<ept id="p3">**</ept>. This file contains a JSON definition for a data source named <bpt id="p1">**</bpt>margies-knowledge-data<ept id="p1">**</ept>.
2. **YOUR_CONNECTION_STRING** 자리 표시자는 Azure Storage 계정의 연결 문자열로 바꿉니다. 이 연결 문자열은 다음과 같습니다.

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    Azure Portal의 스토리지 계정 **액세스 키** 페이지에서 연결 문자열을 확인할 수 있습니다.

3. 업데이트된 JSON 파일을 저장하고 닫습니다.
4. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>skillset.json<ept id="p2">**</ept>. This file contains a JSON definition for a skillset named <bpt id="p1">**</bpt>margies-knowledge-skillset<ept id="p1">**</ept>.
5. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 요소에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 Cognitive Services 리소스의 키 중 하나로 바꿉니다.

    Azure Portal의 Cognitive Services 리소스 **키 및 엔드포인트** 페이지에서 키를 확인할 수 있습니다.

6. At the end of the collection of skills in your skillset, find the <bpt id="p1">**</bpt>Microsoft.Skills.Util.ShaperSkill<ept id="p1">**</ept> skill named <bpt id="p2">**</bpt>define-projection<ept id="p2">**</ept>. This skill defines a JSON structure for the enriched data that will be used for the projections that the pipeline will persist on the knowledge store for each document processed by the indexer.
7. At the bottom of the skillset file, observe that the skillset also includes a <bpt id="p1">**</bpt>knowledgeStore<ept id="p1">**</ept> definition, which includes a connection string for the Azure Storage account where the knowledge store is to be created, and a collection of <bpt id="p2">**</bpt>projections<ept id="p2">**</ept>. This skillset includes three <bpt id="p1">*</bpt>projection groups<ept id="p1">*</ept>:
    - 기술 세트에 있는 쉐이퍼 기술에 대한 **knowledge_projection** 출력을 기반으로 한 *개체* 프로젝션을 포함하는 그룹.
    - 문서에서 추출된 이미지 데이터의 **normalized_images** 컬렉션을 기반으로 한 *파일* 프로젝션을 포함하는 그룹.
    - 다음 *테이블* 프로젝션을 포함하는 그룹.
        - **KeyPhrases**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/key_phrases/** 컬렉션 출력에 매핑된 **keyPhrase** 열을 포함합니다.
        - **Locations**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/key_phrases/** 컬렉션 출력에 매핑되는 **location** 열을 포함합니다.
        - **ImageTags**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/image_tags/** 컬렉션 출력에 매핑되는 **tag** 열을 포함합니다.
        - **Docs**: 자동으로 생성된 키 열, 그리고 쉐이퍼 기술에서 테이블에 사전 할당되지 않은 모든 **knowledge_projection** 출력값을 포함합니다.
8. **storageConnectionString** 값의 **YOUR_CONNECTION_STRING** 자리 표시자를 스토리지 계정의 연결 문자열로 바꿉니다.
9. 업데이트된 JSON 파일을 저장하고 닫습니다.
10. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>index.json<ept id="p2">**</ept>. This file contains a JSON definition for an index named <bpt id="p1">**</bpt>margies-knowledge-index<ept id="p1">**</ept>.
11. 인덱스의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
12. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>indexer.json<ept id="p2">**</ept>. This file contains a JSON definition for an indexer named <bpt id="p1">**</bpt>margies-knowledge-indexer<ept id="p1">**</ept>.
13. 인덱서의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.

### <a name="submit-rest-requests"></a>REST 요청 제출

검색 솔루션 구성 요소를 정의하는 JSON 개체를 준비했으므로 REST 인터페이스에 JSON 문서를 제출하여 해당 구성 요소를 만들 수 있습니다.

1. In the <bpt id="p1">**</bpt>create-search<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>create-search.cmd<ept id="p2">**</ept>. This batch script uses the cURL utility to submit the JSON definitions to the REST interface for your Azure Cognitive Search resource.
2. **YOUR_SEARCH_URL** 및 **YOUR_ADMIN_KEY** 변수 자리 표시자를 각각 **Url**, 그리고 Azure Cognitive Search 리소스의 **관리 키** 중 하나로 바꿉니다.

    Azure Portal의 Azure Cognitive Search 리소스 **개요** 및 **키** 페이지에서 이러한 값을 확인할 수 있습니다.

3. 업데이트된 배치 파일을 저장합니다.
4. **create-search** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.
5. **create-search** 폴더의 터미널 창에서 다음 명령을 입력하여 배치 스크립트를 실행합니다.

    ```
    create-search
    ```

6. 스크립트 실행이 완료되면 Azure Portal의 Azure Cognitive Search 리소스 페이지에서 **인덱서** 페이지를 선택하고 인덱싱 프로세스가 완료될 때까지 기다립니다.

    **새로 고침**을 선택하여 인덱싱 작업 진행률을 추적할 수 있습니다. 인덱싱이 완료되려면 1분 정도 걸릴 수 있습니다.

    > <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: If the script fails, check the placeholders you added in the <bpt id="p2">**</bpt>data_source.json<ept id="p2">**</ept> and <bpt id="p3">**</bpt>skillset.json<ept id="p3">**</ept> files as well as the <bpt id="p4">**</bpt>create-search.cmd<ept id="p4">**</ept> file. After correcting any mistakes, you may need to use the Azure portal user interface to delete any components that were created in your search resource before re-running the script.

## <a name="view-the-knowledge-store"></a>지식 저장소 확인

기술 세트를 사용하는 인덱서를 실행하여 지식 저장소를 만든 후에는 인덱싱 프로세스에서 추출된 보강 데이터가 지식 저장소 프로젝션에 유지됩니다.

### <a name="view-object-projections"></a>개체 프로젝션 보기

The <bpt id="p1">*</bpt>object<ept id="p1">*</ept> projections defined in the Margie's Travel skillset consist of a JSON file for each indexed document. These files are stored in a blob container in the Azure Storage account specified in the skillset definition.

1. Azure Portal에서 이전에 만든 Azure Storage 계정을 확인합니다.
2. 왼쪽 창에서 **스토리지 탐색기** 탭을 선택하여 Azure Portal의 스토리지 탐색기 인터페이스에서 스토리지 계정을 확인합니다.
2. **참고**: **[Create an Azure Cognitive Search solution](22-azure-search.md)** 연습을 이전에 완료했으며 구독에 해당 연습의 Azure 리소스가 아직 남아 있으면 이 섹션을 건너뛰고 **검색 솔루션 만들기** 섹션부터 시작하면 됩니다.
3. 그렇지 않은 경우에는 아래 단계에 따라 필요한 Azure 리소스를 프로비전합니다.
4. Open any of the folders, and then download and open the <bpt id="p1">**</bpt>knowledge-projection.json<ept id="p1">**</ept> file it contains. Each JSON file contains a representation of an indexed document, including the enriched data extracted by the skillset as shown here.

```
{
    "file_id":"abcd1234....",
    "file_name":"Margies Travel Company Info.pdf",
    "url":"https://store....blob.core.windows.net/margies/...pdf",
    "language":"en",
    "sentiment":0.83164644241333008,
    "key_phrases":[
        "Margie’s Travel",
        "Margie's Travel",
        "best travel experts",
        "world-leading travel agency",
        "international reach"
        ],
    "locations":[
        "Dubai",
        "Las Vegas",
        "London",
        "New York",
        "San Francisco"
        ],
    "image_tags":[
        "outdoor",
        "tree",
        "plant",
        "palm"
        ]
}
```

이와 같이 *개체* 프로젝션을 만드는 기능을 사용하면 엔터프라이즈 데이터 분석 솔루션에 통합될 수 있는 보강 데이터 개체를 생성할 수 있습니다. 예를 들어, 추가 처리 또는 데이터 웨어하우스로 로드하기 위해 Azure Data Factory 파이프라인으로 JSON 파일을 수집합니다.

### <a name="view-file-projections"></a>파일 프로젝션 보기

기술 세트에 정의된 *파일* 프로젝션은 인덱싱 프로세스 중 문서에서 추출된 각 이미지에 대한 JPEG 파일을 만듭니다.

1. In the storage explorer interface in the Azure portal, select the <bpt id="p1">**</bpt>margies-images<ept id="p1">**</ept> blob container. This container contains a folder for each document that contained images.
2. 폴더를 열고 내용을 확인합니다. 각 폴더에는 하나 이상의 \*.jpg 파일이 포함되어 있습니다.
3. 이미지 파일을 열어 문서에서 추출한 이미지가 포함되어 있는지 확인합니다.

이와 같이 *파일* 프로젝션을 생성하는 기능은 대량의 문서에서 포함된 이미지를 추출하는 효율적인 방법을 제공합니다.

### <a name="view-table-projections"></a>테이블 프로젝션 보기

기술 세트에 정의된 *테이블* 프로젝션은 보강 데이터의 관계형 스키마를 형성합니다.

1. Azure Portal의 스토리지 탐색기 인터페이스에서 **테이블**을 펼칩니다.
2. Select the <bpt id="p1">**</bpt>Docs<ept id="p1">**</ept> table to view its columns. The columns include some standard Azure Storage table columns - to hide these, modify the <bpt id="p1">**</bpt>Column Options<ept id="p1">**</ept> to select only the following columns:
    - **document_id**(인덱싱 프로세스에서 자동으로 생성되는 키 열)
    - **file_id**(인코딩된 파일 URL)
    - **file_name**(문서 메타데이터에서 추출된 파일 이름)
    - **language**(문서가 작성된 언어)
    - **sentiment** 문서에 대해 계산된 감정 점수.
    - **url** Azure Storage의 문서 Blob에 대한 URL.
3. 인덱싱 프로세스에서 만든 다른 테이블을 표시합니다.
    - **ImageTags**(태그가 표시되는 문서의 **document_id**를 포함하는 개별 이미지 태그에 대한 행 포함).
    - **KeyPhrases**(문구가 표시되는 문서의 **document_id**를 포함하는 개별 핵심 문구에 대한 행 포함).
    - **Locations**(위치가 표시되는 문서의 **document_id**를 포함하는 개별 위치에 대한 행 포함).

제한된 구독을 사용 중이어서 리소스 그룹이 제공되어 있다면 해당 리소스 그룹을 선택하여 속성을 확인합니다.

## <a name="more-information"></a>추가 정보

Azure Cognitive Search를 사용하여 지식 저장소를 만드는 방법에 대한 자세한 내용은 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro)를 참조하세요.
