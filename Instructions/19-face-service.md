---
lab:
  title: 얼굴 감지 및 분석
  module: 'Module 10 - Detecting, Analyzing, and Recognizing Faces'
---

# <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석

The ability to detect and analyze human faces is a core AI capability. In this exercise, you'll explore two Azure Cognitive Services that you can use to work with faces in images: the <bpt id="p1">**</bpt>Computer Vision<ept id="p1">**</ept> service, and the <bpt id="p2">**</bpt>Face<ept id="p2">**</ept> service.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: From June 21st 2022, capabilities of cognitive services that return personally identifiable information are restricted to customers who have been granted <bpt id="p2">[</bpt>limited access<ept id="p2">](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access)</ept>. Additionally, capabilities that infer emotional state are no longer available. These restrictions may affect this lab exercise. We're working to address this, but in the meantime you may experience some errors when following the steps below; for which we apologize. For more details about the changes Microsoft has made, and why - see <bpt id="p1">[</bpt>Responsible AI investments and safeguards for facial recognition<ept id="p1">](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/)</ept>.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 과정용 코드 리포지토리를 아직 복제하지 않았으면 복제해야 합니다.

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
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. When the resource has been deployed, go to it and view its <bpt id="p1">**</bpt>Keys and Endpoint<ept id="p1">**</ept> page. You will need the endpoint and one of the keys from this page in the next procedure.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Computer Vision SDK 사용 준비

이 연습에서는 Computer Vision SDK를 사용해 이미지의 얼굴을 분석하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> 사람의 얼굴을 감지 및 분석하는 기능은 AI의 핵심 기능입니다.

1. Visual Studio Code의 **탐색기** 창에서 **19-face** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. 이 연습에서는 이미지에 포함된 얼굴로 작업을 하는 데 사용할 수 있는 두 가지 Azure Cognitive Services인 **Computer Vision** 서비스와 **Face** 서비스에 대해 살펴봅니다.

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-computervision==0.7.0
    ```
    
3. **computer-vision** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

4. Open the configuration file and update the configuration values it contains to reflect the <bpt id="p1">**</bpt>endpoint<ept id="p1">**</ept> and an authentication <bpt id="p2">**</bpt>key<ept id="p2">**</ept> for your cognitive services resource. Save your changes.

5. **computer-vision** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: detect-faces.py

6. **참고**: 2022년 6월 21일부터 개인 식별 정보를 반환하는 Cognitive Service의 기능은 [제한된 액세스 권한](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access)이 부여된 고객으로 제한됩니다.

    **C#**

    ```C#
    // import namespaces
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

    **Python**

    ```Python
    # import namespaces
    from azure.cognitiveservices.vision.computervision import ComputerVisionClient
    from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
    from msrest.authentication import CognitiveServicesCredentials
    ```

## <a name="view-the-image-you-will-analyze"></a>분석할 이미지 확인

이 연습에서는 Computer Vision 서비스를 사용하여 사람 이미지를 분석합니다.

1. Visual Studio Code에서 **computer-vision** 폴더와 이 폴더에 포함된 **images** 폴더를 차례로 확장합니다.
2. **people.jpg** 이미지를 선택하여 표시합니다.

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

이제 SDK를 사용해 Computer Vision 서비스를 호출하고 이미지의 얼굴을 감지할 준비가 되었습니다.

1. 또한 감정 상태를 유추하는 기능은 더 이상 사용할 수 없습니다.

    **C#**

    ```C#
    // Authenticate Computer Vision client
    ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
    cvClient = new ComputerVisionClient(credentials)
    {
        Endpoint = cogSvcEndpoint
    };
    ```

    **Python**

    ```Python
    # Authenticate Computer Vision client
    credential = CognitiveServicesCredentials(cog_key) 
    cv_client = ComputerVisionClient(cog_endpoint, credential)
    ```

2. 이러한 제한 사항은 이 랩 연습에 영향을 줄 수 있습니다.

3. **AnalyzeFaces** 함수의 **검색할 기능(얼굴) 지정** 주석 아래에 다음 코드를 추가합니다.

    **C#**

    ```C#
    // Specify features to be retrieved (faces)
    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
    {
        VisualFeatureTypes.Faces
    };
    ```

    **Python**

    ```Python
    # Specify features to be retrieved (faces)
    features = [VisualFeatureTypes.faces]
    ```

