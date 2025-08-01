<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "88258b03f2893aa2e69eb8fb24baabbc",
  "translation_date": "2025-07-12T09:28:02+00:00",
  "source_file": "04-tool-use/README.md",
  "language_code": "ko"
}
-->
[![How to Design Good AI Agents](../../../translated_images/lesson-4-thumbnail.546162853cb3daffd64edd92014f274103f76360dfb39fc6e6ee399494da38fd.ko.png)](https://youtu.be/vieRiPRx-gI?si=cEZ8ApnT6Sus9rhn)

> _(위 이미지를 클릭하면 이 강의의 영상을 볼 수 있습니다)_

# 도구 사용 디자인 패턴

도구는 AI 에이전트가 더 넓은 범위의 기능을 갖도록 해주기 때문에 흥미롭습니다. 에이전트가 수행할 수 있는 동작이 제한적이지 않고, 도구를 추가함으로써 다양한 동작을 수행할 수 있게 됩니다. 이 장에서는 AI 에이전트가 특정 도구를 사용해 목표를 달성하는 방법을 설명하는 도구 사용 디자인 패턴에 대해 살펴보겠습니다.

## 소개

이번 강의에서는 다음 질문들에 답하고자 합니다:

- 도구 사용 디자인 패턴이란 무엇인가?
- 어떤 사용 사례에 적용할 수 있는가?
- 디자인 패턴을 구현하는 데 필요한 요소/구성 블록은 무엇인가?
- 신뢰할 수 있는 AI 에이전트를 구축하기 위해 도구 사용 디자인 패턴을 사용할 때 고려해야 할 특별한 점은 무엇인가?

## 학습 목표

이 강의를 마치면 다음을 할 수 있습니다:

- 도구 사용 디자인 패턴과 그 목적을 정의할 수 있다.
- 도구 사용 디자인 패턴이 적용 가능한 사용 사례를 식별할 수 있다.
- 디자인 패턴 구현에 필요한 핵심 요소를 이해할 수 있다.
- 이 디자인 패턴을 사용하는 AI 에이전트의 신뢰성을 확보하기 위한 고려사항을 인지할 수 있다.

## 도구 사용 디자인 패턴이란?

**도구 사용 디자인 패턴**은 LLM이 외부 도구와 상호작용하여 특정 목표를 달성할 수 있도록 하는 데 중점을 둡니다. 도구는 에이전트가 실행할 수 있는 코드로, 동작을 수행합니다. 도구는 계산기 같은 간단한 함수일 수도 있고, 주가 조회나 일기 예보 같은 서드파티 서비스의 API 호출일 수도 있습니다. AI 에이전트 맥락에서 도구는 **모델이 생성한 함수 호출**에 따라 에이전트가 실행하도록 설계됩니다.

## 적용 가능한 사용 사례는 무엇인가?

AI 에이전트는 도구를 활용해 복잡한 작업을 완료하거나 정보를 조회하거나 의사결정을 할 수 있습니다. 도구 사용 디자인 패턴은 데이터베이스, 웹 서비스, 코드 인터프리터 등 외부 시스템과 동적으로 상호작용해야 하는 시나리오에서 자주 사용됩니다. 주요 사용 사례는 다음과 같습니다:

- **동적 정보 조회:** 에이전트가 외부 API나 데이터베이스에 질의하여 최신 데이터를 가져옴 (예: SQLite 데이터베이스 조회, 주가나 날씨 정보 조회)
- **코드 실행 및 해석:** 수학 문제 해결, 보고서 생성, 시뮬레이션 수행을 위해 코드나 스크립트 실행
- **워크플로우 자동화:** 작업 스케줄러, 이메일 서비스, 데이터 파이프라인 등 도구를 통합해 반복적이거나 다단계 작업 자동화
- **고객 지원:** CRM 시스템, 티켓팅 플랫폼, 지식 베이스와 상호작용해 사용자 문의 해결
- **콘텐츠 생성 및 편집:** 문법 검사기, 텍스트 요약기, 콘텐츠 안전 평가 도구 등을 활용해 콘텐츠 제작 지원

## 도구 사용 디자인 패턴 구현에 필요한 요소/구성 블록은 무엇인가?

이 구성 블록들은 AI 에이전트가 다양한 작업을 수행할 수 있게 합니다. 도구 사용 디자인 패턴 구현에 필요한 주요 요소는 다음과 같습니다:

- **함수/도구 스키마**: 사용 가능한 도구에 대한 상세 정의로, 함수 이름, 목적, 필수 매개변수, 예상 출력 등을 포함합니다. 이 스키마는 LLM이 어떤 도구가 있는지 이해하고 유효한 요청을 구성할 수 있게 합니다.

- **함수 실행 로직**: 사용자 의도와 대화 맥락에 따라 도구를 언제 어떻게 호출할지 결정합니다. 플래너 모듈, 라우팅 메커니즘, 조건부 흐름 등이 포함될 수 있습니다.

- **메시지 처리 시스템**: 사용자 입력, LLM 응답, 도구 호출 및 도구 출력 간의 대화 흐름을 관리하는 구성 요소입니다.

- **도구 통합 프레임워크**: 단순 함수든 복잡한 외부 서비스든 에이전트와 도구를 연결하는 인프라입니다.

- **오류 처리 및 검증**: 도구 실행 실패 처리, 매개변수 검증, 예상치 못한 응답 관리 메커니즘입니다.

- **상태 관리**: 대화 맥락, 이전 도구 상호작용, 지속 데이터 등을 추적해 다중 턴 상호작용에서 일관성을 유지합니다.

다음으로 함수/도구 호출에 대해 좀 더 자세히 살펴보겠습니다.

### 함수/도구 호출

함수 호출은 LLM이 도구와 상호작용할 수 있게 하는 주요 방법입니다. '함수'와 '도구'는 종종 같은 의미로 사용되는데, 함수(재사용 가능한 코드 블록)가 에이전트가 작업을 수행하는 도구이기 때문입니다. 함수 코드를 호출하려면 LLM이 사용자의 요청을 함수 설명과 비교해야 합니다. 이를 위해 사용 가능한 모든 함수 설명을 담은 스키마가 LLM에 전달됩니다. LLM은 작업에 가장 적합한 함수를 선택하고 함수 이름과 인수를 반환합니다. 선택된 함수가 호출되고, 그 응답이 LLM에 전달되어 사용자의 요청에 답변하는 데 사용됩니다.

개발자가 에이전트용 함수 호출을 구현하려면 다음이 필요합니다:

1. 함수 호출을 지원하는 LLM 모델
2. 함수 설명을 담은 스키마
3. 각 함수에 대한 코드

도시의 현재 시간을 가져오는 예제로 설명해보겠습니다:

1. **함수 호출을 지원하는 LLM 초기화:**

    모든 모델이 함수 호출을 지원하는 것은 아니므로, 사용하는 LLM이 지원하는지 확인하는 것이 중요합니다. <a href="https://learn.microsoft.com/azure/ai-services/openai/how-to/function-calling" target="_blank">Azure OpenAI</a>는 함수 호출을 지원합니다. Azure OpenAI 클라이언트를 초기화하는 것으로 시작할 수 있습니다.

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
        azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT"), 
        api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
        api_version="2024-05-01-preview"
    )
    ```

1. **함수 스키마 생성:**

    다음으로 함수 이름, 함수가 수행하는 작업 설명, 함수 매개변수 이름과 설명을 포함하는 JSON 스키마를 정의합니다. 이 스키마를 앞서 생성한 클라이언트에 전달하고, 사용자의 샌프란시스코 시간 조회 요청과 함께 보냅니다. 중요한 점은 **도구 호출**이 반환된다는 것이며, 질문에 대한 최종 답변이 아닙니다. 앞서 언급했듯이, LLM은 작업에 선택한 함수 이름과 전달할 인수를 반환합니다.

    ```python
    # Function description for the model to read
    tools = [
        {
            "type": "function",
            "function": {
                "name": "get_current_time",
                "description": "Get the current time in a given location",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "location": {
                            "type": "string",
                            "description": "The city name, e.g. San Francisco",
                        },
                    },
                    "required": ["location"],
                },
            }
        }
    ]
    ```
   
    ```python
  
    # Initial user message
    messages = [{"role": "user", "content": "What's the current time in San Francisco"}] 
  
    # First API call: Ask the model to use the function
      response = client.chat.completions.create(
          model=deployment_name,
          messages=messages,
          tools=tools,
          tool_choice="auto",
      )
  
      # Process the model's response
      response_message = response.choices[0].message
      messages.append(response_message)
  
      print("Model's response:")  

      print(response_message)
  
    ```

    ```bash
    Model's response:
    ChatCompletionMessage(content=None, role='assistant', function_call=None, tool_calls=[ChatCompletionMessageToolCall(id='call_pOsKdUlqvdyttYB67MOj434b', function=Function(arguments='{"location":"San Francisco"}', name='get_current_time'), type='function')])
    ```
  
1. **작업 수행에 필요한 함수 코드:**

    이제 LLM이 실행할 함수를 선택했으므로, 해당 작업을 수행하는 코드를 구현하고 실행해야 합니다. 파이썬으로 현재 시간을 가져오는 코드를 작성할 수 있습니다. 또한 response_message에서 함수 이름과 인수를 추출해 최종 결과를 얻는 코드도 작성해야 합니다.

    ```python
      def get_current_time(location):
        """Get the current time for a given location"""
        print(f"get_current_time called with location: {location}")  
        location_lower = location.lower()
        
        for key, timezone in TIMEZONE_DATA.items():
            if key in location_lower:
                print(f"Timezone found for {key}")  
                current_time = datetime.now(ZoneInfo(timezone)).strftime("%I:%M %p")
                return json.dumps({
                    "location": location,
                    "current_time": current_time
                })
      
        print(f"No timezone data found for {location_lower}")  
        return json.dumps({"location": location, "current_time": "unknown"})
    ```

    ```python
     # Handle function calls
      if response_message.tool_calls:
          for tool_call in response_message.tool_calls:
              if tool_call.function.name == "get_current_time":
     
                  function_args = json.loads(tool_call.function.arguments)
     
                  time_response = get_current_time(
                      location=function_args.get("location")
                  )
     
                  messages.append({
                      "tool_call_id": tool_call.id,
                      "role": "tool",
                      "name": "get_current_time",
                      "content": time_response,
                  })
      else:
          print("No tool calls were made by the model.")  
  
      # Second API call: Get the final response from the model
      final_response = client.chat.completions.create(
          model=deployment_name,
          messages=messages,
      )
  
      return final_response.choices[0].message.content
     ```

    ```bash
      get_current_time called with location: San Francisco
      Timezone found for san francisco
      The current time in San Francisco is 09:24 AM.
     ```

함수 호출은 대부분의 에이전트 도구 사용 디자인의 핵심이지만, 처음부터 직접 구현하는 것은 때때로 어려울 수 있습니다. [Lesson 2](../../../02-explore-agentic-frameworks)에서 배운 것처럼, 에이전틱 프레임워크는 도구 사용을 구현할 수 있는 미리 만들어진 구성 블록을 제공합니다.

## 에이전틱 프레임워크를 활용한 도구 사용 예시

다음은 다양한 에이전틱 프레임워크를 사용해 도구 사용 디자인 패턴을 구현하는 방법의 예시입니다:

### Semantic Kernel

<a href="https://learn.microsoft.com/azure/ai-services/agents/overview" target="_blank">Semantic Kernel</a>은 .NET, Python, Java 개발자를 위한 오픈소스 AI 프레임워크로, LLM과 작업할 때 함수 호출 사용을 간소화합니다. 함수와 매개변수를 자동으로 모델에 설명하는 <a href="https://learn.microsoft.com/semantic-kernel/concepts/ai-services/chat-completion/function-calling/?pivots=programming-language-python#1-serializing-the-functions" target="_blank">직렬화</a> 과정을 통해 이를 수행합니다. 또한 모델과 코드 간의 상호작용을 처리합니다. Semantic Kernel 같은 에이전틱 프레임워크를 사용하면 <a href="https://github.com/microsoft/semantic-kernel/blob/main/python/samples/getting_started_with_agents/openai_assistant/step4_assistant_tool_file_search.py" target="_blank">파일 검색</a>이나 <a href="https://github.com/microsoft/semantic-kernel/blob/main/python/samples/getting_started_with_agents/openai_assistant/step3_assistant_tool_code_interpreter.py" target="_blank">코드 인터프리터</a> 같은 미리 만들어진 도구에 접근할 수 있다는 장점도 있습니다.

다음 다이어그램은 Semantic Kernel에서 함수 호출 과정을 보여줍니다:

![function calling](../../../translated_images/functioncalling-diagram.a84006fc287f60140cc0a484ff399acd25f69553ea05186981ac4d5155f9c2f6.ko.png)

Semantic Kernel에서 함수/도구는 <a href="https://learn.microsoft.com/semantic-kernel/concepts/plugins/?pivots=programming-language-python" target="_blank">플러그인</a>이라고 불립니다. 앞서 본 `get_current_time` 함수를 클래스 형태로 변환해 플러그인으로 만들 수 있습니다. 또한 함수 설명을 받는 `kernel_function` 데코레이터를 가져올 수 있습니다. 그런 다음 GetCurrentTimePlugin으로 커널을 생성하면, 커널이 자동으로 함수와 매개변수를 직렬화해 LLM에 보낼 스키마를 생성합니다.

```python
from semantic_kernel.functions import kernel_function

