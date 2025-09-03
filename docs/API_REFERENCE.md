# API 참조 문서

## 😜 게임 시스템 API

### 게임 상태 관리

#### 게임 상태 상수
```python
game_state = 'TITLE'      # 타이틀 화면
game_state = 'GAME'       # 게임 플레이
game_state = 'SETTINGS'   # 설정 화면
game_state = 'SHOP'       # 씨앗 상점
game_state = 'TOOL_SHOP'  # 도구 상점
game_state = 'ACHIEVEMENT' # 업적 화면
```

#### 상태 변경 함수
```python
def change_game_state(new_state):
    global game_state, previous_game_state
    previous_game_state = game_state
    game_state = new_state
```

### 작물 시스템 API

#### 작물 정보 구조
```python
PLANT_INFO = {
    "sunflower": {
        "seed_image": seed_sunflower_image,
        "seed_name": "해바라기 씨앗",
        "growth_frames": 15,
        "plant_image_original": plant_sunflower_image_original
    }
}
```

#### 작물 상태 구조
```python
field_state = {
    'rect': pygame.Rect(x, y, width, height),
    'planted_plant_type': "sunflower",  # None이면 빈 밭
    'current_growth_frame': 0,          # 현재 성장 단계
    'final_scale_factor': 1.0           # 최종 크기 배율 (0.7~1.3)
}
```

#### 작물 심기 함수
```python
def plant_crop(plot, crop_type):
    if plot['planted_plant_type'] is None:
        plot['planted_plant_type'] = crop_type
        plot['current_growth_frame'] = 0
        plot['final_scale_factor'] = random.uniform(0.7, 1.3)
        return True
    return False
```

#### 작물 성장 함수
```python
def grow_crop(plot, growth_amount):
    if plot['planted_plant_type'] is not None:
        max_frames = PLANT_INFO[plot['planted_plant_type']]['growth_frames']
        plot['current_growth_frame'] = min(
            max_frames, 
            plot['current_growth_frame'] + growth_amount
        )
```

#### 작물 수확 함수
```python
def harvest_crop(plot):
    if plot['planted_plant_type'] is not None:
        if plot['current_growth_frame'] >= PLANT_INFO[plot['planted_plant_type']]['growth_frames']:
            crop_type = plot['planted_plant_type']
            income = calculate_harvest_income(crop_type)
            plot['planted_plant_type'] = None
            plot['current_growth_frame'] = 0
            plot['final_scale_factor'] = 1.0
            return income
    return 0
```

### 도구 시스템 API

#### 도구 정보 구조
```python
tool_info = {
    "id": "watering_can_basic",
    "name": "기본 물뿌리개",
    "price": 50000,
    "multiplier": 2,
    "description": "기본적으로 제공되는 물뿌리개입니다.",
    "image": tool_image
}
```

#### 도구 장착 함수
```python
def equip_tool(tool_category, tool_id):
    if tool_id in player_tools[tool_category]['owned_tool_ids']:
        player_tools[tool_category]['id'] = tool_id
        player_tools[tool_category]['multiplier'] = get_tool_multiplier(tool_id)
        return True
    return False
```

#### 도구 구매 함수
```python
def buy_tool(tool_category, tool_id):
    tool = get_tool_info(tool_category, tool_id)
    if player_money >= tool['price']:
        player_money -= tool['price']
        player_tools[tool_category]['owned_tool_ids'].append(tool_id)
        return True
    return False
```

### 상점 시스템 API

#### 상점 아이템 구조
```python
shop_item = {
    "plant_type": "sunflower",
    "name": "해바라기 씨앗",
    "price": 2000,
    "image": seed_image,
    "description": "해바라기가 만개를 하면 팔 수 있지않을까?"
}
```

#### 구매 함수
```python
def buy_item(item_index):
    item = shop_items[item_index]
    if player_money >= item['price']:
        player_money -= item['price']
        plant_type = item['plant_type']
        player_inventory[plant_type] = player_inventory.get(plant_type, 0) + 1
        return True
    return False
```

### 저장/로드 시스템 API

