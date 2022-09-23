---
lab:
  title: Computer Vision을 사용하여 이미지 분석
  module: Module 8 - Getting Started with Computer Vision
---

# <a name="analyze-images-with-computer-vision"></a>Computer Vision을 사용하여 이미지 분석

Computer vision is an artificial intelligence capability that enables software systems to interpret visual input by analyzing images. In Microsoft Azure, the <bpt id="p1">**</bpt>Computer Vision<ept id="p1">**</ept> cognitive service provides pre-built models for common computer vision tasks, including analysis of images to suggest captions and tags, detection of common objects, landmarks, celebrities, brands, and the presence of adult content. You can also use the Computer Vision service to analyze image color and formats, and to generate "smart-cropped" thumbnail images.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

If you have not already cloned <bpt id="p1">**</bpt>AI-102-AIEngineer<ept id="p1">**</ept> code repository to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the cloned folder in Visual Studio Code.

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

이 연습에서는 Computer Vision SDK를 사용해 이미지를 분석하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> Computer Vision은 소프트웨어 시스템이 이미지를 분석하여 시각적 입력을 해석하는 데 사용할 수 있는 인공 지능 기능입니다.

1. Visual Studio Code의 **탐색기** 창에서 **15-computer-vision** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Microsoft Azure에서 **Computer Vision** Cognitive Service는 흔히 수행하는 Computer Vision 작업용으로 미리 작성된 모델을 제공합니다. 이러한 작업으로는 이미지를 분석하여 캡션과 태그 추천, 일반적인 개체, 주요 건물, 유명인, 브랜드 감지, 성인 콘텐츠 유무 확인 등이 있습니다.

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```
    
3. **image-analysis** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    Computer Vision 서비스를 사용하여 이미지 색 및 형식을 분석한 다음 "스마트 자르기" 썸네일 이미지를 생성할 수도 있습니다.
4. **image-analysis** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: image-analysis.py

    Open the code file and at the top, under the existing namespace references, find the comment <bpt id="p1">**</bpt>Import namespaces<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Computer Vision SDK:

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
    
## <a name="view-the-images-you-will-analyze"></a>분석할 이미지 확인

이 연습에서는 Computer Vision 서비스를 사용해 여러 이미지를 분석합니다.

1. Visual Studio Code에서 **image-analysis** 폴더와 이 폴더에 포함된 **images** 폴더를 차례로 확장합니다.
2. 각 이미지 파일을 차례로 선택하여 Visual Studio Code에 표시합니다.

## <a name="analyze-an-image-to-suggest-a-caption"></a>이미지를 분석하여 캡션 추천

이제 SDK를 사용해 Computer Vision 서비스를 호출하고 이미지를 분석할 준비가 되었습니다.

1. In the code file for your client application (<bpt id="p1">**</bpt>Program.cs<ept id="p1">**</ept> or <bpt id="p2">**</bpt>image-analysis.py<ept id="p2">**</ept>), in the <bpt id="p3">**</bpt>Main<ept id="p3">**</ept> function, note that the code to load the configuration settings has been provided. Then find the comment <bpt id="p1">**</bpt>Authenticate Computer Vision client<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to create and authenticate a Computer Vision client object:

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

2. 이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다.

3. **AnalyzeImage** 함수의 **검색할 기능 지정** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Specify features to be retrieved
List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
{
    VisualFeatureTypes.Description,
    VisualFeatureTypes.Tags,
    VisualFeatureTypes.Categories,
    VisualFeatureTypes.Brands,
    VisualFeatureTypes.Objects,
    VisualFeatureTypes.Adult
};
```

**Python**

```Python
# Specify features to be retrieved
features = [VisualFeatureTypes.description,
            VisualFeatureTypes.tags,
            VisualFeatureTypes.categories,
            VisualFeatureTypes.brands,
            VisualFeatureTypes.objects,
            VisualFeatureTypes.adult]
```
    
4. **AnalyzeImage** 함수의 **이미지 분석 가져오기** 주석 아래에 다음 코드(나중에 코드를 더 추가할 위치를 나타내는 주석 포함)를 추가합니다.

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // get image captions
    foreach (var caption in analysis.Description.Captions)
    {
        Console.WriteLine($"Description: {caption.Text} (confidence: {caption.Confidence.ToString("P")})");
    }

    // Get image tags


    // Get image categories


    // Get brands in the image


    // Get objects in the image


    // Get moderation ratings
    

}            
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

