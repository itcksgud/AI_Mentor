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

* 회원가입 후 로그인 시 관리자 여부 선택

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

