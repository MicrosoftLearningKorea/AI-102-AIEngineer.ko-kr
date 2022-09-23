---
lab:
  title: 이미지에서 텍스트 읽기
  module: Module 11 - Reading Text in Images and Documents
---

# <a name="read-text-in-images"></a>이미지에서 텍스트 읽기

Optical character recognition (OCR) is a subset of computer vision that deals with reading text in images and documents. The <bpt id="p1">**</bpt>Computer Vision<ept id="p1">**</ept> service provides two APIs for reading text, which you'll explore in this exercise.

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

이 연습에서는 Computer Vision SDK를 사용해 텍스트를 읽는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You can choose to use the SDK for either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **20-ocr** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. OCR(광학 인식)은 이미지와 문서에서 텍스트 읽기를 처리하는 Computer Vision 하위 서비스입니다.

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```

3. **read-text** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    **Computer Vision** 서비스에서는 텍스트 읽기용 API 2개를 제공합니다. 이 연습에서는 해당 API를 살펴봅니다.
4. **read-text** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: read-text.py

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
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
```

5. In the code file for your client application, in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, note that the code to load the configuration settings has been provided. Then find the comment <bpt id="p1">**</bpt>Authenticate Computer Vision client<ept id="p1">**</ept>. Then, under this comment, add the following language-specific code to create and authenticate a Computer Vision client object:

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
    
## <a name="use-the-ocr-api"></a>OCR API 사용

The <bpt id="p1">**</bpt>OCR<ept id="p1">**</ept> API is an optical character recognition API that is optimized for reading small to medium amounts of printed text in <bpt id="p2">*</bpt>.jpg<ept id="p2">*</ept>, <bpt id="p3">*</bpt>.png<ept id="p3">*</ept>, <bpt id="p4">*</bpt>.gif<ept id="p4">*</ept>, and <bpt id="p5">*</bpt>.bmp<ept id="p5">*</ept> format images. It supports a wide range of languages and in addition to reading text in the image it can determine the orientation of each text region and return information about the rotation angle of the text in relation to the image

1. In the code file for your application, in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, examine the code that runs if the user selects menu option <bpt id="p2">**</bpt>1<ept id="p2">**</ept>. This code calls the <bpt id="p1">**</bpt>GetTextOcr<ept id="p1">**</ept> function, passing the path to an image file.
2. **read-text/images** 폴더에서 **Lincoln.jpg**를 열어 코드가 처리할 이미지를 표시합니다.
3. 코드 파일로 돌아와서 **GetTextOcr** 함수를 찾은 다음 콘솔에 메시지를 인쇄하는 기존 코드 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Use OCR API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var ocrResults = await cvClient.RecognizePrintedTextInStreamAsync(detectOrientation:false, image:imageData);

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Magenta, 3);

    foreach(var region in ocrResults.Regions)
    {
        foreach(var line in region.Lines)
        {
            // Show the position of the line of text
            int[] dims = line.BoundingBox.Split(",").Select(int.Parse).ToArray();
            Rectangle rect = new Rectangle(dims[0], dims[1], dims[2], dims[3]);
            graphics.DrawRectangle(pen, rect);

            // Read the words in the line of text
            string lineText = "";
            foreach(var word in line.Words)
            {
                lineText += word.Text + " ";
            }
            Console.WriteLine(lineText.Trim());
        }
    }

    // Save the image with the text locations highlighted
    String output_file = "ocr_results.jpg";
    image.Save(output_file);
    Console.WriteLine("Results saved in " + output_file);
}
```

**Python**

```Python
# Use OCR API to read text in image
with open(image_file, mode="rb") as image_data:
    ocr_results = cv_client.recognize_printed_text_in_stream(image_data)

# Prepare image for drawing
fig = plt.figure(figsize=(7, 7))
img = Image.open(image_file)
draw = ImageDraw.Draw(img)

# Process the text line by line
for region in ocr_results.regions:
    for line in region.lines:

        # Show the position of the line of text
        l,t,w,h = list(map(int, line.bounding_box.split(',')))
        draw.rectangle(((l,t), (l+w, t+h)), outline='magenta', width=5)

        # Read the words in the line of text
        line_text = ''
        for word in line.words:
            line_text += word.text + ' '
        print(line_text.rstrip())

