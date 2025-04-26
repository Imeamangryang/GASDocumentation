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
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect 정의](#concepts-ge-definition)   
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Gameplay Effect 적용](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Gameplay Effect 삭제](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply 및 Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Modifier의 GameplayTag](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Gameplay Effect Stacking(중첩)](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Ability 부여](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [면역](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)   
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Cooldown GameplayEffect의 남은 시간 얻어내기](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Cooldown 시작 및 종료 청취(Listening)](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [런타임에서 GameplayEffect 동적 생성하기](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability 정의](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy(리플리케이션 정책)](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly(입력 직접 복제)](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [ASC에 입력 바인딩](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [GameplayAbility를 활성화하지 않고 입력 바인딩](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Ability 부여](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Ability 활성화](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [패시브 Ability](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [Activation Failed Tags](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Ability 취소](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [활성화된 Ability 얻기](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Ability에 데이터 전달하기](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Ability 레벨업](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)   
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task 정의](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Ability Tasks 사용](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue 정의](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [GameplayCue가 발동되지 않도록 방지](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching(일괄 처리)](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [수동 RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [하나의 GE에 여러 개의 GC](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability(신뢰성)](#concepts-gc-reliability)  

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

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers

`SetByCaller`는 `GameplayEffectSpec`이 GameplayTag 또는 FName에 연결된 float 값을 운반하도록 허용합니다. 이 값들은 각각의 `TMap`에 저장됩니다:

- TMap<FGameplayTag, float>
- TMap<FName, float>

SetByCaller는 `GameplayEffect`의 `Modifier`로 사용되거나, 일반적으로 float 값을 다른 시스템으로 전달하는 수단으로 사용될 수 있습니다. 보통 Ability 내부에서 생성된 수치 데이터를 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)나 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)에 전달할 때 `SetByCaller`가 사용됩니다.

| `SetByCaller` 사용처 | 사용 방법                                                                                                                                                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | `GameplayEffect` 클래스에서 미리 정의되어야 하며, `GameplayTag` 버전만 사용할 수 있습니다. 만약 `GameplayEffectSpec`이 일치하는 태그와 float 값 쌍을 가지지 않는다면, 게임은 런타임 오류를 발생시키고 해당 `GameplayEffectSpec`의 값은 0이 반환됩니다. 이는 나눗셈(Divide) 연산 시 잠재적 문제를 일으킬 수 있습니다. 자세한 내용은 [`Modifiers`](#concepts-ge-mods)를 참고하세요.|
| 기타      | 미리 정의될 필요가 없으며, 어디서든 사용할 수 있습니다. `GameplayEffectSpec`에 존재하지 않는 `SetByCaller` 값을 읽으면 개발자가 정의한 기본 값(Default Value)을 반환할 수 있으며, 경고 메시지를 선택적으로 출력할 수도 있습니다.     |

블루프린트에서 `SetByCaller` 값을 할당하려면, 필요한 버전에 대한 블루프린트 노드(`GameplayTag` 또는 `FName`)를 사용합니다.

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

블루프린트에서 `SetByCaller` 값을 읽으려면, Blueprint Library에 커스텀 노드를 만들어야 합니다.

C++에서 `SetByCaller` 값을 할당하려면 필요한 함수 버전(`GameplayTag` 또는 `FName`)을 사용하세요:

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
`FName` 버전보다 `GameplayTag` 버전을 사용하는 것이 좋습니다. 이렇게 하면 블루프린트에서 철자 오류를 방지할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context

[`GameplayEffectContext`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 구조체는 `GameplayEffectSpec`의 시작자(Instigator)와 [`TargetData`](#concepts-targeting-data)에 대한 정보를 보유합니다. 해당 구조체는 또한 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc), [`GameplayEffectExecutionCalculations`](#concepts-ge-ec), [`AttributeSets`](#concepts-as), [`GameplayCues`](#concepts-gc) 등과 같은 다양한 장소에서 임의의 데이터를 전달할 때 서브클래싱하여 사용하는 데 유용합니다.


`GameplayEffectContext`를 서브클래싱하려면 다음 단계를 따르세요:

1. `FGameplayEffectContext` 서브클래스 생성
1. `FGameplayEffectContext::GetScriptStruct()` 재정의
1. `FGameplayEffectContext::Duplicate()` 재정의
1. 새로운 데이터가 리플리케이트되어야 하는 경우 `FGameplayEffectContext::NetSerialize()` 재정의
1. 부모 구조체인 `FGameplayEffectContext`가 사용하는 것처럼 서브클래스를 위해 `TStructOpsTypeTrait` 구현
1. [`AbilitySystemGlobals`](#concepts-asg) 클래스에서 `AllocGameplayEffectContext()`를 재정의하여 서브클래스의 새 객체를 반환하도록 설정.

[GASShooter](https://github.com/tranek/GASShooter)는 서브클래싱된 `GameplayEffectContext`를 사용하여 `TargetData`를 추가하고, 이를 `GameplayCue`에서 접근할 수 있도록 합니다. 특히 산탄총과 같이 여러 적을 한 번에 맞힐 수 있는 경우에 유용합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation

[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` 또는 `MMC`)는 `GameplayEffect`에서 [`Modifiers`](#concepts-ge-mods)로 사용되는 강력한 클래스입니다. 이들은 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)와 유사하게 작동하지만, 기능이 덜 강력하고 가장 중요한 점은 [predicted](#concepts-p)이 가능하다는 것입니다. 이들의 주요 목적은 `CalculateBaseMagnitude_Implementation()`에서 float 값을 반환하는 것입니다. 이 함수를 Blueprint와 C++에서 서브클래싱하고 재정의할 수 있습니다.

`MMC`는 `Instant`, `Duration`, `Infinite`, `Periodic` 등 어떤 종류의 `GameplayEffect`에도 사용할 수 있습니다.

`MMC`의 강점은 `GameplayEffectSpec`에 대한 전체 액세스를 통해 `Source` 또는 `Target`의 여러 `Attribute` 값을 캡처할 수 있다는 점입니다. 이로 인해 `GameplayTag`와 `SetByCaller`를 읽을 수 있습니다. `Attribute`는 스냅샷 방식으로 캡처할 수 있으며, 그렇지 않은 경우에도 캡처할 수 있습니다. 스냅샷된 `Attribute`는 `GameplayEffectSpec`이 생성될 때 캡처되고, 비스냅샷 `Attribute`는 `GameplayEffectSpec`이 적용될 때 캡처되며, `Infinite`와 `Duration` 효과에 대해 `Attribute`가 변경될 때 자동으로 업데이트됩니다. `Attribute` 캡처는 해당 `Attribute`의 `CurrentValue`를 ASC의 기존 모드에서 다시 계산합니다.

> **note** 이 재계산은 `AbilitySet`의 [`PreAttributeChange()`](#concepts-as-preattributechange)를 실행하지 않으므로, 모든 클램핑은 여기에서 다시 수행해야 합니다.

| Snapshot | Source or Target | `GameplayEffectSpec`의 캡처 시점 |  `Infinite` 또는 `Duration` `GE`의 `Attribute`가 변경될 경우 자동 업데이트 여부. |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

`MMC`에서 나온 float 값은 `GameplayEffect`의 `Modifier`에서 계수(coefficient)와 전후 계수 추가에 의해 더 수정될 수 있습니다.`

예시로, 타겟의 mana 속성을 캡처하여 독 효과로부터 이를 감소시키는 `MMC`가 있을 수 있습니다. 해당 감소량은 타겟이 가진 mana 양과 타겟이 가지고 있을 수 있는 태그에 따라 달라집니다.

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

`MMC` 의 생성자에서 `RelevantAttributesToCapture` 에 `FGameplayEffectAttributeCaptureDefinition` 을 추가하지 않고 `Attributes` 캡처를 시도하면 캡처 도중 스펙이 없다는 오류가 발생합니다. `Attributes`을 캡처할 필요가 없는 경우 `RelevantAttributesToCapture`에 아무 것도 추가할 필요가 없습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation

[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution`(해당 용어는 플러그인의 소스 코드에서 자주 보임), 또는 `ExecCalc`)은 `GameplayEffect`가 `ASC`에 변화를 주는 가장 강력한 방법입니다. [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)와 유사하게, `ExecutionCalculation`도 `Attribute`를 캡처할 수 있으며, 이를 선택적으로 스냅샷 방식으로 캡처할 수 있습니다. `MMC`와는 달리, ExecutionCalculation은 하나 이상의 `Attribute`를 변경할 수 있으며, 본질적으로 프로그래머가 원하는 모든 작업을 수행할 수 있습니다. 그러나 이 강력한 기능과 유연성의 단점은 [predicted](#concepts-p)이 불가능하고 C++로 구현해야 한다는 것입니다.

`ExecutionCalculation`은 `Instant`와 `Periodic` `GameplayEffect`에서만 사용 가능합니다. Execute라는 단어가 포함된 것은 일반적으로 이 두 종류의 `GameplayEffect`를 의미합니다.

스냅샷은 `GameplayEffectSpec`이 생성될 때 `Attribute`를 캡처하며, 비스냅샷은 `GameplayEffectSpec`이 적용될 때 `Attribute`를 캡처합니다. `Attribute` 캡처는 해당 Attribute의 `CurrentValue`를 `ASC`의 기존 모드에서 다시 계산합니다. 

> **note** 이 재계산은 `AbilitySet`의 [`PreAttributeChange()`](#concepts-as-preattributechange)를 실행하지 않으므로, 모든 클램핑은 여기에서 다시 수행해야 합니다.

| Snapshot | Source or Target | `GameplayEffectSpec` 캡처 시점 |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

`Attribute` 캡처를 설정하려면, 에픽 게임즈의 ActionRPG Sample Project에서 설정한 패턴을 따르며, `Attributes`를 캡처하는 방법을 정의하는 구조체를 정의하고, 구조체의 생성자에서 해당 구조체의 복사본을 생성해야 합니다. `ExecCalc`마다 이런 구조체가 필요합니다. 

> **Note:** 구조체 이름은 고유해야 합니다. 동일한 이름을 사용하면 A`ttribute` 캡처에서 잘못된 동작이 발생할 수 있습니다(주로 잘못된 `Attribute`의 값이 캡처됨).

`Local Predicted`, `Server Only`, `Server Initiated` [`GameplayAbilities`](#concepts-ga)의 경우, `ExecCalc`는 서버에서만 호출됩니다.

`Source`와 `Target`에서 여러 Attribute를 읽어 복잡한 공식에 따라 피해를 계산하는 것이 `ExecCalc`의 가장 일반적인 예입니다. 포함된 Sample Project에는 `GameplayEffectSpec`의 [`SetByCaller`](#concepts-ge-spec-setbycaller)에서 피해 값을 읽고, `Target`에서 캡처된 방어구 `Attribute`를 기준으로 그 값을 완화하는 간단한 `ExecCalc`가 포함되어 있습니다. 이 예시는 `GDDamageExecCalculation.cpp/.h`에서 확인할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 Sending Data to Execution Calculations

`ExecutionCalculation`에 `Attribute`를 캡처하는 것 외에도 데이터를 전달하는 몇 가지 방법이 있습니다.

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller

[`GameplayEffectSpec`에 설정된 모든 `SetByCaller`](#concepts-ge-spec-setbycaller)값은 `ExecutionCalculation`에서 직접 읽을 수 있습니다.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier

값을 `GameplayEffect`에 하드코딩하려면, 캡처된 `Attribute` 중 하나를 백업 데이터로 사용하는 `CalculationModifier`를 사용하여 값을 전달할 수 있습니다.

아래의 스크린샷 예제에서는 캡처된 Damage `Attribute`에 50을 추가하고 있습니다. 또한, 값을 `Override`로 설정하여 하드코딩된 값만 사용할 수도 있습니다.

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)


`ExecutionCalculation`은 `Attribute`를 캡처할 때 이 값을 읽습니다.

```c++
float Damage = 0.0f;
// ExecutionCalculation에서 CalculationModifier로 설정된 옵션성 피해 값을 GameplayEffect의 Damage GE에 캡처합니다.
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context

`ExecutionCalculation`으로 데이터를 보내려면, [`GameplayEffectContext` on the `GameplayEffectSpec`](#concepts-ge-context)를 커스텀하여 전달할 수 있습니다.

`ExecutionCalculation`에서 `FGameplayEffectCustomExecutionParameter`를 통해 `EffectContext`에 접근할 수 있습니다.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

`GameplayEffectSpec` 또는 `EffectContext`의 내용을 변경해야 하는 경우:

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

`ExecutionCalculation`에서 `GameplayEffectSpec`을 수정할 때는 주의해야 합니다.
`GetOwningSpecForPreExecuteMod()`에 대한 주석을 참고하십시오.

```c++
/** Const 접근이 아닙니다. 특히 Attribute 캡처 후 Spec을 수정할 때 주의하세요. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement

[`CustomApplicationRequirement`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)(`CAR`) 클래스는 `GameplayEffect`가 적용될 수 있는지에 대한 고급 제어를 디자이너에게 제공합니다. 이는 `GameplayEffect`의 단순한 `GameplayTag` 검사보다 더 복잡한 조건을 설정할 수 있게 해줍니다. CAR은 `CanApplyGameplayEffect()`를 오버라이드하여 블루프린트에서 구현할 수 있으며, C++에서는 `CanApplyGameplayEffect_Implementation()`을 오버라이드하여 구현할 수 있습니다.

`CAR`의 사용 예시:
* `Target`이 특정 `Attribute`의 값을 일정 수준 이상 가지고 있어야 하는 경우
* `Target`이 특정 `GameplayEffect`의 스택을 일정 수 이상 가지고 있어야 하는 경우

`CAR`은 더 많은 고급 기능을 수행할 수 있습니다. 예를 들어, 해당 대상에게 이미 `GameplayEffect`의 인스턴스가 적용되어 있는지 확인하고, 새 인스턴스를 적용하는 대신 기존 인스턴스의 [changing the duration](#concepts-ge-duration)할 수 있습니다(이 경우 `CanApplyGameplayEffect()`에서 false를 반환).

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect

[`GameplayAbilities`](#concepts-ga)에는 선택적으로 Ability의 Cost(비용)로 사용할 수 있도록 설계된 `GameplayEffect`가 존재합니다. Cost란, `AbilitySystemComponent` (ASC)가 `GameplayAbility`를 활성화하기 위해 필요한 `Attribute`의 양을 의미합니다. 만약 `GameplayAbilit`y가 `Cost`에 해당하는 `GameplayEffect`를 감당할 수 없다면 활성화되지 않습니다.

해당 `Cost GameplayEffect`는 `Instant` 타입이어야 하며, 하나 이상의 `Modifier`를 통해 `Attribute`에서 값을 차감합니다. 기본적으로 `Cost GameplayEffect`는 예측(Prediction)을 지원해야 하므로 `ExecutionCalculation`을 사용하지 않는 것이 좋습니다. 복잡한 Cost 계산이 필요하다면 `MMC`(GameplayModMagnitudeCalculation)를 사용하는 것이 허용되며 권장됩니다.

처음 시작할 때는 대부분 `GameplayAbility`마다 고유한 `Cost GameplayEffect`를 설정하게 될 것입니다. 하지만 더 고급 기법으로는 여러 개의 `GameplayAbility`에서 하나의` Cost GameplayEffect`를 재사용할 수 있습니다. 이때는 `Cost` 값이 각 `GameplayAbility`에 정의되어야 하며, 생성된 `GameplayEffectSpec`에 `GameplayAbility`별 데이터를 추가로 설정합니다. **이 방법은 인스턴스화된(`Instanced`) GameplayAbility에서만 작동합니다.**

`Cost GameplayEffect`를 재사용하는 두 가지 방법:

1. `MMC` 사용하기 (가장 쉬운 방법)
[`MMC`](#concepts-ge-mmc)를 만들고, `GameplayEffectSpec`에서 `GameplayAbility` 인스턴스로부터 `Cost` 값을 가져옵니다.

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```
이 예제에서 Cost 값은 `GameplayAbility` 자식 클래스에 추가된 `FScalableFloat` 타입의 변수입니다.

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **`UGameplayAbility::GetCostGameplayEffect()` 오버라이드하기**
이 함수를 오버라이드하면 GameplayAbility의 Cost 값을 기반으로 [GameplayEffect를 런타임](#concepts-ge-dynamic)에 생성할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect

[`GameplayAbilities`](#concepts-ga)에는 Cooldown(쿨타임) 용도로 사용할 수 있도록 설계된 `GameplayEffect`가 있습니다. Cooldown은 `GameplayAbility`를 활성화한 후 다시 사용할 수 있을 때까지의 시간을 결정합니다. 만약 `GameplayAbility`가 아직 Cooldown 상태라면 활성화할 수 없습니다. 이 `Cooldown GameplayEffect`는 `Duration` 타입이어야 하며 `Modifier`가 없어야 합니다. 또한 `GameplayEffect`의 `GrantedTag`에 `GameplayAbility`별로 고유한 `GameplayTag(“Cooldown Tag”)`를 할당해야 합니다. 만약 게임에 슬롯 개념이 존재하고, 슬롯 간에 Cooldown을 공유한다면 슬롯당 고유한 GameplayTag를 사용할 수도 있습니다.
`GameplayAbility`는 실제로 `Cooldown Tag`의 존재 여부를 확인하지, `Cooldown GameplayEffect` 자체를 확인하지는 않습니다. 기본적으로 `Cooldown GameplayEffect`는 예측을 지원해야 하므로 `ExecutionCalculation`를 사용하지 않는 것이 좋습니다. 대신, 복잡한 Cooldown 계산에는 `MMC`를 사용하는 것이 허용되며 권장됩니다.

처음에는 `GameplayAbility`마다 고유한 `Cooldown GameplayEffect`를 설정하게 됩니다. 하지만 더 고급 기법으로는 여러 개의 `GameplayAbility`에서 하나의 `Cooldown GameplayEffect`를 재사용할 수 있습니다. 이 경우 Cooldown 시간과 `Cooldown Tag`는 각 `GameplayAbility`에서 정의해야 하며, 생성된 `GameplayEffectSpec`에 해당 데이터를 동적으로 설정합니다. **이 방법은 인스턴스화된(`Instanced`)GameplayAbility에서만 작동합니다.**

`Cooldown GameplayEffect`를 재사용하는 두 가지 방법:

1. **[`SetByCaller`](#concepts-ge-spec-setbycaller)를 활용한 방법**(가장 쉬운 방법)

공유 `Cooldown GameplayEffect`(GE)의 Duration을 `GameplayTag`와 함께 `SetByCaller`로 설정합니다. `GameplayAbility` 서브클래스에서 다음을 정의합니다. `GameplayAbility` 서브클래스에 Duration에 대한 float /` FScalableFloat`, 고유 Cooldown 태그에 대한 `FGameplayTagContainer`, `Cooldown Tag`와 `Cooldown GE`의 태그를 합친 반환 포인터로 사용할 임시 `FGameplayTagContainer`를 정의합니다.

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags()에서 반환할 포인터를 사용할 임시 컨테이너입니다.
// 이것은CooldownTag와 Cooldown GE의 CoolDown 태그를 합친 값입니다.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

그런 다음, `UGameplayAbility::GetCooldownTags()`를 오버라이드하여 `Cooldown Tag`와 기존 `cooldown GameplayEffect`의 Tag를 합친 값을 반환하도록 합니다.

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags는 CDO의 TempCooldownTags에 기록되므로, GameplayAbility의 Cooldown 태그가 변경될 경우(다른 슬롯으로 이동) 이를 지우기 위해 초기화.
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

마지막으로, `UGameplayAbility::ApplyCooldown()`을 오버라이드하여 `Cooldown Tag`를 주입하고, Cooldown `GameplayEffectSpec`에 `SetByCaller`를 추가합니다.

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```
이 그림에서 Cooldown의 Duration `Modifier`는 `SetByCaller`로 설정되며, Data.Cooldown이라는 `data Tag`를 사용합니다. `Data.Cooldown`은 위 코드에서 `OurSetByCallerTag`에 해당합니다.

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **`MMC`를 활용한 방법**

해당 방법은 위의 방법(`ApplyCooldown`)과 동일한 설정을 사용하지만,` Cooldown GE`의 지속 시간을 `SetByCaller`로 설정하는 대신, Duration을 `Custom Calculation Class`로 설정하고, 새로 만들 `MMC`를 가리키도록 합니다.

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// GetCooldownTags()에서 반환할 포인터로 사용할 임시 컨테이너입니다.
// CooldownTags와 Cooldown GE의 Cooldown 태그를 합친 값입니다.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

그런 다음, `UGameplayAbility::GetCooldownTags()`를 오버라이드하여 `Cooldown Tag`와 기존 `Cooldown GE`의 태그를 합친 값을 반환하도록 합니다.

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

마지막으로, `UGameplayAbility::ApplyCooldown()`을 오버라이드하여 `Cooldown Tag`를 Cooldown `GameplayEffectSpec`에 주입합니다.

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Cooldown GameplayEffect의 남은 시간 얻어내기

```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

> **Note**: 클라이언트에서 Cooldown의 남은 시간을 Query(조회)하려면 리플리케이트된 `GameplayEffect`를 수신할 수 있어야 합니다. 이는 `ASC`의 [replication mode](#concepts-asc-rm)에 따라 달라집니다.

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 Cooldown 시작 및 종료 청취(Listening)

Cooldown이 시작되는 시점을 수신하려면, `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`에 바인딩하여 `Cooldown GE`가 적용될 때 응답하거나, `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`에 바인딩하여 `Cooldown Tag`가 추가될 때 응답할 수 있습니다. `Cooldown GE`가 언제 추가되었는지 확인하는 것이 좋은데, `Cooldown GE`를 적용한 `GameplayEffectSpec`에도 접근할 수 있기 때문입니다. 이를 통해 `Cooldown GE`가 로컬에서 예측한 것인지 서버에서 수정한 것인지를 확인할 수 있습니다.

Cooldown이 언제 끝나는지 수신하려면, `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`에 바인딩하여 `Cooldown GE`가 제거되는 시점에 응답하거나, `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`에 바인딩하여 `Cooldown Tag`가 제거되는 시점에 응답하면 됩니다. 서버의 수정된 `Cooldown GE`가 들어오면 로컬에서 예측한 Cooldown이 제거되어 Cooldown이 진행 중임에도 불구하고 `OnAnyGameplayEffectRemovedDelegate()`가 발동되므로 `Cooldown Tag`가 제거되는 시점을 잘 살펴볼 것을 권장합니다. 예측된 `Cooldown GE`를 제거하고 서버의 수정된 `Cooldown GE`를 적용하는 동안 `Cooldown Tag`는 변경되지 않습니다.

> **Note:** 클라이언트에서 `GameplayEffect`가 추가되거나 제거되는 것을 듣기 위해서는 클라이언트가 리플리케이트된 `GameplayEffect`를 받을 수 있어야 합니다. 이는 `ASC`의 [replication mode](#concepts-asc-rm)에 따라 달라집니다.

샘플 프로젝트에는 Cooldown이 시작되고 끝나는 것을 듣는 Costom Blueprint 노드가 포함되어 있습니다. HUD UMG Widget은 이를 사용하여 메테오의 Cooldown의 남은 시간을 업데이트합니다. 해당 `AsyncTask`는 `EndTask()`가 수동으로 호출될 때까지 계속 살아 있습니다. UMG Widget의 `Destruct` 이벤트에서 이를 처리합니다. [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)를 참고하세요.

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 Predicting Cooldowns

현재 Cooldown을 실제로 예측할 수 없습니다. 로컬에서 예측된 `Cooldown GE`가 적용될 때 UI Cooldown 타이머를 시작할 수 있지만, `GameplayAbility`의 실제 Cooldown은 서버의 Cooldown의 남은 시간에 연결되어 있습니다. 플레이어의 지연 시간에 따라, 로컬에서 예측된 Cooldown이 만료되었을 수 있지만, `GameplayAbility`는 여전히 서버에서 Cooldown 중일 수 있으며, 이로 인해 서버의 Cooldown이 만료될 때까지 `GameplayAbility`를 즉시 재활성화할 수 없습니다.

샘플 프로젝트에서는 로컬에서 예측된 Cooldown이 시작될 때 메테오 Ability의 UI 아이콘을 회색으로 처리하고, 서버에서 수정된 `Cooldown GE`가 들어오면 Cooldown 타이머를 시작하는 방식으로 이를 처리합니다.

게임 플레이 결과로, 지연 시간이 높은 플레이어는 짧은 Cooldown 능력에 대해 낮은 발사 속도를 가지게 되어 지연 시간이 낮은 플레이어에 비해 불리한 상황에 처하게 됩니다. Fortnite는 이를 피하기 위해 무기들이 Cooldown `GameplayEffect`를 사용하지 않고 맞춤형 기록 방식을 사용합니다.

진정한 예측된 Cooldown을 허용하는(플레이어가 로컬 Cooldown이 만료되었을 때 `GameplayAbility`를 활성화할 수 있지만 서버에서는 여전히 Cooldown 중인 상태) 것은 에픽 게임즈가 향후 [GAS의 다음 버전에서 구현하고자 하는 기능](#concepts-p-future)입니다.


**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 활성화된 GameplayEffect 지속 시간 변경

`Cooldown GE` 또는 `Duration` `GameplayEffect`의 남은 시간을 변경하려면, `GameplayEffectSpec`의 `Duration`을 변경하고, `StartServerWorldTime`을 업데이트하고, `CachedStartServerWorldTime`을 업데이트하고, `StartWorldTime`을 업데이트한 다음 `CheckDuration()`으로 지속시간 검사를 다시 실행해야 합니다. 서버에서 이 작업을 수행하고 `FActiveGameplayEffect`를 더티로 표시하면 클라이언트에 변경사항이 리플리케이트됩니다.


`Cooldown GE`나 다른 `Duration` `GameplayEffect`의 남은 시간을 변경하려면, `GameplayEffectSpec`의 `Duration`을 변경하고, `StartServerWorldTime`, `CachedStartServerWorldTime`, `StartWorldTime`을 업데이트한 후, `CheckDuration()`으로 Duration 검사를 다시 실행해야 합니다. 서버에서 이를 수행하고 `FActiveGameplayEffect`를 더티 마킹하면, 변경 사항이 클라이언트에 리플리케이트됩니다.

> **Note:** 이것은 `const_cast`가 필요하며 에픽 게임즈가 의도한 Duration 변경 방식이 아닐 수도 있지만, 지금까지는 잘 작동하는 것 같습니다.

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 런타임에서 GameplayEffect 동적 생성하기

런타임에서 동적 `GameplayEffect`를 생성하는 것은 심화 주제입니다. 이 작업은 자주 할 필요는 없습니다.

`Instant` `GameplayEffect`만 C++에서 런타임에 처음부터 생성할 수 있습니다. `Duration`과 `Infinite` `GameplayEffect`는 런타임에서 동적으로 생성할 수 없습니다. 왜냐하면, 이를 리플리케이트할 때 해당 `GameplayEffect` 클래스 정의를 찾게 되는데, 이는 존재하지 않기 때문입니다. 이 기능을 구현하려면, 대신 에디터에서 하던 것처럼 Archetype `GameplayEffect` 클래스를 만들고, 런타임에서 `GameplayEffectSpec `인스턴스를 필요한 대로 커스터마이즈하는 방식으로 접근해야 합니다.

런타임에서 생성된 `Instant` `GameplayEffect`는 [local predicted](#concepts-p) `GameplayAbility`내에서 호출될 수 있습니다. 그러나 동적 생성이 부작용을 일으킬 수 있는지는 아직 불확실합니다.

##### Examples

샘플 프로젝트에서는 캐릭터가 치명적인 타격을 입혔을 때, 그 캐릭터를 처치한 플레이어에게 골드와 경험치를 보내기 위해 GameplayEffect를 생성합니다.

```c++
// 보상을 주기 위해 동적 Instant GameplayEffect를 생성
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

두 번째 예시는 로컬 예측` GameplayAbility` 내에서 생성된 런타임 `GameplayEffect`를 보여줍니다. 코드 내 주석을 참조하여 사용할 때 주의하세요!

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// 런타임 중 GE 생성.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // 런타임 GE는 인스턴트만 작동합니다.

		// 간단한 스케일러블 float Modifier를 추가하여 MyAttribute를 42로 덮어씁니다.
        // 실제 애플리케이션에서는 TriggerEventData를 통해 전달된 정보를 소비합니다.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// GE 적용.

        // 여기서 GESpec을 생성하여 ASC가 GE 클래스 기본 객체에서 GESpec을 생성하는 동작을 피합니다.
        // 동적 GE가 있을 때 기본 GameplayEffect 클래스로 GESpec을 생성하면 Modifier가 손실되기 때문에 이렇게 처리합니다. 
        // 주의: 이 해킹이 문제가 될 수 있는지 불확실합니다!
        // GESpec에서 GE가 UPROPERTY로 참조되므로 GE 객체가 GarbageCollector에 의해 수거되는 것을 방지합니다.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // new, handle 내에서 shared ptr로 수명이 관리되기 때문입니다.
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect Containers

에픽 게임즈의 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/ko-kr/product/action-rpg)는 `FGameplayEffectContainer`라는 구조체를 구현합니다. 이는 기본 GAS에는 없지만 `GameplayEffect`와 [`TargetData`](#concepts-targeting-data)를 담는 데 매우 유용합니다. 이 구조체는 `GameplayEffectSpec`를 생성하고 기본 값을 설정하는 등의 작업을 자동화합니다. `GameplayAbility`에서 `GameplayEffectContainer`를 생성하고 이를 발사된 투사체에 전달하는 것은 매우 쉽고 직관적입니다. 저는 포함된 샘플 프로젝트에서 `GameplayEffectContainer`를 구현하지 않았는데, 이는 기본 GAS에서 이를 사용하지 않고 작업하는 방법을 보여주기 위함입니다. 하지만 이 구조체를 프로젝트에 추가하는 것을 고려해보는 것이 좋습니다.

`GameplayEffectContainer` 안의 `GESpec`에 접근하여 `SetByCaller`를 추가하는 등의 작업을 하려면, `FGameplayEffectContainer`를 분해하고 `GESpec`의 인덱스를 사용하여 `GESpec` 참조에 접근해야 합니다. 이를 위해서는 액세스하려는 `GESpec`의 인덱스를 미리 알아야 합니다.

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainer`는 효율적인 [targeting](#concepts-targeting-containers).을 위한 선택적인 수단도 포함하고 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Ability

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability 정의

[`GameplayAbilities`](https://docs.unrealengine.com/ko-kr/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`)는 게임에서 `Actor`가 수행할 수 있는 모든 행동이나 스킬입니다. 예를 들어, 질주하면서 총을 쏘는 것처럼 둘 이상의 `GameplayAbility`를 동시에 활성화할 수도 있습니다. `GameplayAbility`는 블루프린트 또는 C++에서 구현할 수 있습니다.

`GameplayAbility`의 예시:

* 점프
* 질주
* 총 발사
* 특정 초마다 수동으로 공격 막기
* 포션 사용
* 문 열기
* 자원 수집
* 건물 건설

`GameplayAbility`로 구현해서는 안 되는 것들:
* 기본적인 움직임 입력
* UI와의 상호작용 - `GameplayAbility`를 사용하여 상점 아이템 구매

이는 규칙이 아니라 권장 사항일 뿐입니다. 설계와 구현은 다를 수 있습니다.

`GameplayAbility`는 Attribute의 변동량을 조절하거나 기능을 변경하기 위한 레벨(Level) 기능을 기본적으로 제공합니다.

`GameplayAbility`는 소유하는 클라이언트 또는 서버에서 실행되며, Simulated Proxy에서는 실행되지 않습니다. [`Net Execution Policy`](#concepts-ga-net)에 따라 `GameplayAbility`가 로컬에서 [predicted](#concepts-p) 실행될지 결정됩니다. 이 정책에는 [Cost 및 Cooldown을 적용할 수 있는 GameplayEffect](#concepts-ga-commit)의 기본 동작이 포함되어 있습니다.
`GameplayAbility`는 일정 시간 동안 발생하는 이벤트(예: 이벤트 대기, Attribute 변화 대기, 타겟 선택 대기,` Root Motion Source`를 활용한 `Character` 이동)를 관리하는 [`AbilityTasks`](#concepts-at)를 사용합니다. **시뮬레이션된 클라이언트는 GameplayAbility를 실행하지 않습니다.** 대신 서버에서 Ability가 실행될 때, 애니메이션 몽타주와 같은 시각적 효과는 `AbilityTask` 또는 사운드 및 파티클과 같은 시각적 요소를 처리하는 [`GameplayCues`](#concepts-gc)를 통해 리플리케이트되거나 RPC를 통해 전달됩니다. 

모든 `GameplayAbility`는 `ActivateAbility()` 함수를 오버라이드하여 게임플레이 로직을 구현해야 합니다. 추가적으로 `EndAbility()`에 완료되거나 취소될 때 실행될 로직을 추가할 수 있습니다.

간단한 `GameplayAbility` 흐름도:

![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

조금 더 복잡한 `GameplayAbility` 흐름도:

![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

복잡한 Ability는 여러 `GameplayAbility`를 사용해 서로 활성화하거나 취소하는 등의 방식으로 상호작용하게 구현할 수 있습니다.

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy(리플리케이션 정책)

이 옵션은 사용하지 않는 것이 좋습니다. 이름이 오해를 불러일으키며 실제로 필요하지도 않습니다. [`GameplayAbilitySpecs`](#concepts-ga-spec)은 기본적으로 서버에서 소유하는 클라이언트로 복제됩니다. 앞서 언급했듯이, **GameplayAbility는 Simulated Proxy에서 실행되지 않습니다.** 대신 `AbilityTask`와 `GameplayCue`를 사용해 시각적 변경 사항을 리플리케이트하거나 RPC로 전달합니다. [에픽 게임즈의 Dave Ratti는 이 옵션을 향후 제거할 계획](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)임을 언급한 바 있습니다.

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation

이 옵션은 대부분의 경우 문제를 일으킵니다. 이 옵션을 활성화하면 클라이언트의 `GameplayAbility`가 취소되거나 자연스럽게 완료될 경우, 서버에서 실행 중인 Ability도 강제로 종료됩니다. 여기서 중요한 문제는 서버의 Ability가 완료되지 않은 상태에서도 강제로 종료될 수 있다는 점입니다. 이 문제는 특히 로컬 예측(Local Prediction) `GameplayAbility`를 사용하는 플레이어가 높은 지연 시간(High Latency)을 겪을 때 심각하게 나타납니다. 일반적으로 이 옵션은 비활성화하는 것이 좋습니다.

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly(입력 직접 복제)

이 옵션을 활성화하면 입력 누름(Press) 및 해제(Release) 이벤트가 항상 서버로 리플리케이트됩니다. 하지만 에픽 게임즈에서는 이 옵션을 사용하지 말고 [`AbilityTasks`](#concepts-at)에 내장된 `Generic Replicated Event`를 사용하는 것을 권장합니다. 이는 [입력이 ASC(Ability System Component)에 바인딩](#concepts-ga-input)되어 있을 때 더욱 적절합니다.

Epic Games's comment:
```c++
/** 직접 입력 상태 리플리케이트. 이 함수들은 Ability에 `bReplicateInputDirectly`가 true로 설정된 경우 호출되지만, 일반적으로 사용하지 않는 것이 좋습니다.(대신 Generic Replicated Event를 사용하는 것이 더 좋습니다) */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 ASC에 입력 바인딩

`ASC(Ability System Component)`를 사용하면 입력 액션을 ASC에 직접 바인딩하고, 그 입력을 `GameplayAbility`에 할당할 수 있습니다. 할당된 입력 액션은 `GameplayTag` 요구 사항이 충족되면 입력이 눌릴 때 자동으로 `GameplayAbility`를 활성화합니다. 할당된 입력 액션은 입력에 반응하는 `AbilityTask`를 사용할 때 필수입니다.

`GameplayAbility`를 활성화하는 입력 액션 외에도 `ASC`는 `확인(Confirm)` 및 `취소(Cancel)`와 같은 일반 입력도 수용합니다. 이러한 특수 입력은 [`Target Actors`](#concepts-targeting-actors)를 확인하거나 취소하는 `AbilityTask`에서 사용됩니다.

`ASC`에 입력을 바인딩하려면 먼저 입력 액션 이름을 byte로 변환하는 열거형(Enum)을 생성해야 합니다. 해당 열거형의 이름은 프로젝트 설정에서 사용된 입력 액션의 이름과 정확히 일치해야 합니다. 하지만 `DisplayName`은 중요하지 않습니다.

샘플 프로젝트에서의 예시:

```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

`ASC`가 캐릭터에 존재하는 경우, `SetupPlayerInputComponent()`에서 `ASC`에 입력을 바인딩하는 함수를 포함시킵니다:

```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

`ASC`가 `PlayerState`에 존재하는 경우, `SetupPlayerInputComponent()` 내부에서 경쟁 조건(Race condition)이 발생할 수 있습니다. 이는 `PlayerState`가 클라이언트에 아직 리플리케이트되지 않았을 수 있기 때문입니다. 따라서 `SetupPlayerInputComponent()`와 `OnRep_PlayerState()` 모두에서 입력을 바인딩하도록 시도하는 것을 권장합니다.
`OnRep_PlayerState()`만으로는 충분하지 않은 이유는, `PlayerState`가 리플리케이트될 때 `PlayerController`가 `ClientRestart()`를 호출해 `InputComponent`를 생성하기 전에 `Actor`의 `InputComponent`가 null일 수 있기 때문입니다. 샘플 프로젝트는 이 두 위치 모두에서 입력 바인딩을 시도하면서, 입력이 단 한 번만 바인딩되도록 불리언 변수를 사용해 과정을 제어하는 방법을 보여줍니다.

> **Note:** 샘플 프로젝트에서 열거형(Enum)의 `Confirm`과 `Cancel`은 프로젝트 설정에 정의된 입력 액션 이름(`ConfirmTarget` 및 `CancelTarget`)과 일치하지 않습니다. 그러나 `BindAbilityActivationToInputComponent()`에서 이 둘 사이의 매핑을 제공합니다. 이러한 입력은 특별하므로 이름이 일치할 필요는 없지만 일치시킬 수도 있습니다. 반면, 열거형에 포함된 다른 입력들은 프로젝트 설정의 입력 액션 이름과 반드시 일치해야 합니다. 

하나의 입력으로만 활성화될 `GameplayAbility`(MOBA처럼 항상 같은 "슬롯"에 존재하는 어빌리티)의 경우, 저는 `UGameplayAbility` 서브클래스에 입력을 정의할 수 있는 변수를 추가하는 것을 선호합니다. 그런 다음 Ability를 부여할 때 `ClassDefaultObject`에서 이 값을 읽어 사용할 수 있습니다.

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 GameplayAbility를 활성화하지 않고 입력 바인딩

입력이 눌렸을 때 `GameplayAbility`가 자동으로 활성화되는 것을 원하지 않지만, `AbilityTask`에서 사용할 수 있도록 입력에 바인딩하고 싶다면, `UGameplayAbility` 서브클래스에 새로운 Boolean 변수 `bActivateOnInput`을 추가할 수 있습니다. 이 변수는 기본값을 `true`로 설정한 후, `UAbilitySystemComponent::AbilityLocalInputPressed()`를 오버라이드하면 됩니다.

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// 입력이 GenericConfirm/Cancel에 오버로드되어 있고
    // GenericConfirm/Cancel 콜백이 바인딩된 경우 입력을 소모합니다.
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// InputPressed 이벤트를 호출합니다. 
                    // 여기서는 리플리케이트되지 않습니다.  
                    // 누군가 감지하고 있다면 InputPressed 이벤트를 
                    // 서버로 리플리케이트할 수 있습니다.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability가 활성화되지 않았으므로, 이를 활성화하려 시도합니다.
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 Granting Abilities(Ability 부여)

`GameplayAbility`를 `ASC`에 부여하면, 해당 Ability가 `ASC`의 `ActivatableAbilities` 목록에 추가됩니다. 이를 통해 [`GameplayTag`](#concepts-ga-tags)의 요구사항을 충족하면 원하는 대로 `GameplayAbility`를 활성화할 수 있습니다.

`GameplayAbility`는 서버에서 부여되며, 이후 [`GameplayAbilitySpec`](#concepts-ga-spec)이 자동으로 소유 클라이언트에 복제됩니다. 다른 클라이언트나 Simulated Proxy는 해당 `GameplayAbilitySpec`을 받지 않습니다.

샘플 프로젝트에서는 `Character` 클래스에 `TArray<TSubclassOf<UGDGameplayAbility>>`를 저장하여 게임 시작 시 이를 읽고 Ability를 부여하는 방식으로 구현되어 있습니다:

```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Ability 부여합니다. 단, 서버에서만 실행됩니다.	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

이처럼 `GameplayAbilities`를 부여할 때, `UGameplayAbility` 클래스, Ability 레벨, 바인딩된 입력, 그리고 해당 `GameplayAbility`를 `ASC`에 부여한 `SourceObject(제공자)`를 사용하여 `GameplayAbilitySpec`을 생성합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 Ability 활성화

`GameplayAbility`에 입력 액션이 할당되면, 입력이 눌리고 `GameplayTag` 요구사항을 충족하면 자동으로 활성화됩니다. 하지만 이는 항상 원하는 방식으로 `GameplayAbility`를 활성화하는 방법은 아닐 수 있습니다. `ASC`는 `GameplayTag` 혹은 `GameplayAbility` 클래스나 `GameplayAbilitySpec` Handle 그리고 이벤트를 통한 총 네 가지 방법으로 `GameplayAbility`를 활성화할 수 있습니다. 이벤트를 통해 `GameplayAbility`를 활성화하면, [이벤트와 함께 데이터를 포함한 페이로드](#concepts-ga-data)를 전달할 수 있습니다.

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```

이벤트로 `GameplayAbility`를 활성화하려면, 해당 `GameplayAbility`에 `Trigger`를 설정해야 합니다. `GameplayTag`를 지정하고 `GameplayEvent` 옵션을 선택합니다. 이벤트를 전송하려면 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)` 함수를 사용합니다. 이벤트를 통해 `GameplayAbility`를 활성화하면, 데이터를 포함한 페이로드를 전달할 수 있습니다. 

또한, `GameplayAbility` `Trigger`를 사용하면 `GameplayTag`가 추가되거나 제거될 때도 `GameplayAbility`를 활성화할 수 있습니다.

> **Note:** Blueprint에서 이벤트로 `GameplayAbility`를 활성화할 때는 반드시 `ActivateAbilityFromEvent` 노드를 사용해야 합니다.

> **Note:** `GameplayAbility`를 종료해야 할 시점이 되면 반드시 `EndAbility()`를 호출해야 합니다. 단, 항상 실행되는 패시브 Ability 같은 경우에는 호출할 필요가 없습니다.

**locally predicted** `GameplayAbility`의 활성화 순서:
1. **Owning client**가 `TryActivateAbility()`를 호출합니다.
1. `InternalTryActivateAbility()`를 호출합니다.
1. `CanActivateAbility()`를 호출하여 `GameplayTag` 요건 충족 여부, `ASC`가 Cost를 감당할 수 있는지, `GameplayAbility`가 Cooldown 상태가 아닌지, 현재 활성화된 다른 인스턴스가 없는지 반환합니다.
1. 클라이언트가 `CallServerTryActivateAbility()`를 호출하며, 생성된 `Prediction key`를 서버로 전달합니다.
1. `CallActivateAbility()`를 호출합니다.
1. `PreActivate()`를 호출합니다. (에픽 게임즈는 이를 boilerplate init stuff라고 부릅니다.)
1. `ActivateAbility()`를 호출하여 최종적으로 Ability를 활성화합니다.

**Server**가 `CallServerTryActivateAbility()` 수신:
1. `ServerTryActivateAbility()`를 호출합니다.
1. `InternalServerTryActivateAbility()`을 호출합니다.
1. `InternalTryActivateAbility()`를 호출합니다.
1. `CanActivateAbility()`를 호출하여 `GameplayTag` 요건 충족 여부, `ASC`가 Cost를 감당할 수 있는지, `GameplayAbility`가 Cooldown 상태가 아닌지, 현재 활성화된 다른 인스턴스가 없는지 반환합니다.
1. 성공하면 `ClientActivateAbilitySucceed()`를 호출하여 클라이언트에게 활성화가 서버에 의해 확인되었음을 알리고, `ActivationInfo`를 업데이트하도록 지시합니다. 또한 `OnConfirmDelegate `대리자를 브로드캐스트합니다. 이는 입력 확인과는 다릅니다.
1. `CallActivateAbility()`를 호출합니다.
1. `PreActivate()`를 호출합니다. (에픽 게임즈는 이를 boilerplate init stuff라고 부릅니다.)
1. `ActivateAbility()`를 호출하여 최종적으로 Ability를 활성화합니다.

서버가 활성화에 실패하면, `ClientActivateAbilityFailed()`를 호출하여 클라이언트의 `GameplayAbility`를 즉시 종료하고, 모든 예측된 변경 사항을 되돌립니다.

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 패시브 Ability

자동으로 활성화되어 지속적으로 실행되는 패시브 `GameplayAbility`를 구현하려면, `UGameplayAbility::OnAvatarSet()`을 재정의해야 합니다. 해당 함수는 `GameplayAbility`가 부여되고 `AvatarActor`가 설정될 때 자동으로 호출됩니다. 이후 `TryActivateAbility()`를 호출하여 Ability를 활성화할 수 있습니다.

또한, 커스텀 `UGameplayAbility` 클래스에 `GameplayAbility`가 부여될 때 자동으로 활성화해야 하는지 여부를 나타내는 `bool` 변수를 추가하는 것이 좋습니다. 샘플 프로젝트에서는 방어구 중첩 패시브 Ability에 이 방식을 적용하고 있습니다.

패시브 `GameplayAbility`는 일반적으로 [`Net Execution Policy`](#concepts-ga-net)가 `Server Only`로 설정됩니다.

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

에픽 게임즈는 해당 함수가 패시브 Ability를 초기화하고 `BeginPlay`와 같은 작업을 수행하기에 적합한 위치라고 설명합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 Activation Failed Tags

Ability에는 Ability 활성화 실패 이유를 알려주는 기본 로직이 있습니다. 이를 활성화하려면 기본 실패 케이스에 해당하는 GameplayTag를 설정해야 합니다.

다음 태그(또는 자신만의 명명 규칙)를 프로젝트에 추가하세요:

```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```
그 다음, 이 태그들을 [`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13)에 추가하세요:

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```
이제 Ability 활성화가 실패할 때마다, 해당 GameplayTag가 출력 로그 메시지에 포함되거나 `showdebug AbilitySystem` HUD에서 표시됩니다.

```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![Activation Failed Tags Displayed in showdebug AbilitySystem](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ 위로 가기](#table-of-contents)**

`GameplayAbility`를 내부에서 취소하려면 `CancelAbility()`를 호출합니다. 이 함수는 `EndAbility()`를 호출하고, 그 파라미터 중 `WasCancelled`를 true로 설정합니다. 

외부에서 `GameplayAbility`를 취소하려면, `ASC`는 몇 가지 함수를 제공합니다:

```c++
/** 지정된 Ability CDO를 취소합니다. */
void CancelAbility(UGameplayAbility* Ability);	

/** 전달된 Spec Handle로 표시된 Ability를 취소합니다. Handle이 재활성화된 Ability 목록에 없으면 아무 일도 일어나지 않습니다. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** 지정된 태그로 모든 Ability를 취소합니다. Ignore 인스턴스는 취소하지 않습니다. */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** 태그와 관계없이 모든 Ability를 취소합니다. Ignore 인스턴스는 취소하지 않습니다. */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** 모든 Ability를 취소하고 남아있는 인스턴스된 Ability를 종료합니다. */
virtual void DestroyActiveState();
```

> **Note:** `CancelAllAbilities`는 `Non-Instanced` `GameplayAbility`가 있을 경우 제대로 작동하지 않는 것 같습니다. `Non-Instanced` `GameplayAbility`를 처리하고 멈추는 경우가 발생하는 것 같습니다. `CancelAbilities`는 `Non-Instanced` `GameplayAbility`를 더 잘 처리할 수 있으며, 이는 샘플 프로젝트에서 사용되는 방식입니다 (Jump는 Non-Instanced `GameplayAbility`입니다). 결과는 환경에 따라 달라질 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 활성화된 Ability 얻기

초보자들은 종종 활성화된 Ability를 어떻게 얻을 수 있나요?라고 묻습니다. 이는 Ability의 변수 값을 설정하거나 Ability를 취소하기 위해서일 수 있습니다. 한 번에 여러 개의 `GameplayAbility`가 활성화될 수 있기 때문에, 단 하나의 활성화된 Ability는 존재하지 않습니다. 대신, `ASC`의 `ActivatableAbilities`(`ASC`가 소유한 부여된 `GameplayAbility`) 목록을 검색하여 원하는 [Asset또는 부여된 GameplayTag](#concepts-ga-tags)와 일치하는 Ability를 찾아야 합니다.

`UAbilitySystemComponent::GetActivatableAbilities()`는 순회할 수 있는 `TArray<FGameplayAbilitySpec>`를 반환합니다.

`ASC`는 또한 `GameplayTagContainer`를 매개변수로 받아 `GameplayAbilitySpecs` 목록을 직접 순회하는 대신 검색을 도와주는 다른 헬퍼 함수를 제공합니다. `bOnlyAbilitiesThatSatisfyTagRequirements` 파라미터는 `GameplayTag` 요구사항을 충족하고 지금 당장 활성화될 수 있는 `GameplayAbilitySpec`만 반환합니다. 예를 들어, 무기를 가진 기본 공격 능력과 맨손 기본 공격 `GameplayAbility`가 있을 경우, 무기가 장착되어 있는지에 따라 해당 `GameplayTag` 요구 사항을 설정하고 올바른 능력이 활성화됩니다. 이 함수에 대한 에픽 게임즈의 주석에서 더 많은 정보를 확인할 수 있습니다.

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

원하는 `FGameplayAbilitySpec`을 찾았다면, 그 위에서 `IsActive()`를 호출할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy

`GameplayAbility`의 `Instancing Policy`는 Ability가 활성화될 때 어떻게 인스턴스화되는지를 결정합니다.

| `Instancing Policy`     | 설명                                                          | 사용 예시                                                                                                                                                                                                                                                                                |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 각 `ASC`는 활성화 간에 재사용되는 하나의 `GameplayAbility `인스턴스를 가집니다.    | 가장 자주 사용되는 `Instancing Policy`입니다. 모든 Ability에 사용할 수 있으며, 활성화 간에 지속성을 제공합니다. 디자이너는 필요시 변수들을 수동으로 리셋해야 합니다.                                                                                                                                                               |
| Instanced Per Execution | `GameplayAbility`가 활성화될 때마다 새로운 인스턴스가 생성됩니다. | 변수들이 매번 리셋되므로 해당 `GameplayAbility`는 활성화할 때마다 새로 생성됩니다. 성능은 `Instanced Per Actor`보다 나쁘지만, 변수 리셋이 필요할 때 유용합니다. 샘플 프로젝트에서는 이 방식을 사용하지 않습니다.                                                                                                                                 |
| Non-Instanced           | `GameplayAbility`는 `ClassDefaultObject`에서 작동하며 인스턴스가 생성되지 않습니다.      | 성능이 가장 좋지만 기능적으로 제한적입니다. `Non-Instanced` `GameplayAbility`는 상태를 저장할 수 없고, 동적 변수를 사용할 수 없으며, `AbilityTask` 델리게이트와 바인딩할 수 없습니다. 주로 MOBA나 RTS에서 자주 사용되는 간단한 Ability(예: 미니언 기본 공격)에 적합합니다. 샘플 프로젝트의 Jump `GameplayAbility`는 `Non-Instanced`입니다. |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 Net Execution Policy

`GameplayAbility`의 `Net Execution Policy`는 `GameplayAbility`를 누가 실행하는지와 그 실행 순서를 결정합니다.

| `Net Execution Policy` | 설명                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility`는 소유한 클라이언트에서만 실행됩니다. 로컬에서만 시각적 효과를 변경하는 Ability에 유용할 수 있습니다. 싱글 플레이어 게임에서는 `Server Only`를 사용해야 합니다.                                     |
| `Local Predicted`      | `Local Predicted` `GameplayAbility`는 먼저 소유한 클라이언트에서 활성화되고, 그 후 서버에서 실행됩니다. 서버는 클라이언트가 예측한 내용을 수정합니다. 예측에 대한 자세한 내용은 [Prediction](#concepts-p)을 참조해주세요. |
| `Server Only`          | `GameplayAbility`는 오직 서버에서만 실행됩니다. Passive `GameplayAbility`는 보통 `Server Only`입니다. 싱글 플레이어 게임에서는 이 방식을 사용해야 합니다.                                                                  |
| `Server Initiated`     | `Server Initiated` `GameplayAbility`는 먼저 서버에서 활성화되고, 그 후 소유한 클라이언트에서 실행됩니다. 개인적으로는 이 방법은 많이 사용하지 않았습니다.   |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability Tags

`GameplayAbility`는 내장된 로직을 가진 `GameplayTagContainer`와 함께 제공됩니다. 이 `GameplayTag`는 복제되지 않습니다.

| `GameplayTag Container`     | 설명                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility`가 소유한 `GameplayTag`입니다. 이들은 `GameplayAbility`를 설명하는 데 사용됩니다.                                                                              |
| `Cancel Abilities with Tag` | 해당 `GameplayAbility`가 활성화될 때, 해당 `Ability Tag`에 포함된 `GameplayTag`를 가진 다른 `GameplayAbility`는 취소됩니다.                                                   |
| `Block Abilities with Tag`  | 해당 `GameplayAbility`가 활성화되는 동안, 해당 `Ability Tag`에 포함된 `GameplayTag`를 가진 다른 `GameplayAbility`는 활성화될 수 없습니다.                                          |
| `Activation Owned Tags`     | 해당 `GameplayAbility`가 활성화되는 동안 소유자에게 주어지는 `GameplayTags`입니다. 단, 이들은 리플리케이되지 않습니다.                                                    |
| `Activation Required Tags`  | 해당 `GameplayAbility`는 소유자가 **모든** 해당 `GameplayTag`를 가지고 있어야만 활성화될 수 있습니다.                                                                                                |
| `Activation Blocked Tags`   | 해당 `GameplayAbility`는 소유자가 **어떤** 해당 `GameplayTag`를 가진 경우 활성화될 수 없습니다.                                                                                                  |
| `Source Required Tags`      | 해당 `GameplayAbility`는 소스가 **모든** 해당 `GameplayTag`를 가지고 있어야만 활성화될 수 있습니다. `Source`의 `GameplayTag`는 `GameplayAbility`가 이벤트로 트리거될 때만 설정됩니다. |
| `Source Blocked Tags`       | 해당 `GameplayAbility`는 소스가 **어떤** 해당 `GameplayTag`를 가진 경우 활성화될 수 없습니다. `Source`의 `GameplayTag`는 `GameplayAbility`가 이벤트로 트리거될 때만 설정됩니다.   |
| `Target Required Tags`      |해당 `GameplayAbility`는 타겟이 **모든** 해당 `GameplayTag`를 가지고 있어야만 활성화될 수 있습니다. `Target`의 `GameplayTag`는 `GameplayAbility`가 이벤트로 트리거될 때만 설정됩니다. |
| `Target Blocked Tags`       | 해당 `GameplayAbility`는 타겟이 **어떤** 해당 `GameplayTag`를 가진 경우 활성화될 수 없습니다. `Target`의 `GameplayTag`는 `GameplayAbility`가 이벤트로 트리거될 때만 설정됩니다.   |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec

`GameplayAbilitySpec`은 `GameplayAbility`가 부여된 후 `ASC`에 존재하며, 활성화 가능한 `GameplayAbility` - `GameplayAbility` 클래스, 레벨, 입력 바인딩, 그리고 `GameplayAbility` 클래스와 분리하여 유지해야 하는 런타임 상태를 정의합니다.

`GameplayAbility`가 서버에서 부여되면, 서버는 `GameplayAbilitySpec`을 소유하는 클라이언트에게 복제하여 해당 클라이언트가 이를 활성화할 수 있도록 합니다.

`GameplayAbilitySpec`을 활성화하면, `Instancing Policy`에 따라 `GameplayAbility`의 인스턴스를 생성합니다.(`Non-Instanced` `GameplayAbility`는 생성하지 않음) 

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 Ability에 데이터 전달하기

`GameplayAbility`의 일반적인 패러다임은 `Activate->Generate Data->Apply->End`입니다. 때때로 기존 데이터를 처리해야 할 때가 있습니다. GAS는 외부 데이터를 `GameplayAbility`에 전달하는 몇 가지 방법을 제공합니다:

| Method                                          | 설명                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | 이벤트를 사용하여 데이터 페이로드가 포함된 `GameplayAbility`를 활성화합니다. 이벤트의 페이로드는 로컬 예측(Local Predicted)된 `GameplayAbility`의 경우 클라이언트에서 서버로 복제됩니다. `Optional Object` 또는 [`TargetData`](#concepts-targeting-data) 변수는 기존 변수에 맞지 않는 임의의 데이터를 위한 변수로 사용됩니다. 단점은 입력 바인드를 통해 Ability를 활성화할 수 없다는 점입니다. `GameplayAbility`를 이벤트로 활성화하려면, `GameplayAbility`에서 `Trigger`를 설정해야 합니다. `GameplayTag`를 할당하고 `GameplayEvent` 옵션을 선택합니다. 이벤트를 보내려면, 함수 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`를 사용합니다. |
| Use `WaitGameplayEvent` `AbilityTask`           | `WaitGameplayEvent` `AbilityTask`를 사용하여 `GameplayAbility`가 활성화된 후 이벤트를 기다리도록 할 수 있습니다. 이벤트 페이로드와 이를 보내는 과정은 `GameplayAbility`를 이벤트로 활성화하는 것과 동일합니다. 단점은 `AbilityTask`에 의해 이벤트가 복제되지 않으므로, `Local Only` 및 `Server Only` `GameplayAbilities`에서만 사용해야 한다는 점입니다. 이벤트 페이로드를 복제하는 자체 `AbilityTask`를 작성할 수 있는 가능성도 있습니다.                                                                                                                                                                                                                                                                                               |
| Use `TargetData`                                | 커스텀 `TargetData` 구조체는 클라이언트와 서버 간에 임의의 데이터를 전달하는 좋은 방법입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Store Data on the `OwnerActor` or `AvatarActor` | `소유자(OwnerActor)`, `아바타(AvatarActor)`, 또는 참조를 얻을 수 있는 다른 객체에 저장된 리플리케이트된 변수를 사용하십시오. 해당 방법은 가장 유연하며 입력 바인딩으로 활성화된 `GameplayAbility`와 함께 작동합니다. 그러나 해당 방법은 데이터가 사용할 때 리플리케이트를 통해 동기화될 것인지를 보장하지 않습니다. 이를 미리 보장해야 합니다. 즉, 리플리케이트된 변수를 설정한 후 바로 `GameplayAbility`를 활성화하면 패킷 손실로 인해 수신자에서 발생하는 순서를 보장할 수 없습니다.  |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 Ability Cost and Cooldown

`GameplayAbility`는 선택적 Cost와 Cooldown 기능을 제공합니다. Cost는 `ASC`가 `GameplayAbility`를 활성화하기 위해 가져야 하는 미리 정의된 `Attribute` 값이며, 이는 `Instant` `GameplayEffect` ([`Cost GE`](#concepts-ge-cost))로 구현됩니다. Cooldown은 `GameplayAbility`가 만료될 때까지 재활성화를 방지하는 타이머로, `Duration` `GameplayEffect` ([`Cooldown GE`](#concepts-ge-cooldown))로 구현됩니다.

`GameplayAbility`가 `UGameplayAbility::Activate()`를 호출하기 전에, 먼저 `UGameplayAbility::CanActivateAbility()`를 호출합니다. 이 함수는 소유한 `ASC`가 Cost를 감당할 수 있는지 확인(`UGameplayAbility::CheckCost()`)하고, `GameplayAbility`가 Cooldown 상태가 아닌지 확인(`UGameplayAbility::CheckCooldown()`)합니다.

`GameplayAbility`가 `Activate()`를 호출한 후에는 언제든지 `UGameplayAbility::CommitAbility()`를 사용하여 Cost와 Cooldown을 커밋할 수 있습니다. 해당 함수는 `UGameplayAbility::CommitCost()`와 `UGameplayAbility::CommitCooldown()`을 호출합니다. 디자이너는 Cost와 Cooldown이 동시에 커밋되지 않아야 한다면 이를 별도로 호출할 수 있습니다. Cost와 Cooldown을 커밋하는 것은 `CheckCost()`와 `CheckCooldown()`을 다시 한 번 호출하며, 이는 해당 항목과 관련하여 `GameplayAbility`가 실패할 수 있는 마지막 기회입니다. `GameplayAbility`가 활성화된 후 소유한 `ASC`의 `Attribute`가 변경될 수 있으므로, 커밋 시점에서 Cost를 충족하지 못할 수 있습니다. Cost와 Cooldown을 커밋하는 것은[prediction key](#concepts-p-key)가 유효한 경우 [locally predicted](#concepts-p)가 가능합니다. 

구현에 대한 자세한 내용은 [`CostGE`](#concepts-ge-cost) 및 [`CooldownGE`](#concepts-ge-cooldown)를 참조하세요.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 Leveling Up Abilities

Ability를 레벨 업하는 두 가지 일반적인 방법이 있습니다:

| 레벨업 방법                            | 설명                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ungrant and Regrant at the New Level       | `ASC`에서 `GameplayAbility`를 제거(Ungrant)한 후, 서버에서 다음 레벨로 다시 부여(Regrant)합니다. 이때 활성화 상태의 `GameplayAbility`는 종료됩니다.                                   |
| Increase the `GameplayAbilitySpec's` Level | 서버에서 해당 `GameplayAbilitySpec`을 찾아 레벨을 증가시키고, 이를 Dirty로 표시하여 소유 클라이언트로 리플리케이트되도록 합니다. 활성화 상태의 `GameplayAbility`는 종료하지 않습니다. |

위 두 방법의 주요 차이는 레벨 업 시 활성 상태인 `GameplayAbility`를 취소할지 여부입니다. 사용하는 `GameplayAbilities`에 따라 두 가지 방법을 모두 활용해야 할 가능성이 높습니다. 이를 위해 `UGameplayAbility `서브클래스에 `bool` 변수를 추가하여 어느 방법을 사용할지 지정하는 것을 추천합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 Ability Sets

`GameplayAbilitySet`는 `GameplayAbility`를 부여하는 로직이 있는 캐릭터의 시작 `GameplayAbility`의 입력 바인딩과 목록을 보관하기 위한 편의성 `UDataAsset` 클래스입니다. 서브클래스에는 추가 로직이나 프로퍼티를 포함할 수도 있습니다. 파라곤에는 영웅마다 주어진 모든 `GameplayAbility`를 포함하는 `GameplayAbilitySet`이 있었습니다.
이 클래스는 적어도 지금까지 살펴본 바에 따르면 불필요한 클래스입니다. 샘플 프로젝트는 `GDCharacterBase`와 그 서브클래스 내부에서 `GameplayAbilitySet`의 모든 기능을 처리합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 Ability Batching

기존 `GameplayAbility`의 수명 주기에는 클라이언트에서 서버까지 최소 2~3개의 RPC(Remote Procedure Call)가 포함됩니다.

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (선택 사항)
1. `ServerEndAbility()`

`Gameplay Ability`가 이 모든 작업을 한 프레임 내에서 원자적 그룹으로 수행하는 경우, 이 워크플로를 최적화하여 모든 두세 개의 RPC를 하나로 Batch(결합)할 수 있습니다. `GAS`에서는 이 RPC 최적화를 `Ability Batching`이라고 부릅니다.
대표적인 예로 히트스캔(instant hit) 총을 들 수 있습니다. 히트스캔 총은 활성화, 라인 트레이스, [`TargetData`](#concepts-targeting-data)를 서버에 전송, Ability 종료를 한 프레임 내의 원자적 그룹으로 처리합니다. [GASShooter](https://github.com/tranek/GASShooter) 샘플 프로젝트는 히트스캔 총에 이 기술을 활용하는 방법을 보여줍니다.

반자동 총기는 `CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()` (총알 명중 데이터), `ServerEndAbility()`를 하나의 RPC로 배치하여 세 개의 RPC를 하나로 줄이는 최상의 시나리오입니다.

자동/연발 총기는 첫 번째 총알의 `CallServerTryActivateAbility()`와 `ServerSetReplicatedTargetData()`를 하나의 RPC로 배칭합니다. 이후의 각 총알은 `ServerSetReplicatedTargetData()`가 별도의 RPC로 전송됩니다. 마지막으로, 총이 사격을 멈출 경우 `ServerEndAbility()`가 별도의 RPC로 전송됩니다. 이는 첫 번째 총알 발사 시에만 두 개의 RPC를 배칭해 하나로 줄이고 이후에는 더 이상 최적화할 수 없는 최악의 시나리오입니다. 이 시나리오는 [`Gameplay Event`](#concepts-ga-data)를 통해 `Ability를 활성화하고 TargetData`를 `EventPayload`에 포함하여 클라이언트에서 서버로 전송하는 방식으로도 구현할 수 있습니다. 그러나 이 방식의 단점은 `TargetData`를 Ability 외부에서 생성해야 한다는 점이며, 반면 Batching 접근법은 Ability 내부에서 `TargetData`를 생성합니다.

`Ability Batching`은 기본적으로 [`ASC`](#concepts-asc)에서 비활성화되어 있습니다.
이를 활성화하려면, `ShouldDoServerAbilityRPCBatch()`를 재정의하여 true를 반환하도록 설정합니다.

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

이제 `Ability Batching`이 활성화되었으므로, 배칭하려는 Ability를 활성화하기 전에 `FScopedServerAbilityRPCBatcher` 구조체를 생성해야 합니다. 이 특별한 구조체는 범위 내의 모든 발생하는 모든 Ability를 배칭하려고 시도합니다. `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면 이후에 할성화되는 Ability들은 더 이상 배칭을 시도하지 않습니다.
`FScopedServerAbilityRPCBatcher`는 배칭이 가능한 각 함수에 있는 특수 코드를 사용하여 RPC 호출을 가로채고, 해당 메시지를 Barch 구조체에 대신 패킹합니다. 그리고 `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면, 자동으로 해당 Batch 구조체를 서버에 RPC로 전송합니다. 이 작업은 `UAbilitySystemComponent::EndServerAbilityRPCBatch()`에서 이루어집니다. 서버는 이 Batch RPC를 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)` 함수에서 수신합니다. `BatchInfo` 매개변수에는 Ability 종료해야 하는지 여부에 대한 flag, 활성화 시 입력 여부에 대한 flag, `TargetData`가 포함되어 있는 경우 `TargetData`가 포함됩니다. 해당 함수는 배칭이 올바르게 작동하는지 확인하기 위해 중단점을 설정하기 적합한 곳입니다. 또는, cvar `AbilitySystem.ServerRPCBatching.Log 1`을 사용하여 특수 Ability Batching 로그를 활성화할 수도 있습니다.

이 메커니즘은 C++에서만 가능하며, `FGameplayAbilitySpecHandle`을 통해서만 Ability를 활성화할 수 있습니다.

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```
GASShooter는 반자동 및 자동 총기에 대해 배칭된 `GameplayAbility`를 동일하게 사용하며, `EndAbility()`는 Ability 내에서 직접 호출되지 않습니다. 대신, EndAbility()는 플레이어 입력을 관리하고 현재 발사 모드에 따라 배치된 Ability 호출을 처리하는 로컬 전용 Ability에서 처리됩니다. 모든 RPC가 `FScopedServerAbilityRPCBatcher` 범위 내에서 발생해야 하므로, `EndAbilityImmediately` 파라미터를 제공하여 로컬 전용 Ability가 해당 Ability가 `EndAbility()` 호출을 배칭해야 하는지(반자동), 아니면 배칭하지 않아야 하는지(자동) 지정할 수 있게 합니다. `EndAbility() `호출은 나중에 별도의 RPC로 발생하게 됩니다.

GASShooter는 배칭된 Ability를 트리거하는 로컬 전용 Ability에서 사용하는 Blueprint 노드를 노출하여 Ability 배칭을 허용합니다.

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 Net Security Policy

`GameplayAbility`의 `NetSecurityPolicy`는 Ability가 네트워크에서 실행될 위치를 결정하며, 제한된 Ability를 실행하려는 클라이언트로부터 보호합니다.

| `Net Security Policy`     | 설명                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 보안 요구 사항이 없습니다. 클라이언트와 서버 모두 자유롭게 Ability를 실행하고 종료할 수 있습니다.                                          |
| `ServerOnlyExecution`   | 클라이언트가 Ability의 실행을 요청하면 서버에서 이를 무시합니다. 클라이언트는 여전히 서버에게 Ability를 취소하거나 종료하도록 요청할 수 있습니다. |
| `ServerOnlyTermination` | 클라이언트가 Ability의 취소나 종료를 요청하면 서버에서 이를 무시합니다. 클라이언트는 여전히 Ability의 실행을 요청할 수 있습니다.      |
| `ServerOnly`            | 서버가 Ability의 실행과 종료를 모두 제어합니다. 클라이언트가 어떤 요청을 하더라도 무시됩니다.                                      |

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task 정의

`GameplayAbility`는 한 프레임에서만 실행됩니다. 이로 인해 유연성이 제한됩니다. 시간이 지남에 따라 발생하는 작업이나 특정 시점에 호출되는 델리게이트에 반응해야 하는 작업을 수행하기 위해 우리는 `AbilityTask`라는 지연 작업을 사용합니다. 

GAS는 기본적으로 여러 종류의 `AbilityTask`를 제공합니다:
* `RootMotionSource`로 캐릭터 이동을 위한 작업
* 애니메이션 몽타주를 재생하는 작업
* `Attribute` 변경에 반응하는 작업
* `GameplayEffect` 변경에 반응하는 작업
* 플레이어 입력에 반응하는 작업
* 그 외의 작업들

`UAbilityTask` 생성자는 게임 전역에서 동시에 실행할 수 있는 최대 1000개의 `AbilityTask`만을 허용합니다. 이는 수백 명의 캐릭터가 동시에 존재하는 게임(예: RTS 게임)에서 `GameplayAbility`를 설계할 때 유의해야 합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 커스텀 AbilityTask

여러분은 종종 자신만의 커스텀 `AbilityTask`(C++)를 만들게 될 것입니다. 

샘플 프로젝트에는 두 가지 커스텀 `AbilityTask`가 포함되어 있습니다:

1. `PlayMontageAndWaitForEvent`: 기본 `PlayMontageAndWait`와 `WaitGameplayEvent` `AbilityTask`를 결합한 것입니다. 이 `AbilityTask`는 애니메이션 몽타주가 `AnimNotify에`서 발생한 GameplayEvent를 `GameplayAbility`로 다시 전달하도록 합니다. 애니메이션 몽타주 중 특정 시점에 행동을 트리거하는 데 사용합니다.
1. `WaitReceiveDamage`: 해당 `AbilityTask`는 `OwnerActor`가 피해를 받을 때를 감지합니다. 패시브 갑옷 스택 능력은 영웅이 피해를 입을 때마다 갑옷 스택을 제거합니다.

`AbilityTask`는 다음과 같은 구성 요소로 이루어집니다:
* `AbilityTask`의 새 인스턴스를 생성하는 정적 함수
* `AbilityTask`가 완료되었을 때 방송되는 델리게이트
* 주요 작업을 시작하고 외부 델리게이트에 바인딩하는 `Activate()` 함수
* 외부 델리게이트와의 바인딩을 해제하는 등 정리를 위한 `OnDestroy()` 함수
* 바인딩된 외부 델리게이트에 대한 콜백 함수
* 멤버 변수와 내부 헬퍼 함수들

> **Note:** `AbilityTask`는 한 가지 유형의 출력 델리게이트만 선언할 수 있습니다. 매개변수 사용 여부에 관계없이 모든 출력 델리게이트는 이 유형이어야 합니다. 사용하지 않는 델리게이트 매개변수에는 기본값을 전달해야 합니다.

`AbilityTask`는 해당 `GameplayAbility`를 실행하는 클라이언트나 서버에서만 실행됩니다. 하지만 `AbilityTask`는 `bSimulatedTask = true;`를 `AbilityTask` 생성자에 설정하고, `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`를 오버라이드하며, 필요한 멤버 변수들을 리플리케이트되도록 설정하면 시뮬레이션 클라이언트에서 실행되도록 설정할 수 있습니다. 이는 모든 이동 변경 사항을 리플리케이트하는 대신 전체 이동 `AbilityTask`를 시뮬레이션하고자 하는 드문 상황에서 유용합니다. 모든 `RootMotionSource` 관련 `AbilityTask`가 이렇게 동작합니다. `AbilityTask_MoveToLocation.h/.cpp`를 예시로 참고할 수 있습니다.

`AbilityTask`는 생성자에서 `bTickingTask = true;`를 설정하고 `virtual void TickTask(float DeltaTime);`를 오버라이드하면 `틱(Tick)`을 실행할 수 있습니다. 이는 프레임 간에 부드럽게 값을 보간(lerp)해야 할 때 유용합니다. `AbilityTask_MoveToLocation.h/.cpp`에서 예시를 확인할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 AbilityTask 사용

C++(`GDGA_FireGun.cpp`)에서 `AbilityTask`를 생성하고 활성화하려면 다음과 같이 합니다:

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

Blueprint에서는 `AbilityTask`에 대해 생성한 Blueprint 노드를 사용하면 됩니다. `ReadyForActivation()`을 호출할 필요가 없으며, 이는 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`에서 자동으로 호출됩니다. `K2Node_LatentGameplayTaskCall`은 또한 `AbilityTask` 클래스에 `BeginSpawningActor()`와 `FinishSpawningActor()`가 있으면 자동으로 호출합니다(예: `AbilityTask_WaitTargetData` 참조). 다시 한 번 강조하자면, `K2Node_LatentGameplayTaskCall`은 Blueprint에서만 자동으로 호출됩니다. C++에서는 `ReadyForActivation()`, `BeginSpawningActor()`, `FinishSpawningActor()`를 수동으로 호출해야 합니다.

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

Blueprint에서 `AbilityTask`를 수동으로 취소하려면, `AbilityTask` 객체(`Async Task Proxy`)에서 `EndTask()`를 호출하거나 C++에서 동일하게 호출하면 됩니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 Root Motion Source Ability Tasks

GAS에는 `CharacterMovementComponent`에 연결된 `Root Motion Source`를 사용하여 넉백, 복잡한 점프, 당기기, 돌진 등 시간 경과에 따라 `Character`를 움직일 수 있는 `AbilityTask`가 포함되어 있습니다.

> **Note:** `RootMotionSource` `AbilityTask`의 예측은 엔진 버전 4.19 및 4.25 이상에서는 정상 작동합니다. 하지만 엔진 4.20~4.24 버전에서는 예측에 버그가 있어, 멀티플레이어에서 네트워크 수정이 필요하며 싱글 플레이에서는 완벽하게 작동합니다. 4.25의 [prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) 사항을 4.20-4.24 버전의 엔진에 적용하는 것도 가능합니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 Gameplay Cue 정의

`GameplayCue`(`GC`)는 게임플레이와 관련되지 않은 작업들을 실행하는데 사용됩니다. 예를 들어, 사운드 효과, 파티클 효과, 카메라 흔들기 등입니다. `GameplayCue`는 일반적으로 리플리케이트 되어 실행되며(명시적으로 로컬에서만 `실행`, `추가` 또는 `제거`되지 않는 한) 예측됩니다.

`GameplayCue`는 **반드시 `GameplayCue`라는 부모** `GameplayTag`와 이벤트 유형(`Execute, Add, Remove`)을 함께 `ASC`를 통해 `GameplayCueManager`로 보내어 트리거됩니다. `GameplayCueNotify` 객체와 `IGameplayCueInterface`를 구현한 다른 액터들은 `GameplayCue`의 `GameplayTag(GameplayCueTag)`에 따라 이 이벤트를 구독할 수 있습니다.

> **Note:** 다시 한 번 말씀드리자면, `GameplayCue`의 `GameplayTag`는 반드시 `GameplayCue`라는 부모 `GameplayTag`로 시작해야 합니다. 예를 들어, 유효한 `GameplayCue GameplayTag`는 `GameplayCue.A.B.C`와 같이 생성됩니다.

`GameplayCueNotify`에는 `Static`과 `Actor`라는 두 가지 종류가 있습니다. 각각은 서로 다른 이벤트에 응답하고, 다른 유형의 `GameplayEffect`가 이들을 트리거할 수 있습니다. 해당 이벤트를 오버라이드하여 필요한 로직을 구현하면 됩니다.

| `GameplayCue` 클래스                                                                                                                  | 이벤트             | `GameplayEffect` 타입    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | Static `GameplayCueNotify`는 `ClassDefaultObject`에서 작동하며(인스턴스가 없음을 의미) 타격 임팩트와 같은 일회성 효과에 적합합니다.                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor `GameplayCueNotify`가 추가되면 새 인스턴스를 스폰합니다. 인스턴스화되어 있기 때문에 제거될 때까지 계속 동작을 할 수 있습니다. backing `Duration` 또는 `Infinite` GameplayEffect가 제거되거나 수동으로 remove를 호출하면 제거되는 사운드 및 파티클 이펙트를 루핑하는 데 좋습니다. 또한 동시에 추가할 수 있는 개수를 관리할 수 있는 옵션도 제공되므로 동일한 효과를 여러 번 적용할 때 사운드나 파티클이 한 번만 시작되도록 할 수 있습니다. |

`GameplayCueNotify`는 기술적으로 모든 이벤트에 응답할 수 있지만 일반적으로 위 방식을 사용합니다.

> **Note:** `GameplayCueNotify_Actor`를 사용할 때, `Auto Destroy on Remove`를 체크하지 않으면 이후 동일한 `GameplayCueTag`에 대한 `Add` 호출이 작동하지 않을 수 있습니다.

`Full Replication Mode`가 아닌 `ASC` [Replication Mode](#concepts-asc-rm)를 사용하는 경우, 서버 플레이어(리스닝 서버)에서 `Add` 및 `Remove` `GC` 이벤트가 두 번 발생합니다. 한 번은 `GE`를 적용할 때, 다른 한 번은 "최소" `NetMultiCast`에서 클라이언트로 전송할 때 발생합니다. 하지만 `WhileActive` 이벤트는 여전히 한 번만 발동합니다. 모든 이벤트는 클라이언트에서 한 번만 발생합니다.

샘플 프로젝트에는 스턴과 스프린트 효과를 위한 `GameplayCueNotify_Actor`와 FireGun의 발사체 충돌을 위한 `GameplayCueNotify_Static`이 포함되어 있습니다. 이러한 `GC`는 `GE`를 통해 리플리케이트하는 대신 [로컬에서 트리거](#concepts-gc-local)하여 최적화할 수 있습니다. 샘플 프로젝트에서는 초보자에게 적합한 방법으로 이를 보여주기로 했습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Triggering Gameplay Cues

`GameplayEffect`가 성공적으로 적용되었을 때(태그나 면역에 의해 차단되지 않았을 때) `GameplayEffect` 내부에서 트리거되어야 하는 모든 `GameplayCue`의 `GameplayTag`를 채웁니다.

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility`는 `GameplayCue`를 `Execute`, `Add` 또는 `Remove`하는 블루프린트 노드를 제공합니다.

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

C++에서는 `ASC`에서 직접 함수를 호출하거나 `ASC` 서브클래스에서 블루프린트로 노출할 수 있습니다:

```c++
/** GameplayCue는 독립적으로 올 수 있습니다. 이들은 EffectContext를 전달하여 히트 결과 등을 처리할 수 있습니다. */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** persistent GameplayCue를 추가합니다. */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** persistent GameplayCue를 제거합니다. */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** 자체적으로 추가된 GameplayCue를 제거합니다. 즉, GameplayEffect의 일부로 추가되지 않은 경우입니다. */
void RemoveAllGameplayCues();
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 Local Gameplay Cues

`GameplayAbility`와 `ASC`에서 `GameplayCue`를 노출하는 함수는 기본적으로 리플리케이트됩니다. 각 `GameplayCue` 이벤트는 멀티캐스트 RPC입니다. 이로 인해 많은 RPC 호출이 발생할 수 있습니다. GAS는 동일한 `GameplayCue` RPC가 네트워크 업데이트당 최대 두 번만 실행되도록 제한합니다. 이를 피하기 위해 가능한 경우 로컬 `GameplayCue`를 사용합니다. 로컬 `GameplayCue`는 개별 클라이언트에서만 `Execute`, `Add`, 또는 `Remove`가 실행됩니다.

로컬 `GameplayCue`를 사용할 수 있는 시나리오:
* 발사체 충돌
* 근접 충돌 충돌
* 애니메이션 몽타주에서 발동되는 `GameplayCue`

로컬 `GameplayCue` 함수(`ASC` 서브클래스에 추가해야 할 함수들) 입니다:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

만약 `GameplayCue`가 `로컬에서 추가`되었다면, `로컬에서 제거`되어야 합니다. 만약 `리플리케이트를 통해 추가`되었다면, `리플리케이트를 통해 제거`되어야 합니다.


**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue Parameters

`GameplayCue`는 `FGameplayCueParameters` 구조체를 받아 해당 `GameplayCue`에 대한 추가 정보를 전달합니다. 만약 `GameplayCue`가 `GameplayAbility`나 `ASC`의 함수에서 수동으로 트리거된다면, `GameplayCue`에 전달되는 `FGameplayCueParameters` 구조체를 수동으로 채워야 합니다. 만약 `GameplayCue`가 `GameplayEffect`에 의해 트리거된다면, 다음과 같은 변수들이 `FGameplayCueParameters` 구조체에 자동으로 채워집니다:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (만약 `GameplayEffect`에 `GameplayCue` tag container의 Magnitude를 위한 `Attribute`가 선택되어 있고, 그 `Attribute`에 영향을 미치는 해당 `Modifier`가 있는 경우)

`FGameplayCueParameters` 구조체의 `SourceObject` 변수는 `GameplayCue`를 수동으로 트리거할 때 임의의 데이터를 `GameplayCue`로 전달하는 데 유용한 장소일 수 있습니다.

> **Note:** `Instigator`와 같은 일부 변수는 이미 `EffectContext`에 존재할 수도 있습니다. `EffectContext`는 또한 `GameplayCue`를 월드에 어디에 스폰할지에 대한 `FHitResult`를 포함할 수 있습니다. `EffectContext` 를 서브클래싱하는 것은 `GameplayEffect`에 의해 트리거되는 `GameplayCue`에 더 많은 데이터를 전달하는 좋은 방법일 수 있습니다.

자세한 내용은 `FGameplayCueParameters` 구조체를 채우는 [`UAbilitySystemGlobals`](#concepts-asg)의 3가지 함수들을 참조해주세요. 해당 함수들은 가상 함수이므로, 이를 오버라이드하여 더 많은 정보를 자동으로 채울 수 있습니다.

```c++
/** GameplayCue 파라미터 초기화 */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager

기본적으로 `GameplayCueManager`는 게임 디렉토리 전체를 스캔하여 `GameplayCueNotify`를 찾고, 게임 실행 시 이를  메모리에 로드합니다. 이 경로를 변경하려면 `DefaultGame.ini`에서 `GameplayCueManager`가 스캔하는 경로를 설정할 수 있습니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

`GameplayCueManager`가 모든 `GameplayCueNotify`를 스캔하고 찾도록 할 수 있지만, 게임 시작 시 모든 것을 비동기적으로 로드하지 않도록 설정할 수 있습니다. 이렇게 하면 `GameplayCueNotify`와 그와 관련된 모든 사운드와 파티클이 레벨에서 사용되었는지와 관계없이 메모리에 로드되지 않습니다. Paragon과 같은 대형 게임에서는 이로 인해 수백 메가바이트의 불필요한 자산이 메모리에 로드되어 게임 시작 시 Hitching(버벅거림)이나 Freezing(게임 멈춤)을 초래할 수 있습니다.

게임 시작 시 모든 `GameplayCue`를 비동기적으로 로드하는 대신, `GameplayCue`가 게임 내에서 트리거될 때만 비동기적으로 로드하도록 설정할 수 있습니다. 이 방법은 불필요한 메모리 사용을 줄이고 게임이 시작될 때 `GameplayCue`를 비동기적으로 로드할 때 발생할 수 있는 게임의 하드 프리징을 방지하는 데 도움이 됩니다. 그러나 특정 `GameplayCue`가 게임 중 처음 트리거될 때 약간의 지연이 발생할 수 있습니다. 이 지연은 SSD에서는 발생하지 않으며, HDD에서는 테스트되지 않았습니다. UE Editor를 사용할 경우, GameplayCue가 처음 로드될 때 파티클 시스템을 컴파일해야 할 수 있으므로 약간의 Hitching이나 Freezing이 발생할 수 있습니다. 그러나 빌드에서는 이미 파티클 시스템이 컴파일되었으므로 문제가 되지 않습니다.

먼저 `UGameplayCueManager`를 서브클래싱하고, `AbilitySystemGlobals` 클래스가 우리의 `UGameplayCueManager` 서브클래스를 사용하도록 `DefaultGame.ini`에서 설정해야 합니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

그 후, `UGameplayCueManager` 서브클래스에서 `ShouldAsyncLoadRuntimeObjectLibraries()`를 오버라이드합니다.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 GameplayCue가 발동되지 않도록 방지

때때로 `GameplayCue`가 실행되지 않기를 원할 수 있습니다. 예를 들어, 공격을 차단하는 경우 피해 `GameplayEffect`에 연결된 피격 임팩트를 재생하지 않거나 대신 커스텀 피격 임팩트를 재생하고 싶을 수 있습니다. 이 경우 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 내에서 `OutExecutionOutput.MarkGameplayCuesHandledManually()`를 호출하고, 이후 `ASC`의 `Target`이나 `Source`에 수동으로 `GameplayCue` 이벤트를 전송할 수 있습니다.

특정 `ASC`에서 `GameplayCue`가 전혀 실행되지 않게 하려면, `AbilitySystemComponent->bSuppressGameplayCues = true;`로 설정할 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching(일괄 처리)

트리거된 각 `GameplayCue`는 Unreliable NetMulticast RPC입니다. 여러 `GameplayCue`를 동시에 발사하는 상황에서는, 이를 하나의 RPC로 압축하거나 데이터를 덜 전송하여 대역폭을 절약할 수 있는 몇 가지 최적화 방법이 있습니다.

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 수동 RPC

예를 들어, 샷건이 8개의 총알을 발사한다고 가정해 보겠습니다. 그러면 8개의 트레이스와 임팩트 `GameplayCue`가 발생합니다. [GASShooter](https://github.com/tranek/GASShooter)는 이를 하나의 RPC로 합치는 간단한 방법을 사용하여 모든 트레이스 정보를 [`EffectContext`](#concepts-ge-ec)에 [`TargetData`](#concepts-targeting-data)로 저장합니다. 이렇게 하면 RPC 수가 8에서 1로 줄어들지만, 여전히 그 하나의 RPC에서 약 500바이트의 데이터가 전송됩니다. 더 최적화된 방법은 임팩트 위치를 효율적으로 인코딩하는 커스텀 구조체를 사용하여 RPC를 보내거나, 랜덤 시드 번호를 사용해 수신 측에서 임팩트 위치를 재생성하거나 근사화하는 방법입니다. 클라이언트는 이 커스텀 구조체를 언팩하여 [로컬에서 실행되는 `GameplayCue`](#concepts-gc-local)로 변환합니다.

이 방법은 다음과 같이 작동합니다:

1. `FScopedGameplayCueSendContext`를 선언합니다. 이것은 `UGameplayCueManager::FlushPendingCues()`의 호출을 범위 밖으로 나올 때까지 억제하여, 모든 `GameplayCue`가 `FScopedGameplayCueSendContext` 범위 밖으로 나올 때까지 큐에 저장됩니다.
1. `UGameplayCueManager::FlushPendingCues()`를 오버라이드하여, 일부 `GameplayTag`에 따라 배치할 수 있는 `GameplayCue`들을 커스텀 구조체에 병합하고 이를 클라이언트로 RPC로 전송합니다.
1. 클라이언트는 커스텀 구조체를 수신하고 이를 로컬에서 실행되는 `GameplayCue`로 언팩합니다.

이 방법은 `FGameplayCueParameters`에 맞지 않는 특정 `GameplayCue` 파라미터가 필요할 때, 예를 들어 피해 수치, 치명타 표시, 방어구가 파괴된 표시, 치명적인 타격 표시 등과 같은 `EffectContext`를 추가하려고 할 때 유용하게 사용할 수 있습니다.

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 하나의 GE에 여러 개의 GC

`GameplayEffect`에 있는 모든 `GameplayCue`는 이미 하나의 RPC로 전송됩니다. 기본적으로 `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`는 전체 `GameplayEffectSpec`(그러나 `FGameplayEffectSpecForRPC`로 변환된 형태)을 NetMulticast로 전송합니다. 이는 `ASC`의 ``Replication Mode``와 관계없이 신뢰할 수 없는 방식으로 전송됩니다. 이 방식은 `GameplayEffectSpec`에 포함된 데이터에 따라 많은 대역폭을 차지할 수 있습니다. 이를 최적화하려면 cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`을 설정할 수 있습니다. 이 설정은 `GameplayEffectSpec`을 `FGameplayCueParameters` 구조체로 변환하여, `FGameplayEffectSpecForRPC` 대신 이 구조체만 RPC로 전송하게 합니다. 이 방식은 잠재적으로 대역폭을 절약할 수 있지만, `GESpec`이 `FGameplayCueParameters`로 변환되는 과정에서 정보가 일부 손실될 수 있으며, 이는 각 `GameplayCue`가 요구하는 정보에 따라 다를 수 있습니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 Gameplay Cue Events

`GameplayCue`는 특정 `EGameplayCueEvent`에 반응합니다:

| `EGameplayCueEvent` | 설명                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | `GameplayCue`가 활성화(추가)될 때 호출됩니다.                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | `GameplayCue`가 활성 상태일 때 호출되며, 실제로 바로 적용되지 않았더라도 진행 중인 경우에 호출됩니다(진행 중 조인 등). 이는 `Tick`이 아니며, `GameplayCueNotify_Actor`가 추가되거나 `OnActive`일 때 한 번만 호출됩니다. `Tick()`이 필요하면 `GameplayCueNotify_Actor`의 `Tick()`을 사용해야 합니다. 결국 이것은 `AActor`입니다. |
| `Removed`           | `GameplayCue`가 제거될 때 호출됩니다. 이 이벤트에 응답하는 블루프린트 `GameplayCue` 함수는 `OnRemove`입니다.                                                                                                                                                                                                             |
| `Executed`          | `GameplayCue`가 실행될 때 호출됩니다: Instant Effect나 Periodic Tick(). 이 이벤트에 응답하는 블루프린트 `GameplayCue` 함수는 `OnExecute`입니다.                                                                                                                                                                     |
`GameplayCue` 시작 시 발생하는 `GameplayCue`의 모든 이펙트에 `OnActive`를 사용하되, 늦게 참여하는 플레이어가 놓쳐도 괜찮은 경우 사용합니다. `WhileActive`는 `GameplayCue`에서 지속적으로 발생하는 효과에 사용하며, 늦게 합류한 플레이어도 볼 수 있도록 해야 합니다. 예를 들어 MOBA에서 타워 구조물이 폭발하는 GameplayCue가 있을 때, 초기 폭발 파티클 시스템과 폭발 사운드는 `OnActive`에 넣고 폭발 후 지속적으로 발생하는 불꽃 파티클이나 사운드는 `WhileActive`에 넣을 수 있습니다. 이 시나리오에서는 뒤늦게 합류한 플레이어가 초기 폭발을 `OnActive`에서 재생하는 것은 의미가 없지만, 폭발이 발생한 후 지면에 지속적이고 반복되는 불꽃 이펙트를 `WhileActive`에서 볼 수 있게 하려는 것입니다. `OnRemove`는 `OnActive`와 `WhileActive`에 추가된 모든 항목을 정리해야 합니다. 

* `WhileActive`는 액터가 `GameplayCueNotify_Actor`의 연관성 범위에 들어올 때마다 호출됩니다. 
* `OnRemove`는 액터가 `GameplayCueNotify_Actor`의 연관성 범위를 벗어날 때마다 호출됩니다.

**[⬆ 위로 가기](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 Gameplay Cue Reliability(신뢰성)

`GameplayCue`는 일반적으로 비신뢰성을 가지므로, 직접적으로 게임 플레이에 영향을 미치는 요소에는 적합하지 않습니다.

**실행된** `GameplayCue`: 비신뢰성 멀티캐스트(Unreliable Multicast)를 통해 적용되며 항상 신뢰성이 보장되지 않습니다.

**`GameplayEffect`에서 적용되는 `GameplayCue`**:
* Autonomous Proxy는 `OnActive`, `WhileActive`, `OnRemove` 이벤트를 신뢰성 있게 수신합니다.  `FActiveGameplayEffectsContainer::NetDeltaSerialize()`는 `OnActive`와 `WhileActive`를 호출하기 위해 `UAbilitySystemComponent::HandleDeferredGameplayCues()`를 실행합니다. `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()`는 `OnRemoved`를 호출합니다.
* Simulated Proxy는 `WhileActive`와 `OnRemove`를 신뢰성 있게 수신합니다. `UAbilitySystemComponent::MinimalReplicationGameplayCues`의 리플리케이션은 `WhileActive`와 `OnRemove`를 호출합니다. `OnActive` 이벤트는 비신뢰성 멀티캐스트에 의해 호출됩니다.

**`GameplayEffect`없이 적용되는 `GameplayCue`:**
* Autonomous Proxy는 `OnRemove`를 신뢰성 있게 수신합니다. `OnActive`와 `WhileActive` 이벤트는 비신뢰성 멀티캐스트로 호출됩니다.
* Simulated Proxy는 `WhileActive`와 `OnRemove`를 신뢰성 있게 수신합니다. `UAbilitySystemComponent::MinimalReplicationGameplayCues`의 리플리케이션은 `WhileActive`와 `OnRemove`를 호출합니다. `OnActive` 이벤트는 비신뢰성 멀티캐스트에 의해 호출됩니다.

`GameplayCue`에서 신뢰성이 필요한 경우, 해당 `GameplayCue`를 `GameplayEffect`를 통해 적용하고, `WhileActive`에서 FX를 추가하며 `OnRemove`에서 FX를 제거하도록 설정하세요.

**[⬆ 위로 가기](#table-of-contents)**