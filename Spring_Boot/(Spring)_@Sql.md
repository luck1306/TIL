created 230912 06:40~07:18

	 test class나 test method에서 통합 테스트 도중 주어진 DB에 반대하여 실행되는 SQL.scripts 속성과 SQL.statements을 설정하기 위해 사용된다.

- method level의 선언은 기본적으로 class level의 선언을 재정의 하지만 이 행위(재정의)는 @SqlMergeMode을 거쳐서 설정될 수도 있다.
- Script 실행은 SqlScriptsTestExcutionListener에 의해 실행되며 기본적으로 활성화됩니다.
- 설정 옵션은 다음 소개할 annotation으로 제공됩니다. @SqlConfig는 ScriptUtils와 ResourceDatabasePopulator가 지원하는 것과 다름없지만 xml namespace element)\<jdbc:initialize-database/\>)가 제공하는 집합입니다.