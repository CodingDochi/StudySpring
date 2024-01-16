<p><a href="https://docs.spring.io/spring-framework/reference/">스프링 프레임워크 문서</a></p>
<p><a href="https://docs.spring.io/spring-framework/reference/overview.html">https://docs.spring.io/spring-framework/reference/overview.html</a></p>
<h2 id="what-we-mean-by-spring">What We Mean by "Spring"</h2>
<p>이 참조 문서는 기초인 Spring Framework 자체에 중점을 둡니다. Spring Framework는 모듈로 나뉩니다. 애플리케이션은 필요한 모듈을 선택할 수 있습니다. 중심에는 구성 모델 및 종속성 주입 메커니즘을 포함하는 코어 컨테이너의 모듈이 있습니다.</p>
<p>그 외에도 Spring Framework는 메시징, 트랜잭션 데이터 및 지속성, 웹을 비롯한 다양한 애플리케이션 아키텍처에 대한 기본 지원을 제공합니다. 또한 Servlet 기반 Spring MVC 웹 프레임워크와 동시에 Spring WebFlux 반응형 웹 프레임워크도 포함합니다. </p>
<p>모듈에 대한 참고 사항: Spring의 프레임워크 jar는 JDK 9의 모듈 경로("Jigsaw")에 대한 배포를 허용합니다. Jigsaw 지원 애플리케이션에서 사용하기 위해 Spring Framework 5 jar에는 독립적인 안정적인 언어 수준 모듈 이름("spring.core", "spring.context" 등)을 정의하는 "Automatic-Module-Name" 매니페스트 항목이 있습니다. jar 아티팩트 이름(jar은 "." 대신 "-"를 사용하는 동일한 이름 지정 패턴을 따릅니다(예: "spring-core" 및 "spring-context"). 물론 Spring의 프레임워크 jar는 JDK 8과 9 모두에서 classpath에서 잘 작동합니다.</p>
<h2 id="history-of-spring-and-the-spring-framework">History of Spring and the Spring Framework</h2>
<p>Spring Programming model은 전통적 Java EE에서 엄선된 개별 사양과 통합됩니다. </p>
<ul>
<li>서블릿 API( JSR 340 )</li>
<li>WebSocket API( JSR 356 )</li>
<li>동시성 유틸리티( JSR 236 )</li>
<li>JSON 바인딩 API( JSR 367 )</li>
<li>빈(Bean) 유효성 검사( JSR 303 )</li>
<li>JPA( JSR 338 )</li>
<li>JMS( JSR 914 )</li>
<li>필요한 경우 트랜잭션 조정을 위한 JTA/JCA 설정도 가능합니다.</li>
</ul>
<p>Spring Framework는 또한 애플리케이션 개발자가 Spring Framework에서 제공하는 Spring 특정 메커니즘 대신 사용하도록 선택할 수 있는 종속성 주입(Dependency Injection)( JSR 330 ) 및 공통 어노테이션(Common Annotation)( JSR 250 ) 사양을 지원합니다. 이 특성들은 공통 <code>javax</code>패키지를 기반으로 했습니다.</p>
<p>Spring Framework 6.0에서 Spring은 기존 <code>javax</code>패키지 대신 <code>jakarta</code>네임스페이스를 기반으로 Jakarta EE 9 레벨(예: Servlet 5.0+, JPA 3.0+)로 업그레이드되었습니다 . 최소 EE 9와 이미 지원되는 EE 10을 통해 Spring은 Jakarta EE API의 추가 발전을 위한 기본 지원을 제공할 준비가 되었습니다. Spring Framework 6.0은 웹 서버인 Tomcat 10.1, Jetty 11 및 Undertow 2.3 및 Hibernate ORM 6.1과 완벽하게 호환됩니다.</p>
<p>시간이 지남에 따라 애플리케이션 개발에서 Java/Jakarta EE의 역할이 발전했습니다. J2EE 및 Spring 초기에는 애플리케이션 서버에 배포하기 위해 애플리케이션이 생성되었습니다. 오늘날 Spring Boot의 도움으로 응용 프로그램은 DevOps 및 클라우드 친화적인 방식으로 생성되며 Servlet 컨테이너가 내장되어 있고 변경하기가 쉽지 않습니다. Spring Framework 5부터 WebFlux 애플리케이션은 Servlet API를 직접 사용하지 않으며 Servlet 컨테이너가 아닌 서버(예: Netty)에서 실행할 수 있습니다.</p>
<p>Spring은 계속해서 혁신하고 진화합니다. Spring Framework 외에도 Spring Boot, Spring Security, Spring Data, Spring Cloud, Spring Batch 등과 같은 다른 프로젝트가 있습니다. 각 프로젝트에는 고유한 소스 코드 리포지토리, 이슈 트랙커 및 릴리스 주기가 있음을 기억하는 것이 중요합니다. Spring 프로젝트의 전체 목록은 spring.io/projects를 참조하십시오.</p>
<h2 id="design-philosophy">Design Philosophy</h2>
<p>다음은 Spring Framework의 기본 원칙입니다. </p>
<ul>
<li>Spring을 사용하면 디자인 결정을 가능한 연기할 수 있습니다. 예를 들어, 코드를 변경하지 않고 configuration을 통해 영속성 공급자(persistence providers)를 전환할 수 있습니다. 인프라 문제나 서드파티 API와의 통합도 마찬가지 입니다.</li>
<li>다양한 관점에서 폭 넓은 애플리케이션 요구사항을 지원합니다.</li>
<li>Spring에 의존하는 애플리케이션 및 라이브러리의 유지 관리를 용이하게 하기 위해 엄선된 범위의 JDK 버전 및 타사 라이브러리를 지원합니다. </li>
<li>여러 버전과 기간에 걸쳐 유지되는 직관적 API 디자인</li>
<li>Spring Framework는 패키지 간의 순환 종속성이 없는 클린 코드 구조입니다. 최신의 정확한 javadoc을 강조합니다.</li>
</ul>