# 기여 가이드

## �� 기여 방법

농장 시뮬레이터 프로젝트에 기여하고 싶으시다면 감사합니다! 
이 문서는 프로젝트에 기여하는 방법을 안내합니다.

## �� 기여하기 전에

### 1. 이슈 확인
- [Issues](https://github.com/yourusername/farm-simulator/issues) 페이지에서 
  이미 보고된 문제나 제안사항이 있는지 확인하세요.
- 비슷한 이슈가 있다면 기존 이슈에 댓글로 참여하세요.

### 2. 프로젝트 구조 이해
- [README.md](README.md)를 읽어 프로젝트의 목적과 구조를 이해하세요.
- [API_REFERENCE.md](API_REFERENCE.md)를 참고하여 코드 구조를 파악하세요.

## �� 기여 시작하기

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

## �� 코드 리뷰

### 리뷰 요청 시
- 코드 리뷰어를 지정하세요.
- 변경사항에 대한 설명을 명확하게 작성하세요.
- 테스트 결과를 포함하세요.

### 리뷰 응답 시
- 건설적인 피드백을 제공하세요.
- 코드 품질과 가독성을 중점적으로 검토하세요.
- 보안 문제나 성능 이슈가 있는지 확인하세요.

## �� 문서 업데이트

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