# Get image description
for caption in analysis.description.captions:
    print("Description: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get image categories 


# Get brands in the image


# Get objects in the image


# Get moderation ratings

```
    
5. 변경 내용을 저장하고 **image-analysis** 폴더의 통합 터미널로 돌아옵니다. 그런 후에 다음 명령을 입력하여 **images/street.jpg** 인수를 사용해 프로그램을 실행합니다.

**C#**

```
dotnet run images/street.jpg
```

**Python**

```
python image-analysis.py images/street.jpg
```
    
6. 출력을 살펴봅니다. **street.jpg** 이미지에 대해 제한된 캡션이 포함되어 있습니다.
7. 이번에는 **images/building.jpg** 인수를 사용하여 프로그램을 다시 실행해 **building.jpg** 이미지에 대해 생성되는 캡션을 확인합니다.
8. 이전 단계를 반복하여 **images/person.jpg** 파일에 대한 캡션을 생성합니다.

## <a name="get-suggested-tags-for-an-image"></a>이미지의 추천 캡션 가져오기

이미지 내용에 대한 단서를 제공하는 관련 *태그*를 식별하면 유용한 경우가 있습니다.

1. **AnalyzeImage** 함수의 **이미지 태그 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get image tags
if (analysis.Tags.Count > 0)
{
    Console.WriteLine("Tags:");
    foreach (var tag in analysis.Tags)
    {
        Console.WriteLine($" -{tag.Name} (confidence: {tag.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get image tags
if (len(analysis.tags) > 0):
    print("Tags: ")
    for tag in analysis.tags:
        print(" -'{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 이미지 캡션 외에 추천 태그 목록도 표시됨을 확인합니다.

## <a name="get-image-categories"></a>이미지 범주 가져오기

Computer Vision 서비스는 이미지 범주를 추천할 수 있으며 각 범주 내에서 잘 알려진 주요 건물을 식별할 수 있습니다.

1. **AnalyzeImage** 함수의 **이미지 범주 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get image categories
List<LandmarksModel> landmarks = new List<LandmarksModel> {};
Console.WriteLine("Categories:");
foreach (var category in analysis.Categories)
{
    // Print the category
    Console.WriteLine($" -{category.Name} (confidence: {category.Score.ToString("P")})");

    // Get landmarks in this category
    if (category.Detail?.Landmarks != null)
    {
        foreach (LandmarksModel landmark in category.Detail.Landmarks)
        {
            if (!landmarks.Any(item => item.Name == landmark.Name))
            {
                landmarks.Add(landmark);
            }
        }
    }
}

// If there were landmarks, list them
if (landmarks.Count > 0)
{
    Console.WriteLine("Landmarks:");
    foreach(LandmarksModel landmark in landmarks)
    {
        Console.WriteLine($" -{landmark.Name} (confidence: {landmark.Confidence.ToString("P")})");
    }
}

```

**Python**

```Python
# Get image categories
if (len(analysis.categories) > 0):
    print("Categories:")
    landmarks = []
    for category in analysis.categories:
        # Print the category
        print(" -'{}' (confidence: {:.2f}%)".format(category.name, category.score * 100))
        if category.detail:
            # Get landmarks in this category
            if category.detail.landmarks:
                for landmark in category.detail.landmarks:
                    if landmark not in landmarks:
                        landmarks.append(landmark)

    # If there were landmarks, list them
    if len(landmarks) > 0:
        print("Landmarks:")
        for landmark in landmarks:
            print(" -'{}' (confidence: {:.2f}%)".format(landmark.name, landmark.confidence * 100))

```
    
2. 변경 내용을 저장한 다음 **images** 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 이미지 캡션과 태그 외에 추천 범주 목록, 그리고 인식된 주요 건물도 표시됨을 확인합니다(특히 **building.jpg** 이미지의 경우).

## <a name="get-brands-in-an-image"></a>이미지에서 브랜드 가져오기

리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. **AnalyzeImage** 함수의 **이미지에서 브랜드 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get brands in the image
if (analysis.Brands.Count > 0)
{
    Console.WriteLine("Brands:");
    foreach (var brand in analysis.Brands)
    {
        Console.WriteLine($" -{brand.Name} (confidence: {brand.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get brands in the image
if (len(analysis.brands) > 0):
    print("Brands: ")
    for brand in analysis.brands:
        print(" -'{}' (confidence: {:.2f}%)".format(brand.name, brand.confidence * 100))
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 식별된 브랜드를 살펴봅니다(특히 **person.jpg** 이미지의 경우).

## <a name="detect-and-locate-objects-in-an-image"></a>이미지에서 개체 감지 및 찾기

*개체 감지*는 이미지 내의 개별 개체를 식별하고 해당 위치를 경계 상자로 표시하는 특정 형식의 Computer Vision 기능입니다.

1. **AnalyzeImage** 함수의 **이미지에서 개체 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get objects in the image
if (analysis.Objects.Count > 0)
{
    Console.WriteLine("Objects in image:");

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.Black);

    foreach (var detectedObject in analysis.Objects)
    {
        // Print object name
        Console.WriteLine($" -{detectedObject.ObjectProperty} (confidence: {detectedObject.Confidence.ToString("P")})");

        // Draw object bounding box
        var r = detectedObject.Rectangle;
        Rectangle rect = new Rectangle(r.X, r.Y, r.W, r.H);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.ObjectProperty,font,brush,r.X, r.Y);

    }
    // Save annotated image
    String output_file = "objects.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file);   
}
```

**Python**

```Python
# Get objects in the image
if len(analysis.objects) > 0:
    print("Objects in image:")

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 8))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    color = 'cyan'
    for detected_object in analysis.objects:
        # Print object name
        print(" -{} (confidence: {:.2f}%)".format(detected_object.object_property, detected_object.confidence * 100))
        
        # Draw object bounding box
        r = detected_object.rectangle
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.object_property,(r.x, r.y), backgroundcolor=color)
    # Save annotated image
    plt.imshow(image)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```
    
2. Save your changes and run the program once for each of the image files in the <bpt id="p1">**</bpt>images<ept id="p1">**</ept> folder, observing any objects that are detected. After each run, view the <bpt id="p1">**</bpt>objects.jpg<ept id="p1">**</ept> file that is generated in the same folder as your code file to see the annotated objects.

## <a name="get-moderation-ratings-for-an-image"></a>이미지의 조정 등급 가져오기

일부 대상 그룹에게는 적합하지 않은 이미지도 있으므로 조정을 적용하여 성인용 이미지나 폭력적인 이미지를 식별해야 할 수도 있습니다.

1. **AnalyzeImage** 함수의 **조정 등급 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get moderation ratings
string ratings = $"Ratings:\n -Adult: {analysis.Adult.IsAdultContent}\n -Racy: {analysis.Adult.IsRacyContent}\n -Gore: {analysis.Adult.IsGoryContent}";
Console.WriteLine(ratings);
```

