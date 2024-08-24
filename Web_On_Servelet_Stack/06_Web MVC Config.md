<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html#webflux-framework-config">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>애플리케이션은 요청을 처리하는 데 필요한 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html">특수 Bean 유형</a>에 나열된 인프라 Bean을 선언할 수 있습니다. <code>DispatcherServlet</code>은 각 특수 Bean에 대해 <code>WebApplicationContext</code>를 확인합니다. 일치하는 Bean 유형이 없으면 <a href="https://github.com/spring-projects/spring-framework/tree/main/spring-webmvc/src/main/resources/org/springframework/web/servlet/DispatcherServlet.properties"><code>DispatcherServlet.properties</code></a>에 나열된 기본 유형으로 대체됩니다.</p>
<p>대부분의 경우 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config.html">MVC 구성</a>이 가장 좋은 출발점입니다. 이는 Java 또는 XML로 필수 Bean을 선언하고 이를 사용자 정의하기 위한 상위 레벨 구성 콜백 API를 제공합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Boot는 MVC Java 구성을 사용하여 Spring MVC를 구성하고 추가로 편리한 옵션을 많이 제공합니다.</p>
</blockquote>