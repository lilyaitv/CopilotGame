# 설치 가이드

## 📋 사전 요구사항

### Python 설치
- Python 3.8 이상이 필요합니다
- [Python 공식 웹사이트](https://www.python.org/downloads/)에서 다운로드

### Git 설치 (선택사항)
- 소스코드에서 설치하려면 Git이 필요합니다
- [Git 공식 웹사이트](https://git-scm.com/)에서 다운로드

### VSCode 설치 (선택사항)
- [Visual Studio Code 공식 웹사이트][(ttps://code.visualstudio.com/download)에서 다운로드

## �� 설치 방법

### 방법 1: 소스코드에서 설치

#### 1단계: 저장소 클론
```bash
git clone https://github.com/yourusername/farm-simulator.git
cd farm-simulator
```

#### 2단계: 가상환경 생성 및 실행
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

#### 3단계: 의존성 설치
```bash
pip install pygame
pip install pathlib
```

#### 4단계: 게임 실행
```bash
python main.py
```

### 방법 2: 실행 파일 사용

#### 1단계: 릴리즈 다운로드
- GitHub Releases에서 최신 버전 다운로드
- 압축 파일 해제

#### 2단계: 실행
- Windows: `farm-simulator.exe` 실행
- macOS: `farm-simulator.app` 실행
- Linux: `farm-simulator` 실행

#### 브랜치 생성(선택)
```bash
git checkout -b feature/your-feature-name
# 또는
git checkout -b fix/your-bug-fix
```
## 👽 의존성 목록

### 필수 패키지

pygame>=2.0.0
pathlib


### 개발용 패키지 (선택사항)

pytest
black
flake8


## 🐛 설치 문제 해결

### Python 버전 오류

오류: pygame 설치 실패
해결:
1. pip 업데이트: pip install --upgrade pip
2. Visual C++ 빌드 도구 설치 (Windows)
3. 시스템 패키지 업데이트 (Linux)
Apply to API_REFERENC...

오류: 가상환경 활성화 실패
해결:
1. PowerShell 실행 정책 변경: Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
2. 관리자 권한으로 실행

## ✅ 설치 확인

설치가 완료되었는지 확인하려면:

```bash
python -c "import pygame; print('Pygame 설치 완료!')"
python main.py
```

게임이 정상적으로 실행되면 설치가 완료된 것입니다.

## 🔄 업데이트

### 소스코드 업데이트
```bash
git pull origin main
pip install -r requirements.txt --upgrade
```

### 실행 파일 업데이트
- GitHub Releases에서 최신 버전 다운로드
- 기존 파일 교체


