# Android Studio 터미널에서 Ktlint 효율적으로 사용하기 (A-Z 설정 가이드)

이 가이드는 **Android Studio 터미널에서 ktlint를 설정하고, Git과 연동하여 변경된 파일만 효율적으로 검사하는 전체 과정**을 다룹니다.

---

## 1단계: 터미널 환경 설정 (JAVA_HOME 및 Alias)

가장 먼저, 터미널이 Gradle을 올바르게 실행하고, 우리가 편하게 명령어를 사용할 수 있도록 쉘(shell) 환경을 설정합니다.

### 1. Android Studio가 사용하는 JDK 경로 확인
- Android Studio 메뉴에서 **Settings/Preferences > Build, Execution, Deployment > Build Tools > Gradle** 로 이동합니다.  
- **'Gradle JDK'** 항목에 지정된 경로를 복사합니다.  
- 예시 경로:
  ```
  /Applications/Android Studio.app/Contents/jbr/Contents/Home
  ```

### 2. `~/.zshrc` 파일 수정
- 터미널에 아래를 입력하여 설정 파일을 엽니다.
  ```bash
  open ~/.zshrc
  ```
- 파일의 맨 아래에 아래 코드를 복사하여 붙여넣습니다.
- ⚠️ **주의:** `export JAVA_HOME`의 경로를 위 1번에서 복사한 실제 경로로 수정하세요.

```bash
# ----------------------------------------------------------------
# [Ktlint & Gradle 설정]
# ----------------------------------------------------------------

# 1. JAVA_HOME을 Android Studio의 내장 JDK로 정확하게 설정
#    -> 터미널과 Android Studio가 동일한 Java를 사용하도록 통일
export JAVA_HOME="/Applications/Android Studio.app/Contents/jbr/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"

# 2. Ktlint 검사를 위한 단축키(Alias) 설정
#    -> 길고 복잡한 명령어를 짧게 만들어줌
#    -> ' ' 따옴표가 "permission denied" 오류를 막아주는 핵심
alias kcheck="./gradlew ktlintCheck -PinternalKtlintGitFilter='$(git diff --name-only HEAD)'"
alias kformat="./gradlew ktlintFormat -PinternalKtlintGitFilter='$(git diff --name-only HEAD)'"
```

---

## 2단계: 설정 적용 및 사용법

### 1. 변경사항 적용
- `~/.zshrc` 저장 후, 아래 명령어 실행
  ```bash
  source ~/.zshrc
  ```

### 2. 검사 실행
- 내가 수정한 파일만 검사
  ```bash
  kcheck
  ```
- 성공 시:
  ```
  BUILD SUCCESSFUL
  ```
  코드 스타일이 깨끗하다는 의미입니다.  

- 실패 시:
  ```
  BUILD FAILED
  ```
  어떤 파일, 몇 번째 줄에 어떤 문제가 있는지 상세한 오류 목록이 나타납니다.

### 3. 내가 수정한 파일 **자동 수정하기** (⭐ 강력 추천)
- ktlint가 수정할 수 있는 모든 위반 사항을 자동으로 고치고 싶을 때 사용합니다.
  ```bash
  kformat
  ```
- 이후 다시 `kcheck` 실행 → `BUILD SUCCESSFUL` 이 나오면 모든 문제가 해결된 것입니다.  
- (들여쓰기, 공백, 세미콜론 등 대부분의 문제는 `kformat`으로 자동 해결됩니다.)

---

## 3단계: 문제 해결 가이드 (Troubleshooting)

### 🔴 에러: `ERROR: JAVA_HOME is set to an invalid directory...`
- **원인:** JAVA_HOME 설정이 잘못되었거나 터미널에 적용되지 않았습니다.
- **해결:**  
  - `~/.zshrc` 파일의 경로가 올바른지 확인  
  - 터미널을 반드시 재시작하세요.

---

### 🔴 에러: `zsh: permission denied: com/path/to/File.kt`
- **원인:** `~/.zshrc`의 alias 설정에서 `$(...)` 부분을 작은따옴표(`' '`)로 감싸지 않았습니다.
- **해결:**  
  - 1단계의 alias 설정을 다시 확인하고 정확하게 수정  
  - 터미널 재시작 후 다시 실행

---

### 🟡 현상: 오류 코드를 만들었는데도 `BUILD SUCCESSFUL`이 뜸
- **원인:** Gradle의 캐시 기능이 변경사항을 감지하지 못하고 이전 성공 결과를 그대로 보여주는 문제
- **해결:** 캐시를 무시하고 강제로 검사를 실행합니다. (alias 사용 불가)

```bash
./gradlew ktlintCheck --no-build-cache
./gradlew ktlintFormat --no-build-cache
```

---

✅ 이제 Android Studio 터미널에서 **ktlint를 효율적으로 사용**할 수 있습니다!
