---
layout: post
title: 피해야할 10가지 java coding antipatterns
subtitle: 최악의 사례 10~6 
tags: [java]
comments: true
---

## 여러분들은 이 최악의 관례들을 피해야합니다. 그리고 기존코드를 유지보수하거나 리팩토링할때 이것들을 제거해야합니다.

경험을 통해, 모든 사람들은 좋고 나쁜 사례에 대한 아이디어를 얻으며, 그것은 코딩과 코드리뷰 모두에 적용됩니다. 동료 자바 챔피언 Trisha Gee는 그녀의 기사 "5가지 코드리뷰 안티패턴"에서 코드리뷰과정의 몇가지 최악의 관행들을 지적했습니다. 저도 코딩 과정 자체에 대한 10가지 안티패턴을 지적하고 싶습니다. 절반은 이 기사에 있고, 더 최악의 것들은 곧 자바매거진에 실릴 다음 기사에 있습니다.  
[Ten Java coding antipatterns to avoid: Worst practices #5 through #1](https://blogs.oracle.com/javamagazine/post/java-worst-practices-antipatterns-part-two)

분명히 말씀드리면, 이런 최악의 사례들은 피해야하고 기존 코드를 관리하거나 리팩토링할때 이를 제거해야합니다. 물론 코드리뷰중에 이런 문제가 발견되면 해결해야합니다.

## 최악의 사례 #10 : 지저분한 Import
클래스 맨 위에 있는 import된 클래스와 메소드들의 목록은 사용중인 API에 대한 참조로 사용됩니다. *로 끝나는 import들은 어떤 특정한 정보를 전달해주지도 않으며, 심지어는 사용하지않는 import들이 mislead됩니다. 준랜덤순서로 import하면 읽는데 더 오래걸리기 때문에 유지보수가 쉽지 않습니다.

더 나은 방법: IDE에서 imports를 관리하도록 합니다. Eclipse IDE는 이를 매우 잘 지원합니다. 그것의 organize imports 기능은 클릭한번으로, 사용하지않는 import들을 제거하고, 다른 누락된 import들을 추가해주고, 목록을 일관된 순서로 정렬하며, 자바클래스, javax, 써드파티 클래스와 static imports를 차례로 수행합니다. 여러분들은 Intellij IDEA에서도 이 모든것들을 얻을수 있겠지만 그러려면 서너가지 설정을 조정해야합니다.  
[Defeating IDEA's Dreary Defaults](https://darwinsys.com/blog/2022-idea-defaults/)

고백: 저는 이름모를 대형 앱 프로젝트의 젊고 어리석인 기술리더였을때, eclipse 세팅에서 사용하지않는 imports에 대한 messaging level을 warning에서 error로 설정하고 저장소에 커밋한적이 있습니다. 물론 이 최악의 사례는 오직 개발팀에 훈계와 독촉이 통하지 않을 때 한것입니다. 이것은 프로젝트 전체에 걸쳐 import들을 정리하기위한 제 계획의 일부였습니다. 이 설정을 변경하는것은 인기가 없었지만, Ctrl+Shift+O를 사용하여 수정하는것이 얼마나 쉽고, 이 변경으로 해당 프로젝트의 긴 import 목록이 훨씬 더 쉽게 읽힌다는것을 알게된 소수의 반대 개발자들이 나타났습니다.

## 최악의 사례 #9 : 일관되지않은 들여쓰기
확실히 파이썬은 들여쓰기 챔피언 언어로, 메서드나 흐름 제어의 내용을 나타내기 위해 괄호나 키워드 대신 들여쓰기를 사용합니다. 따라서 파이썬은 코드가 잘못 들여쓰기되면 컴파일 되지 않습니다!

다행히 java(다른 c계열 언어들처럼)는 block구조에 대괄호를 사용하고 여백을 무시합니다. 일관된 들여쓰기는 여전히 중요합니다. 들여쓰기가 컴파일러에 의해 요구되지는 않지만, 사람인 개발자들한테는 필요합니다.  
다음의 코드를 보십시오.  
~~~java
if (condition)
    statement1;
    statement2;
statement3;
~~~
빠르게 읽어보면, statement1과 2가 if에 의해 제어되는것처럼 보입니다. 하지만 statement2는 그렇지않습니다. 왜냐면 이건 파이썬이 아니라 자바이기 때문입니다.

혹은 다음 코드를 보십시오.
~~~java
statement1;
   statement2;
 statement3;
~~~
도대체 개발자가 무슨 생각을 했었던걸까요? 코드가 바람부는날 폭포에서 뿜어져나오는 무언가로 보입니다. statement들은 제어흐름이 동일한 레벨을 가지고 있으므로, 모두 같은 열(Column)에서 시작해야합니다. 다시말하지만, 최신 IDE는 "Fix Indentation"과 같은 기능을 통해 이 damage를 즉시 복구할 수 있습니다.

Ctrl+A 또는 Cmd+A로 전체 파일을 선택하거나 마우스를 사용하여 한 method를 선택합니다. 그런 다음 Editor Code 메뉴에서 들여쓰기 복구를 선택합니다. 문제 해결!

## 최악의 사례 #8 : 연결 없는 JAR files
내가 만난 최악의 경우는 다음과 같은 이름을 가진 파일의 폴더가 있는 프로젝트 였습니다.
~~~
util.jar
system.jar
financial.jar
report.jar
~~~
그 파일들은 약 10년치의 정보들을 가지고 있었습니다. 4개의 프로젝트 각각은 그 기간동안 유지보수자에 의해 업데이트 되었지만, JAR 파일이 어떤 버전의 라이브러리에 의존하는지 기록은 없었습니다.  
JAR 파일 중 일부는 수년 동안 여러 가지 보안 문제를 일으켰던 타사 API의 것이었지만, 문제의 영향을 받는 버전을 사용하고 있는지 몰랐기 때문에 팀의 개발자 중 누구도 이후버전의 JAR 파일로 바꿀만큼의 고려는 하지 않는 것 같았습니다.

제가 몇 년 전에 이와 같은 프로젝트를 만들었을 수도 있다는 것은 인정합니다. 하지만 저는 그것들을 피하겠다고 다짐했습니다.

오늘날 내 모든 프로젝트는 maven 또는 gradle에 의해 관리되며, 각 프로젝트는 각 종속성의 그룹dependency’s group, artifact(JAR 이름) 및 버전넘버를 지정하고 일치하는 JAR 파일을 가져옵니다. 이 파일에는 아티팩트 이름과 버전 번호가 파일 이름에 포함됩니다. 예를 들어 프로젝트의 Maven 구성 파일(pom.xml)에 다음과 같은 내용이 있을 수 있습니다.
~~~xml
<dependency>
    <groupId>com.darwinsys</groupId>
    <artifactId>darwinsys-api</artifactId>
    <version>1.7.5</version>
</dependency>
~~~
pom.xml의 이 코드는 메이븐이 darwinsys-api-1.7.5.jar 파일을 다운로드하여 홈 디렉토리(~.m2/repository/com/darwinsys/darwinsys-api/1.7.5)의 신중하게 구성된 트리에 저장하도록 지시합니다. 이러한 방식으로 두 개 이상의 프로젝트에서 동일한 JAR 파일이 필요할 경우 JAR은 한 번만 다운로드됩니다.  
여기 제 시스템 중 하나에 있는 메이븐 로컬 저장소를 까다롭게 살펴봅시다.
~~~xml
$ ls ~/.m2/repository
aopalliance biz bouncycastle cglib com dev eclipse edu info io
jakarta javax jaxen jline log4j ...
$ ls ~/.m2/repository/com/darwinsys/darwinsys-api
1.5.14
1.5.15
1.7.5
maven-metadata-central.xml
maven-metadata-central.xml.sha1
resolver-status.properties
$ ls ~/.m2/repository/com/darwinsys/darwinsys-api/1.7.5
_remote.repositories
darwinsys-api-1.7.5.jar
darwinsys-api-1.7.5.jar.sha1
darwinsys-api-1.7.5.pom
darwinsys-api-1.7.5.pom.sha1
$
~~~
pom.xml 파일을 보면 해당 프로젝트에서 어떤 버전의 API가 사용되는지 분명할 뿐만 아니라(적어도 기본값이 무엇인지 알고 있고 pom.xml 파일에 나열된 다른 리포지토리가 없는 경우) JAR 파일이 중앙 메이븐 저장소인 메이븐 센트럴에서 가져온 것임을 알 수 있습니다.  
게다가 JAR 파일 자체에는 파일 이름에 버전넘버가 포함되어 있습니다.  
Maven은 Secure Hash Algorithm(sha) 파일을 사용하여 JAR 파일이 변조되지 않았는지 확인합니다. 빌드 도구를 디버그 모드로 실행하면 클래스 경로에 있는 각 JAR 파일의 전체 경로를 포함하는 매우 상세한 출력이 표시됩니다. 또한 Maven은 모든 하위 및 하위 종속성을 트리 형식으로 표시하는 mvn dependency:tree와 같은 기능을 가지고 있습니다.  
JAR 의존성을 통제하는 것은 소프트웨어 개발을 하나의 분야로 만드는 것의 일부입니다! 그렇게 하십시오!

## 최악의 사례 #7 : 의미없는 이름
지금이 Ian의 코딩 제 1규칙을 인용하기 좋은 때 입니다.  
**이름을 만들때를 제외하고 이름을 두글자 이상 입력해서는 안된다.**

요즘 대부분의 개발자들은 full-featured IDE를 사용하고 있고, 주요한 IDE들은 정말 좋은 코드 완성 기능을 가지고 있어서 굳이 긴 이름을 입력할 필요가 없습니다.  
그렇지만 메서드, 필드, 클래스, 변수 및 기타 요소에 의미있는 이름을 붙이는 것을 피할 이유는 없습니다.  
제 생각으론 i, j, k 같은 변수이름은 오직 old style의 for loop를 사용하여 배열을 색인화 하거나 카운트 할때에만 허용됩니다. 또한 s같은 로컬에서 사용되는 문자열의 이름, 몇줄 길이의 메서드 헤더에 있는 이름 또는 짧고 자체적인(self-contained) 람다를 작성할 경우 등이 허용됩니다.  
다른 모든 항목에 대해서는 유용한 이름을 선택하세요.

이것은 특히 var키워드를 사용하여 type선언을 하지 않아도 되는 경우에 중요합니다. 왜그럴까요? 변수이름은 사용자가 사용할 변수의미에 대한 독자의 유일한 단서일 수 있기 때문입니다.  
다음 예를 들어보겠습니다.
~~~java
for (int i = 0; i < functionData.length; i++) {
        functionData[i] = someFunction(i);
}

customerNames.forEach(s->s.substring(1)); // "s" OK here

int bodyFontSize = 11;
~~~
변수만 말하는것이 아닙니다. 메서드 이름도 의미있어야 합니다. JUnit테스트를 작성할 때 test1(), test2()와 같은 이름은 의미가 없을뿐만아니라 있지도않은 순서를 의미해서 오해할수있습니다.  
JUnit은 사용자가 작성한 순서대로 메서드를 실행하지않습니다. 실제로 메서드는 "정렬되지도 않고 특정순서도 아닌" 멤버들을 반환하도록 문서화된 reflection API에 의해 주어진 순서대로 실행됩니다.  
안티패턴의 예:
~~~java
@Test
    public void test1() {   // Bad
        // test here...
    }
~~~

더 좋은 방법:
~~~java
@Test
    public void testPositiveResultsCorrect() { // Better
        // test here...
    }
~~~
기억하세요: 여러분은 이 코드를 작성한 지 몇 달 또는 몇 년이 지난 후에 이 코드를 읽어야 할 사람들 중 한 명입니다. 그러니 여러분 자신에게 친절하세요!

## 최악의 사례 #6 : 펑크난 타이어의 재창조(Reinventing the flat tire)
이 안티패턴의 제목은 제가 수년 전에 연구한 논문에서 따온 것입니다. "바퀴의 재발명"("Reinventing the whee")은 이미 존재하는 무언가를 디자인하고 창조하기위한 흔한 영어 관용구입니다.  
펑크난 타이어의 재발명은 개발자가 이미 표준 라이브러리나 공통 라이브러리 존재하여 쉽게 사용할 수 있는 코드를 새로 작성했을 뿐만 아니라 그 새로운 코드가 public API보다 더 나쁜 작업을 수행했다는것을 의미합니다.  
여기 안티패턴의 예시입니다.
~~~java
String[] candidates = getStrings();
String searchingFor = "The Lost Boys";
int found = -1;
for (int i = 0; i < candidates.length; i++) { // flat tire
    if (candidates[i].equals(searchingFor)) {
        found = i;
    }
}
~~~
그리고 여기 더 좋은 방법이 있습니다.
~~~java
Arrays.sort(candidates); // start of "better" approach
found = Arrays.binarySearch(candidates, searchingFor);
~~~
이진검색에서는 입력을 정렬해야하기때문에 두번째 접근 방식이 더 느리게 실행될수있다고 생각할 수도 있습니다. 그렇지만 안티패턴의 개발자는 일치항목을 찾을때 루프를 벗어나는것을 잊어서 효율성이 형편없다는것을 주목하세요.  
public API를 재창조하는것은 새로운 것이 아니며 종종 지식이 불완전하다는것을 표시하는것이기도 합니다. 물론 언어들이 자바처럼 방대한 표준 라이브러리를 가지고있을 때 그 오류를 충분히 쉽게 범할 수 있습니다.  
다음은 여러분이 알수도 모를수도 있는 API를 재창조하는 예입니다. 이것은 Java 1.7 이후 platform에 있었습니다.
~~~java
var x = getValue();     // legacy way
if (x == null) {
    x = getSomeDefaultValue();
}
System.out.println(x);
~~~

이것은 더 좋고 짧은 방법입니다.
~~~java
var y = Objects.requireNonNullElse(getValue(), getSomeDefaultValue());
System.out.println(y);
~~~
예제에서 일부 일반적 작업에 대한 코딩을 줄이는데 도움이 되는 다양한 오버로드들이 있는 표준 Objects.requireNonNullElse() 라이브러리 루틴을 사용할 수 있었습니다.  
추가로 null과 not null 인수를 핸들링하는것에 대해서는, 아래 링크의 포스팅을 읽어보고 퀴즈를 풀어보길 바랍니다.  
[12 recipes for using the Optional class as it’s meant to be used](https://blogs.oracle.com/javamagazine/post/12-recipes-for-using-the-optional-class-as-its-meant-to-be-used)  
[Quiz yourself: The Optional class and null values in Java](https://blogs.oracle.com/javamagazine/post/quiz-yourself-the-optional-class-and-null-values-in-java)

### 참고
[javaMagazine](https://blogs.oracle.com/javamagazine/post/java-worst-practices-antipatterns-part-two)