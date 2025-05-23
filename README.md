# 🧠 Open WebUI + Pipeline + Docker 연동 가이드

## ✅ 4.25 진행사항

* 파이프라인을 통해 GPT와 연결하는 데 성공했습니다.
* `GPT API KEY` 값을 수정하면 정상적으로 작동합니다.

---

## ✅ 5.23 Docker 기반 Open WebUI + Pipeline 연동 방법

### 1. PowerShell 실행

```bash
powershell
```

### 2. Open WebUI 이미지 다운로드

```bash
docker pull ghcr.io/open-webui/open-webui:main
```

### 3. Open WebUI 컨테이너 실행

```bash
docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

### 4. Pipeline 컨테이너 실행

```bash
docker run -d \
  -p 9099:9099 \
  --add-host=host.docker.internal:host-gateway \
  -v pipelines:/app/pipelines \
  --name pipelines \
  --restart always \
  ghcr.io/open-webui/pipelines:main
```

### 5. 브라우저에서 Open WebUI 접속

* 접속 주소: [http://localhost:3000](http://localhost:3000)

### 6. 관리자 계정 생성

* 회원가입

### 7. 관리자 패널 → 설정 → 연결

* **OpenAI API 연결 관리** 탭에서 ➕ 버튼 클릭

  * **URL**: `http://host.docker.internal:9099`
  * **키**: `0p3n-w3bu!`

### 8. 관리자 패널 → 설정 → 파이프라인

* **"업로드 파이프라인"** 버튼 클릭

  * `.py` 형식의 커스텀 Pipeline 스크립트 업로드
  * 업로드 후 모델 목록에서 사용 가능

---

## 🔁 결과

* Open WebUI에서 모델 선택 시 업로드한 파이프라인이 `Pipeline Example` 등의 이름으로 표시됩니다.
* 이후 사용자 입력이 파이프라인을 통해 Flask 또는 외부 API와 연동되어 처리됩니다.

---

## 🔧 Open WebUI Pipeline 구성 요소 정리

### ✅ 구성요소 역할

| 구성 요소    | 설명                                               |
| -------- | ------------------------------------------------ |
| `pipe`   | 외부 시스템(예: OpenAI API)과의 연결 수행.                   |
| `filter` | 메시지 전/후 처리. `inlet`, `outlet` 훅으로 처리 가능.         |
| `valves` | 사용자 설정 변수. 파이프라인 UI에서 설정 가능 (예: API Key, URL 등). |

---

### ✅ pipe 함수의 리턴 처리

* `stream=False`일 경우:
  `return` 값은 일반 JSON 형식이며, 다음처럼 응답됨:

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1677858242,
  "model": "gpt-3.5-turbo",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "나와라 얍"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 12,
    "total_tokens": 21
  }
}
```

* 따라서 **`choices[0].message.content` 값이 출력 결과로 사용됨.**

---

### ⚠️ stream 출력 관련

* `stream=True`일 경우 추가 분석 필요.*

---

### 💬 채팅 제어 옵션

* `채팅 제어 > 스트림 채팅 응답 > 끄기` 설정 가능.

---

## 💡 pipe 함수로 들어오는 payload 예시

```json
{
  "stream": true,
  "model": "gpt-3.5-turbo",
  "messages": [
    {"role": "user", "content": "ㅎㅇㅎㅇㅎㅇㅎㅇㅎㅇ"},
    {"role": "assistant", "content": "안녕하세요! 무엇을 도와드릴까요? 😊"},
    {"role": "user", "content": "ㄴㄴ 도와주지마"},
    {"role": "assistant", "content": "알겠습니다. 필요하신 건 있으시면 언제든지 말씀해주세요! 😊"},
    {"role": "user", "content": "ㅇㅋ"}
  ]
}
```

```json
{
  "stream": true,
  "model": "gpt-3.5-turbo",
  "messages": [
    {"role": "user", "content": "ㅎㅇ"}
  ]
}
```

```json
{
  "stream": true,
  "model": "gpt-3.5-turbo",
  "messages": [
    {"role": "user", "content": "ㅇㅇㅇㅇㅇ"},
    {"role": "assistant", "content": "안녕하세요! 도움이 필요하신게 있으신가요? 부담없이 물어보세요."},
    {"role": "user", "content": "왜 안나오는거야"},
    {"role": "assistant", "content": "죄송합니다, 저도 가끔씩 잘못된 응답을 할 수 있어요. 무엇을 도와드릴까요? 부연 설명을 해 주시면 더 도와드릴 수 있을 것 같아요."},
    {"role": "user", "content": "."}
  ]
}
```


