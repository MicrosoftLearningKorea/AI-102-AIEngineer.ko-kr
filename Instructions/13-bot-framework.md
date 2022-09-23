---
lab:
  title: Bot Framework SDK를 사용하여 봇 만들기
  module: Module 7 - Conversational AI and the Azure Bot Service
---

# <a name="create-a-bot-with-the-bot-framework-sdk"></a>Bot Framework SDK를 사용하여 봇 만들기

<bpt id="p1">*</bpt>Bots<ept id="p1">*</ept> are software agents that can participate in conversational dialogs with human users. The Microsoft Bot Framework provides a comprehensive platform for building bots that can be delivered as cloud services through the Azure Bot Service.

이 연습에서는 Microsoft Bot Framework SDK를 사용하여 봇을 만들고 배포합니다.

## <a name="before-you-start"></a>시작하기 전에

먼저 봇 개발을 위한 환경을 준비하겠습니다.

### <a name="update-the-bot-framework-emulator"></a>Bot Framework Emulator 업데이트

You're going to use the Bot Framework SDK to create your bot, and the Bot Framework Emulator to test it. The Bot Framework Emulator is updated regularly, so let's make sure you have the latest version installed.

> **참고**: 업데이트에는 이 연습의 지침에 영향을 주는 사용자 인터페이스 변경 내용이 포함되어 있을 수 있습니다.

1. Start the <bpt id="p1">**</bpt>Bot Framework Emulator<ept id="p1">**</ept>, and if you are prompted to install an update, do so for the currently logged in user. If you are not prompted automatically, use the <bpt id="p1">**</bpt>Check for update<ept id="p1">**</ept> option on the <bpt id="p2">**</bpt>Help<ept id="p2">**</ept> menu to check for updates.
2. 사용 가능한 업데이트를 설치한 후에는 나중에 다시 필요하게 될 때까지 Bot Framework Emulator를 닫습니다.

> *봇*은 인간 사용자와의 대화에 참여할 수 있는 소프트웨어 에이전트입니다.

### <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

Microsoft Bot Framework는 Azure Bot Service를 통해 클라우드 서비스로 제공할 수 있는 봇 빌드용으로 포괄적인 플랫폼을 제공합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다.

## <a name="create-a-bot"></a>봇 만들기

Bot Framework SDK를 사용하면 템플릿을 기반으로 봇을 만든 다음 구체적인 요구 사항을 충족하도록 코드를 사용자 지정할 수 있습니다.

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In this exercise, you can choose to use either <bpt id="p2">**</bpt>C#<ept id="p2">**</ept> or <bpt id="p3">**</bpt>Python<ept id="p3">**</ept>. In the steps below, perform the actions appropriate for your preferred language.

1. Visual Studio Code의 **탐색기** 창에서 **13-bot-framework** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. 선택한 언어의 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
3. 터미널에서 다음 명령을 실행하여 필요한 봇 템플릿과 패키지를 설치합니다.

**C#**

```C#
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
```

**Python**

```Python
pip install botbuilder-core
pip install asyncio
pip install aiohttp
pip install cookiecutter==1.7.0
```

4. 템플릿과 패키지를 설치한 후 다음 명령을 실행하여 *EchoBot* 템플릿을 기반으로 봇을 만듭니다.

**C#**

```C#
dotnet new echobot -n TimeBot
```

**Python**

```Python
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Python을 사용 중인 경우 cookiecutter에서 메시지가 표시되면 다음 세부 정보를 입력합니다.
- **bot_name**: TimeBot
- **bot_description**: A bot for our times
    
5. 터미널 창에서 다음 명령을 입력하여 현재 디렉터리를 **TimeBot** 폴더로 변경한 다음 봇용으로 생성된 코드 파일 목록을 표시합니다.

    ```Code
    cd TimeBot
    dir
    ```

## <a name="test-the-bot-in-the-bot-framework-emulator"></a>Bot Framework Emulator에서 봇 테스트

You've created a bot based on the <bpt id="p1">*</bpt>EchoBot<ept id="p1">*</ept> template. Now you can run it locally and test it by using the Bot Framework Emulator (which should be installed on your system).

1. 터미널 창에서 현재 디렉터리가 봇 코드 파일이 포함된 **TimeBot** 폴더인지 확인한 후에 다음 명령을 입력하여 로컬에서 봇 실행을 시작합니다.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```
    
When the bot starts, note the endpoint at which it is running is shown. This should be similar to <bpt id="p1">**</bpt><ph id="ph1">http://localhost:3978</ph><ept id="p1">**</ept>.

2. Bot Framework Emulator를 시작하고 다음과 같이 **/api/messages** 경로가 추가된 엔드포인트를 지정하여 봇을 엽니다.

    `http://localhost:3978/api/messages`

