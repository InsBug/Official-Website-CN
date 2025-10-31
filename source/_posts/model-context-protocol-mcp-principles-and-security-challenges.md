---
title: 模型上下文协议（MCP）的原理与安全挑战
slug: model-context-protocol-mcp-principles-and-security-challenges
author: 裴伟伟
date: 2025-05-16 23:39
categories: AI安全
tags:
  - MCP
  - 模型上下文协议
  - 大模型
  - AI安全
  - 对抗攻击
---

随着大型语言模型（LLM）在各行业的广泛应用，如何高效、安全地将LLM与外部数据源和工具集成成为了关键问题。为此，Anthropic于2024年11月推出了模型上下文协议（Model Context Protocol，简称MCP），旨在为LLM提供一个标准化的接口，以便与外部系统进行交互。

## 什么是上下文（Context）

上下文是指在与LLM交互过程中，提供给模型且能够用于指导LLM理解、推理和生成响应的所有相关信息。这些信息包括但不限于：

\* 用户当前的查询或指令，比如用户直接输入的内容。

\* 对话历史，比如在多轮对话中的交互记录。

\* 外部知识，比如从数据库、文档库、API 或其他信息源检索到的特定数据。

\* 系统指令，这是指LLM预设的、用于定义模型角色、行为边界、输出格式等的指令，是用户不可见的。

\* 用户偏好和背景，比如用户使用的提示词语言等。

\* 任务特定数据，比如在代码生成任务中提供已有的代码片段或库文档。

\* 环境信息，比如如当前时间、地理位置（在获得授权的情况下）等。


上下文的质量和相关性直接影响 LLM 的输出质量。不准确、不完整或无关的上下文可能导致模型产生错误的、不连贯的甚至是有害的、无意义的回答。

## 什么是MCP？

早期的LLM应用（如简单的问答机器人）对上下文管理的要求相对简单。然而，随着 LLM 被用于更复杂的场景，如：

1. 企业级知识库问答： 需要整合海量的、动态更新的内部文档和数据。

2. 个性化智能助理： 需要理解并记忆用户的长期偏好、习惯和历史互动。

3. 多模态交互： 上下文不仅包含文本，还可能包括图像、音频、视频等。

4. Agentic AI 系统： LLM作为核心控制器，需要与其他工具、API 和服务进行复杂的交互，每一步交互都会产生和消耗上下文。

这些复杂应用对上下文管理的精细度、效率、可靠性、可控性都提出了远超以往的要求。

而要满足这些要求，在MCP出现前只有一种办法，就是调用各家的大语言模型接口，通过开发实现自己想要的效果，也就是，LLM与外部系统的集成通常需要为每个数据源或工具编写特定的集成代码，这样的做法是开发成本高、维护复杂。MCP的出现就是为了解决这一问题，通过定义一套通用的协议，使得LLM可以以标准化的方式与各种外部系统进行交互，从而简化开发流程，提高系统的可扩展性和互操作性。

所以，简单而言，模型上下文协议（MCP）是一种开放标准，旨在为LLM提供一个统一的接口，使其能够与外部数据源和工具进行交互。通过MCP，LLM可以在生成过程中动态地获取所需的上下文信息或执行特定的操作，从而增强其功能和适应性。MCP的设计理念类似于USB-C接口，为AI模型提供了一个通用的连接方式。

## MCP的工作原理

MCP的核心在于建立一个标准化的通信层，使得LLM能够在处理用户请求或执行任务时，如果需要访问外部信息或功能，可以通过MCP客户端向MCP服务器发送请求。MCP服务器则负责与相应的外部数据源或工具进行交互，获取数据并按照MCP协议规范进行格式化，最后将格式化后的数据返回给LLM。

这种机制使得LLM能够在生成过程中动态地获取所需的上下文信息或执行特定的操作，从而增强其功能和适应性。例如，当LLM需要获取最新的天气信息时，它可以通过MCP客户端向MCP服务器发送请求，MCP服务器则调用相应的天气API获取数据，并将结果返回给LLM。

MCP采用客户端-服务器架构，主要包括以下三个角色：

1. 主机（Host）：通常是LLM应用程序，如Claude Desktop或集成开发环境（IDE），负责发起与MCP服务器的连接。

2. 客户端（Client）：位于主机应用程序内部，负责维护与MCP服务器的连接，处理通信协议、消息格式和状态管理，确保LLM与服务器之间的可靠、安全通信。

