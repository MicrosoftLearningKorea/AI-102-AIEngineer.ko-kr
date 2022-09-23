---
lab:
  title: Custom Vision을 사용하여 이미지의 개체 감지
  module: Module 9 - Developing Custom Vision Solutions
---

# <a name="detect-objects-in-images-with-custom-vision"></a>Custom Vision을 사용하여 이미지의 개체 감지

이 연습에서는 Custom Vision 서비스를 사용하여 이미지에서 3가지 과일 클래스(사과, 바나나, 오렌지)을 감지하고 찾을 수 있는 *개체 감지* 모델을 학습시킵니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-custom-vision-resources"></a>Custom Vision 리소스 만들기

If you already have <bpt id="p1">**</bpt>Custom Vision<ept id="p1">**</ept> resources for training and prediction in your Azure subscription, you can use them in this exercise. If not, use the following instructions to create them.

1. 새 브라우저 탭에서 `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *custom vision*을 검색한 후에 다음 설정을 사용하여 **Custom Vision** 리소스를 만듭니다.
    - **만들기 옵션**: 모두
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **학습 가격 책정 계층**: F0
    - **예측 가격 책정 계층**: F0

    > **참고**: 구독에 F0 Custom Vision 서비스가 이미 있는 경우에는 해당 서비스에 대해 **S0**을 선택합니다.

3. Wait for the resources to be created, and then view the deployment details and note that two Custom Vision resources are provisioned; one for training, and another for prediction (evident by the <bpt id="p1">**</bpt>-Prediction<ept id="p1">**</ept> suffix). You can view these by navigating to the resource group where you created them.

> <bpt id="p1">**</bpt>Important<ept id="p1">**</ept>: Each resource has its own <bpt id="p2">*</bpt>endpoint<ept id="p2">*</ept> and <bpt id="p3">*</bpt>keys<ept id="p3">*</ept>, which are used to manage access from your code. To train an image classification model, your code must use the <bpt id="p1">*</bpt>training<ept id="p1">*</ept> resource (with its endpoint and key); and to use the trained model to predict image classes, your code must use the <bpt id="p2">*</bpt>prediction<ept id="p2">*</ept> resource (with its endpoint and key).

## <a name="create-a-custom-vision-project"></a>Custom Vision 프로젝트 만들기

To train an object detection model, you need to create a Custom Vision project based on your training resource. To do this, you'll use the Custom Vision portal.

1. 새 브라우저 탭에서 `https://customvision.ai`의 Custom Vision 포털을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. 다음 설정을 사용하여 새 프로젝트를 만듭니다.
    - **이름**: Detect Fruit
    - **설명**: Object detection for fruit.
    - **리소스**: 이전에 만든 Custom Vision 리소스
    - **프로젝트 형식**: 개체 감지
    - **도메인**: 일반
3. 프로젝트가 만들어지고 브라우저에서 열릴 때까지 기다립니다.

## <a name="add-and-tag-images"></a>이미지 추가 및 태그 지정

개체 감지 모델을 학습시키려면 모델이 식별할 클래스가 포함된 이미지를 업로드하고, 태그를 지정하여 각 개체 인스턴스에 대한 경계 상자를 나타내야 합니다.

1. In Visual Studio Code, view the training images in the <bpt id="p1">**</bpt>18-object-detection/training-images<ept id="p1">**</ept> folder where you cloned the repository. This folder contains images of fruit.
2. Custom Vision Portal의 개체 감지 프로젝트에서 **이미지 추가**를 클릭하고 압축 해제된 폴더 안의 모든 이미지를 업로드합니다.
3. 이미지를 업로드한 후 첫 번째 이미지를 선택하여 엽니다.
4. Hold the mouse over any object in the image until an automatically detected region is displayed like the image below. Then select the object, and if necessary resize the region to surround it.

![개체의 기본 영역](./images/object-region.jpg)

또는 단순히 개체를 주변을 끌어 영역을 만들 수 있습니다.

5. 영역이 개체를 둘러싸는 경우 다음과 같이 적절한 개체 유형(*apple*, *banana* 또는 *orange*)의 새 태그를 추가합니다.

![이미지에서 태그가 지정된 개체](./images/object-tag.jpg)

6. 이미지에서 서로 다른 개체를 선택하고 태그를 지정하여 영역 크기를 조정하고 필요에 따라 새 태그를 추가합니다.

