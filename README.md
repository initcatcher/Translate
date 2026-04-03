# Translate Extension

로컬 LLM(gemma4-heretic)을 활용한 Chrome 번역 확장. 구글 번역보다 자연스러운 문맥 기반 번역을 제공합니다.

[djosix/Translate](https://github.com/djosix/Translate)를 fork하여 일본어/영어 → 한국어 번역에 최적화했습니다.

<img width="1040" alt="screenshot" src="https://github.com/user-attachments/assets/fdb7ead8-1663-43c8-ac3e-ecd9a7dc14ca">

## Features

- **로컬 LLM 번역** — Ollama + gemma4-heretic으로 문맥을 이해하는 자연스러운 번역
- **페이지 내 번역** — 텍스트 선택 후 플로팅 버튼 클릭으로 바로 번역
- **팝업 번역** — 확장 아이콘 클릭 후 텍스트 입력으로 실시간 번역
- **프라이버시** — 모든 번역이 로컬에서 처리, 외부 서버로 데이터 전송 없음
- **다중 백엔드 지원** — Ollama(기본), Google, OpenAI, DeepL

---

## 빠른 시작 (Quick Start)

### 1. Ollama 설치

아직 Ollama가 없다면:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 2. gemma4-heretic 모델 다운로드

```bash
ollama pull gemma4-heretic
```

> 모델 크기: ~16GB. RTX 4090(24GB VRAM) 기준 여유롭게 동작합니다.
> VRAM이 부족한 경우 `ollama pull gemma4:E2B` (7.2GB)를 대신 사용하고, 확장 설정에서 모델명을 변경하세요.

### 3. Ollama CORS 설정

Chrome 확장에서 Ollama API에 접근하려면 CORS 허용이 필요합니다.

#### Linux (systemd)

```bash
sudo systemctl edit ollama
```

편집기가 열리면 아래 내용을 입력하고 저장:

```ini
[Service]
Environment="OLLAMA_ORIGINS=*"
```

적용:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

#### macOS

```bash
launchctl setenv OLLAMA_ORIGINS '*'
```

설정 후 Ollama 앱을 재시작하세요.

#### Windows

1. 시스템 환경 변수 편집 열기 (시작 메뉴에서 "환경 변수" 검색)
2. 사용자 변수에 새로 추가:
   - 변수 이름: `OLLAMA_ORIGINS`
   - 변수 값: `*`
3. Ollama 재시작

#### CORS 설정 확인

```bash
curl -s http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -H "Origin: chrome-extension://test" \
  -d '{"model":"gemma4-heretic","prompt":"Hi","stream":false}' \
  | head -c 200
```

응답이 정상적으로 오면 CORS 설정 완료입니다.

### 4. Chrome 확장 설치

1. 이 저장소를 클론합니다:
   ```bash
   git clone https://github.com/initcatcher/Translate.git
   ```
2. Chrome에서 `chrome://extensions` 열기
3. 우측 상단 **개발자 모드** 켜기
4. **"압축해제된 확장 프로그램을 로드합니다"** 클릭
5. 클론한 `Translate` 폴더 선택

### 5. 사용하기

- **텍스트 선택 번역**: 웹페이지에서 텍스트를 드래그하면 "Tr" 버튼이 뜹니다. 클릭하면 번역 결과가 툴팁으로 표시됩니다.
- **팝업 번역**: 확장 아이콘을 클릭하면 번역 창이 열립니다. 텍스트를 입력하면 실시간으로 번역됩니다.

---

## 설정 변경

확장 아이콘 클릭 → **Settings**에서 변경할 수 있습니다:

| 설정 | 기본값 | 설명 |
|------|--------|------|
| Backend | Ollama | 번역 엔진 선택 |
| Target Language | Korean | 번역 대상 언어 |
| API URL | `http://localhost:11434/api/generate` | Ollama API 주소 |
| Model | `gemma4-heretic` | 사용할 모델 |
| Prompt | (내장 프롬프트) | 번역 지시 프롬프트 |

다른 모델을 쓰고 싶다면 Settings에서 Model 필드만 변경하면 됩니다.

---

## 다른 백엔드 사용

Ollama 외에 Google, OpenAI, DeepL 백엔드도 지원합니다.

### Google

기본 내장 키가 작동하면 별도 설정 불필요.

### OpenAI

1. [API 키 발급](https://platform.openai.com/docs/quickstart/create-and-export-an-api-key)
2. Settings → OpenAI 섹션에 API 키 입력

### DeepL

1. [API 키 발급](https://www.deepl.com/en/your-account/keys)
2. Settings → DeepL 섹션에 API 키 입력

---

## 트러블슈팅

| 증상 | 원인 | 해결 |
|------|------|------|
| "Failed to connect to Ollama" | Ollama가 실행 중이지 않음 | `ollama serve` 또는 `systemctl start ollama` |
| "Failed to connect to Ollama" | CORS 미설정 | 위의 CORS 설정 섹션 참고 |
| 번역이 너무 느림 | 모델이 너무 크거나 GPU 미사용 | `ollama ps`로 GPU 사용 확인. VRAM 부족 시 작은 모델로 변경 |
| 번역 결과에 설명이 포함됨 | 프롬프트 문제 | Settings에서 프롬프트 끝에 "Output only the translated text:" 확인 |

---

## Credits

- Original: [djosix/Translate](https://github.com/djosix/Translate)
- Model: [gemma4-heretic](https://ollama.com/library/gemma4-heretic)
