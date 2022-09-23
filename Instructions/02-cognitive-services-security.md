---
lab:
  title: Cognitive Services 보안 관리
  module: Module 2 - Developing AI Apps with Cognitive Services
---

# <a name="manage-cognitive-services-security"></a>Cognitive Services 보안 관리

모든 애플리케이션에서 보안은 중요한 고려 사항입니다. 개발자는 Cognitive Services 등의 리소스가 필요한 사용자만 해당 리소스에 액세스할 수 있도록 제한해야 합니다.

일반적으로는 인증 키를 통해Cognitive Services 액세스를 제어합니다. 인증 키는 Cognitive Services 리소스를 처음 만들 때 생성됩니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

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

## <a name="manage-authentication-keys"></a>인증 키 관리

When you created your cognitive services resource, two authentication keys were generated. You can manage these in the Azure portal or by using the Azure command line interface (CLI).

1. In the Azure portal, go to your cognitive services resource and view its <bpt id="p1">**</bpt>Keys and Endpoint<ept id="p1">**</ept> page. This page contains the information that you will need to connect to your resource and use it from applications you develop. Specifically:
    - 클라이언트 애플리케이션이 요청을 보낼 수 있는 HTTP *엔드포인트*
    - Two <bpt id="p1">*</bpt>keys<ept id="p1">*</ept> that can be used for authentication (client applications can use either of the keys. A common practice is to use one for development, and another for production. You can easily regenerate the development key after developers have finished their work to prevent continued access).
    - The <bpt id="p1">*</bpt>location<ept id="p1">*</ept> where the resource is hosted. This is required for requests to some (but not all) APIs.
2. In Visual Studio Code, right-click the <bpt id="p1">**</bpt>02-cognitive-security<ept id="p1">**</ept> folder and open an integrated terminal. Then enter the following command to sign into your Azure subscription by using the Azure CLI.

    ```
    az login
    ```

    A web browser tab will open and prompt you to sign into Azure. Do so, and then close the browser tab and return to Visual Studio Code.

    > <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: If you have multiple subscriptions, you'll need to ensure that you are working in the one that contains your cognitive services resource.  Use this command to         determine your current subscription - its unique ID is the <bpt id="p1">**</bpt>id<ept id="p1">**</ept> value in the JSON that gets returned.

    > **경고**: `az login`에 대한 인증서 꼭짓점 오류가 발생하는 경우 몇 분 정도 기다렸다가 다시 시도하세요.
    >
    > ```
    > az account show
    > ```
    >
    > 구독을 변경해야 하는 경우 *&lt;Your_Subscription_Id&gt;* 를 올바른 구독 ID로 변경하여 다음 명령을 실행합니다.
    >
    > ```
    > az account set --subscription <Your_Subscription_Id>
    > ```
    >
    > 이후에 실행하는 각 Azure CLI 명령에서 명시적으로 구독 ID를 *--subscription* 매개 변수로 지정할 수도 있습니다.

3. 이제 다음 명령을 사용하여 Cognitive Services 키 목록을 가져올 수 있습니다. 명령에서 *&lt;resourceName&gt;* 은 Cognitive Services 리소스 이름으로, *&lt;resourceGroup&gt;* 은 해당 리소스를 만든 리소스 그룹 이름으로 바꾸세요.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

이 명령은 Cognitive Services 리소스의 키 목록을 반환합니다. 목록에는 **key1** 및 **key2**의 2개 키가 포함되어 있습니다.

