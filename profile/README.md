# HumanLearning

AI 코딩 에이전트와 함께 만든 프로젝트인데, 정작 그 코드를 쓴 사람이 왜 이렇게 만들었는지 설명하지 못하는 일이 많습니다. HumanLearning은 에이전트가 개발 중에 내린 설계·구현·검증 결정을 같은 형식의 기록으로 남기게 하고, 그 기록으로 프로젝트 교과서와 기술 교과서를 만듭니다. 개발자는 결정마다 에이전트가 무엇을 골랐을지 먼저 예측하고, 이유를 읽고, 이해한 만큼 표시하며 자기 프로젝트를 설명할 수 있게 됩니다.

2026 Uni-DTHON 해커톤(주제: AI 시대의 격차를 줄이는 기술)에서 만든 프로젝트입니다.

| 저장소 | 내용 |
| --- | --- |
| [Frontend](https://github.com/2026-UniD-Hackathon-Team4/Frontend) | 웹 앱 (React, Vite) |
| [Backend](https://github.com/2026-UniD-Hackathon-Team4/Backend) | REST API (Spring Boot), 결정 기록 스킬(`skill/human-learning/`), 통합 배포 구성(`deploy/`) |

두 저장소의 `human-learning/` 폴더에는 HumanLearning을 만들면서 이 스킬로 남긴 결정 기록이 들어 있습니다.

## 사용 흐름

1. 프로젝트에 `human-learning` 스킬을 설치합니다. Claude Code와 Codex가 같은 스킬을 읽습니다.
2. 개발하는 동안 에이전트가 실제 선택지가 둘 이상이던 결정마다 프로젝트 루트의 `human-learning/NNNN-slug.md`에 기록을 씁니다. 기록에는 상황, 검토한 선택지(2~4개)와 장단점, 고른 이유, 얻은 것과 포기한 것, 관련 파일과 줄 범위, 검증한 것과 하지 않은 것, 수준(설계·구현·검증), 중요도, 쓰인 기술과 개념이 들어갑니다.
3. 기록을 앱으로 보냅니다. 터미널에서 스킬의 `sync.mjs`를 실행하거나, 앱의 `프로젝트 불러오기`에서 프로젝트 폴더를 고릅니다. 둘 다 같은 `POST /api/sync` 요청을 보냅니다. 형식에 맞지 않는 기록은 거절 코드와 함께 알려 주고 나머지만 저장합니다.
4. 서버가 기록으로 프로젝트 교과서(프로젝트 목표, 전체 구조, 결정 지도), 기록에 나온 기술마다 기술 교과서(개념 카드), 결정마다 퀴즈를 만듭니다.
5. 사용자는 결정 문서에서 상황과 선택지를 보고 에이전트의 선택을 한 번 예측합니다. 예측한 뒤에 선택, 이유, 트레이드오프, 프로젝트의 코드, 검증 여부가 열립니다.
6. 결정과 개념마다 이해 정도를 표시하고, 원하면 퀴즈를 풉니다. 남은 결정 수와 이해 부채가 함께 바뀝니다.

## 주요 기능

| 기능 | 내용 |
| --- | --- |
| 결정 기록 스킬 | 정해진 형식의 Markdown 기록(YAML front matter와 여섯 절). 한 파일 4,000자 이하 |
| 동기화 | 명령 한 줄 또는 브라우저에서 폴더 선택. 같은 기록을 다시 보내면 바뀌지 않고, 바뀐 기록은 갱신되며 학습 진행은 남습니다 |
| 프로젝트 교과서 | 프로젝트 목표와 전체 구조, 설계·구현·검증으로 나눈 결정 지도. 다른 결정으로 대체된 결정은 지우지 않고 표시합니다 |
| 결정 문서 | 예측 한 번, 그다음 선택과 이유, 얻은 것과 포기한 것, 기록이 가리키는 코드 줄, 검증한 것과 하지 않은 것 |
| 이해 부채 | 아직 이해하지 못한 결정의 중요도 합계(핵심 3, 보통 1, 사소 0). 프로젝트마다 남은 결정 수와 함께 보여 줍니다 |
| 기술 교과서 | 기록에 나온 기술마다 한 권, 영역별로 묶음. 개념 카드(Mermaid 도식), 공식 문서 링크, 그 기술을 쓴 결정으로 가는 링크, 다음에 읽으면 좋은 개념 |
| 퀴즈 | 결정마다 객관식 2문항과 자기 설명 1문항. 자기 설명은 채점하지 않고 모범 답안과 빠진 항목을 보여 줍니다. 잘못된 문항은 신고하면 건너뜁니다 |
| 퀴즈 관문 | 설정에서 켭니다(기본 꺼짐). 켜면 아직 열어 보지 않은 결정 가운데 위에 아직 이해하지 못한 핵심 결정이 있는 결정이 잠기고, 그 핵심 결정의 퀴즈를 마쳐야 열립니다 |
| 내용 생성 | Anthropic API 키와 모델을 주면 LLM이 프로젝트 개요, 기술 교과서, 퀴즈, 자기 설명의 피드백을 씁니다. 없거나 호출이 실패하면(30초 안에 답이 없을 때 포함) 기록으로 만든 템플릿을 씁니다 |
| 계정 | 이메일과 비밀번호로 가입합니다. 사용자는 자기 프로젝트와 학습 진행만 봅니다 |

## 구조

| 영역 | Frontend | Backend |
| --- | --- | --- |
| 언어와 런타임 | TypeScript 5.9, React 19 | Java 21, Spring Boot 4.1 |
| 빌드 | Vite 7, pnpm | Gradle (Kotlin DSL, 래퍼 포함) |
| UI와 데이터 | Tailwind CSS 4, shadcn/ui, React Router 7, TanStack Query 5, Mermaid 12 | Spring Web MVC, Spring Data JPA, Bean Validation |
| API | openapi-typescript로 생성한 타입과 openapi-fetch | springdoc OpenAPI (`/v3/api-docs`, `/swagger-ui.html`), 오류는 RFC 9457 `application/problem+json` |
| 인증 | access token 하나를 `localStorage`에 저장 | Spring Security(stateless), JWT HS256(jjwt), BCrypt |
| 저장소 | | H2 파일 DB(기본), PostgreSQL(`postgres` 프로파일) |
| 테스트 | Vitest 4, Testing Library, MSW 2 | JUnit, `@SpringBootTest`와 MockMvc |
| 컨테이너 | `node:24-alpine`에서 빌드, `nginx:1.30-alpine`으로 서빙 | `eclipse-temurin:21-jdk`에서 빌드, `eclipse-temurin:21-jre`로 실행 |

아래 그림은 Docker Compose로 배포했을 때의 구성입니다.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/deployment-dark.png">
  <img src="assets/deployment.png" alt="HumanLearning 배포 구성도. 브라우저의 요청은 공개 포트 HL_HTTP_PORT(기본 8088)로 fe 컨테이너의 nginx에 닿고, nginx는 /api/ 요청을 be 컨테이너의 Spring Boot(포트 8080, 외부 비공개)로 넘기며, Spring Boot는 볼륨 be-data의 H2 파일 DB에 저장합니다. 개발자의 프로젝트에서는 코딩 에이전트(Claude Code, Codex)가 human-learning/*.md 결정 기록을 쓰고, sync.mjs가 그 기록을 읽어 POST /api/sync로 nginx에 보냅니다. Spring Boot는 API 키와 모델이 있을 때만 Anthropic API를 부릅니다. 두 컨테이너는 GitHub의 Frontend와 Backend 저장소에서 docker compose up -d --build로 빌드합니다." width="872">
</picture>

<sub>아이콘: svgl(nginx, Node.js, Docker, GitHub, Anthropic), Lucide, spring.io(Spring Boot), h2database/h2database(H2). 로고의 상표권은 각 소유자에게 있습니다. 그림의 원본은 [`assets/deployment.html`](assets/deployment.html)이며 stack-diagram으로 만들었습니다.</sub>

## 통합 배포 (Docker Compose)

`Backend` 저장소의 `deploy/compose.yaml`이 호스트 한 대에서 두 저장소를 빌드하고 함께 띄웁니다. `fe` 컨테이너(nginx)가 웹 앱을 서빙하고 `/api/`를 `be` 컨테이너로 넘깁니다. `be`는 포트를 공개하지 않아 compose 네트워크 안에서만 닿습니다. 두 이미지는 Docker 안에서 빌드되므로 호스트에 Java와 Node.js는 필요 없습니다.

1. 준비: compose 플러그인이 있는 Docker(`docker compose` 명령)와 git.
2. 빈 폴더에서 두 저장소를 `be`, `fe`라는 이름으로 나란히 클론합니다. compose 파일은 `be/deploy/`에서 `..`(백엔드)와 `../../fe`(프론트엔드)를 빌드합니다.

   ```bash
   git clone https://github.com/2026-UniD-Hackathon-Team4/Backend.git be
   git clone https://github.com/2026-UniD-Hackathon-Team4/Frontend.git fe
   ```

   ```text
   <작업 폴더>/
   ├── be/
   │   └── deploy/
   │       ├── compose.yaml
   │       └── .env.example
   └── fe/
   ```

3. 환경 변수 파일을 만들고 값을 채웁니다. `be/deploy/.env`는 git이 무시하는 파일이고, 절대 커밋하지 않습니다. 같은 이름을 셸에서 export해도 됩니다.

   ```bash
   cp be/deploy/.env.example be/deploy/.env
   ```

   | 변수 | 필수 여부 | 뜻 |
   | --- | --- | --- |
   | `JWT_SECRET` | 필수 | 로그인 토큰의 서명 키, 32바이트 이상. 예: `<32자 이상의 임의 문자열>`(`openssl rand -base64 48`로 만들 수 있음). 없으면 compose가 시작을 거부합니다 |
   | `HL_ALLOWED_ORIGINS` | 선택, 기본 `http://localhost:5173` | CORS가 허용할 출처, 쉼표로 구분. 앱을 여는 주소로 둡니다. 예: `http://localhost:8088`, 리버스 프록시 뒤라면 `https://<공개 도메인>`(경로 없이). 예시 파일의 값은 예시 도메인이므로 바꿉니다 |
   | `HL_HTTP_PORT` | 선택, 기본 `8088` | `fe`의 호스트 포트. 유일하게 공개되는 포트입니다 |
   | `JWT_EXPIRES_IN_SECONDS` | 선택, 기본 `86400` | 토큰 만료 시간(초) |
   | `ANTHROPIC_API_KEY` | 선택 | `<Anthropic API 키>`. `ANTHROPIC_MODEL`과 둘 다 있을 때만 LLM이 내용을 씁니다. 이때 결정 기록의 내용과 자기 설명 답이 Anthropic API로 전송됩니다(키 모양 문자열은 가린 뒤) |
   | `ANTHROPIC_MODEL` | 선택 | `<Anthropic 모델 이름>`. 둘 중 하나라도 비면 기록 템플릿이 모든 내용을 씁니다 |

4. 빌드하고 시작합니다.

   ```bash
   cd be/deploy && docker compose up -d --build
   ```

   `be`가 healthy가 되면(Spring이 `/actuator/health`에 답하면) `fe`가 시작됩니다.

5. 확인합니다. `be/deploy`에서 `docker compose ps`로 `be`가 healthy인지 보고, 호스트에서 아래 명령을 실행합니다. `401`이 나오면 `fe`를 거쳐 `be`까지 닿은 것입니다(로그인하지 않은 요청이라 401). `HL_HTTP_PORT`를 바꿨다면 8088 대신 그 값을 씁니다.

   ```bash
   curl -s -o /dev/null -w '%{http_code}\n' localhost:8088/api/me
   ```

6. 브라우저에서 `http://<호스트>:<HL_HTTP_PORT>`(기본 `http://<호스트>:8088`)를 열고 `/signup`에서 가입한 뒤 로그인합니다. 예제 기록을 넣어 보려면 `프로젝트 불러오기`에서 `be/fixtures/shop` 폴더를 고릅니다(폴더 선택은 데스크톱 브라우저에서만 됩니다).

리버스 프록시: 프록시(예: Nginx Proxy Manager의 proxy host)는 `http://<호스트>:<HL_HTTP_PORT>`를 가리키게 하고 8080 포트는 가리키지 않습니다. LLM 키가 있으면 동기화가 몇 분 걸릴 수 있으니 프록시의 읽기 타임아웃도 길게 둡니다(`fe`의 nginx는 600초). 바꾼 변수는 `be/deploy`에서 `docker compose up -d`로 반영됩니다.

업데이트: 작업 폴더에서 실행합니다.

```bash
git -C be pull && git -C fe pull
cd be/deploy && docker compose up -d --build
```

멈추기: `be/deploy`에서 실행합니다.

| 명령 | 결과 |
| --- | --- |
| `docker compose stop` | 컨테이너를 멈추고 남겨 둡니다. 데이터 유지 |
| `docker compose down` | 컨테이너를 지웁니다. 볼륨과 데이터 유지 |
| `docker compose down -v` | 볼륨까지 지웁니다. 모든 계정, 기록, 학습 진행이 사라집니다 |

데이터: H2 파일 DB는 이름 있는 볼륨 `humanlearning_be-data`에 있고 `be` 컨테이너의 `/data`에 마운트됩니다(`docker compose -p <프로젝트>`로 띄웠다면 `<프로젝트>_be-data`). `restart`, `stop`, `down`, `up -d --build`를 해도 남으므로, 데이터를 지키려면 `down -v`만 쓰지 않으면 됩니다.

## 로컬 개발

백엔드(Java 21 필요, Gradle은 래퍼로 포함):

```bash
cd be
./gradlew bootRun   # http://localhost:8080, H2 파일 ./data/humanlearning (HL_DB_FILE로 변경)
./gradlew test      # H2 인메모리
```

로컬 실행의 환경 변수는 모두 선택입니다. 필요하면 `cp .env.example .env`로 만든 뒤 `set -a; source .env; set +a`로 읽고 `bootRun`합니다. API 문서는 `http://localhost:8080/swagger-ui.html`에 있습니다.

프론트엔드(Node.js 20 이상, pnpm 필요):

```bash
cd fe
pnpm install
pnpm dev        # http://localhost:5173, /api는 http://localhost:8080으로 프록시 (백엔드 실행 필요)
pnpm dev:mock   # 백엔드 없이, 브라우저 안의 MSW 모의 서버가 /api/*에 응답
```

백엔드의 CORS 기본값이 `http://localhost:5173`만 허용하므로 개발 서버는 5173 포트를 고정해서 씁니다. 검사: `pnpm typecheck`, `pnpm lint`, `pnpm test`, `pnpm build`. 백엔드 API가 바뀌면 `pnpm gen:api`(실행 중인 백엔드에서)나 `pnpm gen:api:local`(저장소의 `openapi.json`에서)로 타입을 다시 만듭니다.

## 결정 기록 스킬

설치: `Backend` 저장소의 `skill/human-learning/` 폴더(아래의 `<스킬 폴더>`)에 있는 `SKILL.md`와 `sync.mjs`를 기록할 프로젝트의 두 위치에 복사하고, 프로젝트의 `CLAUDE.md`와 `AGENTS.md`에 `기술적 결정이 생기면 human-learning 스킬로 기록한다` 한 줄을 넣습니다.

```bash
for dir in .claude/skills/human-learning .agents/skills/human-learning; do
  mkdir -p "$dir" && cp <스킬 폴더>/SKILL.md <스킬 폴더>/sync.mjs "$dir"/
done
```

동기화: 기록할 프로젝트의 루트에서 실행합니다. Node.js만 있으면 되고 의존성은 없습니다. 앱 계정은 셸 환경 변수로만 넘기고 기록이나 파일에 적지 않습니다.

```bash
HL_EMAIL=<앱 계정 이메일> HL_PASSWORD=<앱 계정 비밀번호> node .claude/skills/human-learning/sync.mjs --url http://localhost:8088
```

| 입력 | 뜻 |
| --- | --- |
| `--url URL` 또는 `HL_URL` | 서버 주소. 기본값 `http://localhost:8080`(로컬 `bootRun`). 배포한 앱이면 그 공개 주소 |
| `HL_EMAIL`, `HL_PASSWORD` | 앱에서 가입한 계정 |
| `--project NAME` | 앱에 보일 프로젝트 이름, 영문·숫자·`.`·`_`·`-`로 80자 이하. 기본값은 현재 폴더 이름 |
| `--dry-run` | 보내지 않고 보낼 요청 JSON만 출력 |

명령은 `human-learning/*.md`와 각 기록의 `## Files`가 가리키는 코드 줄(한 범위에 최대 40줄)을 보냅니다. 출력은 `created {n}, updated {n}, unchanged {n}, rejected {n}`이고, 거절된 기록이 있으면 종료 코드 2로 끝납니다. 기록 형식, 거절 코드, 비밀 정보 규칙은 [`SKILL.md`](https://github.com/2026-UniD-Hackathon-Team4/Backend/blob/main/skill/human-learning/SKILL.md)에 있습니다.

## 라이선스

두 저장소 모두 MIT 라이선스입니다.
