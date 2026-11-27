# Herdr

[English](README.md) · [简体中文](README.zh-CN.md) · 한국어

Herdr는 터미널과 코딩 에이전트를 위한 데스크톱 앱입니다. 작업을 스페이스(space) 단위로 정리하고, 어떤 에이전트가 입력을 기다리는지 한눈에 보며, 창을 닫아도 터미널을 백그라운드에서 계속 실행할 수 있습니다.

## 설치

### Linux 및 macOS

```sh
curl -fsSL https://releases.herdr.dev/app/install.sh | sh
```

Linux에서는 데스크톱 세션과 동작하는 Vulkan 드라이버가 필요합니다. macOS 빌드는 Apple Silicon을 지원하며, Intel Mac 사용자는 [소스에서 빌드](#소스에서-빌드)할 수 있습니다. Linux 빌드는 x86-64와 ARM64를 제공합니다.

설치 스크립트는 Herdr를 앱 목록에 추가하고 실행 명령을 `~/.local/bin`에 둡니다.
설치 중 안내가 나오면 해당 폴더를 `PATH`에 추가하세요.
macOS에서는 `~/Applications/Herdr.app`에 설치됩니다.
Apple Silicon에서 드래그 앤 드롭으로 설치하려면 [Herdr.dmg](https://releases.herdr.dev/app/Herdr.dmg)를 내려받아 `Applications`로 끌어다 놓으세요.

macOS(Apple Silicon)에서는 [Homebrew](https://brew.sh/)로도 설치할 수 있습니다:

```sh
brew install --cask herdr-app
```

### Windows

`curl`과 `tar`가 설치된 x64 Windows(Windows 10 1803 이상)의 PowerShell에서 실행하세요:

```powershell
irm https://releases.herdr.dev/app/install.ps1 | iex
```

Herdr가 시작 메뉴에 등록됩니다. 설치 스크립트는 사용자 `PATH`에 `%LOCALAPPDATA%\Programs\herdr-app`을 추가하므로, 새 터미널을 열어 반영하세요.

셸 및 PowerShell 설치 스크립트는 코드를 내려받아 실행합니다. 원하면 먼저 [셸 설치 스크립트](distribution/install.sh)나 [PowerShell 설치 스크립트](distribution/install.ps1)를 읽어보세요.
또한 [herdr-core](https://github.com/herdrdev/herdr-core)가 없으면 함께 설치합니다.
앱이 사용할 수 없는 코어가 있으면 앱 자체에서 설치 또는 업데이트를 도와줍니다.

## 작업 찾기

**스페이스(space)**는 한 머신(보통 하나의 저장소)의 작업을 담습니다. 각 스페이스에는 **탭(tab)**이 있고, 탭을 **패널(pane)**로 분할해 터미널이나 모드(mod) 패널을 나란히 배치할 수 있습니다.

이름을 정하지 않은 스페이스는 첫 번째 터미널의 폴더 이름을 따릅니다. 고정된 이름이 필요하면 스페이스나 탭의 이름을 바꾸세요.
새 탭은 현재 포커스된 터미널의 폴더에서 시작합니다.

에이전트 사이드바는 여러 스페이스의 에이전트(다른 에이전트가 띄운 하위 에이전트 포함)를 하나의 목록으로 모아 보여줍니다.
상태를 보고하는 에이전트는 다음과 같이 표시됩니다:

- **작업 중(Working):** 아직 작업하고 있습니다.
- **입력 필요(Blocked):** 사용자의 입력을 기다리고 있습니다.
- **완료(Done):** 다른 곳을 보는 사이에 작업을 마쳤습니다.
- **대기 중(Idle):** 작업 중이 아니거나, 완료된 뒤 이미 확인한 에이전트입니다.

에이전트를 클릭하면 해당 화면을 볼 수 있습니다. 패널(pane)이 없는 하위 에이전트는 현재 탭 위에 **미리보기(peek)**로 열립니다. 다른 작업과 나란히 두고 싶으면 탭으로 유지하세요.
터미널 텍스트에 나온 실행 중인 터미널 주소를 Ctrl-클릭(macOS에서는 Cmd-클릭)하면 미리보기로 열 수 있습니다.
`t7` 같은 주소는 같은 머신의 터미널을, `vps/t7`은 다른 머신의 터미널을 뜻합니다.

창은 터미널을 배치하고, 실제 실행은 코어(`herdr-core`)가 맡습니다.
창을 닫아도 터미널은 계속 실행되며, 패널(pane)이나 탭을 닫으면 해당 터미널이 종료됩니다.
재부팅이나 코어 충돌 뒤에도 유지된 패널(pane)은 마지막 폴더에서 다시 열립니다.
터미널에 재개 방법을 알려주는 프로그램([OSC 7577](https://www.superlogical.com/rex/docs/build/program-resume), 예: `pi`)은 그 위치에서 다시 시작하고, 그 외의 패널(pane)은 새 셸로 열립니다.

## 다른 머신에서 작업하기

설정 → 머신 또는 사이드바의 '머신 추가'에서 SSH 머신을 추가하세요.
SSH 설정에서 호스트를 고르거나 SSH 접속 주소를 직접 입력할 수 있습니다.
해당 머신의 스페이스에 속한 터미널은 내 컴퓨터가 아니라 그 머신에서 실행됩니다.

각 머신은 스페이스, 탭, 분할 상태를 자체 코어에 보관합니다. 연결된 화면들은 이 구조와 실행 중인 터미널을 공유하되, 포커스와 확대/축소 상태는 각자 유지합니다.
원격 머신과 코어가 켜져 있는 한, 로컬 창을 닫거나 노트북이 절전 모드에 들어가도 원격 작업은 계속 실행됩니다.

## 내 방식대로 꾸미기

명령 팔레트를 열어 이름으로 동작을 찾거나, 설정에서 글꼴, 색상, 소리 등 환경 설정을 바꿀 수 있습니다.
설정 › 화면에서는 터미널 줄 높이, 셀 너비, 스크롤 속도도 조절할 수 있습니다. 기본값은 모두 100%이며, 레이아웃에서 `terminal_line_height`(0.8–2), `terminal_cell_width`(0.8–2), `terminal_scroll_speed`(0.1–5)로 재정의할 수 있습니다.
기본 제공 레이아웃에는 Herdr, Terminals, Herdr 98이 포함됩니다.
**레이아웃(layout)**은 창 배치를 정하고, **모드(mod)**는 패널, 메뉴, 대화상자를 그리는 작은 JavaScript 프로그램입니다.

기본 제공 레이아웃이나 모드(mod)를 수정하려면 먼저 복제하세요. 복제한 파일은 그대로 유지되며, 기본 파일은 Herdr 시작 시 업데이트됩니다.
개인 레이아웃, 모드(mod), 키 설정 파일을 저장하면 창이 즉시 다시 불러옵니다.
편집 중 오류가 나면 마지막 정상 버전을 유지하고 오류를 알려줍니다.

Ctrl+Shift+P(macOS는 Cmd+Shift+P)를 누르면 모든 명령과 단축키를 볼 수 있습니다. `keys.json`에서 어떤 키든 바꿀 수 있으며, 이름 있는 타입 인자를 JSON 객체로 넘기는 형식을 포함해 자세한 내용은 모드 가이드의 [키 설정 섹션](defaults/AGENTS.md#keys-keysjson)을 참고하세요.

macOS에서는 Cmd+A로 터미널 화면과 스크롤백 전체를 선택해 Cmd+C로 복사할 수 있습니다.
vim 같은 대체 화면(alternate-screen) 앱에서는 현재 화면만 선택합니다.
Linux와 Windows에서는 Ctrl+A를 셸용으로 남겨두며, 원하면 `keys.json`에 `terminal.select_all`을 바인딩할 수 있습니다.

[레이아웃과 모드](docs/mods.md) · [명령어 레퍼런스](docs/commands.md)

## 업데이트 및 개인정보

설치된 릴리스 빌드는 백그라운드에서 업데이트를 확인하고 실행 중인 앱 옆에 새 버전을 내려받습니다. 앱을 종료했다가 다시 열거나 업데이트 동작을 실행하면 새 버전으로 전환되며, 터미널은 코어에서 계속 실행됩니다.
설정 → 업데이트에서 사용 가능한 버전을 확인할 수 있습니다. 업데이트 후에는 '새로운 기능'이 열리며, 명령 팔레트에서도 다시 열 수 있습니다.
소스에서 빌드한 버전은 자동 업데이트되지 않습니다.

릴리스 빌드에서는 사용 통계 수집이 기본으로 켜져 있습니다. 설정 → 개인정보에서 끌 수 있습니다.
모드(mod)는 샌드박스 확장이 아니라 신뢰된 코드로 실행됩니다. 다른 사람의 모드(mod)를 설치하기 전에 [보안 및 개인정보](docs/security.md)를 읽어보세요.

## 에이전트용 안내

```sh
herdr-app --skill
```

창을 제어하기 위한 에이전트 가이드를 출력합니다. `herdr-core` 스킬도 이 가이드를 안내합니다.
프로그램 실행과 조율에는 `herdr-core`를, 화면 표시 변경에는 `herdr-app`을 사용하세요.

```sh
herdr-app ls
herdr-app status
herdr-app commands
```

앞의 두 명령은 열린 창을 조회하고, `commands`는 창이 열려 있지 않아도 동작합니다.
인자 목록은 [명령어 레퍼런스](docs/commands.md)를 참고하세요.

## 소스에서 빌드

버그 제보, 모드 API 요청, 코드 기여는 [기여 가이드](CONTRIBUTING.md)를 참고하세요.

서브모듈을 포함해 저장소를 클론하세요. [CI](.github/workflows/ci.yml)와 동일한 Rust 1.95.0, Zig 0.16.0 버전을 사용합니다.
Linux에서는 해당 워크플로에 명시된 개발 패키지가 필요합니다.

```sh
git clone --recurse-submodules https://github.com/herdrdev/herdr-app.git
cd herdr-app
just build
just test
just install
```

위 레시피를 실행하려면 [just](https://github.com/casey/just)를 설치하세요.
플랫폼 설치 명령은 릴리스 빌드를 만들고 데스크톱에 앱을 등록합니다.
Linux에서는 이 체크아웃 경로에 링크되므로 폴더를 그대로 두세요.
새 빌드를 사용하려면 Herdr를 종료했다가 다시 여세요.

문서 검사에는 [Bun](https://bun.sh)도 필요합니다: `just docs-check`는 생성된 페이지와 CLI 예제를 검사하고, `just docs-generate`는 레퍼런스 페이지를 갱신합니다.

Windows, Linux, macOS에서 그리기 변경 사항을 비교하려면 각 빌드에서 동일한 고정 워크로드를 실행하세요:

```sh
python scripts/bench.py --bin <app executable> --set spinners --seconds 20
```

실행기에는 `PATH`상의 `hc`와 스크립트를 실행하는 Python이 필요합니다. 실행 설정, 카운터, 결과가 담긴 고유 아티팩트 폴더를 출력한 뒤 전용 앱·터미널·코어를 종료합니다. 모든 세트는 Python 픽스처 프로그램을 사용하며, 기본 세트의 TUI 워크로드는 과거 `btop` 측정을 대체합니다. `--only spinners`는 조용한 네이티브 스피너 시나리오만 측정합니다. `--set titles`는 기본 레이아웃에서 접두사(`π ⠹ task`) 뒤 제목이 회전하는 에이전트 8개를 실행합니다. 비교 시 창이 보이고 디스플레이가 깨어 있는 상태를 유지하며 동일한 창 활성 상태와 디스플레이 설정을 맞추세요(비활성 창은 더 낮은 빈도로 그려질 수 있음). Windows는 각 측정 경계마다 가시성, 포그라운드 활성 여부, 디스플레이 Hz를 기록하며 다른 플랫폼은 이를 사용할 수 없음으로 표시합니다. 가시성은 가려짐(occlusion) 여부를 보장하지 않습니다. Draws는 창 렌더 호출 수입니다. Render-to-paint 경과 시간은 씬 확정, 백엔드 그리기, 업로드, 프레젠테이션 직전까지 측정하며 OS 프로세스 CPU 시간과는 별개입니다. 둘 다 물리적 화면 표시나 FPS HUD의 파생 MAX FPS를 측정하지는 않습니다.

`python scripts/bench_compare.py ghostty --root <artifact folder>`는 macOS 비교를 위해 이전 실행의 가짜 에이전트를 재사용합니다. 벤치마크 검사는 `python -m unittest discover -s scripts -p test_bench.py`로 실행합니다.
