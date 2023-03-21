데이터 생성기
===============

데이터 생성기는 코드를 사용해 모드에 필요한 에셋과 데이터를 만드는 방법입니다.  이를 통해 에셋과 데이터 파일들의 내용을 코드에서 정의하고 문법이나 규격에 상관없이 편하게 생성할 수 있도록 해줍니다.

데이터 생성 시스템은 메인 클래스 `net.minecraft.data.Main` 에서 불러옵니다. 게임 실행 명령 인수를 바꿔 어떤 모드의 데이터를 수집할지, 어떤 파일들을 고려할지 등을 설정하실 수 있습니다. 데이터 생성을 관리하는 클래스는 `net.minecraft.data.DataGenerator` 입니다.

MDK 의 `build.gradle` 은 기본적으로 `runData` 를 추가하여 데이터 생성기를 실행할 수 있도록 해줍니다.

생성 모드
---------------

데이터 생성기는 4가지 종류의 데이터를 생성할 수 있습니다, 어떤 종류의 데이터를 생성할 지는 게임 실행 명령 인수를 통해 설정할 수 있고, 어떤 데이터를 생성하려고 하는지는 `GatherDataEvent#include***` 메서드로 확인하실 수 있습니다.

  * __클라이언트 에셋__
  	 * `asset` 에 들어가는 클라이언트 전용 파일들을 생성합니다: 블록/아이템 모델, BlockState 정의 JSON 파일들, 언어 파일 등.
     * __`--client`__, `includeClient()`
  * __서버 데이터__
  	 * `data` 안에 들어가는 서버 전용 데이터를 생성합니다: 조합법, 도전과제, 태그 등.
     * __`--server`__, `includeServer()`
  * __개발 도구__
  	 * 개발 도구들을 실행합니다: SNBT 를 NBT 로 바꾸기, 그 반대로 바꾸기 등.
     * __`--dev`__, `includeDev()`
  * __보고서 작성__
  	 * 등록된 모든 블록, 아이템, 명령어등을 덤프합니다.
     * __`--reports`__, `includeReports()`

데이터 제공자
--------------

데이터 제공자는 어떤 데이터가 생성되고 제공될 지를 정의하는 클래스 입니다. 모든 데이터 제공자들은 `IDataProvider` 를 구현해야 합니다.
마인크래프트는 대부분의 에셋과 데이터에 사용하기 위한 데이터 제공자를 추상화 클래스로 제공하고 있습니다, 그렇기에 모드 개발자들은 정해진 메서드 몇개만 오버라이드 하면 됩니다.

`GatherDataEvent` 는 데이터 생성기가 초기화 될 때 모드 버스에 방송됩니다, `DataGenerator` 인스턴스를 이 이벤트에서 접근하실 수 있습니다. `DataGenerator#addProvider` 를 사용해 데이터 제공자를 만들고 등록하세요.

### 클라이언트 에셋
  * `net.minecraftforge.common.data.LanguageProvider` - 언어 문자열 생성: `#addTranslations` 를 오버라이드 하세요
  * `ModelProvider<?>` - 모든 모델 제공자들의 기반이 되는 클래스
    * _이 클래스들은 `net.minecraftforge.client.model.generators` 패캐지에 있습니다_
    * `ItemModelProvider` - 아이템 모델 생성; `#registerModels` 를 오버라이드 하세요
    * `BlockStateProvider` - BlockState 정의, 블록 모델, 아이템 모델 생성: `#registerStatesAndModels` 를 오버라이드 하세요
    * `BlockModelProvider` - 블록 모델 생성; `#registerModels` 를 오버라이드 하세요

### 서버 데이터
  * `net.minecraftforge.common.data.GlobalLootModifierProvider` - [글로벌 노획물 수정자][glm] 생성; `#start` 를 오버라이드 하세요
  * _이 클래스들은 `net.minecraft.data` 패캐지에 있습니다_
  * `LootTableProvider` - 노획물 테이블 생성; `#getTables` 를 오버라이드 하세요
  * `RecipeProvider` - 조합법 및 해금되는 도전과제 생성: `#buildShapelessRecipes` 를 오버라이드 하세요
  * `TagsProvider` - 태그 데이터 생성: `#addTags` 를 오버라이드 하세요
  * `AdvancementProvider` - 도전과제 데이터 생성,`#registerAdvancements` 를 오버라이드 하세요

[glm]: ../items/globallootmodifiers.md