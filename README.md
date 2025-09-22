# Bitbucket 에 연결되 Klint 를 local 에서 먼저 돌려서 검사할 때 기본 가이드


1) Android 터미널에서 환경변수 설정하기 

Android Studio에서 Settings(Preferences) → Build, Execution, Deployment → Gradle → Gradle JDK 경로를 복사해서 JAVA_HOME에 설정하세요. 
일반적으로 macOS: /Applications/Android Studio.app/Contents/jbr/Contents/Home 형태입니다.

source ~/.zshrc로 적용합니다.

2) 이전 코드와 수정된 코드의 diff (alias 없이)

아래는 환경변수/경로 부분만(alias 라인 제외) 비교한 unified diff 형식입니다.
이전 코드(원본): Android Studio 내장 JDK 경로를 바로 사용한 예
수정 코드(예시): 시스템 JDK(또는 사용자 지정 JDK)로 바꾼 예 — 실제 경로는 본인 환경에 맞춰 수정하세요.

 # [Ktlint & Gradle 설정]

# 1. JAVA_HOME을 Android Studio의 내장 JDK로 정확하게 설정
#    -> 터미널과 Android Studio가 동일한 Java를 사용하도록 통일
export JAVA_HOME="/Applications/Android Studio.app/Contents/jbr/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"


3) alias를 통한 간단한 명령어 (권장: 개발 편의)

아래는 짧고 사용하기 쉬운 alias 예시입니다. ~/.zshrc 등에 추가하세요. (원하시면 alias 앞뒤에 설명 주석도 유지하세요.)

# Ktlint 단축키(예: 변경된 파일만 검사/포맷)
alias kcheck='./gradlew ktlintCheck -PinternalKtlintGitFilter="$(git diff --name-only HEAD)"'
alias kformat='./gradlew ktlintFormat -PinternalKtlintGitFilter="$(git diff --name-only HEAD)"'


최종:

# 1. JAVA_HOME을 Android Studio의 내장 JDK로 정확하게 설정
export JAVA_HOME="/Applications/Android Studio.app/Contents/jbr/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"

# 2. Ktlint 단축키(Alias)의 버그 수정 (따옴표 추가)
# git diff의 결과를 하나의 문자열로 묶어주기 위해 '(...)' 를 추가
alias kcheck="./gradlew --rerun-tasks ktlintCheck -PinternalKtlintGitFilter='$(git diff --name-only HEAD)'"
alias kformat="./gradlew --rerun-tasks ktlintFormat -PinternalKtlintGitFilter='$(git diff --name-only HEAD)'"



추가:
# Gradle 빌드/클린 단축
alias gbuild='./gradlew assembleDebug'
alias gclean='./gradlew clean'

# zshrc 재적용
alias src='source ~/.zshrc'

사용 예:
kcheck — 변경된 파일만 ktlint 검사
kformat — 변경된 파일만 자동 포맷
kcheckAll/kformatAll — 전체 검사/포맷
src — 설정 변경 후 바로 적용