**Python**

```Python
# Get moderation ratings
ratings = 'Ratings:\n -Adult: {}\n -Racy: {}\n -Gore: {}'.format(analysis.adult.is_adult_content,
                                                                    analysis.adult.is_racy_content,
                                                                    analysis.adult.is_gory_content)
print(ratings)
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 각 이미지의 등급을 살펴봅니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In the preceding tasks, you used a single method to analyze the image, and then incrementally added code to parse and display the results. The SDK also provides individual methods for suggesting captions, identifying tags, detecting objects, and so on - meaning that you can use the most appropriate method to return only the information you need, reducing the size of the data payload that needs to be returned. See the <bpt id="p1">[</bpt>.NET SDK documentation<ept id="p1">](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)</ept> or <bpt id="p2">[</bpt>Python SDK documentation<ept id="p2">](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/computervision?view=azure-python)</ept> for more details.

## <a name="generate-a-thumbnail-image"></a>썸네일 이미지 생성

기본적으로 표시하려는 주체가 새 이미지 치수 내에 포함되도록 이미지를 잘라서 작은 이미지 버전인 *썸네일*을 만들어야 하는 경우도 있습니다.

1. 코드 파일에서 **GetThumbnail** 함수를 찾은 다음 **썸네일 생성** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Generate a thumbnail
using (var imageData = File.OpenRead(imageFile))
{
    // Get thumbnail data
    var thumbnailStream = await cvClient.GenerateThumbnailInStreamAsync(100, 100,imageData, true);

    // Save thumbnail image
    string thumbnailFileName = "thumbnail.png";
    using (Stream thumbnailFile = File.Create(thumbnailFileName))
    {
        thumbnailStream.CopyTo(thumbnailFile);
    }

    Console.WriteLine($"Thumbnail saved in {thumbnailFileName}");
}
```

**Python**

```Python
# Generate a thumbnail
with open(image_file, mode="rb") as image_data:
    # Get thumbnail data
    thumbnail_stream = cv_client.generate_thumbnail_in_stream(100, 100, image_data, True)

# Save thumbnail image
thumbnail_file_name = 'thumbnail.png'
with open(thumbnail_file_name, "wb") as thumbnail_file:
    for chunk in thumbnail_stream:
        thumbnail_file.write(chunk)

print('Thumbnail saved in.', thumbnail_file_name)
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 각 이미지에 대해 코드 파일과 같은 폴더에 생성되는 **thumbnail.jpg** 파일을 엽니다.

## <a name="more-information"></a>추가 정보

In this exercise, you explored some of the image analysis and manipulation capabilities of the Computer Vision service. The service also includes capabilities for reading text, detecting faces, and other computer vision tasks.

**Computer Vision** 서비스를 사용하는 방법에 대한 자세한 내용은 [Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)를 참조하세요.
