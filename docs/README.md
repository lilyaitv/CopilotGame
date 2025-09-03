# 농장 시뮬레이터 (Farm Simulator) - 개발자 문서

## �� 목차
1. [프로젝트 개요](#프로젝트-개요)
2. [시스템 요구사항](#시스템-요구사항)
3. [설치 및 실행](#설치-및-실행)
4. [게임 시스템](#게임-시스템)
5. [기술 아키텍처](#기술-아키텍처)
6. [라이브러리 워크플로우](#라이브러리-워크플로우)
7. [개발 가이드](#개발-가이드)
8. [문제 해결](#문제-해결)

## �� 프로젝트 개요

농장 시뮬레이터는 Python과 Pygame을 사용하여 개발된 2D 농업 시뮬레이션 게임입니다. 
플레이어는 씨앗을 심고, 물을 주고, 작물을 수확하여 돈을 벌고 농장을 발전시킬 수 있습니다.

### 주요 기능
- 🌱 5가지 작물 시스템 (해바라기, 상추, 감자, 완두콩, 용과)
- ��️ 도구 업그레이드 시스템 (물뿌리개, 낫)
- �� 씨앗 상점 및 도구 상점
- 🏆 엔딩 시스템 (5가지 엔딩)
- �� 배경음악 및 효과음
- �� 게임 저장/불러오기
- ⚙️ 설정 시스템 (볼륨 조절 등)

## �� 시스템 요구사항

### 최소 요구사항
- **OS**: Windows 10/11, macOS 10.14+, Ubuntu 18.04+
- **Python**: 3.8 이상
- **메모리**: 4GB RAM
- **저장공간**: 500MB

### 권장 사양
- **Python**: 3.9 이상
- **메모리**: 8GB RAM
- **저장공간**: 1GB

## �� 설치 및 실행

### 1. 저장소 클론
```bash
git clone [repository-url]
cd farm-simulator
```

### 2. 가상환경 생성 및 활성화
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. 의존성 설치
```bash
pip install -r requirements.txt
```

### 4. 게임 실행
```bash
python main.py
```

## �� 게임 시스템

### 작물 시스템
| 작물 | 성장 시간 | 가격 | 설명 |
|------|-----------|------|------|
| 해바라기 | 15일 | 3,000골드 | 기본 작물, 빠른 성장 |
| 상추 | 25일 | 30,000골드 | 중급 작물, 안정적인 수익 |
| 감자 | 30일 | 70,000골드 | 고급 작물, 높은 수익률 |
| 완두콩 | 50일 | 400,000골드 | 프리미엄 작물, 특별한 가치 |
| 용과 | 100일 | 5,000,000골드 | 전설의 작물, 최고 수익 |

### 도구 시스템
#### 물뿌리개 (성장 촉진)
- **녹슨 물뿌리개**: 기본 배율 1x
- **기본 물뿌리개**: 배율 2x (50,000골드)
- **좋아진 물뿌리개**: 배율 8x (750,000골드)
- **전설의 물뿌리개**: 배율 64x, 전체 밭 영향 (100,000,000골드)

#### 낫 (수확 효율)
- **기본 낫**: 배율 1x
- **조금 쓸만한 낫**: 배율 1.5x (150,000골드)
- **모든걸 베어버릴것 같은 낫**: 배율 6x (100,000,000골드)
- **전설의 낫**: 배율 10x (300,000,000골드)

### 엔딩 시스템
1. **전설의 농부**: 520일차 이하 10억 달성
2. **자연과의 조화**: 521~550일차 10억 달성
3. **황금빛 수확**: 551~590일차 10억 달성
4. **농장의 성장**: 591~630일차 10억 달성
5. **첫 수확의 기쁨**: 631일차 이상 10억 달성

## ��️ 기술 아키텍처

### 프로젝트 구조
arm-simulator/
├── main.py # 메인 게임 파일
├── assets/ # 게임 자산
│ ├── images/ # 이미지 파일
│ ├── sounds/ # 오디오 파일
│ └── fonts/ # 폰트 파일
├── data/ # 게임 데이터
│ ├── settings.json # 설정 파일
│ └── save_data.json # 저장 파일
├── docs/ # 문서
└── requirements.txt # 의존성 목록


### 핵심 클래스 및 모듈
- **EndingManager**: 엔딩 조건 확인 및 표시
- **TemporaryMessageDisplay**: 임시 메시지 표시 시스템
- **게임 상태 관리**: TITLE, GAME, SETTINGS, SHOP, TOOL_SHOP, ACHIEVEMENT

## 🔧 라이브러리 워크플로우

### Pygame 워크플로우
```python
# 1. 초기화
pygame.init()
pygame.mixer.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))

# 2. 이벤트 루프
while running:
    for event in pygame.event.get():
        handle_event(event)
    
    update_game_logic()
    draw_screen()
    pygame.display.flip()
    clock.tick(60)
```

### Pathlib 워크플로우
```python
# 경로 관리
BASE_DIR = Path(__file__).resolve().parent
ASSETS_DIR = BASE_DIR / "assets"
IMAGES_DIR = ASSETS_DIR / "images"

# 파일 로드
image_path = IMAGES_DIR / "title_bg.png"
if image_path.exists():
    image = pygame.image.load(image_path)
```

### JSON 워크플로우
```python
# 설정 저장
def save_settings(settings):
    with open(SETTINGS_FILE, "w", encoding="utf-8") as f:
        json.dump(settings, f, indent=4, ensure_ascii=False)

# 설정 로드
def load_settings():
    with open(SETTINGS_FILE, "r", encoding="utf-8") as f:
        return json.load(f)
```

## ��‍�� 개발 가이드

### 새로운 작물 추가
1. **이미지 파일 추가**: `assets/images/`에 씨앗 및 성장 이미지
2. **PLANT_INFO 업데이트**: `main.py`의 PLANT_INFO 딕셔너리 수정
3. **상점 아이템 추가**: `shop_items` 리스트에 새 작물 정보 추가
4. **가격 정보 설정**: `CROP_PRICES` 딕셔너리에 가격 정보 추가

### 새로운 도구 추가
1. **이미지 파일 추가**: `assets/images/`에 도구 이미지
2. **도구 정보 추가**: `tool_shop_items` 딕셔너리에 새 도구 정보 추가
3. **플레이어 도구 업데이트**: `player_tools` 구조에 새 도구 타입 추가

### 새로운 엔딩 추가
1. **엔딩 이미지 추가**: `assets/images/ending6.png`
2. **EndingManager 업데이트**: `endings` 리스트에 새 엔딩 조건 추가
3. **업적 시스템 업데이트**: `ending_achieved` 리스트 확장

## �� 문제 해결

### 일반적인 문제들
해결: assets/images/ 폴더에 해당 이미지 파일이 있는지 확인
#### 이미지 로드 오류
해결: assets/fonts/ 폴더에 Galmuri14.ttf 파일이 있는지 확인
#### 사운드 재생 오류
해결: assets/sounds/ 폴더에 해당 오디오 파일이 있는지 확인


### 성능 최적화
- **이미지 스케일링**: `pygame.transform.scale()` 사용 시 한 번만 스케일링
- **이벤트 처리**: 불필요한 이벤트 처리 최소화
- **메모리 관리**: 사용하지 않는 이미지/사운드 언로드

## 📝 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.

## 🤝 기여하기

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## �� 문의

프로젝트에 대한 문의사항이 있으시면 이슈를 생성해 주세요.

주요 문서들:
1. **README.md** - 프로젝트 개요 및 시작 가이드
2. **INSTALLATION.md** - 상세한 설치 가이드
3. **API_REFERENCE.md** - 개발자를 위한 API 문서
4. **CONTRIBUTING.md** - 기여 가이드
5. **CHANGELOG.md** - 변경 이력

이 문서들은 프로젝트의 구조, 기능, 개발 방법 등을 포괄적으로 다루고 있습니다.
