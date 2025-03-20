# GAS 문서 번역본

해당 문서는 Unreal Engine 5의 멀티플레이어 샘플 프로젝트의 GameplayAbilitySystem 플러그인의 이해를 돕는 문서입니다. 이 문서는 공식 문서가 아니며, 프로젝트 역시 에픽게임즈의 검증을 받지 않았습니다. 따라서 문서 정보의 정확성을 보장하지 않습니다.

이 문서는 GAS의 주요 개념과 클래스들을 설명하고, 경험을 바탕으로 몇 가지 추가 코멘트를 제공하는 것을 목적으로 합니다.

샘플 프로젝트와 문서는 **언리얼 엔진 5.3**(UE5) 버전입니다. 이전 버전의 언리얼 엔진을 위한 이 문서의 브랜치가 있지만, 더 이상 지원되지 않으며 버그나 오래된 정보가 있을 수 있습니다. 사용 중인 엔진 버전과 일치하는 브랜치를 사용하시기 바랍니다.

[GASShooter](https://github.com/tranek/GASShooter)는 멀티플레이어 FPS/TPS를 위한 GAS의 심화 기술을 보여주는 또 다른 샘플 프로젝트입니다.

또한 언제나 플러그인의 원본 소스 코드를 참고하는 것이 좋습니다.

<a name="table-of-contents"></a>
## 목차

> 1. [GameplayAbilitySystem Plugin의 소개](#intro)
> 1. [샘플 프로젝트](#sp)
> 1. [GAS 프로젝트 세팅 방법](#setup)
> 1. [GAS Concepts](#concepts)  


<a name="intro"></a>
## 1. GameplayAbilitySystem Plugin의 소개
[Official Documentation](https://docs.unrealengine.com/ko-kr/Gameplay/GameplayAbilitySystem/index.html) 참고,
>게임플레이 어빌리티 시스템(Gameplay Ability System) 은 RPG나 MOBA 타이틀에서 볼 수 있는 어빌리티 및 어트리뷰트 유형을 구축하기 위한 고도로 유연한 프레임워크입니다. 게임 내 캐릭터가 사용할 액션이나 패시브 어빌리티, 이러한 액션의 결과로 다양한 어트리뷰트를 높이거나 낮추는 상태 이펙트를 만들 수 있고, '재사용 대기 시간' 타이머나 자원 비용을 구현하여 액션의 사용 빈도를 조절하거나, 어빌리티의 레벨과 레벨에 따른 이펙트를 변경하거나, 파티클 및 사운드 이펙트를 활성화하는 등의 작업이 가능합니다. 게임플레이 어빌리티 시스템을 사용하면 점프처럼 단순한 것부터 최신 RPG나 MOBA 타이틀 내 인기 캐릭터의 기술 모음처럼 복잡한 것까지 다양한 인게임 어빌리티를 설계 및 구현하고 효과적으로 연결할 수 있습니다.

게임플레이어빌리티시스템 플러그인은 Epic Games에서 개발했으며 Unreal Engine에서 사용 가능합니다. 또한 해당 플러그인은 Paragon, Fortnite와 같은 AAA급 상용 게임에서 테스트와 검증을 마쳤습니다.

플러그인은 싱글 및 멀티플레이어 게임에서 바로 사용할 수 있는 다음과 같은 솔루션들을 제공합니다:
* 조정가능한 자원과 쿨타임을 가진 level-based 캐릭터 능력 혹은 스킬 구현([GameplayAbilities](#concepts-ga))
* 액터가 소유한 수치(데이터)`Attributes` 조작([Attributes](#concepts-a))
* 액터에 상태 효과 적용 ([GameplayEffects](#concepts-ge))
* 엑터에 `GameplayTags` 적용 ([GameplayTags](#concepts-gt))
* 시각 효과 또는 음향 효과 생성 ([GameplayCues](#concepts-gc))
* 위에 언급된 모든 요소들의 Replication 지원

멀티플레이어 게임에서 GAS는 [client-side prediction](#concepts-p)을 지원합니다:
* 어빌리티 활성화
* 애니메이션 몽타주 재생
* `Attributes` 변경
* `GameplayTags` 적용
* `GameplayCues` 스폰
* `CharacterMovementComponent`에 연결된 `RootMotionSource`를 통한 움직임

**GAS는 기본적으로 C++로 개발하는 것을 권장합니다.**, 하지만 `GameplayAbilities`와 `GameplayEffects`는 디자이너가 블루프린트에서 생성할 수도 있습니다.

현재 알려진 GAS 이슈:
* `GameplayEffect` latency reconciliation (can't predict ability cooldowns resulting in players with higher latencies having lower rate of fire for low cooldown abilities compared to players with lower latencies).
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* Lack of boilerplate templates, multiplayer examples, and documentation. Hopefully this helps with that!

**[⬆ 위로 가기](#table-of-contents)**

<a name="sp"></a>
