# 디버그 프로파일러

마인크래프트는 디버그 프로파일러를 사용하여 특정 코드가 얼마나 시간을 소요하는지 측정할 수 있도록 합니다. `TickEvent` 나 타일 엔티티 tick 처리 등에서 이를 사용하는 것이 유용할 수 있는데, 모드 개발자 또는 서버 어드민이 랙의 원인을 쉽게 파악할 수 있도록 해줍니다.

## 디버그 프로파일러 사용하기

디버그 프로파일러는 쓰기 간단합니다. 명령어 두번이면 되는데, `/debug start` 로 프로파일링을 시작하고 `/debug stop` 으로 끝냅니다.
기억해 두셔야 할 점은 데이터 수집을 오랫동안 할 수록 더욱 정확한 결과를 얻으실 수 있다는 것입니다.
최소한 1분 정도는 데이터를 수집하는 것이 권장됩니다.

!!! note
    당연하게도, 프로파일링은 실제로 존재하는 코드에만 사용하실 수 있습니다. 엔티티나 타일 엔티티에 프로파일러를 사용하실려면 월드에 존재하여야만 합니다.

프로파일러를 끝낸 이후 게임을 실행한 디렉토리 아래 `debug` 라는 새로운 디렉토리가 생성되었을 것입니다.
파일의 이름은 현재 시간으로 다음과 같이 포매팅 됩니다: `profile-results-yyyy-mm-dd_hh.mi.ss.txt`

## 프로파일러 결과 보기

파일 맨 위에는 게임 버전과 프로파일링이 진행된 시간이 밀리세컨드로 표시되어 있습니다.

그 아래는 하위와 비슷한 형식으로 프로파일링 결과가 표시되어 있습니다.
```
[00] levels - 96.70%/96.70%
[01] |   World Name - 99.76%/96.47%
[02] |   |   tick - 99.31%/95.81%
[03] |   |   |   entities - 47.72%/45.72%
[04] |   |   |   |   regular - 98.32%/44.95%
[04] |   |   |   |   blockEntities - 0.90%/0.41%
[05] |   |   |   |   |   unspecified - 64.26%/0.26%
[05] |   |   |   |   |   minecraft:furnace - 33.35%/0.14%
[05] |   |   |   |   |   minecraft:chest - 2.39%/0.01%
```
각 부분이 무슨 뜻을 가지는지 간략하게 설명하면 다음과 같습니다:

| [02]   | tick   | 99.31%                                                                                    | 95.81%              |
|:-------|:-------|:------------------------------------------------------------------------------------------|:--------------------|
| 부분의 깊이 | 부분의 이름 | 부모 기준으로 부분이 소요한 시간의 백분율. 깊이 0은 틱 전체에서 소요한 시간의 백분율. 깊이 1은 그 부모(깊이 0)에서 소요된 시간중 소요한 시간의 백분율 | 틱 전체에서 소요한 시간의 백분율. |

## 직접 짠 코드 프로파일링 하기

디버그 프로파일러는 `Entity` 와 `TileEntity` 프로파일링을 자동으로 해주지만 그것 이외에 다른 것에 프로파일러를 쓰고 싶다면 다음과 같이 부분을 직접 만드셔야 합니다:
```java
  IProfiler#push(부분이름 : String);
  //프로파일러를 사용할 코드
  IProfiler#pop();
```
`IProfiler` 인스턴스는 `World`, `MinecraftServer` 또는 `Minecraft` 인스턴스에서 접근하실 수 있습니다.
이제 사용하신 부분 이름을 프로파일링 결과에서 찾아보시면 됩니다.