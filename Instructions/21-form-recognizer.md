---
lab:
  title: 양식에서 데이터 추출
  module: Module 11 - Reading Text in Images and Documents
---

# <a name="extract-data-from-forms"></a>양식에서 데이터 추출 

Suppose a company currently requires employees to manually purchase order sheets and enter the data into a database. They would like you to utilize AI services to improve the data entry process. You decide to build a machine learning model that will read the form and produce structured data that can be used to automatically update a database.

<bpt id="p1">**</bpt>Form Recognizer<ept id="p1">**</ept> is a cognitive service that enables users to build automated data processing software. This software can extract text, key/value pairs, and tables from form documents using optical character recognition (OCR). Form Recognizer has pre-built models for recognizing invoices, receipts, and business cards. The service also provides the capability to train custom models. In this exercise, we will focus on building custom models.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 과정용 코드 리포지토리를 아직 복제하지 않았으면 복제해야 합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

To use the Form Recognizer service, you need a Form Recognizer or Cognitive Services resource in your Azure subscription. You'll use the Azure portal to create a resource.

1.  `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.

2. **&#65291;리소스 만들기** 단추를 선택하고 *Form Recognizer*를 검색한 후에 다음 설정을 사용하여 **Form Recognizer** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: F0

    > **참고**: 구독에 F0 Form Recognizer 서비스가 이미 포함되어 있으면 여기서는 **S0**을 선택합니다.

3. 현재 기업이 직원에게 주문 시트를 수동으로 구매하고 데이터베이스에 데이터를 입력할 것을 요구한다고 가정합니다. 

## <a name="gather-documents-for-training"></a>학습용 문서 수집

![송장 이미지](../21-custom-form/sample-forms/Form_1.jpg)  

이 리포지토리의 **21-custom-form/sample-forms** 폴더에서 샘플 양식을 사용하고, 여기에는 모델을 학습하고 테스트하는 데 필요한 모든 파일이 포함되어 있습니다.

1. AI 서비스를 활용하여 데이터 항목 프로세스를 개선하려고 합니다.

    **.jpg** 파일을 사용하여 해당 모델을 학습합니다.  

    데이터베이스를 자동으로 업데이트하는 데 사용할 수 있는 구조적 데이터를 생성하고 양식을 읽는 기계 학습 모델을 빌드하기로 합니다. 

2. Azure Portal([https://portal.azure.com](https://portal.azure.com))로 돌아갑니다.

3. 이전에 Form Recognizer 리소스를 만든 **리소스 그룹**을 표시합니다.

4. On the <bpt id="p1">**</bpt>Overview<ept id="p1">**</ept> page for your resource group, note the <bpt id="p2">**</bpt>Subscription ID<ept id="p2">**</ept> and <bpt id="p3">**</bpt>Location<ept id="p3">**</ept>. You will need these values, along with your <bpt id="p1">**</bpt>resource group<ept id="p1">**</ept> name in subsequent steps.

![리소스 그룹 페이지 예제](./images/resource_group_variables.png)

5. Visual Studio Code의 탐색기 창에서 **21-custom-form** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기**를 선택합니다.

6. 터미널 창에서 다음 명령을 입력하여 Azure 구독에 대해 인증된 연결을 설정합니다.
    
```
az login --output none
```

7. 사용자가 자동화된 데이터 처리 소프트웨어를 빌드하는 데 사용할 수 있는 Cognitive Service는 **Form Recognizer**입니다.

8. 다음 명령을 실행하여 Azure 위치를 나열합니다.

```
az account list-locations -o table
```

9. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus*임).

    > **중요**: **Name** 값을 적어 두었다가 12단계에서 사용하세요.

10. 이 소프트웨어는 OCR(광학 인식) 기술을 사용해 양식 문서에서 텍스트, 키/값 쌍 및 테이블을 추출할 수 있습니다.

11. Form Recognizer에는 송장, 영수증 및 명함 인식용으로 미리 빌드된 모델이 포함되어 있습니다. 
    - Azure 리소스 그룹에서 스토리지 계정 만들기
    - 로컬 _sampleforms_ 폴더에서 스토리지 계정의 _sampleforms_ 컨테이너로 파일 업로드
    - 공유 액세스 서명 URI 인쇄

12. 이 서비스에서는 사용자 지정 모델을 학습시키는 기능도 제공합니다.

    이 연습에서는 사용자 지정 모델 빌드 과정을 중점적으로 진행합니다.  

13. **21-custom-form** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

```
setup
```

14. 스크립트 실행이 완료되면 표시되는 출력을 검토하여 Azure 리소스의 SAS URI를 확인합니다.

> **중요**: 다음 작업을 계속 진행하기 전에 나중에 다시 검색할 수 있는 위치(예: Visual Studio Code의 새 텍스트 파일)에 SAS URI를 붙여넣습니다.

15. In the Azure portal, refresh the resource group and verify that it contains the Azure Storage account just created. Open the storage account and in the pane on the left, select <bpt id="p1">**</bpt>Storage Browser (preview)<ept id="p1">**</ept>. Then in Storage Browser, expand <bpt id="p1">**</bpt>BLOB CONTAINERS<ept id="p1">**</ept> and select the <bpt id="p2">**</bpt>sampleforms<ept id="p2">**</ept> container to verify that the files have been uploaded from your local <bpt id="p3">**</bpt>21-custom-form/sample-forms<ept id="p3">**</ept> folder.

## <a name="train-a-model-using-the-form-recognizer-sdk"></a>Form Recognizer SDK를 사용하여 모델 학습

이제 **.jpg** 및 **.json** 파일을 사용하여 모델을 학습시킵니다.

1. In Visual Studio Code, in the <bpt id="p1">**</bpt>21-custom-form/sample-forms<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>fields.json<ept id="p2">**</ept> and review the JSON document it contains. This file defines the fields that you will train a model to extract from the forms.
2. Open <bpt id="p1">**</bpt>Form_1.jpg.labels.json<ept id="p1">**</ept> and review the JSON it contains. This file identifies the location and values for named fields in the <bpt id="p1">**</bpt>Form_1.jpg<ept id="p1">**</ept> training document.
3. Open <bpt id="p1">**</bpt>Form_1.jpg.ocr.json<ept id="p1">**</ept> and review the JSON it contains. This file contains a JSOn representation of the text layout of <bpt id="p1">**</bpt>Form_1.jpg<ept id="p1">**</ept>, including the location of all text areas found in the form.

    *이 연습에서는 필드 정보 파일이 제공되었습니다. 자체 프로젝트에서는 [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)를 사용하여 이러한 파일을 만들 수 있습니다. 도구를 사용할 때 필드 정보 파일이 자동으로 만들어지고 연결된 스토리지 계정에 저장됩니다.*

4. Visual Studio Code의 **21-custom-form** 폴더에서 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
5. **train-model** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.

6. 언어 기본 설정에 적합한 명령을 실행하여 Form Recognizer 패키지를 설치합니다.

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

7. **train-model** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

8. 구성 파일을 편집하여 다음 항목이 반영되도록 설정을 수정합니다.
    - Form Recognizer 리소스의 **엔드포인트**
    - Form Recognizer 리소스의 **키**
    - Blob 컨테이너의 **SAS URI**

9. **train-model** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: train-model.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **클라이언트**를 만듭니다.
    - 이 코드는 학습 클라이언트를 사용해 해당 Blob Storage 컨테이너의 이미지로 모델을 학습하고, 생성된 SAS URI를 사용하여 액세스할 수 있습니다.

10. **train-model** 폴더에서 학습 애플리케이션용 코드 파일을 엽니다.

    - **C#** : Program.cs
    - **Python**: train-model.py

11. **train-model** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python train-model.py
```