#### 게임 저장 함수
```python
def save_game(filename="save_data.json"):
    data = {
        "player_inventory": player_inventory,
        "player_tools": player_tools,
        "player_money": player_money,
        "date": {"year": current_year, "month": current_month, "day": current_day},
        "fields": field_states,
        "pots": pot_states
    }
    
    save_path = DATA_DIR / filename
    with open(save_path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)
```

#### 게임 로드 함수
```python
def load_game(filename="save_data.json"):
    save_path = DATA_DIR / filename
    try:
        with open(save_path, "r", encoding="utf-8") as f:
            data = json.load(f)
            # 데이터 복원
            player_inventory = data.get("player_inventory", player_inventory)
            player_money = data.get("player_money", player_money)
            # ... 기타 데이터 복원
    except FileNotFoundError:
        print("저장 파일이 없습니다. 새 게임을 시작합니다.")
```

### 설정 시스템 API

#### 설정 저장 함수
```python
def save_settings(settings):
    try:
        with open(SETTINGS_FILE, "w") as f:
            json.dump(settings, f, indent=4)
        print("설정이 저장되었습니다.")
    except Exception as e:
        print(f"설정 저장 중 오류 발생: {e}")
```

#### 설정 로드 함수
```python
def load_settings():
    if SETTINGS_FILE.exists():
        try:
            with open(SETTINGS_FILE, "r") as f:
                loaded = json.load(f)
                # 기본값과 병합
                for key, default_value in DEFAULT_SETTINGS.items():
                    if key not in loaded:
                        loaded[key] = default_value
                return loaded
        except Exception as e:
            print(f"설정 로드 중 오류 발생: {e}")
    return DEFAULT_SETTINGS.copy()
```

### 엔딩 시스템 API

#### 엔딩 조건 확인
```python
def check_ending_conditions(game_data):
    for ending in endings:
        if ending["condition"](game_data):
            return ending
    return None
```

#### 엔딩 표시
```python
def show_ending(ending):
    # 페이드 인/아웃 효과
    # 엔딩 이미지 표시
    # 엔딩 텍스트 타이핑 효과
    # 사용자 입력 대기
```

### 메시지 시스템 API

#### 임시 메시지 생성
```python
def show_message(text, duration=2.0, color=BLACK):
    message = TemporaryMessageDisplay(
        text, 
        (WIDTH // 2, 100), 
        duration=duration, 
        color=color, 
        font=font
    )
    active_temporary_messages.append(message)
```

#### 다중 색상 메시지
```python
def show_multi_color_message(text_parts, duration=2.0):
    message = TemporaryMessageDisplay(
        text_parts,  # [(text, color), (text, color), ...]
        (WIDTH // 2, 100), 
        duration=duration, 
        font=font, 
        is_multi_colored=True
    )
    active_temporary_messages.append(message)
```

## 🤗 렌더링 API

### 화면 그리기 함수

#### 게임 화면
```python
def draw_game_screen():
    # 배경 그리기
    # UI 요소 그리기
    # 밭/화분 그리기
    # 작물 그리기
    # 도구 버튼 그리기
```

#### 상점 화면
```python
def draw_shop_screen():
    # 상점 패널 그리기
    # 아이템 목록 그리기
    # 상세 정보 패널 그리기
    # 구매 버튼 그리기
```

#### 설정 화면
```python
def draw_settings_screen():
    # 설정 패널 그리기
    # 볼륨 슬라이더 그리기
    # 닫기 버튼 그리기
```

### 유틸리티 함수

#### 텍스트 줄바꿈
```python
def wrap_text(text, font, max_width):
    words = text.split(' ')
    lines = []
    current_line = []
    for word in words:
        test_line = ' '.join(current_line + [word])
        if font.size(test_line)[0] <= max_width:
            current_line.append(word)
        else:
            lines.append(' '.join(current_line))
            current_line = [word]
    lines.append(' '.join(current_line))
    return lines
```

#### 이미지 스케일링
```python
def scale_image(image, target_size):
    return pygame.transform.scale(image, target_size)

def smooth_scale_image(image, target_size):
    return pygame.transform.smoothscale(image, target_size)
```