![이미지에서 태그가 지정된 두 개의 개체](./images/object-tags.jpg)

7. Use the <bpt id="p1">**</bpt><ph id="ph1">&gt;</ph><ept id="p1">**</ept> link on the right to go to the next image, and tag its objects. Then just keep working through the entire image collection, tagging each apple, banana, and orange.

8. 마지막 이미지에 태그를 지정했으면 **이미지 세부 정보** 편집기를 닫고 **학습 이미지** 페이지의 **태그** 아래에서 **태그가 지정됨**을 선택하여 태그가 지정된 이미지를 모두 확인합니다.

![프로젝트의 태그가 지정된 이미지](./images/tagged-images.jpg)

## <a name="use-the-training-api-to-upload-images"></a>교육 API를 사용하여 이미지 업로드

You can use the graphical tool in the Custom Vision portal to tag your images, but many AI development teams use other tools that generate files containing information about tags and object regions in images. In scenarios like this, you can use the Custom Vision training API to upload tagged images to the project.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In this exercise, you can choose to use the API from either the <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept> SDK. In the steps below, perform the actions appropriate for your preferred language.

1. Custom Vision 포털의 **학습 이미지** 페이지 오른쪽 위에 있는 *설정*(&#9881;) 아이콘을 클릭하여 프로젝트 설정을 표시합니다.
2. **일반**(왼쪽에 있음) 아래에서 이 프로젝트를 고유하게 식별하는 **프로젝트 ID**를 확인합니다.
3. On the right, under <bpt id="p1">**</bpt>Resources<ept id="p1">**</ept> note that the key and endpoint are shown. These are the details for the <bpt id="p1">*</bpt>training<ept id="p1">*</ept> resource (you can also obtain this information by viewing the resource in the Azure portal).
4. Visual Studio Code의 **18-object-detection** 폴더 아래에서 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
5. Right-click the <bpt id="p1">**</bpt>train-detector<ept id="p1">**</ept> folder and open an integrated terminal. Then install the Custom Vision Training package by running the appropriate command for your language preference:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

6. **train-detector** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the endpoint and key for your Custom Vision <bpt id="p1">*</bpt>training<ept id="p1">*</ept> resource, and the project ID for the object detection project you created previously. Save your changes.

7. Azure 구독에 학습 및 예측용 **Custom Vision** 리소스가 이미 포함되어 있으면 이 연습에서 해당 리소스를 사용할 수 있습니다.

    > 그렇지 않은 경우에는 다음 지침에 따라 해당 리소스를 만듭니다.

8. **train-detector** 폴더에 포함된 하위 폴더에는 JSON 파일에서 참조된 이미지 파일이 저장되어 있습니다.


9. **train-detector** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: train-detector.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **CustomVisionTrainingClient**를 만듭니다. 이 CustomVisionTrainingClient와 프로젝트 ID를 사용하여 프로젝트에 대한 **Project** 참조를 만듭니다.
    - **Upload_Images** 함수가 JSON 파일에서 태그가 지정된 영역 정보를 추출하고, 해당 정보를 사용하여 영역이 포함된 이미지 배치를 만든 후 프로젝트에 해당 배치를 업로드합니다.
10. **train-detector** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.
    
**C#**

```
dotnet run
```

**Python**

```
python train-detector.py
```
    
11. Wait for the program to end. Then return to your browser and view the <bpt id="p1">**</bpt>Training Images<ept id="p1">**</ept> page for your project in the Custom Vision portal (refreshing the browser if necessary).
12. 새로 태그가 지정된 이미지 몇 개가 프로젝트에 추가되었음을 확인합니다.

## <a name="train-and-test-a-model"></a>모델 학습 및 테스트

프로젝트의 이미지에 태그를 지정했으므로 이제 모델을 학습시킬 준비가 되었습니다.

1. In the Custom Vision project, click <bpt id="p1">**</bpt>Train<ept id="p1">**</ept> to train an object detection model using the tagged images. Select the <bpt id="p1">**</bpt>Quick Training<ept id="p1">**</ept> option.
2. 학습이 완료될 때까지 기다리고(10분 정도 걸릴 수 있음) *정확성*, *리콜* 및 *mAP* 성능 메트릭을 검토합니다. 이러한 메트릭은 분류 모델의 예측 정확도를 측정하며 모두 높아야 합니다.
3. At the top right of the page, click <bpt id="p1">**</bpt>Quick Test<ept id="p1">**</ept>, and then in the <bpt id="p2">**</bpt>Image URL<ept id="p2">**</ept> box, enter <ph id="ph1">`https://aka.ms/apple-orange`</ph> and view the prediction that is generated. Then close the <bpt id="p1">**</bpt>Quick Test<ept id="p1">**</ept> window.

## <a name="publish-the-object-detection-model"></a>개체 감지 모델 게시

이제 클라이언트 애플리케이션에서 사용하도록 학습된 모델을 게시할 수 있습니다.

1. Custom Vision 포털의 **성능** 페이지에서 **&#128504; 게시**를 클릭하여 다음 설정을 사용해 학습시킨 모델을 게시합니다.
    - **모델 이름**: fruit-detector
    - **예측 리소스**: 이전에 만든 **예측** “-Prediction”으로 끝나는 리소스(학습 리소스 <u>아님</u>)
2. **프로젝트 설정** 페이지 왼쪽 위에서 *프로젝트 설정*(&#128065;) 아이콘을 클릭하여 Custom Vision 포털 홈 페이지로 돌아옵니다. 이제 홈 페이지에 프로젝트가 나열됩니다.
3. On the Custom Vision portal home page, at the top right, click the <bpt id="p1">*</bpt>settings<ept id="p1">*</ept> (&amp;#9881;) icon to view the settings for your Custom Vision service. Then, under <bpt id="p1">**</bpt>Resources<ept id="p1">**</ept>, find your <bpt id="p2">*</bpt>prediction<ept id="p2">*</ept> resource which ends with "-Prediction" (<bpt id="p3">&lt;u&gt;</bpt>not<ept id="p3">&lt;/u&gt;</ept> the training resource) to determine its <bpt id="p4">**</bpt>Key<ept id="p4">**</ept> and <bpt id="p5">**</bpt>Endpoint<ept id="p5">**</ept> values (you can also obtain this information by viewing the resource in the Azure portal).

## <a name="use-the-image-classifier-from-a-client-application"></a>클라이언트 애플리케이션에서 이미지 분류자 사용

Now that you've published the image classification model, you can use it from a client application. Once again, you can choose to use <bpt id="p1">**</bpt>C#<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Python<ept id="p2">**</ept>.

1. Visual Studio Code에서 **18-object-detection** 폴더로 이동한 다음 선호하는 언어(**C-Sharp** 또는 **Python**)에 해당하는 폴더에서 **test-detector** 폴더를 확장합니다.
2. Right-click the <bpt id="p1">**</bpt>test-detector<ept id="p1">**</ept> folder and open an integrated terminal. Then enter the following SDK-specific command to install the Custom Vision Prediction package:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

> **참고**: Python SDK 패키지에는 학습 패키지와 예측 패키지가 모두 포함되어 있으며, 이러한 패키지가 이미 설치되어 있을 수도 있습니다.

3. Open the configuration file for your client application (<bpt id="p1">*</bpt>appsettings.json<ept id="p1">*</ept> for C# or <bpt id="p2">*</bpt>.env<ept id="p2">*</ept> for Python) and update the configuration values it contains to reflect the endpoint and key for your Custom Vision <bpt id="p3">*</bpt>prediction<ept id="p3">*</ept> resource, the project ID for the object detection project, and the name of your published model (which should be <bpt id="p4">*</bpt>fruit-detector<ept id="p4">*</ept>). Save your changes.
4. 클라이언트 애플리케이션의 코드 파일(C#의 경우 *Program.cs*, Python의 경우 *test-detector.py*)을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **CustomVisionPredictionClient**를 만듭니다.
    - The prediction client object is used to get object detection predictions for the <bpt id="p1">**</bpt>produce.jpg<ept id="p1">**</ept> image, specifying the project ID and model name in the request. The predicted tagged regions are then drawn on the image, and the result is saved as <bpt id="p1">**</bpt>output.jpg<ept id="p1">**</ept>.
5. **test-detector** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python test-detector.py
```

6. 프로그램 실행이 완료되면 생성된 **output.jpg** 파일을 표시하여 이미지에서 감지된 개체를 확인합니다.

## <a name="more-information"></a>추가 정보

Custom Vision 서비스를 통한 개체 감지에 대한 자세한 내용은 [Custom Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/)를 참조하세요.