12. 프로그램이 종료될 때까지 기다렸다가 모델 출력을 검토합니다.
13. Write down the Model ID in the terminal output. You will need it for the next part of the lab. 

## <a name="test-your-custom-form-recognizer-model"></a>사용자 지정 Form Recognizer 모델 테스트 

1. **21-custom-form** 폴더 내의 선호하는 언어(**C-Sharp** 또는 **Python**) 하위 폴더에서 **test-model** 폴더를 확장합니다.

2. **test-model** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널을 엽니다**.

3. **test-model** 폴더의 터미널에서 언어 기본 설정에 적합한 명령을 실행하여 Form Recognizer 패키지를 설치합니다.

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

이전에 pip를 사용하여 Python 환경에 패키지를 설치했다면 이 작업을 반드시 수행할 필요는 없습니다. 하지만 이 단계를 수행하면 패키지가 설치되어 있는지를 확인할 수 있습니다.

4. In the same terminal for the <bpt id="p1">**</bpt>test-model<ept id="p1">**</ept> folder, install the Tabulate library. This will provide your output in a table:

**C#**

```
dotnet add package Tabulate.NET --version 1.0.5
```

**Python**

```
pip install tabulate
```

5. **test-model** 폴더에서 구성 파일(언어 기본 설정에 따라 **appsettings.json** 또는 **.env**)을 편집하여 다음 값을 추가합니다.
    - Form Recognizer 엔드포인트
    - Form Recognizer 키
    - The Model ID generated when you trained the model (you can find this by switching the terminal back to the <bpt id="p1">**</bpt>cmd<ept id="p1">**</ept> console for the <bpt id="p2">**</bpt>train-model<ept id="p2">**</ept> folder). <bpt id="p1">**</bpt>Save<ept id="p1">**</ept> your changes.

6. **test-model** 폴더에서 클라이언트 애플리케이션의 코드 파일(C#의 경우 *Program.cs*, Python의 경우 *test-model.py*)을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **클라이언트**를 만듭니다.
    - 클라이언트를 사용하여 **test1.jpg** 이미지의 값과 양식 필드를 추출합니다.
    

7. **test-model** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python test-model.py
```
    
8. 출력을 보고 모델의 출력이 "CompanyPhoneNumber" 및 "DatedAs"와 같은 필드 이름을 제공하는 방법에 대해 살펴봅니다.   

## <a name="more-information"></a>추가 정보

Form Recognizer 서비스를 사용하는 방법에 대한 자세한 내용은 [Form Recognizer 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)를 참조하세요.
