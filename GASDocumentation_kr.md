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

**GAS는 기본적으로 C++로 개발하는 것을 권장합니다.** 하지만 `GameplayAbilities`와 `GameplayEffects`는 디자이너가 블루프린트에서 생성할 수도 있습니다.

현재 알려진 GAS 이슈:
* `GameplayEffect` latency reconciliation (can't predict ability cooldowns resulting in players with higher latencies having lower rate of fire for low cooldown abilities compared to players with lower latencies).
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* Lack of boilerplate templates, multiplayer examples, and documentation. Hopefully this helps with that!

**[⬆ 위로 가기](#table-of-contents)**

<a name="sp"></a>


## 2. 샘플 프로젝트
이 문서에는 언리얼 엔진을 처음 접하는 분들을 위해 멀티플레이어 3인칭 슈팅 샘플 프로젝트가 포함되어 있습니다. 이 글을 통해 GAS를 접하시는 분들은 언리얼 엔진의 C++, 블루프린트, UMG, 리플리케이션 및 기타 중급 주제에 대해 어느 정도 알고 계셔야 합니다. 이 프로젝트는 기본적인 3인칭 슈팅 멀티플레이어 프로젝트를 설정하는 예제를 제공합니다. 플레이어와 AI가 제어하는 영웅은 PlayerState 클래스의 AbilitySystemComponent(ASC)를 사용하고, AI가 제어하는 미니언은 Character 클래스의 ASC를 사용하는 방식입니다.

이 프로젝트의 목표는 게임 개발에서 흔히 요청되는 Ability를 잘 설명된 코드로 시연하면서, GAS의 기본에 대해 최대한 단순하게 설명하는 것입니다. 이 프로젝트는 초급자를 대상으로 하기 때문에 [발사체 예측](#concepts-p-spawn)과 같은 고급 주제는 보여주지 않습니다.

시연 내용:
*PlayerState와 Character의 ASC
*리플리케이션된 Attribtute
*리플리케이션된 애니메이션 몽타주
*GameplayTag 사용 예시
*GameplayAbility 내부 및 외부에서 GameplayEffect 적용 및 제거
*캐릭터의 체력 변화 시 갑옷에 의한 피해 데미지 적용
*GameplayEffectExecutionCalculation 사용 예시
*스턴 효과
*죽음 및 리스폰
*서버의 어빌리티에서 액터(발사체) 스폰
*조준 사격 및 질주 시 로컬 플레이어의 속도를 예측적으로 변화시키는 효과
*질주 시 스태미나를 지속적으로 소모
*마나를 사용하여 어빌리티 발동
*패시브 어빌리티
*GameplayEffect 중첩
*액터 타겟팅
*블루프린트로 GameplayAbility 생성
*C++로 GameplayAbility 생성
*액터별 인스턴스화된 GameplayAbility 
*인스턴스화되지 않은 GameplayAbility(점프)
*정적 GameplayCue (파이어건 발사체 충격 파티클 이펙트)
*액터 GameplayCue (질주 및 기절 파티클 이펙트)

Hero 클래스에는 다음과 같은 어빌리티가 존재합니다:

| 어빌리티                    | 입력 바인드         | Predicted  | C++ / Blueprint | 설명                                                                                                                                                                 |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 점프                      | 스페이스바           | Yes        | C++             | 영웅이 점프합니다.                                                                                                                                                         |
| 총                       | 마우스 왼쪽 버튼   | No         | C++             | 영웅의 총에서 발사체를 발사합니다. 애니메이션은 예측되지만 발사체는 예측되지 않습니다.                                                                                |
| 조준경 조준            | 마우스 오른쪽 버튼  | Yes        | Blueprint       | 버튼을 누른 상태에서 영웅은 천천히 걷고 카메라는 확대되어 총으로 더 정확한 조준을 할 수 있습니다.                                                    |
| 질주                     | 왼쪽 시프트          | Yes        | Blueprint       | 버튼을 누른 상태에서 영웅은 스테미나를 사용하여 더 빠른 속도로 달릴 수 있습니다.                                                                                               |
| 전방 대쉬               | Q                   | Yes        | Blueprint       | 스테미나를 소모하여 전방으로 돌진합니다.                                                                                                                              |
| 아머 스택(패시브)       | 패시브             | No         | Blueprint       | 매 4초마다 영웅은 최대 4개의 방어력를 획득합니다. 데미지를 받으면 방어력 스택 하나가 제거됩니다.                                                    |
| 메테오                     | R                   | No         | Blueprint       | 플레이어는 적이 위치한 곳에 메테오를 떨어뜨려 피해를 입히고 기절시킵니다. 타겟팅는 예측되지만 스폰되는 메테오는 예측되지 않습니다.                     |

`GameplayAbilities`가 C++ 또는 Blueprint에서 만들었는지 여부는 중요하지 않습니다. 위 Ability들은 두 가지를 혼합하여 각 언어마다 어떻게 사용하는지를 설명하기 위함입니다.

미니언에는 미리 정의된 `GameplayAbilities`가 없습니다. 붉은 미니언들은 체력 회복량이 많고, 푸른 미니언들은 시작 체력이 좀 더 높습니다.

`GameplayAbilities`의 이름을 지을 때, `GameplayAbility`의 로직이 Blueprint에 생성되었음을 나타내기 위해 접미사 `_BP`를 사용했습니다. 접미사가 없으면 로직이 C++로 생성되었다는 것을 의미합니다.


**블루프린트 에셋 작명 접두사**

| 접두사      | 에셋 타입         |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ 위로 가기](#table-of-contents)**

<a name="setup"></a>

## 3. GAS를 사용하는 프로젝트 설정
GAS를 사용하여 프로젝트를 설정하는 기본 단계:
1. 에디터에서 GameplayAbilitySystem 플러그인 활성화.
1. `[YourProjectName].Build.cs` 파일의 `PrivateDependencyModuleNames`에 "GameplayAbilities", "GameplayTags","GameplayTasks"을 추가.
1. Visual Studio 프로젝트 파일 새로 고침/재생성.
1. 4.24부터는 `UAbilitySystemGlobals::Get().InitGlobalData()`를 호출하여 [`TargetData`](#concepts-targeting-data)를 사용합니다. 샘플 프로젝트는 이 작업을 UAssetManager::StartInitialLoading()을 수행합니다. 자세한 내용은 [`InitGlobalData()`](#concepts-asg-initglobaldata)를 참조해주세요.

이것으로 GAS를 활성화하는 데 필요한 모든 작업이 완료되었습니다. 이제 `Character` 혹은 `PlayerState`에 [`ASC`](#concepts-asc)와 [`AttributeSet`](#concepts-as)을 추가하고 [`GameplayAbilities`](#concepts-ga) 및 [`GameplayEffects`](#concepts-ge)를 만들기 시작해보세요!

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
`AbilitySystemComponent` (`ASC`)는 GAS의 핵심입니다. 시스템과의 모든 상호 작용을 처리하는 `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html))입니다. [`GameplayAbilities`](#concepts-ga)을 사용하거나[`Attributes`](#concepts-a)를 가지거나, [`GameplayEffects`](#concepts-ge)를 받으려는 `액터`에는 반드시 `ASC`가 하나씩 붙어 있어야 합니다. 이러한 오브젝트는 모두 `ASC` 안에 존재하며, ([`AttributeSet`](#concepts-as)에 의해 리플리케이트되는`Attributes`를 제외하고) `ASC`에 의해 관리 및 리플리케이트됩니다. 이를  subclass할 것을 권장드리지만 필수는 아닙니다.

 `ASC`가 붙은 `액터`를 `ASC`의 `OwnerActor`라고 합니다. 그리고 `ASC`의 물리적 표현 `액터`를 `AvatarActor`라고 합니다. MOBA 게임에서 단순한 AI 미니언의 경우처럼 `OwnerActor`와 `AvatarActor`가 동일한 `액터`일 수도 있고, 혹은 플레이어가 조종하는 영웅과 같이 `OwnerActor`가 `PlayerState`이고 `AvatarActor`가 영웅의 `Character` 클래스인 경우도 있을 수도 있습니다. 대부분의 `액터`는 자체적으로 `ASC`를 갖습니다. MOBA 게임의 영웅처럼 `액터`가 리스폰되고 스폰 사이에 `Attributes` 또는 `GameplayEffects`의 지속성이 필요한 경우, `PlayerState` 클래스가 `ASC`의 이상적인 위치입니다.

 **Note:** `ASC`가 `PlayerState`에 있는 경우, `PlayerState`의 `NetUpdateFrequency`를 늘려야 합니다.  기본적으로 `PlayerState`에서 매우 낮은 값으로 설정되어 있어 클라이언트에서`Attributes` 그리고 `GameplayTags` 등의 변경이 일어나기 전에 지연이 발생하거나 감지될 수 있습니다. [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)를 활성화하세요. 포트나이트 또한 이를 사용합니다.

 `OwnerActor`와 `AvatarActor` 가 다른 `액터`인 경우 둘 다 `IAbilitySystemInterface`를 구현해야 합니다. 이 인터페이스에는 반드시 오버라이드해야 하는 함수가 하나 있는데, 바로 `ASC`에 대한 포인터를 반환하는 `UAbilitySystemComponent* GetAbilitySystemComponent() const`입니다. `ASC`는 이 인터페이스 함수를 찾아 시스템 내부에서 서로 상호작용합니다.

 `ASC`는 현재 활성화된 `GameplayEffects`를 `FActiveGameplayEffectsContainer ActiveGameplayEffects`에 보관합니다.

`ASC`는 부여된 `Gameplay Abilities`를 `FGameplayAbilitySpecContainer ActivatableAbilities`에서 보관합니다. `ActivatableAbilities.Items`을 반복처리할 때마다, 어빌리티 제거로 인해 목록이 변경되지 않도록 루프 위에 `ABILITYLIST_SCOPE_LOCK();`을 추가해야 합니다. 범위 내 모든 `ABILITYLIST_SCOPE_LOCK();`은 `AbilityScopeLockCount`를 증가시킨 다음 범위를 벗어나면 감소합니다. `ABILITYLIST_SCOPE_LOCK();` 범위 내에서 어빌리티를 제거하려고 시도하시면 안됩니다. (어빌리티를 지우는 함수는 내부적으로 `AbilityScopeLockCount`를 확인하여 목록에 락이 걸렸을 경우 어빌리티를 제거하지 못하도록 합니다).

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode

`ASC`는 `GameplayEffects`, `GameplayTags` 및 `GameplayCues`를 리플리케이트하기 위한 세 가지의 리플리케이션 모드(`Full`, `Mixed`, and `Minimal`)를 지정할 수 있습니다. `Attributes`는 해당 `AttributeSet`에 의해 리플리케이트됩니다.

| 리플리케이션 모드   | 사용 시기                          | 설명                                                                                                                   |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | Single Player                           | 모든 `GameplayEffects`가 모든 클라이언트에 리플리케이트됩니다.                                                                          |
| `Mixed`            | Multiplayer, Player가 컨트롤하는 `액터` | `GameplayEffects`는 소유 클라이언트에만 리플리케이트됩니다. `GameplayTags`와 `GameplayCues`만 모두에게 리플리케이트됩니다. |
| `Minimal`          | Multiplayer, AI가 컨트롤하는 `액터`     | `GameplayEffects`가 아무에게도 리플리케이트되지 않습니다. `GameplayTags`와 `GameplayCues`만 모두에게 리플리케이트됩니다.           |

**Note:** `Mixed` 리플리케이션 모드에서는 `OwnerActor's`의 소유자가 `Controller`일 것으로 예상합니다. 기본적으로 `PlayerState's`의 소유자는 `Controller`이지만 `Character's`의 소유자는 그렇지 않습니다.  `Mixed` 리플리케이션 모드를`PlayerState`가 아닌 `OwnerActor`와 함께 사용하시는 경우, 유효한 `Controller`를 가진  `OwnerActor`의 `SetOwner()`를 호출해야 합니다.

4.24부터 `PossessedBy()`는 이제`Pawn`의 소유자를 새 `Controller`로 설정합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 설정 및 초기화

`ASC`는 일반적으로 `OwnerActor`의 생성자에서 생성되며 명시적으로 리플리케이트된 것으로 표시됩니다. **이 작업은 C++에서 수행해야 합니다.**

```c++
AGDPlayerState::AGDPlayerState()
{
	// 어빌리티 시스템 컴포넌트를 생성하고 명시적으로 리플리케이트되도록 설정합니다.
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```