3. **실시간 채팅** 창에서 대화가 열리면 *Hello and welcome!* 메시지가 표시될 때까지 기다립니다.
4. *Hello*와 같은 메시지를 입력하고 봇의 응답을 확인합니다. 입력한 메시지가 그대로 다시 표시됩니다.
5. Bot Framework Emulator를 닫고 Visual Studio Code로 돌아온 후 터미널 창에서 **Ctrl+C**를 입력하여 봇을 중지합니다.

## <a name="modify-the-bot-code"></a>봇 코드 수정

You've created a bot that echoes the user's input back to them. It's not particularly useful, but serves to illustrate the basic flow of a conversational dialog. A conversation with a bot consists of a sequence of <bpt id="p1">*</bpt>activities<ept id="p1">*</ept>, in which text, graphics, or user interface <bpt id="p2">*</bpt>cards<ept id="p2">*</ept> are used to exchange information. The bot begins the conversation with a greeting, which is the result of a <bpt id="p1">*</bpt>conversation update<ept id="p1">*</ept> activity that is triggered when a user initializes a chat session with the bot. Then the conversation consists of a sequence of further activities in which the user and bot take it in turns to send <bpt id="p1">*</bpt>messages<ept id="p1">*</ept>.

1. Visual Studio Code에서 봇의 다음 코드 파일을 엽니다.
    - **C#** : TimeBot/Bots/EchoBot.cs
    - **Python**: TimeBot/bot.py

    Note that the code in this file consists of <bpt id="p1">*</bpt>activity handler<ept id="p1">*</ept> functions; one for the <bpt id="p2">*</bpt>Member Added<ept id="p2">*</ept> conversation update activity (when someone joins the chat session) and another for the <bpt id="p3">*</bpt>Message<ept id="p3">*</ept> activity (when a message is received). The conversation is based on the concept of <bpt id="p1">*</bpt>turns<ept id="p1">*</ept>, in which each turn represents an interaction in which the bot receives, processes, and responds to an activity. The <bpt id="p1">*</bpt>turn context<ept id="p1">*</ept> is used to track information about the activity being processed in the current turn.

2. 코드 파일 맨 위에 다음 네임스페이스 가져오기 문을 추가합니다.

**C#**

```C#
using System;
```

**Python**

```Python
from datetime import datetime
```

3. *메시지* 활동용 활동 처리기 함수를 다음 코드와 일치하도록 수정합니다.

**C#**

```C#
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    string inputMessage = turnContext.Activity.Text;
    string responseMessage = "Ask me what the time is.";
    if (inputMessage.ToLower().StartsWith("what") && inputMessage.ToLower().Contains("time"))
    {
        var now = DateTime.Now;
        responseMessage = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");
    }
    await turnContext.SendActivityAsync(MessageFactory.Text(responseMessage, responseMessage), cancellationToken);
}
```

**Python**

```Python
async def on_message_activity(self, turn_context: TurnContext):
    input_message = turn_context.activity.text
    response_message = 'Ask me what the time is.'
    if (input_message.lower().startswith('what') and 'time' in input_message.lower()):
        now = datetime.now()
        response_message = 'The time is {}:{:02d}.'.format(now.hour,now.minute)
    await turn_context.send_activity(response_message)
```
    
4. 변경 내용을 저장하고 터미널 창에서 현재 디렉터리가 봇 코드 파일이 포함된 **TimeBot** 폴더인지 확인한 후에 다음 명령을 입력하여 로컬에서 봇 실행을 시작합니다.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```

앞에서와 마찬가지로 봇이 시작되면 봇이 실행 중인 엔드포인트가 표시되는지 확인합니다.

5. Bot Framework Emulator를 시작하고 다음과 같이 **/api/messages** 경로가 추가된 엔드포인트를 지정하여 봇을 엽니다.

    `http://localhost:3978/api/messages`

6. **실시간 채팅** 창에서 대화가 열리면 *Hello and welcome!* 메시지가 표시될 때까지 기다립니다.
7. *Hello*와 같은 메시지를 입력하고 봇의 응답을 확인합니다. *Ask me what the time is*가 응답으로 표시됩니다.
8. *What is the time?* 을 입력하고 응답을 확인합니다.

    Bot Framework SDK를 사용하여 봇을 만들고, Bot Framework Emulator를 사용하여 봇을 테스트합니다.

9. Bot Framework Emulator를 닫고 Visual Studio Code로 돌아온 후 터미널 창에서 **Ctrl+C**를 입력하여 봇을 중지합니다.

## <a name="more-information"></a>추가 정보

Bot Framework에 대해 자세히 알아보려면 [Bot Framework 설명서](https://docs.microsoft.com/azure/bot-service/index-bf-sdk)를 참조하세요.