3. 服务器（Server）：提供上下文、工具和提示等资源，响应来自客户端的请求，与外部系统进行交互，并将结果返回给客户端。

这种架构使得LLM可以通过标准化的方式与各种外部系统进行交互，简化了开发流程，提高了系统的可扩展性和互操作性。

我们以获取当前城市天气的示例说明MCP的通信过程：

1. 识别需求：LLM在生成过程中识别出需要获取当前城市的天气信息。

2. 构建请求：LLM通过MCP客户端构建一个结构化的请求，指定需要调用的函数（如getWeatherAdvice）和相关参数。

3. 发送请求：MCP客户端将请求发送给MCP服务器。

4. 处理请求：MCP服务器接收到请求后，调用相应的外部系统（如天气API）获取当前城市的天气信息或出行建议（如果服务器的设定的功能是如此）。

5. 返回结果：MCP服务器将获取的天气信息和出行建议按照协议规范格式化后返回给MCP客户端。

6. 继续生成：LLM接收到当前天气信息后，将其融入到生成过程中，继续完成响应。

通过这种方式，LLM可以在生成过程中动态地获取所需的上下文信息或执行特定的操作，从而增强其功能和适应性，由此，MCP的能力相比与RAG的外部知识增强而言，具有更好的动态数据获取能力以及生成过程的一致性，同时可以通过MCP协议很好的控制外部数据的输入，从而规避潜在的隐私风险和安全风险。

## 查询天气的MCP示例

以上面的通信过程为例，下面是基于天气信息通过MCP协议与ChatGPT的gpt-3.5-turbo模型通信，根据天气信息给出对应的出行建议。

``` Python
import os
# Set Chinese Characteristic Proxy
os.environ["http_proxy"] = "socks5h://127.0.0.1:7890"
os.environ["https_proxy"] = "socks5h://127.0.0.1:7890"

from flask import Flask, request, jsonify
import requests
from openai import OpenAI

app = Flask(__name__)

# Initializing OpenAI Client
client = OpenAI(api_key="YOUR_API_KEY")

# Set OpenWeatherMap API Key
OPENWEATHER_API_KEY = "YOUR_API_KEY"

@app.route("/mcp", methods=["POST"])
def mcp_handler():
    try:
        data = request.get_json()
        method = data.get("method")
        params = data.get("params", {})
        request_id = data.get("id")

        if method == "getWeatherAdvice":
            city = params.get("city")
            country = params.get("country", "CN")

            ifnot city:
                return jsonify({
                    "jsonrpc": "2.0",
                    "error": {"code": -32602, "message": "Missing 'city' parameter."},
                    "id": request_id
                })

            # Get Weather Data
            weather = fetch_weather(city, country)
            ifnot weather:
                return jsonify({
                    "jsonrpc": "2.0",
                    "error": {"code": -32001, "message": "Failed to retrieve weather data."},
                    "id": request_id
                })

            # Generate Wearing Advice
            advice = generate_advice(weather)

            return jsonify({
                "jsonrpc": "2.0",
                "result": {
                    "weather": weather,
                    "advice": advice
                },
                "id": request_id
            })
        else:
            return jsonify({
                "jsonrpc": "2.0",
                "error": {"code": -32601, "message": f"Method '{method}' not found."},
                "id": request_id
            })
    except Exception as e:
        return jsonify({
            "jsonrpc": "2.0",
            "error": {"code": -32603, "message": f"Internal error: {str(e)}"},
            "id": None
        })

def fetch_weather(city, country):
    try:
        url = f"http://api.openweathermap.org/data/2.5/weather"
        params = {
            "q": f"{city},{country}",
            "appid": OPENWEATHER_API_KEY,
            "units": "metric",
            "lang": "zh_cn"
        }
        response = requests.get(url, params=params)
        
        if response.status_code != 200:
            print(f"Error: API returned status code {response.status_code}")
            return None
            
        data = response.json()
        weather = {
            "description": data["weather"][0]["description"],
            "temperature": data["main"]["temp"],
            "humidity": data["main"]["humidity"],
            "wind_speed": data["wind"]["speed"]
        }
        return weather
    except Exception as e:
        print(f"Error in fetch_weather: {str(e)}")
        return None

def generate_advice(weather, max_retries=3, retry_delay=1):
    prompt = (
        f"当前天气状况为：{weather['description']}，气温：{weather['temperature']}°C，"
        f"湿度：{weather['humidity']}%，风速：{weather['wind_speed']} m/s。"
        "请根据以上信息，提供适合的穿着建议，并说明是否需要携带雨具。"
    )

    try:
        response = client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "你是一位贴心的穿搭顾问。"},
                {"role": "user", "content": prompt}
            ],
            temperature=0.7,
            max_tokens=300
        )

        # Get Advice from ChatGPT
        advice = response.choices[0].message.content.strip()
        ifnot advice:
            return"无法生成有效的穿着建议。"

        return advice

    except Exception as e:
        error_message = str(e)
        print(f"Error in generate_advice: {error_message}")
        return"无法生成穿着建议，请稍后再试。"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

上面代码是示例的MCP服务器程序，通过Json RPC 2.0格式的请求，客户端可以通过类似下面的请求根据城市获得对应的天气信息以及相关的出行建议：

``` Bash
curl -X POST http://localhost:5000/mcp \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getWeatherAdvice",
    "params": {
      "city": "Shanghai",
      "country": "NC"
    },
    "id": 1
  }'