## 🔧 유틸리티 API

### 날짜 관리
```python
def update_date():
    global current_day, current_month, current_year
    current_day += 1
    if current_day > get_days_in_month(current_year, current_month):
        current_day = 1
        current_month += 1
        if current_month > 12:
            current_month = 1
            current_year += 1

def get_days_in_month(year, month):
    if month == 2 and is_leap_year(year):
        return 29
    return days_in_month_base[month]
```

### 랜덤 유틸리티
```python
def random_float(min_val, max_val):
    return random.uniform(min_val, max_val)

def random_int(min_val, max_val):
    return random.randint(min_val, max_val)

def random_choice(choices):
    return random.choice(choices)
```

### 파일 경로 유틸리티
```python
def get_asset_path(category, filename):
    return BASE_DIR / "assets" / category / filename

def get_data_path(filename):
    return BASE_DIR / "data" / filename

def ensure_directory_exists(path):
    path.mkdir(parents=True, exist_ok=True)
```
```

### 📁 docs/CONTRIBUTING.md
```markdown
# 기여 가이드

## 😁 기여 방법

농장 시뮬레이터 프로젝트에 기여하고 싶으시다면 감사합니다! 
이 문서는 프로젝트에 기여하는 방법을 안내합니다.

## 🤗 기여하기 전에