# Save the image with the text locations highlighted
plt.axis('off')
plt.imshow(img)
outputfile = 'ocr_results.jpg'
fig.savefig(outputfile)
print('Results saved in', outputfile)
```

4. Examine the code you added to the <bpt id="p1">**</bpt>GetTextOcr<ept id="p1">**</ept> function. It detects regions of printed text from an image file, and for each region it extracts the lines of text and highlights there position on the image. It then extracts the words in each line and prints them.
5. 변경 내용을 저장하고 **read-text** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

이제 C# 출력에 **await** 연산자를 사용하는 비동기 함수 관련 경고가 표시될 수 있습니다. 이러한 경고는 이러한 메시지는 무시해도 됩니다.

**Python**

```
python read-text.py
```

6. 메시지가 표시되면 **1**을 입력하고 출력을 살펴봅니다. 출력은 이미지에서 추출된 텍스트입니다.
7. 코드 파일과 같은 폴더에 생성된 **ocr_results.jpg** 파일을 표시하여 이미지에서 주석이 추가된 텍스트 줄을 확인합니다.

## <a name="use-the-read-api"></a>Read API 사용

The <bpt id="p1">**</bpt>Read<ept id="p1">**</ept> API uses a newer text recognition model than the OCR API, and performs better for larger images that contain a lot of text. It also supports text extraction from <bpt id="p1">*</bpt>.pdf<ept id="p1">*</ept> files, and can recognize both printed text and handwritten text in multiple languages.

**Read** API는 비동기 작업 모델을 사용합니다. 이 모델에서는 텍스트 인식 시작 요청이 제출되며, 해당 요청에서 반환된 작업 ID를 이후 작업에서 사용하여 진행률을 확인하고 결과를 검색할 수 있습니다.

1. In the code file for your application, in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, examine the code that runs if the user selects menu option <bpt id="p2">**</bpt>2<ept id="p2">**</ept>. This code calls the <bpt id="p1">**</bpt>GetTextRead<ept id="p1">**</ept> function, passing the path to a PDF document file.
2. In the <bpt id="p1">**</bpt>read-text/images<ept id="p1">**</ept> folder, right-click <bpt id="p2">**</bpt>Rome.pdf<ept id="p2">**</ept> and select <bpt id="p3">**</bpt>Reveal in File Explorer<ept id="p3">**</ept>. Then in File Explorer, open the PDF file to view it.
3. Visual Studio Code의 코드 파일로 돌아와서 **GetTextRead** 함수를 찾은 다음 콘솔에 메시지를 인쇄하는 기존 코드 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Use Read API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var readOp = await cvClient.ReadInStreamAsync(imageData);

    // Get the async operation ID so we can check for the results
    string operationLocation = readOp.OperationLocation;
    string operationId = operationLocation.Substring(operationLocation.Length - 36);

    // Wait for the asynchronous operation to complete
    ReadOperationResult results;
    do
    {
        Thread.Sleep(1000);
        results = await cvClient.GetReadResultAsync(Guid.Parse(operationId));
    }
    while ((results.Status == OperationStatusCodes.Running ||
            results.Status == OperationStatusCodes.NotStarted));

    // If the operation was successfuly, process the text line by line
    if (results.Status == OperationStatusCodes.Succeeded)
    {
        var textUrlFileResults = results.AnalyzeResult.ReadResults;
        foreach (ReadResult page in textUrlFileResults)
        {
            foreach (Line line in page.Lines)
            {
                Console.WriteLine(line.Text);
            }
        }
    }
}  
```

**Python**

```Python
# Use Read API to read text in image
with open(image_file, mode="rb") as image_data:
    read_op = cv_client.read_in_stream(image_data, raw=True)

    # Get the async operation ID so we can check for the results
    operation_location = read_op.headers["Operation-Location"]
    operation_id = operation_location.split("/")[-1]

    # Wait for the asynchronous operation to complete
    while True:
        read_results = cv_client.get_read_result(operation_id)
        if read_results.status not in [OperationStatusCodes.running, OperationStatusCodes.not_started]:
            break
        time.sleep(1)

    # If the operation was successfuly, process the text line by line
    if read_results.status == OperationStatusCodes.succeeded:
        for page in read_results.analyze_result.read_results:
            for line in page.lines:
                print(line.text)
```
    
4. Examine the code you added to the <bpt id="p1">**</bpt>GetTextRead<ept id="p1">**</ept> function. It submits a request for a read operation, and then repeatedly checks status until the operation has completed. If it was successful, the code processes the results by iterating through each page, and then through each line.
5. 변경 내용을 저장하고 **read-text** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

6. 메시지가 표시되면 **2**를 입력하고 출력을 살펴봅니다. 출력은 문서에서 추출된 텍스트입니다.

## <a name="read-handwritten-text"></a>필기 텍스트 읽기

**Read** API는 인쇄된 텍스트뿐 아니라 영어 필기 텍스트도 추출할 수 있습니다.

1. In the code file for your application, in the <bpt id="p1">**</bpt>Main<ept id="p1">**</ept> function, examine the code that runs if the user selects menu option <bpt id="p2">**</bpt>3<ept id="p2">**</ept>. This code calls the <bpt id="p1">**</bpt>GetTextRead<ept id="p1">**</ept> function, passing the path to an image file.
2. **read-text/images** 폴더에서 **Note.jpg**를 열어 코드가 처리할 이미지를 표시합니다.
3. **read-text** 폴더의 통합 터미널에서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

4. 메시지가 표시되면 **3**을 입력하고 출력을 살펴봅니다. 출력은 문서에서 추출된 텍스트입니다.

## <a name="more-information"></a>추가 정보

**Computer Vision** 서비스를 사용하여 텍스트를 읽는 방법에 대한 자세한 내용은 [Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)를 참조하세요.
