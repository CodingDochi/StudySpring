<p>이 섹션에서는 Spring MVC 애플리케이션의 <code>spring-test</code>에서 사용할 수 있는 옵션을 요약합니다.</p>
<ul>
<li>
<p>Servlet API Mocks: 단위 테스트 컨트롤러, 필터 및 기타 웹 구성 요소에 대한 Servlet API 계약의 모의 구현입니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/testing/unit.html#mock-objects-servlet">Servlet API</a> 모의 객체를 참조하세요.</p>
</li>
<li>
<p>TestContext Framework: 테스트 메서드 전반에 걸쳐 로드된 구성의 효율적인 캐싱 및 <code>MockServletContext</code>를 사용하여 <code>WebApplicationContext</code> 로드 지원을 포함하여 JUnit 및 TestNG 테스트에서 Spring 구성 로드 지원. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/testing/testcontext-framework.html">TestContext Framework</a>를 참조하세요.</p>
</li>
<li>
<p>Spring MVC 테스트: <code>DispatcherServlet</code>(즉, annotation 지원)을 통해 annotation이 달린 컨트롤러를 테스트하기 위한 <code>MockMvc</code>라고도 알려진 프레임워크로, Spring MVC 인프라는 있지만 HTTP 서버는 없습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework.html">Spring MVC 테스트</a>를 참조하세요.</p>
</li>
<li>
<p>클라이언트 측 REST: <code>spring-test</code>는 <code>RestTemplate</code>을 내부적으로 사용하는 클라이언트 측 코드를 테스트하기 위한 모의 서버로 사용할 수 있는 <code>MockRestServiceServer</code>를 제공합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-client.html">클라이언트 REST 테스트</a>를 참조하세요.</p>
</li>
<li>
<p><code>WebTestClient</code>: WebFlux 애플리케이션 테스트용으로 제작되었지만 HTTP 연결을 통해 모든 서버에 대한 엔드투엔드 통합 테스트에도 사용할 수 있습니다. 이는 비차단 반응형 클라이언트이며 비동기 및 스트리밍 시나리오를 테스트하는 데 매우 적합합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/testing/webtestclient.html"><code>WebTestClient</code></a>를 참조하세요.</p>
</li>
</ul>