class GetCurrentTimePlugin:
    async def __init__(self, location):
        self.location = location

    @kernel_function(
        description="Get the current time for a given location"
    )
    def get_current_time(location: str = ""):
        ...

```

```python 
from semantic_kernel import Kernel

# Create the kernel
kernel = Kernel()

# Create the plugin
get_current_time_plugin = GetCurrentTimePlugin(location)

# Add the plugin to the kernel
kernel.add_plugin(get_current_time_plugin)
```
  
### Azure AI Agent Service

<a href="https://learn.microsoft.com/azure/ai-services/agents/overview" target="_blank">Azure AI Agent Service</a>는 개발자가 기본 컴퓨팅 및 저장소 리소스를 관리하지 않고도 고품질 확장 가능한 AI 에이전트를 안전하게 구축, 배포할 수 있도록 설계된 최신 에이전틱 프레임워크입니다. 특히 엔터프라이즈 애플리케이션에 적합하며, 완전 관리형 서비스로 엔터프라이즈급 보안을 제공합니다.

LLM API를 직접 사용하는 것과 비교했을 때 Azure AI Agent Service가 제공하는 장점은 다음과 같습니다:

- 자동 도구 호출 – 도구 호출 파싱, 도구 실행, 응답 처리 과정을 서버 측에서 자동으로 수행
- 안전하게 관리되는 데이터 – 대화 상태를 직접 관리하는 대신 스레드를 통해 필요한 모든 정보를 저장
- 즉시 사용 가능한 도구 – Bing, Azure AI Search, Azure Functions 등 데이터 소스와 상호작용할 수 있는 도구 제공

Azure AI Agent Service에서 제공하는 도구는 두 가지 범주로 나뉩니다:

1. 지식 도구:
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/bing-grounding?tabs=python&pivots=overview" target="_blank">Bing 검색 기반</a>
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/file-search?tabs=python&pivots=overview" target="_blank">파일 검색</a>
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/azure-ai-search?tabs=azurecli%2Cpython&pivots=overview-azure-ai-search" target="_blank">Azure AI Search</a>

2. 실행 도구:
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/function-calling?tabs=python&pivots=overview" target="_blank">함수 호출</a>
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/code-interpreter?tabs=python&pivots=overview" target="_blank">코드 인터프리터</a>
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/openapi-spec?tabs=python&pivots=overview" target="_blank">OpenAI 정의 도구</a>
    - <a href="https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/azure-functions?pivots=overview" target="_blank">Azure Functions</a>

Agent Service는 이러한 도구들을 `toolset`으로 함께 사용할 수 있게 하며, 특정 대화의 메시지 기록을 추적하는 `threads`를 활용합니다.

Contoso라는 회사의 영업 사원이라고 가정해 보겠습니다. 영업 데이터에 관한 질문에 답할 수 있는 대화형 에이전트를 개발하고자 합니다.

다음 이미지는 Azure AI Agent Service를 사용해 영업 데이터를 분석하는 방법을 보여줍니다:

![Agentic Service In Action](../../../translated_images/agent-service-in-action.34fb465c9a84659edd3003f8cb62d6b366b310a09b37c44e32535021fbb5c93f.ko.jpg)

이 서비스에서 도구를 사용하려면 클라이언트를 생성하고 도구 또는 도구 세트를 정의하면 됩니다. 실제 구현 예시는 다음 파이썬 코드를 참고하세요. LLM은 도구 세트를 보고 사용자 정의 함수 `fetch_sales_data_using_sqlite_query`를 사용할지, 미리 만들어진 코드 인터프리터를 사용할지 결정합니다.

```python 
import os
from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential
from fecth_sales_data_functions import fetch_sales_data_using_sqlite_query # fetch_sales_data_using_sqlite_query function which can be found in a fetch_sales_data_functions.py file.
from azure.ai.projects.models import ToolSet, FunctionTool, CodeInterpreterTool

