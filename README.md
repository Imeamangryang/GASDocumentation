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
>	 4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [설정 및 초기화](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [GameplayTag 변경에 응답하기](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [플러그인 .ini 파일에서 GameplayTag 불러오기](#concepts-gt-loadfromplugin)
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute 정의](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Attribute 변경에 응답하기](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [AttributeSet 정의](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [AttributeSet 설계](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [개별 Attribute를 가진 서브 컴포넌트](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [런타임에 AttributeSet 추가 및 제거하기](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [아이템 Attribute (무기 탄약)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [아이템에 일반 float 사용](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [아이템의 `AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [아이템의 `ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Attribute 정의](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Attribute 초기화](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  

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

 > **Note:** `ASC`가 `PlayerState`에 있는 경우, `PlayerState`의 `NetUpdateFrequency`를 늘려야 합니다.  기본적으로 `PlayerState`에서 매우 낮은 값으로 설정되어 있어 클라이언트에서`Attributes` 그리고 `GameplayTags` 등의 변경이 일어나기 전에 지연이 발생하거나 감지될 수 있습니다. [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)를 활성화하세요. 포트나이트 또한 이를 사용합니다.

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

> **Note:** `Mixed` 리플리케이션 모드에서는 `OwnerActor's`의 소유자가 `Controller`일 것으로 예상합니다. 기본적으로 `PlayerState's`의 소유자는 `Controller`이지만 `Character's`의 소유자는 그렇지 않습니다.  `Mixed` 리플리케이션 모드를`PlayerState`가 아닌 `OwnerActor`와 함께 사용하시는 경우, 유효한 `Controller`를 가진  `OwnerActor`의 `SetOwner()`를 호출해야 합니다.

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

`ASC`는 서버와 클라이언트 모두 `OwnerActor` 및 `AvatarActor`를 사용하여 초기화해야 합니다. `Pawn`의 `Controller`를 설정한 후(소유한 후) 초기화할 수 있습니다. 싱글 플레이어 게임은 서버 경로만 신경쓰면 됩니다.

`ASC`가 `Pawn`에 있는 플레이어 캐릭터의 경우 `Pawn`의 `PossessedBy()` 함수를 서버에서 초기화하고 `PlayerController` 의 `AcknowledgePossession()` 함수를 클라이언트에서 초기화합니다.

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC Mixed 모드에서 리플리케이션이 되려면 ASC 소유자의 소유자가 컨트롤러여야 합니다.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

플레이어가 제어하는 캐릭터가 `PlayerState`에 존재하는 `ASC`의 경우, 일반적으로 `Pawn`의 `PossessedBy()` 함수에서 서버를 초기화하고 `Pawn`의`OnRep_PlayerState()` 함수에서 클라이언트를 초기화합니다. 이렇게 하면 `PlayerState`가 클라이언트에 존재하게 됩니다.

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 서버에서 ASC를 설정합니다. 클라이언트는 OnRep_PlayerState()에서 이 작업을 수행합니다.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI에는 PlayerController가 없으므로 여기서 다시 초기화할 수 있습니다.   
		// PlayerController가 있는 영웅일 경우 두 번 초기화해도 아무런 문제가 없습니다.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 클라이언트에 대한 ASC를 설정합니다. 서버는 PossessedBy에서 이 작업을 수행합니다.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// 클라이언트에 대한 ASC 액터 정보를 초기화합니다. 서버가 새 액터를 보유하면 ASC를 초기화합니다.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

만약 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`라는 메시지가 표시될 경우 클라이언트에서 `ASC`를 초기화하지 않은 것입니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gt"></a>

### 4.2 Gameplay Tags

[`FGameplayTags`](https://docs.unrealengine.com/ko-kr/API/Runtime/GameplayTags/FGameplayTag/index.html)는 `GameplayTagManager`에 등록된 `Parent.Child.Grandchild...`와 같은 형식의 계층적 이름입니다. 이러한 태그는 오브젝트의 상태를 분류하고 설명하는 데 매우 유용합니다. 예를 들어, 어떤 캐릭터가 기절할 경우 우리는 그 캐릭터에게 기절 시간 동안 `State.Debuff.Stun` `GameplayTag`을 줄 수 있습니다.

여러분은 아마 bool이나 열거형으로 다루던 것들을 `GameplayTags`로 바꾸고 객체에 특정 `GameplayTags`가 있는지 여부에 대해 boolean 논리로 체크하는 자신을 발견하게 될 겁니다.

오브젝트에 태그를 부여할 때는 일반적으로 해당 오브젝트에 `ASC`가 있는 경우 이를 추가하여 GAS가 해당 오브젝트와 상호작용할 수 있도록 합니다. `UAbilitySystemComponent`는 소유한 `GameplayTags`에 접근할 수 있는 함수를 제공하는 `IGameplayTagAssetInterface`를 구현합니다.

여러 개의 `GameplayTags`는`FGameplayTagContainer`에 저장할 수 있습니다. `FGameplayTagContainer`는 효율적으로 동작하기 때문에 단순 `TArray<FGameplayTag>` 쓰기보단 `GameplayTagContainer`를 사용하는 것이 좋습니다. 또한 태그는 표준 `FNames`이지만, 프로젝트 세팅에서 `Fast Replication`이 활성화된 경우 리플리케이션을 위해 `FGameplayTagContainer`에 함께 패킹하여 효율적으로 사용할 수 있습니다. `Fast Replication`을 사용하려면 서버와 클라이언트에 동일한 `GameplayTags` 목록이 있어야 합니다. 일반적으로는 문제가 되지 않기 때문에 이 옵션을 활성화하는 것이 좋습니다. `GameplayTagContainer`를 순회하고 싶을 경우 `TArray<FGameplayTag>`를 반환받는 것도 가능합니다.

`FGameplayTagCountContainer`에 저장된 `GameplayTags`에는 해당 `GameplayTag`의 인스턴스 수를 저장하는 `TagMap`이 있습니다. `FGameplayTagCountContainer`에 `GameplayTag`가 존재하는데도 `TagMapCount`가 0일 수 있습니다. 디버깅 했을 때 `ASC`에 아직 `GameplayTag`가 남아있는 경우 이 문제가 발생할 수 있습니다.`HasTag()`, `HasMatchingTag()`와 같은 함수는 `TagMapCount`를 검사하여 `GameplayTag`가 없거나 `TagMapCount`가 0인 경우 false를 반환합니다.

`GameplayTags`는 `DefaultGameplayTags.ini`에서 미리 정의해줘야 합니다. UE5 에디터부터는 프로젝트 세팅에 인터페이스를 제공하여 개발자가 `DefaultGameplayTags.ini`를 수동으로 편집하지 않고도 `GameplayTags`를 관리할 수 있도록 합니다. `GameplayTag` 에디터는 `GameplayTags` 생성, 이름 변경, 레퍼런스 검색, 삭제가 가능합니다.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

`GameplayTag` 참조를 검색하면 에디터에 익숙한 `Reference Viewer` 그래프가 표시되어 `GameplayTag`를 참조하는 모든 에셋이 표시됩니다. 하지만 `GameplayTag`를 참조하는 C++ 클래스는 표시되지 않습니다.

`GameplayTags` 이름을 바꾸면 리디렉션이 생성되어 원래 `GameplayTags`를 여전히 참조하는 에셋이 새 `GameplayTags`로 리디렉션할 수 있습니다. 가능하면 새 `GameplayTags`를 생성하고 모든 참조를 새 `GameplayTags`로 수동으로 업데이트한 다음 이전 G`GameplayTags`를 삭제하여 리디렉션이 생성되지 않도록 하는 것이 좋습니다.

`Fast Replication` 외에도, `GameplayTag` 에디터는 자주 복제되는 `GameplayTags`를 자동으로 채워 최적화할 수 있는 옵션을 제공합니다.

`GameplayEffect`에 `GameplayTags`를 추가한 경우 해당 `GameplayTags`는 리플리케이트됩니다. `ASC`는 리플리케이트되지 않는 `LooseGameplayTags`를 추가할 수 있으며, 이는 수동으로 관리해야 합니다. 샘플 프로젝트에서는 `State.Dead`에 `LooseGameplayTags`를 사용하여 소유 클라이언트가 생명력이 0으로 떨어졌을 때 즉시 반응할 수 있도록 합니다. 리스폰 시 에는 `TagMapCount`를 다시 0으로 설정합니다. `LooseGameplayTags`를 사용할 때는 `TagMapCount`를 수동으로 조정하는 것보다 `UAbilitySystemComponent::AddLooseGameplayTag()`와 `UAbilitySystemComponent::RemoveLooseGameplayTag()` 함수를 사용하는 것이 더 바람직합니다.

C++에서 `GameplayTag`에 대한 참조를 얻는 방법:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

부모 또는 자식 `GameplayTags` 가져오기와 같은 고급 `GameplayTag` 조작은 `GameplayTagManager`에서 제공하는 함수를 참고해주세요. `GameplayTagManager`에 액세스하려면 `GameplayTagManager.h`를 포함하고 `UGameplayTagManager::Get().FunctionName`과 같은 방식으로 호출합니다. `GameplayTagManager`는 실제로 `GameplayTags`를 관계형 노드(부모, 자식 등)로 저장하기 때문에 문자열 조작이나 비교 연산보다 더 빠르게 처리됩니다.

`GameplayTags`와 `GameplayTagContainers`에는 선택적으로 `UPROPERTY` 지정자인 `Meta = (Categories = "GameplayCue")`가 있어 Blueprint에서 `GameplayCue` 부모 태그를 가진 `GameplayTags`만 필터링하여 표시할 때 유용합니다. 이 변수는 `GameplayTag` 또는 `GameplayTagContainer` 변수가 `GameplayCues`에만 사용되어야 한다는 것을 알고 있을 때 유용합니다.

또 다른 방법으로는 `FGameplayCueTag`라는 구조체가 존재하는데, 이는 `FGameplayTag`를 캡슐화하고 Blueprint에서 `GameplayCue` 부모 태그를 가진 `GameplayTags`만 자동으로 필터링합니다.

함수에서 `GameplayTag` 파라미터를 필터링하려면 `UFUNCTION` 지정자 `Meta = (GameplayTagFilter = "GameplayCue")`를 사용합니다. 하지만 `GameplayTagContainer` 파라미터는 이 방식으로 필터링할 수 없습니다. 이를 가능하게 하려면 엔진을 수정해야 하는데, `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` 파일의 `SGameplayTagGraphPin::ParseDefaultValueData()`가 어떻게 `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`를 호출하고, `FilterString`을 `SGameplayTagWidget`에 전달하여 `SGameplayTagGraphPin::GetListContent()`에서 필터를 적용하는지 확인해 보세요. `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp`의 `GameplayTagContainer` 버전 함수는 메타 필드 속성을 확인하지 않고 필터를 전달합니다.

샘플 프로젝트에선 `GameplayTags`를 광범위하게 사용합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gt-change"></a>

### 4.2.1 GameplayTag 변경에 응답하기
`ASC`는 `GameplayTags`가 추가되거나 제거될 때 호출되는 델리게이트를 제공합니다. 이 델리게이트는 `EGameplayTagEventType`을 받아, `GameplayTag`가 추가/제거될 때만 실행되도록 하거나, `GameplayTag`의 `TagMapCount`가 변경될 때마다 실행되도록 지정할 수 있습니다.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(
	FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")),
	EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

콜백 함수에는 `GameplayTag`와 새로운 `TagCount`를 파라미터 받습니다.

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>

### 4.2.2 플러그인 .ini 파일에서 GameplayTag 불러오기

자신만의 .ini 파일에 `GameplayTags`가 포함된 플러그인을 만든 경우, 플러그인의 `StartupModule()` 함수에서 해당 플러그인의 `GameplayTag` .ini 디렉토리를 로드할 수 있습니다.

예를 들어, 언리얼 엔진에 포함된 CommonConversation 플러그인은 다음과 같이 처리합니다:

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

이 코드는 `Plugins\CommonConversation\Config\Tags` 디렉토리에서 `GameplayTags`가 포함된 .ini 파일을 찾고, 플러그인이 활성화된 경우 엔진 시작 시 프로젝트에 로드합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute 정의

`Attribute`는 [`FGameplayAttributeData`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 구조체로 정의된 float값입니다. 이 값들을 통해 캐릭터의 체력, 레벨, 포션의 충전 수 등 무엇이든 나타낼 수 있습니다. 게임플레이와 관련된 수치값이 `Actor`에 속해 있다면, 해당 값은 `Attribute`로 정의하는 것이 좋습니다. `Attribute`는 일반적으로 [`GameplayEffects`](#concepts-ge)에 의해서만 수정되어야 ASC가 변경 사항을 [예측](#concepts-p)할 수 있습니다.

`Attribute`는 [`AttributeSet`](#concepts-as)에 의해 정의되고 관리됩니다. `AttributeSet`은 `Attribute`를 리플리케이트하고 관리합니다. `Attribute`를 정의하는 방법은 [`AttributeSets`](#concepts-as) 섹션을 참조하세요.

**Tip:** 만약 에디터의 `Attribute` 목록에 `Attribute`을 표시하고 싶지 않다면, `Meta = (HideInDetailsView)`라는 `property specifier`를 사용하면 됩니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-a-value"></a>

#### 4.3.2 BaseValue vs CurrentValue

`Attribute`는 `BaseValue`와 `CurrentValue`라는 두 개의 값으로 구성됩니다. `BaseValue`는 `Attribute`의 영구적인 값이고 `CurrentValue`는 `BaseValue`와 `GameplayEffect`의 임시 수정값이 더해진 값입니다. 예를 들어, `Character`의 이동 속도 `Attribute`의 `BaseValue`가 600u/s(단위/초)라고 가정해보겠습니다. 아직 이동 속도를 변경하는 `GameplayEffect`가 없다면 `CurrentValue`도 600u/s일 것입니다. 여기서 일시적으로 50u/s 이동 속도 버프를 받으면 `BaseValue`는 600u/s로 동일하게 유지되고 `CurrentValue`는 600 + 50이 되어 총 650u/s가 됩니다. 이동 속도 버프가 만료되면 `CurrentValue`는 다시 `BaseValue`인 600u/s로 되돌아갑니다.

GAS를 처음 접하는 사람들이 `BaseValue`과 `Attribute`의 최대값의 개념을 혼동하여 잘못 처리하는 경우가 종종 있습니다. `BaseValue`와 최대값은 다른 개념입니다. 어빌리티나 UI에서 변경하거나 참조할 수 있는 `Attribute`의 최대값은 별도의 `Attribute`로 정의해야 합니다. 하드코딩된 최대값과 최소값의 경우, `FAttributeMetaData`로 `DataTable`을 정의하는 방법이 있지만, 구조체 위에 있는 에픽 게임즈의 주석을 보면 "아직 진행 중인 작업"이라고 되어 있습니다. 자세한 정보는 `AttributeSet.h`를 참조해보세요. 혼동을 방지하기 위해 어빌리티나 UI에서 참조할 수 있는 최대값은 별도의 `Attribute`로 만들고, `Attribute` 클램핑에만 사용되는 하드코딩된 최대값과 최소값은 `AttributeSet`에 하드코딩된 float로 정의하는 것을 권장드립니다. `Attribute` 클램핑은 `CurrentValue` 변경에 대한 [PreAttributeChange()](#concepts-as-preattributechange)와 `GameplayEffect`로부터의 `BaseValue` 변경에 대한 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)에서 논의됩니다.

`BaseValue`에 대한 영구적인 변경은 `Instant` `GameplayEffect`에서 발생하며, `Duration`과 `Infinite` `GameplayEffect`는 `CurrentValue`를 변경합니다. Periodic `GameplayEffect`는 `Instant` `GameplayEffect`처럼 취급되어 `BaseValue`를 변경합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-a-meta"></a>

#### 4.3.3 Meta Attributes

일부 `Attribute`는 `Attribute`와 상호작용하기 위해 임시 값에 대한 자리 표시자(placeholder)로 사용됩니다. 이러한 Attribute를  `Meta Attribute`라고 합니다. `GamepalyEffect`가 직접적으로 캐릭터의 체력 `Attribute`를 변경하는 대신, 데미지를 `Meta Attribute`로 정의하여 사용할 경우 데미지 값이 버프 및 디버프와 함께 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec)에서 수정될 수 있고, `AttributeSet`에서 추가로 조정하는 것도 가능합니다. 예를 들어, 데미지를 현재의 방어막 `Attribute`에서 먼저 차감한 후 나머지를 체력 `Attribute`에서 차감하는 식입니다. 데미지 `Meta  Attribute`는 `GameplayEffect` 간에 지속되지 않으며, 매번 덮어쓰여집니다. `Meta Attribute`은 일반적으로 리플리케이션되지 않습니다.

`Meta Attribute`는 "얼마나 많은 데미지를 가했는가?"와 "이 데미지를 어떻게 처리할 것인가?" 사이의 논리적 구분을 제공합니다. 이러한 논리적 구분을 통해 `GameplayEffect`와 `Execution Calculation`은 타겟이 데미지를 어떻게 처리하는지 알 필요가 없게 됩니다. 데미지 예시를 계속 이어서 말해보자면, `GameplayEffect`는 얼마만큼의 데미지를 가할지 결정한 다음 `AttributeSet`이 해당 데미지를 어떻게 처리할지 결정합니다. 모든 캐릭터가 동일한 `Attribute`를 갖고 있지 않을 수도 있으며, 특히 `AttributeSet`을 서브 클래스로 분류하는 경우 더욱 그렇습니다. 기본 `AttributeSet` 클래스에는 체력 `Attribute`만 있고 서브 클래싱된 `AttributeSet`에 방어막 `Attribute`를 추가한 경우 방어막 `Attribute`를 가진 `AttributeSet`의 서브 클래스는 기본 `AttributeSet` 클래스와 다르게 받은 데미지를 분배할 것입니다.

`Meta Attribute`는 좋은 설계 패턴이지만 필수는 아닙니다. 모든 데미지의 인스턴스에 대해 하나의 `Execution Calculation`과 모든 캐릭터가 공유하는 하나의 `AttributeSet` 클래스를 사용한다면, `Execution Calculation` 내부에서 체력, 방어막 등으로 데미지를 직접 분배하고 이러한 `Attribute`를 직접 수정하는 것도 괜찮은 방법일 겁니다. 유연성은 포기하는 방식이지만, 상황에 따라 괜찮을 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-a-changes"></a>

#### 4.3.4 Attribute 변경에 응답하기

UI 또는 다른 게임플레이를 업데이트하기 위해 `Attribute`가 변경될 때 감지하려면, `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`를 사용합니다. 이 함수는 바인딩할 수 있는 델리게이트를 반환하며, `Attribute`가 변경될 때마다 자동으로 호출됩니다. 델리게이트는 `NewValue`, `OldValue` 그리고 `FGameplayEffectModCallbackData`를 포함한 `FOnAttributeChangeData` 매개 변수를 제공합니다.

> **Note:** `FGamePlayEffectModCallbackData`는 서버에서만 설정됩니다.

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

샘플 프로젝트는 `GDPlayerState`에서 `Attrubute` 값 변경 델리게이트에 바인딩하여 HUD를 업데이트하고, 체력이 0에 도달했을 때 플레이어 죽음에 반응하는 방식으로 사용합니다.

이를 `AsyncTask`로 래핑하는 커스텀 블루프린트 노드가 샘플 프로젝트에 포함되어 있습니다. 해당 노드는 `UI_HUD` UMG 위젯에서 체력, 마나 및 스태미나를 업데이트하는 데 사용됩니다. 이 `AsyncTask`는 `EndTask()`를 수동으로 호출할 때까지 영구적으로 유지되며, UMG Widget의 `Destruct` 이벤트에서 이를 수행합니다. 자세한 내용은 `AsyncTaskAttributeChanged.h/cpp`를 참조해주세요.

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-a-derived"></a>

#### 4.3.5 Derived Attributes

하나 이상의 다른 `Attribute`에서 일부 또는 전체 값을 파생하는 `Attribute`를 만들려면, 하나 이상의 `Attribute Based` 또는 [MMC(Modular GameplayEffect Calculation)](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods)가 포함된 `Infinite` `GameplayEffect`를 사용합니다. `Derived Attribute`는 자신이 의존하고 있는 `Attribute`가 업데이트될 때 자동으로 업데이트됩니다.

`Derived Attribute`에 대한 모든 `Modifier`의 최종 공식은 `Modifier Aggregators` 공식과 동일합니다. 만약 특정 순서대로 계산할 경우, 모든 계산을 `MMC` 내에서 수행해야 합니다.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

> **Note:** PIE에서 여러 클라이언트를 사용하는 경우 Editor Preferences에서 `Run Under One Process`(하나의 프로세스 아래에서 실행) 옵션을 비활성화해야 합니다. 그렇지 않으면 첫 번째 클라이언트를 제외한 다른 클라이언트에서 독립적인 `Attributes`가 업데이트될 때 `Derived Attribute`가 갱신되지 않습니다.

예를 들어, `Infinite` `GameplayEffect`가 `TestAttrA`의 값을 `TestAttrB`와 `TestAttrC`  `Attribute`로부터 파생시킵니다. 사용된 공식은 `TestAttrA = (TestAttrA + TestAttrB) * (2 * TestAttrC)`이며, TestAttrA는 해당 `Attribute` 중 하나가 값을 업데이트할 때마다 자동으로 다시 계산됩니다.

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 AttributeSet

<a name="concepts-as-definition"></a>
#### 4.4.1 AttributeSet 정의 

`AttributeSet`은 `Attribute`를 정의 및 관리하고 변경 사항을 처리하는 역할을 합니다. 사용자는 [`UAttributeSet`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UAttributeSet/index.html)의 서브 클래스를 구현해서 사용해야 합니다. `Owner Actor`의 생성자에서 `AttributeSet`을 생성하면 자동으로 해당 `ASC`에 등록됩니다. **이 작업은 C++에서 수행해야 합니다.**

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 AttributeSet 설계

`ASC`는 하나 혹은 여러 개의 `AttributeSet`을 가질 수 있습니다. AttributeSet은 메모리 오버헤드가 미미하기 때문에 얼마나 많은 `AttributeSet`을 사용할지는 사용자의 조직적인 결정에 달려있습니다.

게임 내 모든 액터가 공유하는 하나의 큰 모놀리식 `AttributeSet`을 사용하고, 필요한 Attribute만 사용하는 방식도 가능합니다. 이 경우 사용되지 않는 Attribute는 무시합니다.

다른 방법으로 `Attribute`들을 그룹화하여 여러 개의 `AttributeSet`을 만들어 `Actor`에 필요한 것만 선택적으로 추가할 수도 있습니다. 예를 들어, 체력 관련 `Attribute`를 위한 `AttributeSet`, 마나 관련 `Attribute`를 위한 `AttributeSet`을 만들 수 있습니다. MOBA 게임에서 영웅은 마나를 필요로 하지만 미니언은 필요하지 않다면, 영웅은 마나 관련 `AttributeSet`을, 미니언은 이를 제외한 `AttributeSet`을 가지게 하면 됩니다.

또한 `AttributeSet`은 서브 클래싱할 수 있기 때문에, 이를 통해 `Actor`가 가질 `Attribute`를 선택적으로 결정할 수 있습니다. `Attribute`들은 내부적으로 `AttributeSetClassName.AttributeName` 형식으로 참조되는데, `AttributeSet`을 서브 클래싱했을 경우에도 부모 클래스의 `Attribute`들은 똑같이 부모 클래스의 이름을 접두사로 사용하게 됩니다.

여러 개의 `AttributeSet`을 가질 수는 있지만, 동일한 클래스의 `AttributeSet`은 하나만 `ASC`에 포함시킬 수 있습니다. 동일한 클래스의 `AttributeSet`을 두 개 이상 추가할 경우 `ASC`가 어느 `AttributeSet`을 사용할지 알지 못하고, 그냥 그 중 하나를 선택하게 됩니다.

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 개별 Attribute를 가진 서브 컴포넌트

`Pawn`에 여러 개의 피해를 입을 수 있는 컴포넌트가 있을 경우(예: 각기 다른 갑옷 부위별 피해 계산), 최대 수의 피해를 입을 수 있는 컴포넌트를 알고 있다면, 하나의 `AttributeSet`에 여러 개의 `Attribute`(예: DamageableCompHealth0, DamageableCompHealth1 등)를 정의하여 각 슬롯에 해당하는 피해 컴포넌트를 나타낼 수 있습니다. 그런 다음, 피해를 입을 각 컴포넌트의 인스턴스에서 슬롯 번호를 지정하여 `GameplayAbility`나 [`Executions`](#concepts-ge-ec)에서 어떤 `Attribute`에 피해를 적용할지 알 수 있도록 합니다. 만약 `Pawn`이 가진 피해 컴포넌트가 최대 피해 슬롯 수보다 적거나 아예 없더라도, 동작하는 데에 큰 문제는 없습니다. `AttributeSet`에 `Attribute`이 있다고 해서 반드시 그 `Attribute`을 사용해야 하는 것은 아닙니다. 사용하지 않는 `Attribute`는 매우 적은 메모리만 차지합니다.

하지만 서브 컴포넌트가 많은 `Attribute`를 가질 경우 서브 컴포넌트가 너무 많거나, 서브 컴포넌트가 분리되어 다른 플레이어와 공유되거나, 기타 이유로 이 방식이 적합하지 않다면 `Attribute` 대신 컴포넌트에서 일반적인 float 값을 저장하는 방식으로 변경하는 것이 좋습니다. 이 경우 [Item Attributes](#concepts-as-design-itemattributes)를 참고해보세요.

<a name="concepts-as-design-addremoveruntime"></a>

##### 4.4.2.2 런타임에 AttributeSet 추가 및 제거하기

`AttributeSet`은 런타임에 `ASC`에서 추가 및 제거할 수 있지만, 다소 위험할 수 있습니다. 예를 들어, 클라이언트에서 서버보다 먼저 `AttributeSet`을 제거한 후 서버에서 `Attribute `값 변경이 클라이언트로 리플리케이트되면 클라이언트에서는 `Attribute`가 해당 `AttributeSet`을 찾지 못해 게임이 크래시가 일어날 수 있습니다.

예시. 무기를 인벤토리에 추가할 때:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

예시. 무기를 인벤토리에서 제거할 때:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

<a name="concepts-as-design-itemattributes"></a>

##### 4.4.2.3 아이템 Attribute (무기 탄약)

`Attribut`e`를 가진 장착 가능한 아이템(무기 탄약, 방어구 내구도 등)을 구현하는 방법은 여러 가지가 있습니다. 이 모든 접근 방식은 아이템에 직접 값을 저장하며, 이는 여러 플레이어가 아이템을 장착할 수 있는 경우 필수적입니다.

> 1. 아이템에 float 사용 (**권장**)
> 1. 아이템에 별도의 `AttributeSet` 사용
> 1. 아이템에 별도의 `ASC` 사용

<a name="concepts-as-design-itemattributes-plainfloats"></a>

###### 4.4.2.3.1 아이템에 일반 float 사용

`Attribute`를 사용하는 대신, 아이템 클래스 인스턴스에 일반 float 값을 저장해보세요. 포트나이트와 [GASShooter](https://github.com/tranek/GASShooter)는 해당 방식으로 총기의 탄약을 관리합니다. 예를 들어, 총기의 경우 최대 탄창 크기, 현재 탄창 내 탄약, 보유 탄약 등을 총기 인스턴스에 `COND_OwnerOnly`로 리플리케이트된 float 값으로 직접 저장합니다. 무기가 보유 탄약을 공유한다면, 보유 탄약을 캐릭터의 `Attribute`로 옮겨 공유되는 탄약 `AttributeSet`에 저장할 수 있습니다. (재장전 어빌리티는 `Cost GE`를 사용해 보유 탄약에서 탄창 내 탄약으로 이동시킬 수 있습니다). 현재 탄창 내 탄약을 `Attribute`로 사용하지 않기 때문에, `UGameplayAbility`의 일부 함수를 재정의하여 총기에서 float 값을 기준으로 비용을 확인하고 적용해야 합니다. GamepalyAbility를 부여할 때  총기를 [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)의 `SourceObject`로 설정하면, 어빌리티 내부에서 해당 어빌리티를 부여한 총기에 접근할 수 있습니다.

자동 사격 중 로컬 탄약 수가 리플리케이트되어 클라이언트 측 탄약 수가 서버의 탄약 수로 덮어씌워지지 않도록 하려면, `PreReplication()`에서 `IsFiring` `GameplayTag`가 있는 동안에는 리플리케이트를 비활성화하면 됩니다. 이로써 자체적으로 로컬 예측을 수행하게 됩니다. 

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

장점:
1. `AttributeSet` 사용의 제한을 피할 수 있습니다. (아래 참조)

제한 사항:
1. 기존 `GameplayEffect` 워크플로를 사용할 수 없음. (탄약 소모에 대한 `Cost GE` 등)
1. `UGameplayAbility`의 주요 함수를 오버라이드하여 총기의 float 값에 대해 탄약 비용을 확인하고 적용해야 합니다.

<a name="concepts-as-design-itemattributes-attributeset"></a>

###### 4.4.2.3.2 아이템의 `AttributeSet`

[플레이어의 인벤토리에 아이템을 추가할 때 플레이어의 ASC에 추가되는 아이템](#concepts-as-design-addremoveruntime)에 별도의 `AttributeSet`을 사용하면 작동할 수 있지만 몇 가지 주요 제한사항이 있습니다. 제 경우 [GASShooter](https://github.com/tranek/GASShooter) 초기 버전에서 무기의 탄약 시스템에서 해당 방식을 사용한 적이 있습니다. 무기는 최대 탄창 크기, 현재 탄창에 있는 탄약, 예비 탄약 등과 같은 `Attribute`를 무기 클래스에 있는 `AttributeSet`에 저장합니다. 만약 무기가 예비 탄약을 공유하는 경우, 예비 탄약을 캐릭터의 공유 탄약 `AttributeSet`으로 이동시키는 것이 좋습니다. 무기가 서버에서 플레이어의 인벤토리에 추가되면, 무기는 자신의 `AttributeSet`을 플레이어의 `ASC::SpawnedAttributes`에 추가합니다. 그러면 서버는 이를 클라이언트에 리플리케이트합니다. 무기가 인벤토리에서 제거되면, 무기의 `AttributeSet`도 `ASC::SpawnedAttributes`에서 제거됩니다.

`AttributeSet`이 `OwnerActor`가 아닌 다른 곳(예: 무기)에 있는 경우, 처음에는 `AttributeSet`에서 컴파일 오류가 발생할 수 있습니다. 이를 해결하려면 `AttributeSet`을 생성할 때 생성자 대신 `BeginPlay()`에서 생성하고 무기에 `IAbilitySystemInterface`(플레이어 인벤토리에 무기를 추가할 때 `ASC`에 대한 포인터를 설정)를 구현하면 됩니다.

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

이 부분은 [GASShooter의 이전 버전](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)을 통해 확인할 수 있습니다.

장점:
1. 기존 `GameplayAbility` 및 `GameplayEffect` 워크플로를 사용할 수 있습니다. (탄약 사용에 대한 `Cost GE` 등)
1. 아이템이 매우 적은 경우 설정이 간단합니다.

제한 사항:
1. 모든 무기 유형에 대해 새로운 `AttributeSet` 클래스를 만들어야 합니다. `Attribute`를 변경하면 `ASC`의 `SpawnedAttributes` 배열에서 해당 `AttributeSet` 클래스의 첫 번째 인스턴스를 찾기 때문에 `ASC`는 기능적으로 한 클래스의 `AttributeSet` 인스턴스를 하나만 가질 수 있습니다. 동일한 `AttributeSet` 클래스의 인스턴스를 추가할 경우 무시됩니다. 
1. 플레이어의 인벤토리에는 각 유형의 무기가 하나씩만 지닐 수 있는데, 이는 앞서 설명한 `AttributeSet`클래스당 하나의 `AttributeSet` 인스턴스만 허용하기 때문이었습니다.
1. `AttributeSet`을 제거하는 것은 위험합니다. GASShooter에서 플레이어가 로켓으로 자폭한 경우, 플레이어는 즉시 인벤토리에서 로켓 발사기를 제거(`ASC`에서 해당 `AttributeSet` 포함)합니다. 서버가 로켓 발사기의 탄약 `Attribute` 변경을 클라이언트에 리플리케이트할 때 해당 `AttributeSet`가 클라이언트의 `ASC`에 더 이상 존재하지 않게 되어 게임이 크래시합니다.

<a name="concepts-as-design-itemattributes-asc"></a>

###### 4.4.2.3.3 아이템의 `ASC`

각 아이템에 `AbilitySystemComponent`를 통째로 넣는다는 것은 극단적인 접근 방식입니다. 개인적으로 이 작업을 시도해본 적도 없고 본 적도 없습니다. 이렇게 작동하려면 많은 엔지니어링이 필요할 것입니다.

> 질문: 여러 개의 AbilitySystemComponent를 동일한 소유자(Owner)에게 두고, 서로 다른 아바타(예: pawn, 무기/아이템/투사체)에 대해 사용하려는 경우가 가능할까요? (소유자는 PlayerState로 설정)
>
>
> 여기서 제가 처음으로 떠올린 문제는 소유 Actor에 대해 IGameplayTagAssetInterface와 IAbilitySystemInterface를 구현하는 것입니다. 
>
> 전자의 경우에는 가능할 수도 있습니다. 모든 ASC에서 태그를 집계하는 방식을 사용할 수 있을 것입니다. 하지만 주의해야 할 점은 HasAllMatchingGameplayTags가 교차 ASC 집계를 통해서만 충족될 수도 있다는 것입니다. 단순히 각 ASC로 호출을 전달하고 결과를 OR 연산으로 결합하는 방식은 충분하지 않을 수 있습니다. 하지만 후자의 경우는 더 까다롭습니다. 어떤 ASC가 권위적인(권한을 가진) 것일까요? 누군가가 GE를 적용하려 할 때, 어느 ASC가 이를 받아야 할까요? 이런 부분을 해결할 수 있을지도 모르겠지만, 소유자 아래에 여러 ASC가 있을 때 발생하는 문제는 가장 어려운 부분일 것입니다.
>
> Pawn과 무기에 별도의 ASC를 두는 것은 자체적으로 의미가 있을 수 있습니다. 예를 들어, 무기를 설명하는 태그와 소유 Pawn을 설명하는 태그를 구분하는 경우입니다. 무기에 부여된 태그가 소유자에게도 "적용"되고 그 외에는 아무런 영향이 없다는 접근 방식이 말이 될 수도 있습니다. (예: Attribute와 GameplayEffect는 독립적이지만 소유자는 위에서 설명한 것처럼 소유한 태그를 집계합니다.) 이 방식은 작동할 가능성이 충분히 있습니다. 하지만 동일한 소유자를 가진 여러 ASC를 두는 것은 복잡한 상황을 초래할 수 있습니다.

*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

장점:
1. 기존 `GameplayAbility` 및 `GameplayEffect` 워크플로를 사용할 수 있음. (탄약 사용에 대한 `cost GE` 등)
1. `AttributeSet` 클래스 재사용 가능. (각 무기의 ASC에 하나씩)

제한 사항:
1. 엔지니어링 비용이 어느 정도일지 알 수 없음.
1. 실제로 구현이 가능하지 불확실.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-attributes"></a>

#### 4.4.3 Attribute 정의

**`Attribute`는 `AttributeSet`의 헤더 파일에서 C++로만 정의할 수 있습니다.** 이 매크로 블록을 모든 `AttributeSet` 헤더 파일의 맨 위에 추가하는 것이 좋습니다. 그러면 `Attribute`에 대한 getter 및 setter 함수가 자동으로 생성됩니다.

```c++
// AttributeSet.h의 매크로 사용
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

리플리케이트되는 생명력 Attribute는 다음과 같이 정의할 수 있습니다:

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

또한 헤더 파일에 `OnRep` 함수를 다음과 같이 정의합니다:

```c++
// .h
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`의 .cpp 파일에서는 예측 시스템에서 사용하는 `GAMEPLAYATTRIBUTE_REPNOTIFY` 매크로를 사용하여 `OnRep` 함수를 다음과 같이 작성합니다:

```c++
// .cpp
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

마지막으로, `Attribute`를 `GetLifetimeReplicatedProps`에 다음과 같이 추가해야 합니다:

```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`는 로컬 값이 서버에서 내려오는 값과 이미 동일한 경우에도 `OnRep` 함수가 트리거되도록 설정합니다(예측으로 인해 발생). 기본적으로 로컬 값이 서버에서 내려오는 값과 동일하면 `OnRep` 함수는 트리거되지 않습니다.

`Attribute`가 `Meta Attribute`처럼 리플리케이트되지 않는 경우에는 `OnRep` 함수와 `GetLifetimeReplicatedProps` 단계를 생략할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-init"></a>

#### 4.4.4 Attribute 초기화

`Attribute`(`BaseValue`와 그에 따른 `CurrentValue`)을 초기화하는 방법에는 여러 가지가 있습니다. 에픽 게임즈는 instant `GameplayEffect`를 사용하는 방법을 권장합니다. 이 방법은 샘플 프로젝트에서도 사용된 방식입니다.

샘플 프로젝트에서 `Attributes`를 초기화하는 instant `GameplayEffect`를 만드는 방법은 샘플 프로젝트의 `GE_HeroAttributes` 블루프린트를 참고하세요. 이 `GameplayEffect`의 적용은 C++에서 이루어집니다.

`Attribute`를 정의할 때 `ATTRIBUTE_ACCESSORS` 매크로를 사용했다면, 각 `Attribute`에 대해 `AttributeSet`에서 자동으로 초기화 함수가 생성되며, 해당 함수는 C++에서 원하는 대로 호출할 수 있습니다.

```c++
// InitHealth(float InitialValue)는 ATTRIBUTE_ACCESSORS 매크로로 정의된 'Health' Attribute에 대해 자동으로 생성된 함수입니다.
AttributeSet->InitHealth(100.0f);
```

`Attribute` 초기화 방법에 대한 자세한 내용은 `AttributeSet.h`에서 확인할 수 있습니다.

> **Note:** 이전에는 `FAttributeSetInitterDiscreteLevel`이 `FGameplayAttributeData`와 함께 작동하지 않았습니다. 이 기능은 `Attribute`가 원시 float 값일 때 사용되었으며, `FGameplayAttributeData`가 `Plain Old Data` (`POD)가 아니라고 오류를 발생시킵니다. 이 문제는 4.24에서 수정되었습니다. (참고: [UE-76557](https://issues.unrealengine.com/issue/UE-76557))

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>

#### 4.4.5 PreAttributeChange()

`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`는 `Attribute`의 `CurrentValue`가 변경되기 전에 그 변화를 처리하기 위해 `AttributeSet`에서 사용하는 주요 함수 중 하나입니다. 이 함수는 `NewValue`라는 참조 매개변수를 통해 `CurrentValue`에 대한 변경 사항을 클램핑(제한)하는 이상적인 위치입니다.

예를 들어, 샘플 프로젝트에서 이동 속도 Modifier를 클램핑하는 방법은 다음과 같습니다:

```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 150 units/s 미만으로 감속할 수 없고 1000 units/s 이상으로 부스트할 수 없습니다.
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()` 함수는 `AttributeSet.h`에 추가한 매크로 블록([Attribute 정의](#concepts-as-attributes))에 의해 생성됩니다.

이 함수는 `Attribute`에 대한 변경이 있을 때마다 트리거됩니다. `Attribute` 설정자( `AttributeSet.h` [Attribute 정의](#concepts-as-attributes) 매크로 블록에서 정의된)나 [`GameplayEffects`](#concepts-ge)를 통해 변경이 발생할 수 있습니다.

> **Note:** 여기서 이루어지는 클램핑은 `ASC`에서 Modifier의 값을 영구적으로 변경하지 않습니다. 단지 Modifier를 쿼리할 때 반환되는 값을 변경합니다. 즉, [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)와 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)와 같은 Modifier의 값을 기반으로 `CurrentValue`를 다시 계산하는 모든 로직은 클램핑을 다시 구현해야 합니다.

> **Note:**  `PreAttributeChange()`에 대한 에픽 게임즈의 코멘트는 이를 GameplayEvent에 사용하지 말고 주로 클램핑을 위해 사용하라고 안내하고 있습니다. `Attribute` 변경에 대한 GameplayEvent를 처리하기 위한 추천 위치는 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`입니다. ([Attribute 변경에 응답하기](#concepts-a-changes)).

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>

#### 4.4.6 PostGameplayEffectExecute()

`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`는 Instant [`GameplayEffect`](#concepts-ge)로 인해 `Attribute`의 `BaseValue`가 변경된 후에만 트리거됩니다. 이는 `GameplayEffect`로 인해 `Attribute`가 변경된 후 추가적인 `Attribute` 조작을 수행하기 적합한 위치입니다.

예를 들어, 샘플 프로젝트에서는 여기에서 최종 피해 `Meta Attribute`를 생명력 `Attribute`에서 빼는 작업을 수행합니다. 만약 방어막 `Attribute`가 있었다면, 먼저 방어막에서 피해를 빼고, 남은 피해를 생명력에서 차감하는 방식입니다. 샘플 프로젝트는 또한 이 위치를 사용하여 피격 반응 애니메이션을 적용하고, 피해량 UI를 표시하며, 킬러에게 경험치와 골드 보상을 할당합니다. 설계상, 피해 `Meta Attribute`는 항상 Instant `GameplayEffect`를 통해 전달되며 `Attribute` 설정자를 통해서는 전달되지 않습니다.

마나와 스태미나와 같이 Instant `GameplayEffect`에 의해서만 `BaseValue`가 변경되는 다른 `Attribute`들도 여기에서 최대값에 맞춰 클램핑될 수 있습니다.

> **Note:** `PostGameplayEffectExecute()`가 호출될 때 `Attribute`의 변경은 이미 이루어졌지만, 아직 클라이언트로 복제되지 않은 상태입니다. 따라서 여기에서 값을 클램핑해도 클라이언트에 두 번의 네트워크 업데이트가 발생하지 않습니다. 클라이언트는 클램핑 후에만 업데이트를 받게 됩니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>

#### 4.4.7 OnAttributeAggregatorCreated()

`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`는 해당 `AttributeSet`에서 `Attribute`에 대한 `Aggregator`가 생성될 때 트리거됩니다. 해당 함수는 [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)의 커스텀 설정을 가능하게 합니다. `AggregatorEvaluateMetaData`는 `Aggregator`가 `Attribute`에 적용된 모든 [`Modifiers`](#concepts-ge-mods)를 기반으로 `Attribute`의 `CurrentValue`를 평가할 때 사용됩니다. 기본적으로 `AggregatorEvaluateMetaData`는 `Aggregator`가 어떤 `Modifier`가 자격이 있는지 판단하는 데 사용됩니다. 예를 들어, `MostNegativeMod_AllPositiveMod`는 모든 플러스 `Modifier`는 허용하지만, 마이너스 `Modifier`는 가장 큰 음수 값 하나만 허용합니다. 이 방식은 Paragon에서 사용되었으며, 플레이어에게 여러 개의 느려짐 효과가 있을 때 가장 큰 음수의 이동 속도 감소 효과만 적용되도록 하고, 모든 플러스적인 이동 속도 버프는 모두 적용되도록 했습니다. 자격이 없는 `Modifier`는 `ASC`에 여전히 존재하지만, 최종 `CurrentValue`에는 집계되지 않습니다. 조건이 변경되면 나중에 자격이 될 수 있습니다. 예를 들어, 가장 마이너스인 `Modifier`가 만료되면, 다음으로 마이너스인 `Modifier`(만약 존재한다면)가 자격을 얻습니다.

AggregatorEvaluateMetaData를 사용하여 가장 마이너스인 `Modifier` 하나와 모든 플러스적인 `Modifier`를 허용하는 예시:

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

자격을 위한 커스텀 `AggregatorEvaluateMetaData`는 `FAggregatorEvaluateMetaDataLibrary`에 정적 변수로 추가해야 합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>

#### 4.5.1 Gameplay Effect 정의

[`GameplayEffects`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UGameplayEffect/index.html)(`GE`)는 Ability가 자신을 포함한 다른 객체의 [`Attributes`](#concepts-a) 및 [`GameplayTags`](#concepts-gt)를 변경하기 위한 수단입니다. 즉각적인 `Attribute` 변화를 일으킬 수 있거나(예: 피해나 치유) 이동 속도 증가나 기절과 같은 장기적인 상태 버프/디버프를 적용할 수도 있습니다. `UGameplayEffect` 클래스는 단일 GameplayEffect를 정의하는 **데이터 전용** 클래스입니다. 추가적인 로직은 `GameplayEffect`에 포함되지 않아야 합니다. 보통 디자이너는 `UGameplayEffect`의 여러 블루프린트 자식 클래스를 생성하여 사용합니다.

`GameplayEffect`는 [`Modifiers`](#concepts-ge-mods)와 [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec)을 통해 `Attribute`를 변경합니다.

`GameplayEffects`에는 세 가지 지속 시간 타입을 가집니다: `Instant`, `Duration`, `Infinite`

또한, `GameplayEffect`는 [`GameplayCues`](#concepts-gc)를 추가하거나 실행할 수 있습니다. `Instant GameplayEffect`는 `GameplayCue` `GameplayTag`에서 `Execute`를 호출하는 반면, `Duration` 또는 `Infinite GameplayEffect`는 `GameplayCue` `GameplayTag`에서 `Add`와 `Remove`를 호출합니다.

| Duration 타입 | GameplayCue 이벤트 | 사용 시기                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | `Attribute`의 `BaseValue`에 즉각적이고 영구적인 변화를 줄 때 사용합니다. 이 경우 `GameplayTag`는 적용되지 않으며, 한 프레임도 존재하지 않습니다.                                                                                                                  |
| `Duration`    | Add & Remove      | `Attribute`의 `CurrentValue`를 일시적으로 변경하거나 특정 기간 동안 `GameplayTag`를 적용할 때 사용합니다. 해당 기간은 `UGameplayEffect` 클래스 또는 블루프린트에서 정의됩니다.       |
| `Infinite`    | Add & Remove      | `Attribute`의 `CurrentValue`를 일시적으로 변경하거나 무한으로 `GameplayTag`를 적용할 때 사용합니다. 해당 Effect는 스스로 만료되지 않으며, 반드시 Ability나 `ASC`를 통해 수동으로 제거해야 합니다. |

`Duration` 및 `Infinite` 타입의 `GameplayEffect`는 `Periodic Effect`(주기적 효과)를 사용할 수 있습니다. `Periodic Effect`는 설정된 주기(Period)마다 해당 `Modifier`와 `Execution`을 실행합니다. `Periodic Effect`는 `Attribute`의 `BaseValue`를 변경하거나 `GameplayCue`를 실행할 때 `Instant` 타입의 `GameplayEffect`로 처리됩니다. 이는 DOT(Damage Over Time) 같은 효과에 유용합니다.

> **Note:** `Periodic Effect`는 [predicted](#concepts-p)될 수 없습니다.

또한 `Duration` 및 `Infinite` 타입의 `GameplayEffect`는 `Ongoing Tag Requirement`(진행 중 태그 요구 조건)를 통해 [Gameplay Effect Tags](#concepts-ge-tags)를 일시적으로 비활성화하거나 활성화할 수 있습니다. 비활성화되면 `Modifier`와 적용된 `GameplayTag`는 제거되지만, `GameplayEffect` 자체는 제거되지 않습니다. 이후 요구 조건이 충족되면 `Modifier`와 `GameplayTag`가 다시 적용됩니다.

`Duration` 또는 `Infinite` `GameplayEffect` 의 `Modifiers`를 수동으로 다시 계산해야 하는 경우(`Attribute` 에서 가져오지 않는 데이터를 사용하는 `MMC` 가 있다고 가정할 때), `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`을 호출하면 됩니다.`UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()` 을 사용하여 이미 가지고 있는 레벨과 동일하게 설정할 수 있습니다.
`Attribute`를 기반으로 한 `Modifier`는 해당 `Attribute`가 업데이트될 때 자동으로 업데이트됩니다.

`SetActiveGameplayEffectLevel()` 함수가 `Modifier`를 업데이트하는 핵심 작업은 다음과 같습니다:

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
//Private 함수이기 때문에, 만약 호출할 수 있었다면 레벨을 굳이 다시 설정하지 않고도 이 세 함수를 호출했을 것입니다.
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffect`는 일반적으로 인스턴스화되지 않습니다. Ability나 `ASC`가 `GameplayEffect`를 적용할 때, `GameplayEffect`의 `ClassDefaultObject`를 기반으로 [`GameplayEffectSpec`](#concepts-ge-spec)이 생성됩니다. 성공적으로 적용된 `GameplayEffectSpec`은 `FActiveGameplayEffect`라는 구조체에 추가되며, `ASC`는 이를 특별한 컨테이너 구조체인 `ActiveGameplayEffect`에서 관리합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-applying"></a>

#### 4.5.2 Gameplay Effect 적용

`GameplayEffect`는 다양한 방법으로 적용할 수 있으며, 주로 [`GameplayAbilities`](#concepts-ga)의 함수나 `ASC`의 함수를 통해 이루어집니다. 이러한 함수들은 보통 `ApplyGameplayEffectTo` 형태를 가지며, 이러한 다양한 함수들은 결국 `Target`의 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`를 호출하여 Effect를 적용합니다.

`GameplayAbility` 외부(예: 투사체)에서 `GameplayEffect`를 적용하려면, `Target`의 `ASC`를 가져와서 해당 ASC의 함수 중 하나를 사용해 `ApplyGameplayEffectToSelf`를 호출해야 합니다.

`Duration` 또는 `Infinite` 타입의 `GameplayEffect`가 `ASC`에 적용되었을 때 이를 감지하려면, 다음과 같이 델리게이트에 바인딩하면 됩니다:

```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```

해당 콜백 함수는 다음과 같습니다:

```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

서버는 항상 이 함수를 호출하며, 리플리케이션 모드와 관계없이 호출됩니다. Autonomous Proxy는 `Full` 및 `Mixed` 모드에서만 리플리케이트된 `GameplayEffect`에 대해 이 함수를 호출합니다. Simulated Proxy는 `Full` [replication mode](#concepts-asc-rm)에서만 이 함수를 호출합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-removing"></a>

#### 4.5.3 Gameplay Effect 삭제

`GameplayEffect`는 [`GameplayAbilities`](#concepts-ga)의 함수나 `ASC`의 함수를 통해 다양한 방식으로 제거할 수 있습니다. 일반적으로 `RemoveActiveGameplayEffect` 형태를 가지며, 이러한 함수들은 결국 `Target`의 `FActiveGameplayEffectsContainer::RemoveActiveEffects()`를 호출합니다.

`GameplayAbility` 외부(예: 투사체)에서 `GameplayEffect`를 제거하려면, 타겟의 `ASC`를 가져와서 해당 ASC의 함수 중 하나를 사용해 `RemoveActiveGameplayEffect`를 호출해야 합니다.

`Duration` 또는 `Infinite` 타입의 `GameplayEffect`가 `ASC`에서 제거되었을 때 이를 감지하려면, 다음과 같이 델리게이트에 바인딩하면 됩니다:

```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```

해당 콜백 함수는 다음과 같습니다:

```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

서버는 항상 이 함수를 호출하며, 리플리케이션 모드와 관계없이 호출됩니다. Autonomous Proxy는 `Full` 및 `Mixed` 모드에서만 리플리케이트된 `GameplayEffect`에 대해 이 함수를 호출합니다. Simulated Proxy는 `Full` [replication mode](#concepts-asc-rm)에서만 이 함수를 호출합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-mods"></a>

#### 4.5.4 Gameplay Effect Modifiers

`Modifier`는 `Attribute`를 변경하며, [예측](#concepts-p)적으로 `Attribute`를 변경할 수 있는 유일한 방법입니다. `GameplayEffect`는 `Modifier`를 0개 혹은 여러 개 가질 수 있습니다. 각 `Modifier`는 지정된 연산을 통해 하나의 `Attribute`만 변경할 수 있습니다.

| 연산  | 내용                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | `Modifier`에서 지정한 `Attribute`에 결과 값을 더합니다. 빼기를 원할 경우 음수를 사용합니다.                  |
| `Multiply` | `Modifier`에서 지정한 `Attribute`에 결과 값을 곱합니다.                                                    |
| `Divide`   | `Modifier`에서 지정한 `Attribute`를 결과 값으로 나눕니다.                                                  |
| `Override` | `Modifier`에서 지정한 `Attribute`를 결과 값으로 덮어씁니다.                                                   |

`Attribute`의 `CurrentValue`는 모든 `Modifier`가 `BaseValue`에 추가된 집합적인 결과입니다.

`Modifier`가 어떻게 집계되는지는 `GameplayEffectAggregator.cp`p의 `FAggregatorModChannel::EvaluateWithBase`에 다음과 같은 공식으로 정의됩니다:

```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

`Override` `Modifier`는 최종 값을 덮어쓰며, 가장 마지막에 적용된 `Modifier`가 우선권을 가집니다.

> **Note:** 퍼센트 기반의 변화를 사용할 때는 `Multiply` 연산을 사용하여 덧셈 이후에 적용되도록 하세요.

> **Note:** 퍼센트 변경은 [Prediction](#concepts-p)과 함께 사용 시 문제가 발생할 수 있습니다.

`Modifier`에는 네 가지 타입이 있습니다: Scalable Float, Attribute Based, Custom Calculation Class, Set By Caller. 이들은 모두 float 값을 생성하며, `Modifier`의 연산 값을 기반으로 연산을 수행해 지정된 `Attribute`를 변경합니다.

| `Modifier` 타입            | 내용                                                                                                                                                                  |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloat`는 Data Table의 행(변수)과 열(레벨)을 참조하는 구조체입니다. Scalable Float는 자동으로 지정된 테이블 행의 값을 Ability의 현재 레벨(또는 [`GameplayEffectSpec`](#concepts-ge-spec)에서 오버라이드된 레벨)에서 읽습니다. 이 값은 추가적으로 계수(coefficient)를 통해 조정될 수 있습니다. Data Table/Row가 지정되지 않으면 값을 1로 간주하고, 계수를 사용하여 모든 레벨에서 단일 값을 하드 코딩할 수 있습니다. ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                          |
| `Attribute Based`          | `Attribute Based` `Modifier`는 `Source` (`GameplayEffectSpec`을 생성한 주체)나 `Target`(`GameplayEffectSpec`을 받은 대상)의 `CurrentValue` 또는 `BaseValue `를 사용합니다. 이 값은 계수, 전/후 추가 값을 사용해 추가적으로 수정됩니다. `Snapshotting`은 `GameplayEffectSpec`이 생성될 때 백업된 `Attribute` 값을 캡처하며, `Non-Snapshotting`은 `GameplayEffectSpec`이 적용될 때 값을 캡처합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `Custom Calculation Class`는 복잡한 `Modifier`에 가장 큰 유연성을 제공합니다. 이 `Modifier`는 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) 클래스를 사용하며, 추가적으로 계수와 전/후 추가 값을 사용해 결과 float 값을 수정할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller` `Modifier`는 런타임에 Ability 또는 `GameplayEffectSpec`을 생성한 주체가 외부에서 값을 설정하는 Modifier입니다. 예를 들어, 플레이어가 버튼을 누른 시간에 따라 Ability의 피해량을 설정하려면 `SetByCaller`를 사용할 수 있습니다. `SetByCaller`는 `TMap<FGameplayTag, float>`으로 `GameplayEffectSpec`에 저장됩니다. `Modifier`는 `Aggregator`에 지정된 `GameplayTag`와 연결된 `SetByCaller` 값을 확인하도록 지시합니다. `GameplayTag` 버전만 사용할 수 있으며 `FName` 버전은 비활성화됩니다. `Modifier`가 `SetByCaller`로 설정되었지만 `GameplayEffectSpec`에 올바른 `GameplayTag`와 연결된 `SetByCaller`가 존재하지 않는 경우, 런타임 오류가 발생하고 값이 0으로 반환됩니다. `Divide` 연산의 경우 문제가 발생할 수 있습니다. [`SetByCallers`](#concepts-ge-spec-setbycaller)의 사용 방법에 대한 더 많은 정보는 `SetByCaller `관련 문서를 참조하세요. |

**[⬆ 위로 가기](#table-of-contents)**

##### 4.5.4.1 Multiply 및 Divide Modifiers

기본적으로, 모든 `Multiply` 및 `Divide` `Modifier`는 `Attribute`의 `BaseValue`에 곱하거나 나누기 전에 서로 더해집니다.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*from `GameplayEffectAggregator.cpp`*

이 공식에서 `Multiply`와 `Divide` `Modifier`는 `Bias` 값이 `1`로 설정됩니다. (참고로 `Addition`은 `Bias`가 `0`입니다.) 위 코드는 다음과 같이 해석됩니다:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

위 공식은 몇 가지 예상치 못한 결과를 초래합니다. 첫째, 이 공식은 모든 Modifier를 더한 후 `BaseValue`에 곱하거나 나누어 적용합니다. 대부분의 사람들은 서로 곱하거나 나누어 계산될 것이라고 예상합니다. 예를 들어 `Multiply Modifier`가 1.5인 경우 두 개를 적용하면 `BaseValue`가 `1.5 x 1.5 = 2.25`배가 되어야 할 것으로 예상하지만, 실제로는 `1.5 + 1.5 = 2`가 되어 `BaseValue`에 `2`를 곱하게 됩니다 (`50% 증가 + 또 다른 50% 증가 = 100% 증가`). 이 예시는 `GameplayPrediction.h`의 예시와 같습니다. 기본 속도 `500`에 `10%` 속도 버프를 적용하면 `550`이 됩니다. 여기에 또 다른 `10%` 버프를 추가하면 `600`이 됩니다.

그리고 둘째, 이 공식은 Paragon에 맞게 설계되었기 때문에 값에 대해 문서화되지 않은 규칙이 있습니다.

Multiply와 Divide의 덧셈 공식에 대한 규칙:
* 규칙 1: `(값이 1 미만인 항이 1개 이하) AND (여러 개의 값이 [1, 2) 범위에 존재 가능)`
* 규칙 2: `(값이 2 이상인 항은 하나만 존재 가능)`

이 공식에서 `Bias`는 범위 `[1, 2)` 내의 숫자의 정수 자릿수를 빼줍니다. 첫 번째 `Modifier`의 `Bias`는 합산 시작 값에서 빼지기 때문에 (합산 시작 값은 루프 전에 Bias로 설정됨), 개별 값 하나만 있을 때는 작동하며, `1 미만의 값`이 하나만 존재하는 경우에도 제대로 작동합니다.

`Multiply`의 몇 가지 예시:
Multipliers: `0.5`  
`1 + (0.5 - 1) = 0.5`, correct

Multipliers: `0.5, 0.5` 
`1 + (0.5 - 1) + (0.5 - 1) = 0`.

혹시 1을 예상하셨나요? `Multiply Modifier`가 여러 개 있는 경우 `1 미만의 값`은 의미가 없습니다. Paragon은 [greatest negative value for `Multiply` `Modifiers`](#cae-nonstackingge)만 사용하도록 설계되었기 때문에 1 미만의 값이 하나만 존재하게 됩니다.

Multipliers: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, correct

Multipliers: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`.
혹시 10을 예상하셨나요? `Modifier의 합계`는 항상`sum of the Modifiers - number of Modifiers + 1`이 됩니다.

많은 게임들은 `Multiply`와 `Divide` `Modifier`가 `BaseValue`에 곱하거나 나누어지기 전에 서로 곱하고 나누어지기를 원합니다. 이를 구현하려면 `FAggregatorModChannel::EvaluateWithBase()`의 **엔진 코드**를 변경해야 합니다.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Modifier의 GameplayTag

`SourceTag`와 `TargetTag`는 각 [Modifier](#concepts-ge-mods)에 대해 설정할 수 있습니다. 이들은 `GameplayEffect`의 [`Application Tag requirements`](#concepts-ge-tags)처럼 작동합니다. 즉, 태그는 효과가 적용될 때만 고려됩니다. 예를 들어, 주기적인 Infinite GameplayEffect에서는 첫 번째 적용 시에만 태그가 고려되고, Periodic Execution에서는 고려되지 않습니다.

`Attribute Based` Modifier는 또한 `SourceTagFilter`와 `TargetTagFilter`를 설정할 수 있습니다. 이 필터들은 `Attribute Based` Modifier의 소스가 되는 Attribute의 Magnitude를 결정할 때 사용되어 특정 Modifier를 그 Attribute에서 제외시킵니다. 소스 또는 타겟에 필터의 모든 태그가 없을 경우 해당 Modifier는 제외됩니다.

위 내용은 다음을 의미합니다:
source ASC와 target ASC의 태그는 `GameplayEffect`에 의해 캡처됩니다. source ASC 태그는 `GameplayEffectSpec`이 생성될 때 캡처되고, target ASC 태그는 효과가 실행될 때 캡처됩니다. Infinite GameplayEffect나 Duration GameplayEffect의 Modifier가 적용될 자격이 있는지(즉, Aggregator가 자격이 있는지) 결정할 때 필터가 설정된 경우, 캡처된 태그는 필터와 비교됩니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-stacking"></a>

#### 4.5.5 Gameplay Effect Stacking(중첩)

기본적으로 `GameplayEffect`는 새로운 인스턴스를 적용할 때 이전에 존재한 `GameplayEffectSpec`에 대해 알지 못하고 신경 쓰지 않습니다. `GameplayEffect`는 스택되도록 설정할 수 있으며, 이 경우 새로운 `GameplayEffectSpec` 인스턴스가 추가되는 대신 현재 존재하는 `GameplayEffectSpec`의 스택 수가 변경됩니다. 스택은 `Duration`과 `Infinite`에서만 동작합니다.

스택에는 두 가지 유형이 있습니다:Aggregate by Source와 Aggregate by Target.

| 스택 유형       | 설명                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Aggregate by Source | 각 `Source ASC`마다 타겟에 대한 별도의 스택 인스턴스가 있습니다. 각 Source는 X 만큼의 스택을 적용할 수 있습니다.                     |
| Aggregate by Target | 타겟에 대해 하나의 스택 인스턴스만 존재합니다. 각 Source는 공유된 스택 한도까지 스택을 적용할 수 있습니다. |

스택에는 만료, 지속 시간 새로 고침, 주기 초기화에 대한 Policy도 있습니다. 이들에 대한 도움말 툴팁은 `GameplayEffect` Blueprint에서 확인할 수 있습니다.

샘플 프로젝트에는 `GameplayEffect` 스택 변경 사항을 수신하는 커스텀 Blueprint 노드가 포함되어 있습니다. HUD UMG 위젯은 이를 사용하여 플레이어의 패시브 방어구 스택 수를 업데이트합니다. 이 `AsyncTask`는 수동으로 `EndTask()`가 호출될 때까지 계속 살아 있습니다. 호출은 UMG 위젯의 `Destruct` 이벤트에서 수행됩니다. `AsyncTaskEffectStackChanged.h/cpp`를 참조하십시오.

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-ga"></a>

#### 4.5.6 Ability 부여

`GameplayEffect`는 `ASC`에 새로운 [`GameplayAbilities`](#concepts-ga)를 부여할 수 있습니다. `Duration` 혹은 `Infinite` `GameplayEffect`만이 Ability를 부여할 수 있습니다.

일반적인 사용 사례 중 하나는 다른 플레이어가 특정 동작(예: 넉백이나 끌어당김)에 반응하도록 강제하는 것입니다. 예를 들어, 특정 행동을 적용하기 위해 `GameplayEffect`를 그들에게 부여하고, 자동으로 활성화되는 GameplayAbility(부여 시 Ability를 자동으로 활성화하는 방법에 대해서는 [Passive Abilities](#concepts-ga-activating-passive) 참조)를 부여하면 원하는 동작이 실행됩니다.

디자이너는 `GameplayEffect`가 어떤 Ability를 부여할지, 어떤 레벨로 부여할지, 어떤 입력에 [바인딩](#concepts-ga-input)할지, 그리고 부여된 Ability의 Removal Policy(제거 방침)을 선택할 수 있습니다.

| 제거 정책             | 설명                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | `GameplayEffect`가 제거될 때 부여된 Ability는 즉시 취소되고 제거됩니다.                                                   |
| Remove Ability on End      | 부여된 Ability는 완료될 때까지 유지되며 이후 타겟에서 제거됩니다.                                                                                                   |
| Do Nothing                 | `GameplayEffect`가 제거되더라도 부여된 Ability는 영향을 받지 않습니다. Ability는 수동으로 제거될 때까지 영구적으로 유지됩니다 |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-tags"></a>

#### 4.5.7 Gameplay Effect Tags

`GameplayEffect`는 여러 개의 [`GameplayTagContainers`](#concepts-gt)를 가질 수 있습니다. 디자이너는 각 카테고리에 대해 `추가된 GameplayTagContainers`와 `제거된 GameplayTagContainers`를 설정할 수 있으며, 그 결과는 컴파일 시 `Combined` `GameplayTagContainer`에 표시됩니다.

- 추가된 태그: 상위 클래스에 없던 태그를 해당 GameplayEffect가 추가하는 경우입니다.
- 제거된 태그: 상위 클래스에는 있지만 해당 서브 클래스에는 없는 태그를 의미합니다.

| 카테고리                          | 설명                                                                                                                                                                                                                                                                                                                                                                       |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect`가 가진 태그입니다. 해당 태그는 별도의 기능을 수행하지 않으며 `GameplayEffect`를 설명하는 용도로만 사용됩니다.                                                                                                                                                                                                                                        |
| Granted Tags                      | `GameplayEffect`에 존재하며, 해당 `GameplayEffect`가 적용된 `ASC`에도 전달되는 태그입니다. `GameplayEffect`가 제거되면 `ASC`에서도 태그가 제거됩니다. 이는 `Duration` 및 `Infinite` `GameplayEffect`에만 작동합니다.                                                                                                                             |
| Ongoing Tag Requirements          | `GameplayEffect`가 적용된 후, 해당 태그들은 `GameplayEffect`가 활성(on) 또는 비활성(off) 상태인지 결정합니다. `GameplayEffect`는 비활성 상태에서도 적용될 수 있습니다. 태그 요구 사항을 충족하지 않아 비활성 상태였던 `GameplayEffect`가 요구 사항을 다시 충족하면, `GameplayEffect`는 활성화되며 그 Modifier를 다시 적용합니다. 이 기능은 `Duration` 및 `Infinite` `GameplayEffect`에서만 작동합니다. |
| Application Tag Requirements      | `GameplayEffect`가 타겟에 적용될 수 있는지 여부를 결정하는 태그입니다. 요구 사항이 충족되지 않으면 `GameplayEffect`는 적용되지 않습니다.                                                                                                                                                                                                                      |
| Remove Gameplay Effects with Tags | `GameplayEffect`가 성공적으로 적용되면 타겟의 `Asset Tags`나 `Granted Tags`에 해당 태그를 가진 다른 `GameplayEffect`가 제거됩니다.                                                                                                                                                                                            |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 면역

`GameplayEffect`는 [`GameplayTags`](#concepts-gt)를 기반으로 다른 `GameplayEffect`의 적용을 차단하는 면역(Immunity)을 부여할 수 있습니다. 면역은 `Application Tag Requirement`와 같은 다른 수단을 통해서도 효과적으로 달성할 수 있지만, 해당 시스템을 사용하면 U`AbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate` 델리게이트를 통해 면역으로 인해 `GameplayEffect`가 차단되었을 때 알림을 받을 수 있습니다.

`GrantedApplicationImmunityTag`는` Source ASC`(Source에 Ability가 있었던 경우 해당 Abilty의 AbilityTag도 포함)에 지정된 태그가 있는지를 검사합니다. 이를 통해 특정 캐릭터나 Source에 기반한 태그로부터 오는 `GameplayEffect`를 모두 차단할 수 있습니다.

`Granted Application Immunity Query`는 들어오는 `GameplayEffectSpec`이 지정된 쿼리 중 하나와 일치하는지를 확인하여 적용을 차단하거나 허용합니다.

해당 쿼리들은 `GameplayEffect` 블루프린트에서 마우스를 올리면 유용한 툴팁으로 설명을 제공해 줍니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-spec"></a>
4.5.9 Gameplay Effect Spec

[`GameplayEffectSpec`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`)은 `GameplayEffect`의 인스턴스화된 버전으로 생각할 수 있습니다. GESpec은 이를 대표하는 `GameplayEffect` 클래스에 대한 참조, 생성 시점의 레벨, 그리고 이를 생성한 주체를 포함합니다. `GameplayEffect`는 디자이너가 런타임 이전에 만들어야 하는 반면, `GameplayEffectSpec`은 런타임에 자유롭게 생성 및 수정될 수 있습니다. `GameplayEffect`를 적용할 때, `GameplayEffectSpec`은 `GameplayEffect`로부터 생성되며 실제로 Target에 적용되는 것이 바로 이 GESpec입니다.

`GameplayEffectSpec`은 `GameplayEffects`에서 `UAbilitySystemComponent::MakeOutgoingSpec()`을 사용해 생성되며, 해당 함수는 `BlueprintCallable`입니다. `GameplayEffectSpecs`은 즉시 적용될 필요는 없습니다. 일반적으로 `GameplayEffectSpecs`을 Ability에서 생성된 프로젝트타일에 전달하고, 해당 프로젝트타일이 나중에 맞은 대상에게 이를 적용하는 방식으로 사용됩니다. `GameplayEffectSpecs`가 성공적으로 적용되면 `FActiveGameplayEffect`라는 새로운 구조체가 반환됩니다.

참고해두면 좋은 `GameplayEffectSpec`의 주요 내용:

* 해당 `GameplayEffectSpec`가 생성된 `GameplayEffect` 클래스
* `GameplayEffectSpec`의 레벨. 보통 `GameplayEffectSpec`를 생성한 Ability의 레벨과 같지만 다를 수도 있음.
* `GameplayEffectSpec`의 지속 시간. 기본적으로 원본 `GameplayEffect`의 지속 시간이지만 다르게 설정될 수 있음.
* `GameplayEffectSpec`의 Period(Period Effect의 경우). 기본적으로 원본 `GameplayEffect`의 Period지만 변경될 수 있음.
* `GameplayEffectSpec`의 현재 스택 수. 스택 한계는 원본 `GameplayEffect`에 설정되어 있음.
* [`GameplayEffectContextHandle`](#concepts-ge-context)은 해당 `GameplayEffectSpec`를 생성한 주체를 나타냄.
* 스냅샷팅(Snapshotting)에 의해 `GameplayEffectSpec` 생성 시점에 캡처된 `Attribute`.
* `GameplayEffect`가 부여하는 `GameplayTag` 외에 Target에게 `GameplayEffectSpec`가 추가로 부여되는 `DynamicGrantedTag`.
* `GameplayEffect`가 가지는 `AssetTag` 외에 `GameplayEffectSpec`가 추가로 가지는 `DynamicAssetTag`.
* `SetByCaller` `TMaps`.

**[⬆ 위로 가기](#table-of-contents)**