```

## MCP的安全风险与漏洞

尽管MCP为LLM与外部系统的集成提供了极大的灵活性与拓展能力，但正是这种开放架构，也为攻击者提供了更多的攻击面和渗透路径。除了LLM自身的提示注入、隐私泄露等传统问题外，引入第三方MCP工具、服务和连接通道，极可能导致新的安全风险爆发。

以下是当前MCP环境下较为关键的安全隐患：

1. 提示词注入（Prompt Injection）

提示注入问题在MCP上下文中尤为严重。攻击者可能借助用户输入或上下文伪装，诱导LLM生成包含危险行为的工具调用指令（如调用系统命令、重启服务、提取敏感信息）。MCP客户端若无相应过滤机制，便可能将这些危险请求直接传送给MCP服务器或后端工具，造成权限越界操作。

2. 工具投毒（Tool Poisoning）

由于MCP的工具注册和调用机制较为自由，攻击者可能冒充或注入伪造的MCP服务节点，诱导LLM将数据发送至恶意工具，进而窃取数据、修改上下文、影响生成逻辑。尤其当LLM支持从工具响应中继续推理时，攻击者可能通过返回“伪造数据”改变LLM的认知，造成响应欺骗或“引导式谬误”。

3. 命令注入（Command Injection）

若MCP服务器在处理请求参数（如城市名、路径、脚本名）时直接拼接至 Shell 命令或脚本，而没有进行足够的转义和校验，攻击者就可以注入命令片段，造成系统级别入侵。

4. 权限提升（Privilege Escalation）

攻击者可以尝试通过MCP接口访问原本只有管理员才能使用的工具（如数据库控制台、远程重启接口）。如果MCP缺乏严格的权限控制机制或访问隔离逻辑，普通请求可能借助LLM生成的上下文间接获得高权限操作通道。

5. 缺乏认证机制

目前MCP设计阶段未对通信双方（MCP客户端与MCP服务器）定义统一的身份认证协议。攻击者若能嗅探通信通道或中间人伪造MCP响应，则可能实现身份伪造、数据篡改、会话劫持等攻击。

6. 缓存污染（Cache Posioning）

若MCP服务带有缓存逻辑，攻击者可能先通过低权限请求污染缓存，再诱导LLM请求相同资源，使其读取到被操纵的“假数据”。

7. 工具调用滥用（Abuse of External Tools）

即便是合法的工具，在缺乏频率限制与上下文审查的情况下，也可能被攻击者利用造成DoS（如频繁调用搜索API）、信息泄露（调用未授权API返回大量数据）或经济损失（调用计费API服务）。

## 结语

模型上下文协议（MCP）是连接大型语言模型与外部世界的重要桥梁，它带来了令人振奋的动态智能能力，但同时也打开了一扇新的攻防大门。和所有新兴技术一样，安全问题往往不是在设计时暴露，而是在广泛部署与多方调用之后集中爆发。随着LLM能力持续增强，MCP也将承担越来越多高权限、跨平台的系统调用任务——它不再只是“数据增强协议”，更是AI系统的“执行接口”。也正因此，保障MCP协议的完整性、通信安全、调用合规性，是构建可信AI系统的关键一环。