4. To test your cognitive service, you can use <bpt id="p1">**</bpt>curl<ept id="p1">**</ept> - a command line tool for HTTP requests. In the <bpt id="p1">**</bpt>02-cognitive-security<ept id="p1">**</ept> folder, open <bpt id="p2">**</bpt>rest-test.cmd<ept id="p2">**</ept> and edit the <bpt id="p3">**</bpt>curl<ept id="p3">**</ept> command it contains (shown below), replacing <bpt id="p4">*</bpt><ph id="ph1">&amp;lt;</ph>yourEndpoint<ph id="ph2">&amp;gt;</ph><ept id="p4">*</ept> and <bpt id="p5">*</bpt><ph id="ph3">&amp;lt;</ph>yourKey<ph id="ph4">&amp;gt;</ph><ept id="p5">*</ept> with your endpoint URI and <bpt id="p6">**</bpt>Key1<ept id="p6">**</ept> key to use the Text Analytics API in your cognitive services resource.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.0/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':[{'id':1,'text':'hello'}]}"
    ```

5. 변경 내용을 저장하고 **02-cognitive-security** 폴더의 통합 터미널에서 다음 명령을 실행합니다.

    ```
    rest-test
    ```

이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

6. If a key becomes compromised, or the developers who have it no longer require access, you can regenerate it in the portal or by using the Azure CLI. Run the following command to regenerate your <bpt id="p1">**</bpt>key1<ept id="p1">**</ept> key (replacing <bpt id="p2">*</bpt><ph id="ph1">&amp;lt;</ph>resourceName<ph id="ph2">&amp;gt;</ph><ept id="p2">*</ept> and <bpt id="p3">*</bpt><ph id="ph3">&amp;lt;</ph>resourceGroup<ph id="ph4">&amp;gt;</ph><ept id="p3">*</ept> for your resource).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Cognitive Services 리소스용 키 목록이 반환됩니다. **key1**이 마지막으로 검색한 이후로 변경되었음을 확인할 수 있습니다.

7. 이전 키를 사용하여 **rest-test** 명령을 다시 실행하고( **^** 키를 사용하여 이전 명령을 차례로 다시 실행할 수 있음) 이번에는 명령 실행이 실패함을 확인합니다.
8. Edit the <bpt id="p1">*</bpt>curl<ept id="p1">*</ept> command in <bpt id="p2">**</bpt>rest-test.cmd<ept id="p2">**</ept> replacing the key with the new <bpt id="p3">**</bpt>key1<ept id="p3">**</ept> value, and save the changes. Then rerun the <bpt id="p1">**</bpt>rest-test<ept id="p1">**</ept> command and verify that it succeeds.

> <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: In this exercise, you used the full names of Azure CLI parameters, such as <bpt id="p2">**</bpt>--resource-group<ept id="p2">**</ept>.  You can also use shorter alternatives, such as <bpt id="p1">**</bpt>-g<ept id="p1">**</ept>, to make your commands less verbose (but a little harder to understand).  The <bpt id="p1">[</bpt>Cognitive Services CLI command reference<ept id="p1">](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)</ept> lists the parameter options for each cognitive services CLI command.

## <a name="secure-key-access-with-azure-key-vault"></a>Azure Key Vault를 사용하여 키 액세스 보호

You can develop applications that consume cognitive services by using a key for authentication. However, this means that the application code must be able to obtain the key. One option is to store the key in an environment variable or a configuration file where the application is deployed, but this approach leaves the key vulnerable to unauthorized access. A better approach when developing applications on Azure is to store the key securely in Azure Key Vault, and provide access to the key through a <bpt id="p1">*</bpt>managed identity<ept id="p1">*</ept> (in other words, a user account used by the application itself).

### <a name="create-a-key-vault-and-add-a-secret"></a>키 자격 증명 모음 만들기 및 비밀 추가

먼저 키 자격 증명 모음을 만들고 Cognitive Services 키의 *비밀*을 추가해야 합니다.

1. Azure Cognitive Search 리소스의 **key1** 값을 적어 둡니다(클립보드에 복사해도 됨).
2. Azure Portal **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *Key Vault*를 검색한 후에 다음 설정을 사용하여 **Key Vault** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Cognitive Service 리소스와 같은 리소스 그룹*
    - **Key Vault 이름**: 고유한 이름 입력
    - **지역**: Cognitive Service 리소스와 같은 지역
    - **가격 책정 계층**: 표준
3. 배포가 완료될 때까지 기다렸다가 키 자격 증명 모음 리소스로 이동합니다.
4. 왼쪽 탐색 창의 설정 섹션에서 **비밀**을 선택합니다.
5. **+ 생성/가져오기**를 선택하고 다음 설정을 사용하여 새 비밀을 추가합니다.
    - **업로드 옵션**: 수동
    - **이름**: Cognitive-Services-Key *(뒷부분에서 이 이름을 기준으로 비밀을 검색하는 코드를 실행할 것이므로 이 이름을 정확하게 사용해야 함)*
    - **값**: **key1** Cognitive Services 키

### <a name="create-a-service-principal"></a>서비스 주체 만들기

To access the secret in the key vault, your application must use a service principal that has access to the secret. You'll use the Azure command line interface (CLI) to create the service principal, find its object ID, and grant access to the secret in Azure Vault.

1. Return to Visual Studio Code, and in the integrated terminal for the <bpt id="p1">**</bpt>02-cognitive-security<ept id="p1">**</ept> folder, run the following Azure CLI command, replacing <bpt id="p2">*</bpt><ph id="ph1">&amp;lt;</ph>spName<ph id="ph2">&amp;gt;</ph><ept id="p2">*</ept> with a suitable name for an application identity (for example, <bpt id="p3">*</bpt>ai-app<ept id="p3">*</ept>). Also replace <bpt id="p1">*</bpt><ph id="ph1">&amp;lt;</ph>subscriptionId<ph id="ph2">&amp;gt;</ph><ept id="p1">*</ept> and <bpt id="p2">*</bpt><ph id="ph3">&amp;lt;</ph>resourceGroup<ph id="ph4">&amp;gt;</ph><ept id="p2">*</ept> with the correct values for your subscription ID and the resource group containing your cognitive services and key vault resources:

    > **팁**: 구독 ID를 모르는 경우 **az account show** 명령을 실행하여 구독 정보를 검색합니다. 출력의 **id** 특성이 구독 ID입니다.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

The output of this command includes information about your new service principal. It should look similar to this:

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "ai-app",
        "name": "http://ai-app",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

**appId**, **password** 및 **tenant** 값은 뒷부분에서 필요하므로 적어 두세요(이 터미널을 닫으면 암호를 검색할 수가 없으므로 값을 지금 적어 두어야 합니다. 나중에 필요한 값을 찾을 수 있도록 Visual Studio Code에서 새 텍스트 파일에 출력을 붙여넣을 수 있습니다).

2. 서비스 주체의 **개체 ID**를 가져오려면 다음 Azure CLI 명령을 실행하여 *&lt;appId&gt;* 를 서비스 주체의 앱 ID 값으로 바꿉니다.

    ```
    az ad sp show --id <appId> --query objectId --out tsv
    ```

3. 새 서비스 주체에 Key Vault의 비밀 액세스 권한을 할당하려면 다음 Azure CLI 명령을 실행합니다. 이때 *&lt;keyVaultName&gt;* 은 Azure Key Vault 리소스의 이름으로, *&lt;objectId&gt;* 는 서비스 주체의 개체 ID 값으로 바꿉니다.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### <a name="use-the-service-principal-in-an-application"></a>애플리케이션에서 서비스 주체 사용

이제 애플리케이션에서 서비스 주체 ID를 사용할 수 있습니다. 이 ID를 사용하면 애플리케이션이 키 자격 증명 모음의 비밀 Cognitive Services 키에 액세스한 다음 Cognitive Services 리소스에 연결하는 데 해당 키를 사용할 수 있습니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In this exercise, we'll store the service principal credentials in the application configuration and use them to authenticate a <bpt id="p2">**</bpt>ClientSecretCredential<ept id="p2">**</ept> identity in your application code. This is fine for development and testing, but in a real production application, an administrator would assign a <bpt id="p1">*</bpt>managed identity<ept id="p1">*</ept> to the application so that it uses the service principal identity to access resources, without caching or storing the password.

1. Visual Studio Code에서 **02-cognitive-security** 폴더를 확장하고 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. Right-click the <bpt id="p1">**</bpt>keyvault-client<ept id="p1">**</ept> folder and open an integrated terminal. Then install the packages you will need to use Azure Key Vault and the Text Analytics API in your cognitive services resource by running the appropriate command for your language preference:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. **keyvault-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 포함되어 있는 구성 값을 업데이트하여 다음 설정을 반영합니다.
    
    - Cognitive Services 리소스의 **엔드포인트**
    - **Azure Key Vault** 리소스의 이름
    - 서비스 주체의 **테넌트**
    - 서비스 주체의 **appId**
    - 서비스 주체의 **암호**

     변경 내용을 저장합니다.
4. **keyvault-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 SDK의 네임스페이스를 가져왔습니다.
    - **Main** 함수의 코드가 애플리케이션 구성 설정을 검색하며 서비스 주체 자격 증명을 사용하여 키 자격 증명 모음에서 Cognitive Services 키를 가져옵니다.
    - **GetLanguage** 함수가 SDK를 사용해 서비스용 클라이언트를 만든 다음 해당 클라이언트를 사용하여 입력한 텍스트의 언어를 감지합니다.
5. **keyvault-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. When prompted, enter some text and review the language that is detected by the service. For example, try entering "Hello", "Bonjour", and "Gracias".
7. 애플리케이션 테스트를 완료한 후 "quit"을 입력하여 프로그램을 중지합니다.

## <a name="more-information"></a>추가 정보

Cognitive Services를 보호하는 방법에 대한 자세한 내용은 [Cognitive Services 보안 설명서](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)를 참조하세요.
