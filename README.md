# SKKU OpenAI Compatible API 사용 가이드

SKKU OpenAI Compatible API를 **코드(curl / Python)**에서 사용하는 방법을 정리한 문서입니다.

## 목차

1. [사전 준비](#1-사전-준비)
2. [사용 가능 모델](#2-사용-가능-모델)
3. [API 직접 호출 (curl / Python)](#3-api-직접-호출-curl--python)

---

## 1. 사전 준비

| 항목       | 내용                                                         |
| ---------- | ------------------------------------------------------------ |
| API Key    | SKKU GPT에서 발급받은 개인 API Key (`x-api-key` 헤더로 전달) |
| 엔드포인트 | OpenAI 호환 Chat Completions (`/chat/completions`)           |

> ⚠️ API Key는 개인 자격증명입니다. 스크린샷·코드·저장소에 노출되지 않도록 주의하세요.

---

## 2. 사용 가능 모델

요청 본문의 `model` 필드에 아래 모델 ID를 그대로 입력합니다.

| 제공사 | 모델 ID         |
| ------ | --------------- |
| OpenAI | `gpt-6.1-sol`   |
| OpenAI | `gpt-6-astra`   |
| OpenAI | `gpt-6-luna`    |
| OpenAI | `gpt-6.1-terra` |
| OpenAI | `gpt-5.6-terra` |

### 공식 문서

요청/응답 포맷과 지원 필드는 OpenAI 공식 문서를 기준으로 확인합니다.

- [Chat Completions API Reference](https://platform.openai.com/docs/api-reference/chat)

> SKKU API 사용 시에는 OpenAI 원본 주소(`https://api.openai.com/v1/`)가 아닌 SKKU 엔드포인트와 `x-api-key`를 사용합니다.

---

## 3. API 직접 호출 (curl / Python)

- Endpoint: `https://kingogpt.skku.edu/agent/openai_compatible/a1/v1/chat/completions`
- 인증 헤더: `x-api-key: {API_KEY}`
- 요청/응답 포맷: OpenAI Chat Completions 호환

### 3-1. curl (스트리밍)

```bash
curl --location 'https://kingogpt.skku.edu/agent/openai_compatible/a1/v1/chat/completions' \
--header 'x-api-key: {API_KEY}' \
--header 'Content-Type: application/json' \
--data '{
  "model": "gpt-5.6-terra",
  "messages": [
    {
      "role": "user",
      "content": "나는 robiG 야. 내이름을 기억해"
    }
  ],
  "stream": true
}'
```

### 3-2. Python (`requests`)

```python
import requests
import json

# ==========================================
# SKKU GPT API 설정
# ==========================================

BASE_URL = "https://kingogpt.skku.edu/agent/openai_compatible/a1/v1"
X_API_KEY = ""  # 발급받은 API Key 입력
HEADERS = {
    "X-Api-Key": f"{X_API_KEY}",
    "Content-Type": "application/json"
}

# ==========================================
# GPT 모델 호출
# ==========================================

payload = {
    "model": "gpt-6.1-sol",
    "messages": [
        {
            "role": "user",
            "content": "안녕하세요. 간단히 자기소개해 주세요."
        }
    ],
    "stream": False
}

response = requests.post(
    f"{BASE_URL}/chat/completions",
    headers=HEADERS,
    json=payload,
    timeout=60
)

print("HTTP Status:", response.status_code)

try:
    result = response.json()
    print(json.dumps(result, indent=2, ensure_ascii=False))

    if response.status_code == 200 and "choices" in result:
        print("\n✅ OpenAI Compatible API 정상 작동")
        print("AI 응답:", result["choices"][0]["message"]["content"])
    else:
        print("\n❌ API 응답 확인 필요")

except Exception as e:
    print("오류:", e)
    print(response.text)
```

실행: 위 코드를 `llm.py`로 저장한 뒤 `pip install requests && python llm.py`