project_client = AIProjectClient.from_connection_string(
    credential=DefaultAzureCredential(),
    conn_str=os.environ["PROJECT_CONNECTION_STRING"],
)

# Initialize function calling agent with the fetch_sales_data_using_sqlite_query function and adding it to the toolset
fetch_data_function = FunctionTool(fetch_sales_data_using_sqlite_query)
toolset = ToolSet()
toolset.add(fetch_data_function)

# Initialize Code Interpreter tool and adding it to the toolset. 
code_interpreter = code_interpreter = CodeInterpreterTool()
toolset = ToolSet()
toolset.add(code_interpreter)

agent = project_client.agents.create_agent(
    model="gpt-4o-mini", name="my-agent", instructions="You are helpful agent", 
    toolset=toolset
)
```

## 신뢰할 수 있는 AI 에이전트를 구축하기 위해 도구 사용 디자인 패턴을 사용할 때 고려해야 할 특별한 점은?

LLM이 동적으로 생성하는 SQL에 대한 일반적인 우려는 보안 문제, 특히 SQL 인젝션이나 데이터베이스 삭제 및 변조 같은 악의적 행위 위험입니다. 이러한 우려는 데이터베이스 접근 권한을 적절히 설정함으로써 효과적으로 완화할 수 있습니다. 대부분의 데이터베이스는 읽기 전용으로 구성하는 것이 일반적입니다. PostgreSQL이나 Azure SQL 같은 데이터베이스 서비스의 경우, 앱에 읽기 전용(SELECT) 역할을 할당해야 합니다.

앱을 안전한 환경에서 실행하는 것도 보호를 강화합니다. 엔터프라이즈 시나리오에서는 운영 시스템에서 데이터를 추출해 읽기 전용 데이터베이스나 데이터 웨어하우스로 변환하며, 사용자 친화적인 스키마를 적용합니다. 이 방식은 데이터 보안, 성능 및 접근성을 최적화하고, 앱에 제한적이고 읽기 전용 접근 권한을 부여합니다.

## 추가 자료

-

Azure AI Agents Service 워크숍</a>
- <a href="https://github.com/Azure-Samples/contoso-creative-writer/tree/main/docs/workshop" target="_blank">Contoso Creative Writer 다중 에이전트 워크숍</a>
- <a href="https://learn.microsoft.com/semantic-kernel/concepts/ai-services/chat-completion/function-calling/?pivots=programming-language-python#1-serializing-the-functions" target="_blank">Semantic Kernel 함수 호출 튜토리얼</a>
- <a href="https://github.com/microsoft/semantic-kernel/blob/main/python/samples/getting_started_with_agents/openai_assistant/step3_assistant_tool_code_interpreter.py" target="_blank">Semantic Kernel 코드 인터프리터</a>
- <a href="https://microsoft.github.io/autogen/dev/user-guide/core-user-guide/components/tools.html" target="_blank">Autogen 도구</a>

## 이전 강의

[에이전틱 디자인 패턴 이해하기](../03-agentic-design-patterns/README.md)

## 다음 강의

[에이전틱 RAG](../05-agentic-rag/README.md)

**면책 조항**:  
이 문서는 AI 번역 서비스 [Co-op Translator](https://github.com/Azure/co-op-translator)를 사용하여 번역되었습니다. 정확성을 위해 최선을 다하고 있으나, 자동 번역에는 오류나 부정확한 부분이 있을 수 있음을 유의해 주시기 바랍니다. 원문은 해당 언어의 원본 문서가 권위 있는 자료로 간주되어야 합니다. 중요한 정보의 경우 전문적인 인간 번역을 권장합니다. 본 번역의 사용으로 인해 발생하는 오해나 잘못된 해석에 대해 당사는 책임을 지지 않습니다.