<p>Spring AOP는 순수 Java로 구현됩니다. 특별한 컴파일 과정이 필요하지 않습니다. Spring AOP는 클래스 로더 계층 구조를 제어할 필요가 없으므로 서블릿 컨테이너 또는 애플리케이션 서버에서 사용하기에 적합합니다.</p>
<p>Spring AOP는 현재 메서드 실행 조인 포인트(Spring Bean에서 메서드 실행을 어드바이스)만 지원합니다. 핵심 Spring AOP API를 손상시키지 않고 필드 가로채기(interception)에 대한 지원을 추가할 수 있지만 필드 가로채기(interception)는 구현되지 않습니다. 필드 액세스를 어드바이스하고 조인 포인트를 업데이트해야 하는 경우 AspectJ와 같은 언어를 고려하세요.</p>
<p>AOP에 대한 Spring AOP의 접근 방식은 대부분의 다른 AOP 프레임워크와 다릅니다. 목표는 가장 완전한 AOP 구현을 제공하는 것이 아닙니다(Spring AOP는 상당히 가능하지만). 오히려 AOP 구현과 Spring IoC 간의 긴밀한 통합(integration)을 제공하여 엔터프라이즈 애플리케이션의 일반적인 문제를 해결하는 것이 목표입니다.</p>
<p>따라서 예를 들어 Spring Framework의 AOP 기능은 일반적으로 Spring IoC 컨테이너와 함께 사용됩니다. Aspect는 일반 Bean 정의 구문(syntax)을 사용하여 구성됩니다(이는 강력한 "자동 프록시" 기능을 허용하지만). 이는 다른 AOP 구현과의 중요한 차이점입니다. 매우 세분화된 객체(일반적으로 도메인 객체)에 대한 조언과 같은 일부 작업은 Spring AOP를 사용하여 쉽고 효율적으로 수행할 수 없습니다. 이러한 경우에는 AspectJ가 최선의 선택입니다. 그러나 우리의 경험에 따르면 Spring AOP는 AOP를 적용할 수 있는 엔터프라이즈 Java 애플리케이션의 대부분의 문제에 대한 탁월한 솔루션을 제공합니다.</p>
<p>Spring AOP는 포괄적인 AOP 솔루션을 제공하기 위해 AspectJ와 경쟁하려고 노력하지 않습니다. 우리는 Spring AOP와 같은 프록시 기반 프레임워크와 AspectJ와 같은 본격적인(full-blown) 프레임워크가 모두 가치 있고 경쟁이 아닌 보완적이라고 믿습니다. Spring은 Spring AOP 및 IoC를 AspectJ와 완벽하게 통합하여 일관된 Spring 기반 애플리케이션 아키텍처 내에서 AOP의 모든 사용을 가능하게 합니다. 이 통합은 Spring AOP API 또는 AOP Alliance API에 영향을 미치지 않습니다. Spring AOP는 이전 버전과 호환됩니다. Spring AOP API에 대한 설명은 <a href="https://docs.spring.io/spring-framework/reference/core/aop-api.html">다음 장</a>을 참조하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Framework의 핵심 원칙 중 하나는 비침습성(non-invasiveness)입니다. 이는 프레임워크 특정 클래스와 인터페이스를 비즈니스 또는 도메인 모델에 도입(introduce)하도록 강요해서는 안 된다는 생각입니다. 그러나 어떤 곳에서는 Spring Framework가 Spring Framework 관련 종속성을 코드베이스에 도입(introduce)할 수 있는 옵션을 제공합니다. 이러한 옵션을 제공하는 이유는 특정 시나리오에서 그러한 방식으로 일부 특정 기능을 읽거나 코딩하는 것이 더 쉬울 수 있기 때문입니다. 그러나 Spring Framework는 (거의) 항상 선택 사항을 제공합니다. 특정 사용 사례나 시나리오에 가장 적합한 옵션이 무엇인지에 대해 정보에 입각한 결정을 내릴 수 있는 자유가 있습니다.</p>
<p>이 장과 관련된 선택 중 하나는 어떤 AOP 프레임워크(및 어떤 AOP 스타일)를 선택할 것인지입니다. AspectJ, Spring AOP 또는 둘 다를 선택할 수 있습니다. 또한 @AspectJ 어노테이션 스타일 접근 방식이나 Spring XML 구성(configuration) 스타일 접근 방식 중 하나를 선택할 수도 있습니다. 이 장에서 @AspectJ 스타일 접근 방식을 먼저 소개하기로 결정했다는 사실이 Spring 팀이 Spring XML 구성 스타일보다 @AspectJ 어노테이션 스타일 접근 방식을 선호한다는 의미로 받아들여져서는 안 됩니다.</p>
<p>각 스타일의 장점과 단점에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/choosing.html">사용할 AOP 선언 스타일 선택</a>을 참조하세요.</p>
</blockquote>