4. **AnalyzeFaces** 함수의 **이미지 분석 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // Get faces
    if (analysis.Faces.Count > 0)
    {
        Console.WriteLine($"{analysis.Faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 3);
        SolidBrush brush = new SolidBrush(Color.LightGreen);

        // Draw and annotate each face
        foreach (var face in analysis.Faces)
        {
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Person at approximately {r.Left}, {r.Top}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}        
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

    # Get faces
    if analysis.faces:
        print(len(analysis.faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in analysis.faces:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Person at approximately {}, {}'.format(r.left, r.top)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('Results saved in', outputfile)
```

5. 변경 내용을 저장하고 **computer-vision** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python detect-faces.py
    ```

6. 출력을 살펴봅니다. 감지된 얼굴 수가 표시됩니다.
7. 이 문제를 해결하기 위해 노력하고 있지만, 그 동안에는 아래 단계를 수행하면 몇 가지 오류가 발생할 수 있습니다. 이에 대해 사과드립니다.

## <a name="prepare-to-use-the-face-sdk"></a>Face SDK 사용 준비

**Computer Vision** 서비스는 기본적인 얼굴 감지 기능을 제공(기타 여러 이미지 분석 기능도 제공함)하는 반면 **Face** 서비스에서는 얼굴 분석 및 인식을 위한 더욱 포괄적인 기능을 제공합니다.

1. Visual Studio Code의 **탐색기** 창에서 **19-face** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Microsoft가 변경한 내용 및 그 이유에 대한 자세한 내용은 [얼굴 인식에 대한 책임 있는 AI 투자 및 보호 조치](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/)를 참조하세요.

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-face==0.4.1
    ```
    
3. **face-api** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

4. Open the configuration file and update the configuration values it contains to reflect the <bpt id="p1">**</bpt>endpoint<ept id="p1">**</ept> and an authentication <bpt id="p2">**</bpt>key<ept id="p2">**</ept> for your cognitive services resource. Save your changes.

5. **face-api** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: analyze-faces.py

6. Open the code file and at the top, under the existing namespace references, find the comment <bpt id="p1">**</bpt>Import namespaces<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Computer Vision SDK:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.Azure.CognitiveServices.Vision.Face;
    using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.cognitiveservices.vision.face import FaceClient
    from azure.cognitiveservices.vision.face.models import FaceAttributeType
    from msrest.authentication import CognitiveServicesCredentials
    ```

7. In the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, note that the code to load the configuration settings has been provided. Then find the comment <bpt id="p1">**</bpt>Authenticate Face client<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to create and authenticate a <bpt id="p1">**</bpt>FaceClient<ept id="p1">**</ept> object:

    **C#**

    ```C#
    // Authenticate Face client
    ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
    faceClient = new FaceClient(credentials)
    {
        Endpoint = cogSvcEndpoint
    };
    ```

    **Python**

    ```Python
    # Authenticate Face client
    credentials = CognitiveServicesCredentials(cog_key)
    face_client = FaceClient(cog_endpoint, credentials)
    ```

8. In the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, under the code you just added, note that the code displays a menu that enables you to call functions in your code to explore the capabilities of the Face service. You will implement these functions in the remainder of this exercise.

## <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석

Face 서비스의 가장 기본적인 기능 중 하나는 이미지에서 얼굴을 감지하고 해당 특성을 확인하는 것입니다. 이러한 특성으로는 머리 자세, 흐릿한 형체, 안경 유무 등이 있습니다.

1. In the code file for your application, in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, examine the code that runs if the user selects menu option <bpt id="p2">**</bpt>1<ept id="p2">**</ept>. This code calls the <bpt id="p1">**</bpt>DetectFaces<ept id="p1">**</ept> function, passing the path to an image file.
2. 코드 파일에서 **DetectFaces** 함수를 찾은 다음 **검색할 얼굴 기능 지정** 주석 아래에 다음 코드를 추가합니다.

    **C#**

    ```C#
    // Specify facial features to be retrieved
    List<FaceAttributeType?> features = new List<FaceAttributeType?>
    {
        FaceAttributeType.Occlusion,
        FaceAttributeType.Blur,
        FaceAttributeType.Glasses
    };
    ```

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeType.occlusion,
                FaceAttributeType.blur,
                FaceAttributeType.glasses]
    ```

3. **DetectFaces** 함수의 방금 추가한 코드 아래에서 **얼굴 가져오기** 주석을 찾은 후 다음 코드를 추가합니다.

**C#**

```C
// Get faces
using (var imageData = File.OpenRead(imageFile))
{    
    var detected_faces = await faceClient.Face.DetectWithStreamAsync(imageData, returnFaceAttributes: features, returnFaceId: false);

    if (detected_faces.Count > 0)
    {
        Console.WriteLine($"{detected_faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Black);
        int faceCount=0;

        // Draw and annotate each face
        foreach (var face in detected_faces)
        {
            faceCount++;
            Console.WriteLine($"\nFace number {faceCount}");
            
            // Get face properties
            Console.WriteLine($" - Mouth Occluded: {face.FaceAttributes.Occlusion.MouthOccluded}");
            Console.WriteLine($" - Eye Occluded: {face.FaceAttributes.Occlusion.EyeOccluded}");
            Console.WriteLine($" - Blur: {face.FaceAttributes.Blur.BlurLevel}");
            Console.WriteLine($" - Glasses: {face.FaceAttributes.Glasses}");

            // Draw and annotate face
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Face ID: {face.FaceId}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Get faces
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data,
                                                            return_face_attributes=features,                     return_face_id=False)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'
        face_count = 0

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            face_count += 1
            print('\nFace number {}'.format(face_count))

            detected_attributes = face.face_attributes.as_dict()
            if 'blur' in detected_attributes:
                print(' - Blur:')
                for blur_name in detected_attributes['blur']:
                    print('   - {}: {}'.format(blur_name, detected_attributes['blur'][blur_name]))
                    
            if 'occlusion' in detected_attributes:
                print(' - Occlusion:')
                for occlusion_name in detected_attributes['occlusion']:
                    print('   - {}: {}'.format(occlusion_name, detected_attributes['occlusion'][occlusion_name]))

            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face ID: {}'.format(face.face_id)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```

4. Examine the code you added to the <bpt id="p1">**</bpt>DetectFaces<ept id="p1">**</ept> function. It analyzes an image file and detects any faces it contains, including attributes for age, emotions, and the presence of spectacles. The details of each face are displayed, including a unique face identifier that is assigned to each face; and the location of the faces is indicated on the image using a bounding box.
5. 변경 내용을 저장하고 **face-api** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    이제 C# 출력에 **await** 연산자를 사용하는 비동기 함수 관련 경고가 표시될 수 있습니다. 이러한 경고는 이러한 메시지는 무시해도 됩니다.

    **Python**

    ```
    python analyze-faces.py
    ```

6. 메시지가 표시되면 **1**을 입력하고 출력을 살펴봅니다. 출력에는 감지된 각 얼굴의 ID와 특성이 포함되어 있습니다.
7. 코드 파일과 같은 폴더에 생성된 **detected_faces.jpg** 파일을 표시하여 주석이 추가된 얼굴을 확인합니다.

## <a name="more-information"></a>추가 정보

There are several additional features available within the <bpt id="p1">**</bpt>Face<ept id="p1">**</ept> service, but following the <bpt id="p2">[</bpt>Responsible AI Standard<ept id="p2">](https://aka.ms/aah91ff)</ept> those are restricted behind a Limited Access policy. These features include identifying, verifying, and creating facial recognition models. To learn more and apply for access, see the <bpt id="p1">[</bpt>Limited Access for Cognitive Services<ept id="p1">](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access)</ept>.

얼굴 감지에 **Computer Vision** 서비스를 사용하는 방법에 대한 자세한 내용은 [Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces)를 참조하세요.

**Face** 서비스에 대해 자세히 알아보려면 [Face 설명서](https://docs.microsoft.com/azure/cognitive-services/face/)를 참조하세요.
