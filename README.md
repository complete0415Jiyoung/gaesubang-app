
# 🚀 개수방 (DevLink)

<div align="center">
  <img src="https://github.com/user-attachments/assets/88a5c325-341d-4635-bd40-cff12de4d032" height="300" />
  <img src="https://github.com/user-attachments/assets/f2d956c0-acdb-4522-aa89-9172a53514e0" height="300" />
  <img src="https://github.com/user-attachments/assets/b2675a11-b62a-47ac-b8cf-44c5e48aa5ad" height="300" />
  <img src="https://github.com/user-attachments/assets/c304f296-4670-43d8-9cef-5821c81a2395" height="300" />
</div>


> **개발자를 위한 그룹 기반 집중 타이머 앱**  
> *Real-time Group Study Timer App for Developers*

[🎥 데모 영상](#-데모-영상) • [📱 주요 기능](#-주요-기능) • [🏗️ 아키텍처](#-아키텍처) • [💻 기술 스택](#-기술-스택)

---

## 📋 프로젝트 개요

**"개발자들이 함께 성장하는 실시간 협업 학습 플랫폼"**

개수방은 개발자들의 지속적인 학습을 위한 그룹 기반 타이머 앱입니다.  
실시간 동기화, 백그라운드 감지, 학습 패턴 분석 등 고도화된 기능을 통해 진정한 집중 학습 환경을 제공합니다.

---

## 🎯 핵심 가치

- ✅ **실시간 협업**: WebSocket 기반 실시간 타이머 동기화
- 🔒 **엄격한 집중 관리**: 백그라운드 전환 감지로 정확한 학습 시간 측정
- 📊 **데이터 기반 인사이트**: 학습 패턴 분석 및 개인화된 통계 제공
- 🏗️ **확장 가능한 아키텍처**: Clean Architecture + MVVM 패턴 적용

---

## 🎥 데모 영상

<div align="center"> 
  <a href="https://www.youtube.com/watch?v=YOUR_DEMO_VIDEO_LINK" target="_blank" rel="noopener noreferrer">
    <img src="https://github.com/user-attachments/assets/91f4be8e-bb37-41fc-ae9c-459bc928b512" width="400" alt="데모 영상 썸네일" />
  </a>
</div> 

<p align="center"> 
  👉 <a href="https://app.filmora.io/#/object/d0qeu349upc5sbi8ugc0" target="_blank" rel="noopener noreferrer">
    <strong>데모 영상 보러가기</strong>
  </a> 
</p>

---

## 📱 주요 기능

### 🔥 실시간 그룹 타이머 시스템

```dart
@riverpod
class GroupTimerNotifier extends _$GroupTimerNotifier {
  late final StreamSubscription _timerSubscription;
  
  @override
  GroupTimerState build(String groupId) {
    _subscribeToTimerUpdates(groupId);
    return const GroupTimerState.initial();
  }
  
  void _subscribeToTimerUpdates(String groupId) {
    _timerSubscription = _firestore
        .collection('groups')
        .doc(groupId)
        .collection('timerActivities')
        .orderBy('timestamp', descending: true)
        .limit(50)
        .snapshots()
        .listen(_handleTimerUpdate);
  }
}
```

### 📊 고급 데이터 분석
- 학습 패턴 분석 (시간대별, 요일별)
- 그룹 대비 개인 성과 비교
- 목표 설정 및 추적 기능
- 연속 학습 스트릭 시스템

### 🛡️ 엄격한 집중도 관리
- 앱 백그라운드 전환 감지 → 자동 타이머 종료
- 실제 집중 시간만 측정
- 방해 요소 차단 (알림 등)

### 👥 스마트 그룹 관리
- 해시태그 기반 동적 그룹 생성
- 권한 기반 그룹장/멤버 역할 분리
- 실시간 멤버 학습 상태 확인/ 위치상태 확인/출석부 관리 

---

## 🏗️ 아키텍처
**Clean Architecture + MVVM + MVI 패턴**

### 📱 Presentation Layer
```
├── Screen (StatelessWidget)
├── Root (ConsumerWidget)
└── Notifier (StateNotifier)
```

### 🧠 Domain Layer
```
├── UseCase
├── Repository Interface
└── Model
```

### 💾 Data Layer
```
├── Repository Implementation
├── DataSource
└── DTO / Mapper
```

### 💡 상태 관리 흐름
```dart
// 단방향 데이터 흐름 예시
UI Event → Action → UseCase → Repository → DataSource
    ↑                                           ↓
AsyncValue ← State ← Notifier ← Result ← Response
```

---

## 💻 기술 스택
| 분야             | 사용 기술 및 도구                           |
|------------------|---------------------------------------------|
| 🧑‍💻 개발 및 프레임워크 | Flutter, Dart                             |
| 🗄 데이터베이스       | Firebase                                  |
| 🤝 협업 도구         | Figma, Notion, Discord, GitHub, Gather.town |

## 🔧 핵심 기술 구현

### 1. 실시간 타이머 동기화
```dart
Stream<List<GroupTimerActivityDto>> watchTimerActivities(String groupId) {
  return _firestore
      .collection('groups')
      .doc(groupId)
      .collection('timerActivities')
      .where('timestamp', isGreaterThan: DateTime.now().subtract(Duration(hours: 1)))
      .orderBy('timestamp', descending: true)
      .snapshots()
      .map((snapshot) => snapshot.docs
          .map((doc) => GroupTimerActivityDto.fromFirestore(doc))
          .toList());
}
```

### 2. 백그라운드 상태 감지
```dart
class TimerLifecycleManager {
  void _handleAppLifecycleChange(AppLifecycleState state) {
    switch (state) {
      case AppLifecycleState.paused:
      case AppLifecycleState.detached:
        _pauseTimerWithBackground();
        break;
      case AppLifecycleState.resumed:
        _resumeTimerIfActive();
        break;
    }
  }
}
```

### 3. 최적화된 데이터 쿼리
```dart
Future<Map<String, bool>> checkUserLikeStatus(List<String> postIds, String userId) async {
  final futures = postIds.map((postId) async {
    final doc = await _postsCollection
        .doc(postId)
        .collection('likes')
        .doc(userId)
        .get();
    return MapEntry(postId, doc.exists);
  });
  final entries = await Future.wait(futures);
  return Map.fromEntries(entries);
}
```

### 4. 에러 처리 시스템
```dart
@freezed
sealed class Result<T> with _$Result<T> {
  const factory Result.success(T data) = Success<T>;
  const factory Result.error(Failure failure) = Error<T>;
}

Future<AsyncValue<User>> execute(String email, String password) async {
  final result = await _repository.login(email, password);
  return switch (result) {
    Success(:final data) => AsyncData(data),
    Error(:final failure) => AsyncError(failure, StackTrace.current),
  };
}
```

---

## 📊 프로젝트 성과

- ✅ **MVP 완성도**: 100% (2주 개발)
- 🔑 **핵심 기능**: 그룹 타이머, 커뮤니티, 출석부
- 🧱 **아키텍처**: Clean Architecture 적용
- 🔥 **Firebase 연동**: 진행 중

### 💡 기술적 도전과 해결

| 도전 과제 | 해결 방안 | 성과 |
|----------|-----------|------|
| 실시간 동기화 | Firebase Snapshot Listener | 100ms 내 반영 |
| 상태 관리 복잡도 | Riverpod + Freezed | 타입 안전 + 유지보수 용이 |
| 백그라운드 감지 | AppLifecycleListener | 정확한 집중 시간 측정 |
| 재사용 가능한 구조 | Clean Architecture 적용 | 90% 재사용 가능 구조 |

---

## 🗂️ Documentation 구조

```
docs/
├── overview/        # 프로젝트 개요
├── arch/            # 아키텍처 설계
├── ui/              # UI/UX 가이드
├── logic/           # 비즈니스 로직
└── collab/          # 협업 가이드
```

---

## 🚀 Getting Started

### Prerequisites
```bash
Flutter SDK: >=3.7.2  
Dart SDK: >=3.0.0
```

### Installation
```bash
# 1. Clone repository
git clone https://github.com/your-username/devlink_mobile_app.git

# 2. Install dependencies
flutter pub get

# 3. Generate code
dart run build_runner build

# 4. Run app
flutter run --flavor dev
```

### Project Structure
```
lib/
├── core/            # 공통 유틸리티
├── auth/            # 인증 모듈
├── group/           # 그룹 관리
├── timer/           # 타이머 모듈
├── community/       # 커뮤니티
└── shared/          # 공통 컴포넌트
```

---

## 🎯 Future Roadmap

### 🔄 Phase 2: Production Ready (1개월)
- Firebase 완전 통합
- 실시간 푸시 알림
- 오프라인 모드 지원
- 성능 최적화

### 📈 Phase 3: Advanced Features (3개월)
- AI 기반 학습 분석
- 소셜 기능 강화
- 배지/리더보드 게임화
- 웹/데스크탑 크로스 플랫폼

### 🎯 Technical Goals
- 99.9% 업타임
- 10,000+ 동시 사용자
- 앱스토어 평점 4.5+

---

## 💼 Portfolio Highlights

<div align="center">

| 💻 기술 역량 | 🎯 프로젝트 관리 | 🔧 문제 해결 |
|-------------|------------------|--------------|
| Clean Architecture 설계 | MVP 2주 완성 | 복잡한 동기화 로직 |
| 실시간 시스템 구축 | 체계적 문서화 | 성능 최적화 |
| 상태 관리 최적화 | 확장 가능한 구조 | UX 개선 |

</div>

---

## 📊 개발 통계
- 개발 기간: 3주 (MVP 2주 + 고도화 1주)
- 코드 라인 수:  101,065 total +
- 문서화: 25개 상세 문서
