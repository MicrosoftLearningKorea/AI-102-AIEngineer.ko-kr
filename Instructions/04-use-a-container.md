---
lab:
  title: Cognitive Services 컨테이너 사용
  module: Module 2 - Developing AI Apps with Cognitive Services
---

# <a name="use-a-cognitive-services-container"></a>Cognitive Services 컨테이너 사용

Using cognitive services hosted in Azure enables application developers to focus on the infrastructure for their own code while benefiting from scalable services that are managed by Microsoft. However, in many scenarios, organizations require more control over their service infrastructure and the data that is passed between services.

Many of the cognitive services APIs can be packaged and deployed in a <bpt id="p1">*</bpt>container<ept id="p1">*</ept>, enabling organizations to host cognitive services in their own infrastructure; for example in local Docker servers, Azure Container Instances, or Azure Kubernetes Services clusters. Containerized cognitive services need to communicate with an Azure-based cognitive services account to support billing; but application data is not passed to the back-end service, and organizations have greater control over the deployment configuration of their containers, enabling custom solutions for authentication, scalability, and other considerations.

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
5. When the resource has been deployed, go to it and view its <bpt id="p1">**</bpt>Keys and Endpoint<ept id="p1">**</ept> page. You will need the endpoint and one of the keys from this page in the next procedure.

## <a name="deploy-and-run-a-text-analytics-container"></a>Text Analytics 컨테이너 배포 및 실행

Azure에서 호스트되는 Cognitive Services를 사용하는 애플리케이션 개발자는 직접 작성하는 코드용 인프라 관련 작업을 중점적으로 수행하는 동시에 Microsoft에서 관리하는 확장성 있는 서비스도 활용할 수 있습니다.

1. Azure Portal **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *container instances*를 검색한 후에 다음 설정을 사용하여 **Container Instances** 리소스를 만듭니다.

    - **기본 사항**:
        - **구독**: ‘Azure 구독’
        - **리소스 그룹**: Cognitive Services 리소스가 포함된 리소스 그룹 선택
        - **컨테이너 이름**: 고유한 이름 입력
        - **지역**: 사용 가능한 지역을 선택합니다.
        - **이미지 소스**: 기타 레지스트리
        - **이미지 형식**: 공개
        - **이미지**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/language:1.1.013570001-amd64`
        - **OS 유형**: Linux
        - **Size**: vCPU 1개, 메모리 4GB
    - **네트워킹**:
        - **네트워킹 유형**: 공개
        - **DNS 이름 레이블**: 컨테이너 엔드포인트의 고유한 이름 입력
        - **포트**: TCP 포트를 80에서 **5000**으로 변경
    - **고급**:
        - **정책 다시 시작**: 실패 시
        - **환경 변수**:

            | 원본으로 표시 | 키 | 값 |
            | -------------- | --- | ----- |
            | 예 | `ApiKey` | Cognitive Services 리소스용 키 중 하나 |
            | 예 | `Billing` | Cognitive Services 리소스의 엔드포인트 URI |
            | 예 | `Eula` | `accept` |

        - **명령 재정의**: [ ]
    - **태그**:
        - 태그 추가 안 함

2. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
3. 컨테이너 인스턴스 리소스의 **개요** 페이지에서 다음 속성을 살펴봅니다.
    - **상태**: *실행 중*이어야 합니다.
    - **IP 주소**: Container Instances에 액세스하는 데 사용할 수 있는 공용 IP 주소입니다.
    - **FQDN**: Container Instances 리소스의 *정규화된 도메인 이름*입니다. IP 주소 대신 이 이름을 사용하여 Container Instances에 액세스할 수 있습니다.

    > 그러나 조직에서는 서비스 인프라 및 서비스 간에 전달되는 데이터를 더욱 철저하게 제어해야 하는 경우가 많습니다.
    >
    > ```
    > docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    > ```
    >
    > The command will look for the image on your local machine, and if it doesn't find it there it will pull it from the <bpt id="p1">*</bpt>mcr.microsoft.com<ept id="p1">*</ept> image registry and deploy it to your Docker instance. When deployment is complete, the container will start and listen for incoming requests on port 5000.

## <a name="use-the-container"></a>컨테이너 사용

1. Visual Studio Code의 **04-containers** 폴더에서 **rest-test.cmd**를 열고 이 파일에 포함되어 있는 **curl** 명령(아래에 나와 있음)을 편집합니다. 이때 *&lt;your_ACI_IP_address_or_FQDN&gt;* 을 컨테이너의 IP 주소나 FQDN으로 바꿉니다.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages?" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. 대다수 Cognitive Services API는 *컨테이너*로 패키지하여 배포할 수 있습니다. 따라서 조직은 로컬 Docker 서버, Azure Container Instances, Azure Kubernetes Services 클러스터 등의 자체 인프라에서 Cognitive Services를 호스트할 수 있습니다.
3. 컨테이너화된 Cognitive Services는 청구 지원을 위해 Azure 기반 Cognitive Services 계정과 통신해야 합니다. 그러나 애플리케이션 데이터는 백 엔드 서비스로 전달되지 않으며 조직은 컨테이너 배포 구성을 더욱 자세히 제어할 수 있습니다. 그러므로 인증, 확장성 및 기타 고려 사항 충족을 위한 사용자 지정 솔루션을 활용할 수 있습니다.

    ```
    rest-test
    ```

4. 명령이 입력 문서 2개에서 감지된 언어(영어 및 프랑스어) 관련 정보가 포함된 JSON 문서를 반환함을 확인합니다.

## <a name="clean-up"></a>정리

컨테이너 인스턴스를 사용한 실험을 완료한 후에는 컨테이너 인스턴스를 삭제해야 합니다.

1. Azure Portal에서 이 연습용으로 리소스를 만든 리소스 그룹을 엽니다.
2. 컨테이너 인스턴스 리소스를 선택하여 삭제합니다.

## <a name="more-information"></a>자세한 정보

Cognitive Services를 컨테이너화하는 방법에 대한 자세한 내용은 [Cognitive Services 컨테이너 설명서](https://docs.microsoft.com/azure/cognitive-services/containers/)를 참조하세요.