### 1. 이슈 확인
- [Issues](https://github.com/yourusername/farm-simulator/issues) 페이지에서 
  이미 보고된 문제나 제안사항이 있는지 확인하세요.
- 비슷한 이슈가 있다면 기존 이슈에 댓글로 참여하세요.

### 2. 프로젝트 구조 이해
- [README.md](README.md)를 읽어 프로젝트의 목적과 구조를 이해하세요.
- [API_REFERENCE.md](API_REFERENCE.md)를 참고하여 코드 구조를 파악하세요.

## 😉 기여 시작하기

### 1. 저장소 포크
- GitHub에서 이 저장소를 포크하세요.
- 포크된 저장소를 로컬에 클론하세요.

```bash
git clone https://github.com/yourusername/farm-simulator.git
cd farm-simulator
```

### 2. 개발 환경 설정
```bash
# 가상환경 생성
python -m venv venv

# 가상환경 활성화
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# 의존성 설치
pip install -r requirements.txt
```

### 3. 브랜치 생성
```bash
git checkout -b feature/your-feature-name
# 또는
git checkout -b fix/your-bug-fix
```

## 📝 코딩 가이드라인

### Python 스타일 가이드
- [PEP 8](https://www.python.org/dev/peps/pep-0008/) 스타일 가이드를 따르세요.
- 최대 줄 길이는 79자로 제한하세요.
- 들여쓰기는 4칸 공백을 사용하세요.

### 코드 예시
```python
def calculate_crop_income(crop_type, quantity, tool_multiplier):
    """작물 수확 수익을 계산합니다.
    
    Args:
        crop_type (str): 작물 타입
        quantity (int): 수확량
        tool_multiplier (float): 도구 배율
    
    Returns:
        int: 계산된 수익
    """
    base_price = CROP_PRICES[crop_type]['price_per_item']
    return int(quantity * base_price * tool_multiplier)
```

### 주석 작성
- 모든 함수와 클래스에 docstring을 작성하세요.
- 복잡한 로직에는 인라인 주석을 추가하세요.
- 주석은 한국어로 작성하세요.

### 변수명 규칙
- 변수명은 소문자와 언더스코어를 사용하세요.
- 상수는 대문자와 언더스코어를 사용하세요.
- 의미가 명확한 이름을 사용하세요.

```python
# 좋은 예
player_money = 1000
MAX_GROWTH_FRAMES = 100
crop_growth_rate = 1.5

# 피해야 할 예
pm = 1000
m = 100
r = 1.5
```

## 🎮 게임 기능 추가

### 새로운 작물 추가
1. **이미지 파일 추가**
   - `assets/images/`에 씨앗 이미지 추가
   - `assets/images/`에 성장 이미지 추가

2. **코드 수정**
   ```python
   # PLANT_INFO에 추가
   "new_crop": {
       "seed_image": seed_new_crop_image,
       "seed_name": "새 작물 씨앗",
       "growth_frames": 30,
       "plant_image_original": plant_new_crop_image_original
   }
   
   # shop_items에 추가
   {
       "plant_type": "new_crop",
       "name": "새 작물 씨앗",
       "price": 50000,
       "image": seed_new_crop_image,
       "description": "새로운 작물입니다."
   }
   
   # CROP_PRICES에 추가
   "new_crop": {
       "type": "fixed",
       "quantity": 1,
       "price_per_item": 80000,
       "name": "새 작물"
   }
   ```

### 새로운 도구 추가
1. **이미지 파일 추가**
   - `assets/images/`에 도구 이미지 추가

2. **코드 수정**
   ```python
   # tool_shop_items에 추가
   "new_tool": [
       {
           "id": "new_tool_basic",
           "name": "기본 새 도구",
           "price": 100000,
           "multiplier": 2,
           "description": "새로운 도구입니다.",
           "image": new_tool_image
       }
   ]
   ```

### 새로운 엔딩 추가
1. **이미지 파일 추가**
   - `assets/images/ending6.png` 추가

2. **코드 수정**
   ```python
   # EndingManager의 endings 리스트에 추가
   {
       "condition": lambda gd: gd.get("money", 0) >= 1_000_000_000 and gd.get("days", 0) <= 400,
       "message": "엔딩6: 400일차 이하 10억 달성!",
       "text": ["새로운 엔딩 내용..."]
   }
   ```

## 🧪 테스트

### 테스트 실행
```bash
# 전체 테스트 실행
python -m pytest

# 특정 테스트 파일 실행
python -m pytest tests/test_game_logic.py

# 커버리지와 함께 실행
python -m pytest --cov=main
```

### 테스트 작성 가이드
- 모든 새로운 기능에 대한 테스트를 작성하세요.
- 테스트는 `tests/` 폴더에 저장하세요.
- 테스트 함수명은 `test_`로 시작하세요.

```python
def test_crop_growth():
    """작물 성장 테스트"""
    plot = create_test_plot()
    initial_frame = plot['current_growth_frame']
    
    grow_crop(plot, 5)
    
    assert plot['current_growth_frame'] == initial_frame + 5
```

## 📤 Pull Request 제출

### 1. 변경사항 커밋
```bash
git add .
git commit -m "feat: 새로운 작물 '토마토' 추가

- 토마토 씨앗 및 성장 이미지 추가
- PLANT_INFO에 토마토 정보 추가
- 상점에 토마토 씨앗 추가
- 토마토 수확 가격 설정"
```

### 2. 원격 저장소에 푸시
```bash
git push origin feature/your-feature-name
```

### 3. Pull Request 생성
- GitHub에서 Pull Request를 생성하세요.
- 제목은 간결하고 명확하게 작성하세요.
- 설명에는 다음 내용을 포함하세요:
  - 변경사항 요약
  - 변경 이유
  - 테스트 방법
  - 관련 이슈 번호

### Pull Request 템플릿
```markdown
## 변경사항 요약
새로운 작물 '토마토'를 추가했습니다.

## 변경 이유
게임의 다양성을 높이고 플레이어에게 더 많은 선택지를 제공하기 위함입니다.

## 변경 내용
- [ ] 새로운 이미지 파일 추가
- [ ] PLANT_INFO 업데이트
- [ ] 상점 아이템 추가
- [ ] 수확 가격 설정
- [ ] 테스트 코드 작성

## 테스트 방법
1. 게임 실행
2. 상점에서 토마토 씨앗 구매
3. 토마토 심기 및 성장 확인
4. 토마토 수확 및 수익 확인

## 관련 이슈
Closes #123
```

## 😊 코드 리뷰

### 리뷰 요청 시
- 코드 리뷰어를 지정하세요.
- 변경사항에 대한 설명을 명확하게 작성하세요.
- 테스트 결과를 포함하세요.

### 리뷰 응답 시
- 건설적인 피드백을 제공하세요.
- 코드 품질과 가독성을 중점적으로 검토하세요.
- 보안 문제나 성능 이슈가 있는지 확인하세요.

## 😘 문서 업데이트

### 코드 변경 시 문서도 함께 업데이트
- 새로운 기능에 대한 설명 추가
- API 문서 업데이트
- 설치 가이드 업데이트
- 예제 코드 업데이트

## 🎯 기여 영역

### 현재 필요한 기능들
- [ ] 새로운 작물 추가
- [ ] 새로운 도구 추가
- [ ] 새로운 엔딩 추가
- [ ] 성능 최적화
- [ ] 버그 수정
- [ ] 문서 개선
- [ ] 테스트 코드 추가

### 우선순위
1. **높음**: 버그 수정, 보안 이슈
2. **중간**: 새로운 기능, 성능 개선
3. **낮음**: 문서 개선, 코드 스타일

## 🏆 기여자 명예의 전당

프로젝트에 기여해주신 분들의 이름을 여기에 기록합니다.

- [@username](https://github.com/username) - 초기 개발 및 핵심 시스템 구현
- [@contributor](https://github.com/contributor) - UI 개선 및 버그 수정

## �� 문의

기여 과정에서 궁금한 점이 있으시면:
- [Issues](https://github.com/yourusername/farm-simulator/issues)에 질문을 올려주세요.
- [Discussions](https://github.com/yourusername/farm-simulator/discussions)에서 토론에 참여하세요.

감사합니다! 🎉
```

### 🙌 docs/CHANGELOG.md
```markdown
# 변경 이력

이 파일은 농장 시뮬레이터의 모든 중요한 변경사항을 기록합니다.

## [Unreleased]

### 추가된 기능
- 새로운 작물 시스템
- 도구 업그레이드 시스템
- 엔딩 시스템

### 변경된 기능
- UI 개선
- 성능 최적화

### 수정된 버그
- 이미지 로드 오류 수정
- 사운드 재생 문제 해결

## [1.0.0] - 2024-01-01

### 추가된 기능
- 기본 게임 시스템
- 5가지 작물 (해바라기, 상추, 감자, 완두콩, 용과)
- 기본 도구 (물뿌리개, 낫)
- 씨앗 상점
- 게임 저장/불러오기
- 설정 시스템 (볼륨 조절)
- 배경음악 및 효과음
- 튜토리얼 시스템

### 기술적 특징
- Python 3.8+ 지원
- Pygame 기반 2D 그래픽
- JSON 기반 데이터 저장
- 모듈화된 코드 구조

## [0.9.0] - 2023-12-15

### 추가된 기능
- 프로토타입 게임 시스템
- 기본 작물 성장 메커니즘
- 간단한 UI

### 알려진 문제
- 메모리 누수
- 성능 이슈
- 일부 이미지 로드 실패

## [0.8.0] - 2023-12-01

### 추가된 기능
- 초기 게임 엔진
- 기본 렌더링 시스템
- 이벤트 처리 시스템

## 기여자

- [@username](https://github.com/username) - 프로젝트 리더
- [@contributor](https://github.com/contributor) - 개발자
- [@tester](https://github.com/tester) - 테스터

## 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.
```

이제 docs 폴더와 모든 문서 파일을 생성하겠습니다. 각 문서는 체계적으로 구성되어 있으며, 개발자와 사용자 모두가 프로젝트를 이해하고 기여할 수 있도록 작성되었습니다.

주요 문서들:
1. **README.md** - 프로젝트 개요 및 시작 가이드
2. **INSTALLATION.md** - 상세한 설치 가이드
3. **API_REFERENCE.md** - 개발자를 위한 API 문서
4. **CONTRIBUTING.md** - 기여 가이드
5. **CHANGELOG.md** - 변경 이력

이 문서들은 프로젝트의 구조, 기능, 개발 방법 등을 포괄적으로 다루고 있습니다.
