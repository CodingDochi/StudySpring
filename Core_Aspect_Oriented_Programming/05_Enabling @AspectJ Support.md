<p>Spring 구성(configuration)에서 @AspectJ 측면(aspect)을 사용하려면, @AspectJ 측면(aspect)을 기반으로 Spring AOP를 구성하고 해당 측면(aspect)의 어드바이스 여부에 따라 자동 프록시 빈을 구성하기 위한 Spring 지원을 활성화해야 합니다. 자동 프록시(auto-proxying)란 Spring이 하나 이상의 측면(aspect)에서 Bean을 어드바이스한다고 결정하면 해당 Bean에 대한 프록시를 자동으로 생성하여 메서드 호출을 가로채고(intercept) 필요에 따라 어드바이스가 실행되도록 보장한다는 의미입니다.</p>
<p>@AspectJ 지원은 XML 또는 Java 스타일 구성(configuration)으로 활성화할 수 있습니다. 두 경우 모두 AspectJ의 <code>aspectjweaver.jar</code> 라이브러리가 애플리케이션(버전 1.9 이상)의 classpath에 있는지 확인해야 합니다. 이 라이브러리는 AspectJ 배포판의 <code>lib</code> 디렉터리나 Maven Central 저장소에서 사용할 수 있습니다.</p>
<h1 id="enabling-aspectj-support-with-java-configuration">Enabling @AspectJ Support with Java Configuration</h1>
<p>Java <code>@Configuration</code>으로 @AspectJ 지원을 활성화하려면 다음 예제와 같이 <code>@EnableAspectJAutoProxy</code> 어노테이션을 추가합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableAspectJAutoProxy</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="enabling-aspectj-support-with-xml-configuration">Enabling @AspectJ Support with XML Configuration</h1>
<p>XML 기반 구성으로 @AspectJ 지원을 활성화하려면 다음 예제와 같이 <code>aop:aspectj-autoproxy</code> 요소를 사용하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspectj-autoproxy</span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>이는 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/xsd-schemas.html">XML 스키마 기반 구성(configuration)</a>에 설명된 대로 스키마 지원을 사용한다고 가정합니다. <code>aop</code> 네임스페이스에서 태그를 가져오는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/xsd-schemas.html#aop">AOP 스키마</a>를 참조하세요.</p>