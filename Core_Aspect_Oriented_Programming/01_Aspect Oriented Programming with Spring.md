<p>AOP(관점 지향 프로그래밍)는 프로그램 구조에 대한 또 다른 사고 방식을 제공하여 객체 지향 프로그래밍(OOP)을 보완(complement)합니다. OOP의 모듈화 핵심 단위는 클래스인 반면, AOP의 모듈화 단위는 측면(aspect)입니다. Aspect를 사용하면 여러 타입과 객체에 걸친 문제를 모듈화(예: 트랜잭션 관리)할 수 있습니다. (이러한 우려는 AOP 문헌에서 종종 "크로스커팅(crosscutting)" 우려로 불립니다.)</p>
<p>Spring의 주요 구성 요소 중 하나는 AOP 프레임워크입니다. Spring IoC 컨테이너는 AOP에 의존하지 않지만(원하지 않는 경우 AOP를 사용할 필요가 없음을 의미), AOP는 Spring IoC를 보완하여 매우 유능한 미들웨어 솔루션을 제공합니다.</p>
<blockquote>
<h3 id="aspectj-포인트컷을-사용한-spring-aop">AspectJ 포인트컷을 사용한 Spring AOP</h3>
<p>Spring은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/schema.html">스키마 기반 접근 방식</a>이나 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj.html">@AspectJ 어노테이션 스타일</a>을 사용하여 사용자 정의 측면을 작성하는 간단하고 강력한 방법을 제공합니다. 이 두 스타일 모두 위빙(weaving)을 위해 Spring AOP를 계속 사용하면서 완전한 타입의 advice와 AspectJ pointcut 언어 사용을 제공합니다.</p>
<p>이 장에서는 스키마 및 <code>@AspectJ</code> 기반 AOP 지원에 대해 설명합니다. 하위 수준(lower-level) AOP 지원은 <a href="https://docs.spring.io/spring-framework/reference/core/aop-api.html">다음 장에서</a> 설명됩니다.</p>
</blockquote>
<p>AOP는 Spring Framework에서 다음을 위해 사용됩니다.</p>
<ul>
<li>
<p>선언적 엔터프라이즈 서비스를 제공합니다. 그러한 서비스 중 가장 중요한 것은 <a href="https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative.html">선언적 트랜잭션 관리</a>입니다.</p>
</li>
<li>
<p>사용자가 AOP로 OOP 사용을 보완하여 사용자 정의 측면(aspect)을 구현할 수 있도록 합니다.</p>
</li>
</ul>
<blockquote>
<p><strong>[Note]</strong><br>
generic 선언적 서비스나 풀링(pooling)과 같은 사전 패키지된(pre-packaged) 선언적 미들웨어 서비스에만 관심이 있는 경우, Spring AOP로 직접 작업할 필요가 없으며 이 장의 대부분을 건너뛸 수 있습니다.</p>
</blockquote>