<p>당신이 Bean 정의를 생성할 때, 당신은 해당 Bean 정의에 의해 정의된 클래스의 실제 인스턴스를 생성하기 위한 레시피를 생성합니다. 빈 정의가 레시피라는 생각은 중요합니다. 왜냐하면 클래스와 마찬가지로(as with) 단일 레시피에서 많은 객체 인스턴스를 생성할 수 있다는 뜻이기 때문입니다.</p>
<p>특정 빈 정의에서 생성된 객체에 플러그인될 다양한 종속성과 구성(configuration) 값을 제어할 수 있을 뿐만 아니라 특정 빈 정의에서 생성된 객체의 범위(scope)도 제어할 수 있습니다. 이 접근 방식은 강력하고 유연합니다. Java 클래스 수준에서 객체 범위를 하드 코딩하는(bake in) 대신 구성을 통해 생성하는 객체의 범위를 선택할 수 있기 때문입니다. </p>
<hr>
<p><strong>bake in</strong><br>
"bake in"은 특정한 속성, 기능 또는 설정을 고정시키는 것을 의미합니다. 이것은 보통 코드 레벨에서 하드코딩되거나 고정된 형태로 구현되는 것을 의미합니다. 따라서 "bake in the scope of an object at the Java class level"은 자바 클래스 레벨에서 객체의 범위를 하드코딩하거나 고정하는 것을 의미합니다.</p>
<p>예를 들어, 객체를 생성할 때마다 그 범위를 클래스 내에서 정의하고 한정짓는 것은 해당 클래스가 그 객체를 사용하는 방법에 관한 결정을 내재화하고 있는 것입니다. 하지만 스프링과 같은 프레임워크에서의 DI(Dependency Injection)를 사용하면 객체의 범위를 설정 파일이나 어노테이션 등의 외부 구성 요소를 통해 유연하게 지정할 수 있습니다. 이것이 위 문장에서 언급한 "configuration instead of having to bake in the scope of an object at the Java class level"의 의미입니다.</p>
<hr>
<p>Bean은 여러 범위 중 하나에 배포되도록 정의할 수 있습니다. Spring Framework는 6개의 범위를 지원하며 그 중 4개는 웹 인식(web-aware) <code>ApplicationContext</code>를 사용하는 경우에만 사용할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-custom">사용자 정의 범위</a>를 만들 수도 있습니다.</p>
<table id="beans-factory-scopes-tbl" class="tableblock 프레임-모든 그리드-모든 스트레치">
<caption class="title">표 1. Bean 범위</caption>




<thead>
<tr>
<th class="tableblock haalign-left valign-top">scope</th>
<th class="tableblock haalign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-singleton">singleton</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">(기본값) 각 Spring IoC 컨테이너에 대해 단일 객체 인스턴스에 대한 단일 Bean 정의를 범위(scope)로 지정합니다.
   </p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-prototype">prototype</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock"> 다수의 객체 인스턴스에 대해 단일 Bean 정의를 범위로 지정합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-request">request</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">단일 Bean 정의를 단일 HTTP 요청의 lifecycle의 범위로 지정합니다. 그건,
   각 HTTP 요청에는 단일 Bean 정의 뒤에서 생성되어 나온 자체 Bean 인스턴스가 있다는 뜻입니다. 웹 인식(web-aware) Spring <code>ApplicationContext</code>의 컨텍스트에서만 유효합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-session">session</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">단일 Bean 정의의 범위를 HTTP <code>Session</code>의 lifecycle로 지정합니다. 오직 웹 인식(web-aware) Spring <code>ApplicationContext</code>의 컨텍스트에서만 유효합니다.</p></td>
</tr>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-application">application</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">단일 Bean 정의의 범위를 <code>ServletContext</code>의 lifecycle로 지정합니다. 오직 웹 인식(web-aware) Spring <code>ApplicationContext</code>의 컨텍스트에서만 유효합니다.</p></td>
</tr>
<tr>
<td class="tableblock haalign-left valign-top"><p class="tableblock"><a href="../../web/websocket/stomp/scope.html" class="xref page"> websocket</a></p></td>
<td class="tableblock haalign-left valign-top"><p class="tableblock">단일 Bean 정의의 범위를 <code>WebSocket</code>의 lifecycle로 지정합니다. 오직 웹 인식(web-aware) Spring <code>ApplicationContext</code>의 컨텍스트에서만 유효합니다.</p></td>
</tr>
</tbody>
</table>
<blockquote>
<p><strong>[Note]</strong><br>
스레드 범위(scope)를 사용할 수 있지만 기본적으로 등록(registered)되지 않습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/support/SimpleThreadScope.html"><code>SimpleThreadScope</code></a> 설명서를 참조하세요. this 또는 다른 사용자 정의 범위를 등록하는 방법에 대한 지침은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-custom-using">사용자 정의 범위 사용</a>을 참조하세요.</p>
</blockquote>
<h1 id="the-singleton-scope">The Singleton Scope</h1>
<p>싱글톤 빈의 단 하나의 공유 인스턴스만 관리되며, 해당 빈 정의와 일치하는 ID들을 가진 빈에 대한 요청들은 스프링 컨테이너에서 그 특정한 하나의 빈 인스턴스만 반환되는 결과를 낳습니다.</p>
<p>다르게 말하면, 빈 정의를 정의하고 그것의 범위가 싱글톤으로 지정되면 Spring IoC 컨테이너는 해당 빈 정의에 의해 정의된 객체의 인스턴스를 정확히 하나만 생성합니다. 이 단일 인스턴스는 그러한 싱글톤 Bean의 캐시에 저장되며 해당 명명된(named) Bean에 대한 모든 후속 요청(request) 및 참조(reference)는 캐시된 객체를 반환합니다. 다음 이미지는 싱글톤 범위의 작동 방식을 보여줍니다.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/singleton.png"></p>
<p>Spring의 싱글톤 빈 개념은 GoF(Gang of Four) 패턴 책에 정의된 싱글톤 패턴과 다릅니다. GoF 싱글톤은 ClassLoader당 하나의 특정 클래스 인스턴스만 생성되도록 객체의 범위(scope)를 하드코딩합니다. Spring 싱글톤의 범위(scope)는 per-container 및 per-bean 범위로 가장 잘 설명됩니다. 이는 단일 Spring 컨테이너 내부의 특정 클래스에 대해 하나의 Bean을 정의하면, Spring 컨테이너는 해당 Bean 정의에 의해 정의된 클래스 인스턴스를 하나만 생성한다는 의미입니다. 싱글톤 범위(scope)는 Spring의 기본 범위입니다. XML에서 빈을 싱글톤으로 정의하려면 다음 예제와 같이 Bean을 정의할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.DefaultAccountService<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token comment">&lt;!-- the following is equivalent, though redundant (singleton scope is the default) --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.DefaultAccountService<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>singleton<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<h1 id="the-prototype-scope">The Prototype Scope</h1>
<p>빈 배포는 non-singleton 프로토타입 범위(scope)이므로, 특정 Bean에 대한 요청이 이루어질 때마다 새로운 Bean 인스턴스가 생성됩니다. 즉, Bean이 다른 Bean에 주입되거나 당신이 <code>getBean()</code> 메소드를 컨테이너에 호출하여 요청한다는 것입니다. 일반적으로 모든 Stateful Bean에는 프로토타입 범위를 사용해야 하고 Stateless Bean에는 싱글톤 범위를 사용해야 합니다.</p>
<hr>
<p>빈의 범위(scope)는 해당 빈 인스턴스의 생명주기와 관련이 있습니다.</p>
<ol>
<li>
<p><strong>싱글톤(Singleton) 범위</strong>:</p>
<ul>
<li>애플리케이션 전체에서 하나의 인스턴스만 존재하므로, 여러 요청 또는 스레드에서 공유됩니다.</li>
<li>싱글톤 빈은 상태를 공유하게 되어, 하나의 클라이언트가 상태를 변경하면 다른 클라이언트들도 동일한 상태를 볼 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>프로토타입(Prototype) 범위</strong>:</p>
<ul>
<li>요청할 때마다 새로운 인스턴스가 생성되므로, 각 요청 또는 사용 시에 독립적인 상태를 가질 수 있습니다.</li>
<li>프로토타입 빈은 상태를 공유하지 않으며, 각각의 클라이언트나 사용자에 대해 독립적으로 상태를 유지할 수 있습니다.</li>
</ul>
</li>
</ol>
<p>또한, 빈이 상태를 가지는지 여부는 해당 빈이 내부적으로 상태를 유지하고 있는지 여부를 나타냅니다.</p>
<ul>
<li><strong>상태를 가지는 빈(Stateful Bean)</strong>: 내부 변수나 필드를 통해 상태를 유지하고 변경할 수 있습니다. 주로 특정 사용자나 클라이언트와 관련된 데이터를 유지할 때 사용됩니다.</li>
<li><strong>상태를 가지지 않는 빈(Stateless Bean)</strong>: 입력에만 의존하여 동작하며, 같은 입력에 대해서는 항상 같은 결과를 반환합니다. 부작용이 없으며 예측 가능하고 안정적으로 동작합니다.</li>
</ul>
<p>따라서 상태를 공유해야 하는 경우 싱글톤 범위를 사용하고, 상태를 독립적으로 유지해야 하는 경우 프로토타입 범위를 사용하는 것이 일반적인 관례입니다.</p>
<hr>
<p>다음 다이어그램은 Spring 프로토타입 범위를 보여줍니다.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/prototype.png"></p>
<p>(일반적인 DAO(data access object)는 대화 상태를 유지하지 않기 때문에 데이터 액세스 객체(DAO)는 일반적으로 프로토타입으로 구성(configure)되지 않습니다. 싱글톤 다이어그램의 핵심을 재사용하는 것이 더 쉬웠습니다.)</p>
<hr>
<p>"대화 상태(conversational state)"란 사용자와 시스템 간의 상호작용을 유지하고 추적하는 상태를 의미합니다. 일반적으로 웹 애플리케이션에서는 사용자가 여러 페이지를 통해 정보를 입력하고 제출하는 등의 상호작용을 수행하게 됩니다. 이러한 상호작용은 대화(conversation)로 볼 수 있으며, 이 대화의 상태를 유지하고 관리하는 것이 대화 상태입니다.</p>
<p>대화 상태는 일반적으로 사용자 세션(session)과 관련이 있습니다. 사용자가 애플리케이션과 상호작용할 때마다 서버는 사용자의 상태를 추적하고 유지하기 위해 세션을 사용합니다. 예를 들어, 사용자가 로그인한 상태인지, 어떤 항목을 장바구니에 추가했는지, 이전에 방문한 페이지는 무엇인지 등을 세션을 통해 추적합니다.</p>
<p>따라서 "대화 상태"는 사용자와 시스템 간의 상호작용을 추적하고 유지하는 상태를 의미합니다. 위 문장에서는 데이터 액세스 객체(DAO)가 일반적으로 대화 상태를 유지하지 않는다고 언급되었습니다. 이는 DAO가 일반적으로 각 데이터 액세스 요청에 대해 즉시 처리되고 반환되는 것이 일반적이며, 상태를 유지하지 않는다는 것을 의미합니다. 따라서 DAO를 프로토타입으로 설정하는 것은 일반적이지 않습니다.</p>
<hr>
<p>다음 예제에서는 Bean을 XML의 프로토타입으로 정의합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.DefaultAccountService<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>prototype<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다른 범위(scope)와 달리 Spring은 프로토타입 Bean의 전체 라이프사이클을 관리하지 않습니다. 컨테이너는 프로토타입 개체를 인스턴스화, 구성(configure) 및 어셈블하여 클라이언트에 전달하며, 더 이상 해당 프로토타입 인스턴스에 대해 기록하지 않습니다. 따라서 범위에 관계없이 모든 객체에 대해 초기화 lifdcycle 콜백 메서드가 호출되지만 프로토타입의 경우 configured destruction lifecycle 콜백이 호출되지 않습니다. </p>
<p>클라이언트 코드는 프로토타입 범위 객체를 정리하고 프로토타입 Bean이 쥐고 있던 값비싼 리소스를 해제해야 합니다. 프로토타입 범위 Bean이 보유했던 리소스를 Spring 컨테이너가 해제하도록 하려면, 정리해야 하는 Bean에 대한 참조를 보유하는 사용자 정의 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-bpp">Bean post-processor(후처리기)</a>를 사용해 보십시오.</p>
<p>어떤 측면에서 프로토타입 범위의 Bean과 관련된(in regard to) Spring 컨테이너의 역할은 Java <code>new</code> 연산자를 대체합니다. 해당 시점 이후의 모든 lifecycle 관리는 클라이언트에서 처리해야 합니다. (Spring 컨테이너의 Bean 수명 주기에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle">수명 주기 콜백</a>을 참조하세요.)</p>
<h1 id="singleton-beans-with-prototype-bean-dependencied">Singleton Beans with Prototype-bean Dependencied</h1>
<p>프로토타입 Bean에 대한 종속성과 함께 싱글톤 범위 Bean을 사용하는 경우 인스턴스화 시 종속성이 resolve된다는 점에 유의하세요. 따라서 프로토타입 범위 Bean을 싱글톤 범위 Bean에 종속성 주입하면 새 프로토타입 Bean이 인스턴스화되고 싱글톤 Bean에 종속성 주입됩니다. 프로토타입 인스턴스는 싱글톤 범위의 Bean에 제공되는 유일한 인스턴스입니다.</p>
<p>그러나 싱글톤 범위 Bean이 런타임 시 반복적으로 프로토타입 범위 Bean의 새 인스턴스를 획득하기를 원한다고 가정해 보세요. 프로토타입 범위의 Bean을 싱글톤 Bean에 종속성 주입할 수 없습니다. 왜냐하면 Spring 컨테이너가 싱글톤 Bean을 인스턴스화하고 해당 종속성을 resolve 및 주입할 때 주입이 한 번만 발생하기 때문입니다. 런타임에 프로토타입 Bean의 새 인스턴스가 두 번 이상 필요한 경우 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-method-injection.html">메소드 주입</a>을 참조하세요.</p>
<h1 id="request-session-application-and-websocket-scopes">Request, Session, Application, and WebSocket Scopes</h1>
<p><code>request</code>, <code>session</code>, <code>application</code> 및 <code>websocket</code> 범위는 웹 인식(web-aware) Spring <code>ApplicationContext</code> 구현(예: <code>XmlWebApplicationContext</code>)을 사용하는 경우에만 사용할 수 있습니다. <code>ClassPathXmlApplicationContext</code>와 같은 일반 Spring IoC 컨테이너와 함께 이러한 범위를 사용하는 경우 알 수 없는 Bean 범위에 대해 불평하는 <code>IllegalStateException</code>이 발생합니다.</p>
<h2 id="initial-web-configuration">Initial Web Configuration</h2>
<p><code>request</code>, <code>session</code>, <code>application</code> 및 <code>websocket</code> 수준(웹 범위(web-scoped) Bean)에서 Bean 범위 지정을 지원하려면 Bean을 정의하기 전에 몇 가지 사소한 초기 구성(configuration)이 필요합니다. (이 초기 설정은 표준 범위인 <code>singleton</code> 및 <code>prototype</code>에는 필요하지 않습니다.)</p>
<p>이 초기 설정을 수행하는 방법은 특정 Servlet 환경에 따라 다릅니다.</p>
<p>Spring Web MVC 내에서 범위가 지정된 Bean에 액세스하는 경우, 사실상(in effect) Spring <code>DispatcherServlet</code>에 의해 처리되는 요청(request) 내에서는 특별한 설정이 필요하지 않습니다. <code>DispatcherServlet</code>은 이미 모든 관련 상태를 노출합니다.</p>
<p>Spring의 <code>DispatcherServlet</code> 외부에서 요청(request)이 처리되는 서블릿 웹 컨테이너를 사용하는 경우(예: JSF를 사용하는 경우) <code>org.springframework.web.context.request.RequestContextListener ServletRequestListener</code>를 등록(register)해야 합니다. 이는 <code>WebApplicationInitializer</code> 인터페이스를 사용하여 프로그래밍 방식으로 수행할 수 있습니다. 또는 웹 애플리케이션의 <code>web.xml</code> 파일에 다음 선언을 추가합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span><span class="token punctuation">&gt;</span></span>
	...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener-class</span><span class="token punctuation">&gt;</span></span>
			org.springframework.web.context.request.RequestContextListener
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener-class</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener</span><span class="token punctuation">&gt;</span></span>
	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>또는 리스너 설정에 문제가 있는 경우 Spring의 <code>RequestContextFilter</code> 사용을 고려해보세요. 필터 매핑은 주변 웹 애플리케이션 구성(configuration)에 따라 달라지므로 적절하게 변경해야 합니다. 다음 목록은 웹 애플리케이션의 필터 부분을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span><span class="token punctuation">&gt;</span></span>
	...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>filter</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>filter-name</span><span class="token punctuation">&gt;</span></span>requestContextFilter<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>filter-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>filter-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.filter.RequestContextFilter<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>filter-class</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>filter</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>filter-mapping</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>filter-name</span><span class="token punctuation">&gt;</span></span>requestContextFilter<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>filter-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>url-pattern</span><span class="token punctuation">&gt;</span></span>/*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>url-pattern</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>filter-mapping</span><span class="token punctuation">&gt;</span></span>
	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>DispatcherServlet</code>, <code>RequestContextListener</code> 및 <code>RequestContextFilter</code>는 모두 정확히 동일한 작업을 수행합니다. 즉(namely), HTTP 요청 개체(request object)를 해당 요청(request)을 처리하는 <code>Thread</code>에 바인딩합니다. 이렇게 하면 request 및 session 범위의 Bean을 호출 체인 아래에서 사용할 수 있습니다.</p>
<hr>
<p>"<strong>call chain</strong> 아래에서 사용한다"는 일련의 메서드 호출 과정에서 특정한 시점에 어떤 동작 또는 객체가 사용될 수 있다는 것을 의미합니다. 요청이 DispatcherServlet으로 들어오면, 해당 서블릿은 요청을 처리하고 적절한 컨트롤러로 라우팅합니다. 이때 DispatcherServlet은 요청을 처리하는 동안 HTTP 요청 객체를 현재 스레드에 바인딩합니다. 이렇게 바인딩된 HTTP 요청 객체는 DispatcherServlet이 호출하는 서비스(service) 메서드를 포함하여 호출된 다른 메서드나 객체들에서도 접근할 수 있습니다.</p>
<hr>
<h2 id="request-scope">Request scope</h2>
<p>Bean 정의에 대해 다음 XML 구성을 고려하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>loginAction<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.LoginAction<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>request<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>Spring 컨테이너는 각각의 모든 HTTP 요청에 대해 <code>loginAction</code> 빈 정의를 사용하여 <code>LoginAction</code> 빈의 새 인스턴스를 생성합니다. 즉, <code>loginAction</code> 빈의 범위는 HTTP 요청(request) 레벨로 지정됩니다. 당신은 생성된 인스턴스의 내부 상태를 원하는 만큼 변경할 수 있습니다. 동일한 <code>loginAction</code> 빈 정의에서 생성된 다른 인스턴스는 이러한 상태 변경을 볼 수 없기 때문입니다.  이는 개별 요청에 따라 다릅니다. 요청 처리가 완료되면, 범위가 해당 요청에 지정된 Bean이 삭제됩니다.</p>
<p>어노테이션 기반 컴포넌트 또는 Java 구성(configuration)을 사용하는 경우 <code>@RequestScope</code> 어노테이션을 사용하여 컴포넌트를 request 범위에 할당할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RequestScope</span>
<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginAction</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p><strong>component와 configuration의 차이</strong>:</p>
<ul>
<li>"component"는 스프링에서 관리하는 빈을 나타냅니다. 일반적으로 <code>@Component</code>, <code>@Service</code>, <code>@Repository</code>, <code>@Controller</code> 등의 애노테이션을 사용하여 선언됩니다.</li>
<li>"configuration"은 스프링 빈을 정의하고 구성하는 설정 클래스를 의미합니다. 일반적으로 <code>@Configuration</code> 애노테이션을 가진 클래스를 사용하여 선언됩니다. 이 클래스에는 <code>@Bean</code> 애노테이션을 사용하여 스프링 빈을 정의합니다.</li>
</ul>
<hr>
<h2 id="session-scope">Session Scope</h2>
<p>Bean 정의에 대해 다음 XML 구성을 고려하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>session<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>Spring 컨테이너는 단일 HTTP <code>Session</code>의 수명 동안 <code>userPreferences</code> 빈 정의를 사용하여 <code>UserPreferences</code> 빈의 새 인스턴스를 생성합니다. 즉, <code>userPreferences</code> Bean은 HTTP <code>Session</code> 레벨에서 효과적으로 범위가 지정됩니다. 요청(request) 범위 Bean과 마찬가지로(as with) 생성된 인스턴스의 내부 상태를 원하는 만큼 변경할 수 있습니다. 동일한 <code>userPreferences</code> Bean 정의에서 생성된 인스턴스를 사용하는 다른 HTTP <code>Session</code> 인스턴스에서는 이러한 상태 변경을 볼 수 없습니다. 왜냐하면, 이들은 개별 HTTP <code>Session</code>에 특정되기 때문입니다. HTTP <code>Session</code>이 결국 삭제되면 해당 특정 HTTP <code>Session</code>으로 범위가 지정된 Bean도 삭제됩니다.</p>
<p>어노테이션 기반 컴포넌트 또는 Java 구성(configuration)을 사용할 때 <code>@SessionScope</code> 주석을 사용하여 구성 요소를 <code>session</code> 범위에 할당할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SessionScope</span>
<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserPreferences</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="application-scope">Application Scope</h2>
<p>Bean 정의에 대해 다음 XML 구성을 고려하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>appPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.AppPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>application<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>Spring 컨테이너는 전체 웹 애플리케이션에 대해 <code>appPreferences</code> 빈 정의를 한 번 사용하여 <code>AppPreferences</code> 빈의 새 인스턴스를 생성합니다. 즉, <code>appPreferences</code> Bean은 <code>ServletContext</code> 레벨에서 범위가 지정되고 일반(regular) <code>ServletContext</code> 속성(attribute)으로 저장됩니다. 이는 Spring 싱글톤 Bean과 다소 유사하지만 두 가지 중요한 점에서 다릅니다. 이는 Spring <code>ApplicationContext</code>(주어진 웹 애플리케이션에 여러 개가 있을 수 있음)가 아닌 <code>ServletContext</code>당 싱글톤이며 실제로 노출되어 다음과 같이 <code>ServletContext</code> 속성(attribute)으로 표시됩니다. </p>
<p>어노테이션 기반 구성 요소 또는 Java 구성(configuration)을 사용하는 경우 <code>@ApplicationScope</code> 어노테이션을 사용하여 컴포넌트를 <code>application</code> 범위에 할당할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ApplicationScope</span>
<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppPreferences</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="websocket-scope">WebSocket Scope</h2>
<p>WebSocket 범위는 WebSocket 세션의 수명 주기와 연결되어 있으며 WebSocket 애플리케이션을 통한 STOMP에 적용됩니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/stomp/scope.html">WebSocket 범위</a>를 참조하세요.</p>
<h2 id="scoped-beans-as-depedencies">Scoped Beans as Depedencies</h2>
<p>Spring IoC 컨테이너는 객체(빈)의 인스턴스화뿐만 아니라 협력자(또는 종속성)의 연결도 관리합니다. 예를 들어 HTTP 요청(request) 범위의 Bean을 수명이 더 긴 다른 Bean에 주입하려는 경우 범위가 지정된(scoped) Bean 대신(in place of) AOP 프록시를 주입하도록 선택할 수 있습니다. 즉, 범위가 지정된(scoped) 객체와 동일한 public 인터페이스를 노출하지만 관련 범위(예: HTTP 요청)에서 실제 대상(target) 객체를 검색(retreive)하고 메서드 호출을 실제 객체에 위임(delegate)할 수도 있는 프록시 객체를 삽입해야 합니다.</p>
<hr>
<p><img src="https://hwanseok-dev.github.io/assets/images/spring/spring-aop-proxy.jpg"> <img src="https://media.geeksforgeeks.org/wp-content/uploads/20240202170221/changing-of-proxies-768.webp"> <img src="https://media.geeksforgeeks.org/wp-content/uploads/20240205165856/proxypatternclassdiagram-768.webp"></p>
<hr>
<blockquote>
<p><strong>[Note]</strong><br>
<code>singleton</code>으로 범위가 지정된 빈 사이에 <code>&lt;aop:scoped-proxy/&gt;</code>를 사용할 수도 있으며, 참조는 직렬화(serializable) 가능한 중간(intermediate) 프록시를 통과하므로 역직렬화(deserialization) 시 대상 싱글톤 빈을 다시 얻을 수 있습니다.</p>
<p><code>prototype</code>범위의 빈에 대해 <code>&lt;aop:scoped-proxy/&gt;</code>를 선언할 때 공유(shared) 프록시에 대한 모든 메소드 호출은 호출이 전달되는 새로운 대상 인스턴스의 생성으로 이어집니다.</p>
<p>또한 범위가 지정된(scoped) 프록시는 lifecycle에 안전한 방식으로 더 짧은 범위에서 Bean에 액세스하는 유일한 방법은 아닙니다. 또한 주입 지점(즉, 생성자 또는 setter 인수 또는 autowired 필드)을 <code>ObjectFactory&lt;MyTargetBean&gt;</code>으로 선언하여 <code>getObject()</code> 호출을 통해 필요할 때마다 현재 인스턴스를 검색(retreive)할 수 있습니다. 인스턴스를 별도로 저장하거나 보유(holding)하고 있지 않아도 말입니다. </p>
<p>확장된 변형으로 <code>getIfAvailable</code> 및 <code>getIfUnique</code>를 포함하여 여러 추가 액세스 변형을 전달하는 <code>ObjectProvider&lt;MyTargetBean&gt;</code>을 선언할 수 있습니다.</p>
<p>이것의 JSR-330 변형은 <code>Provider</code>라고 하며 <code>Provider&lt;MyTargetBean&gt;</code> 선언과 함께 쓰이고, 모든 검색 시도에 대해, 해당 <code>get()</code> 호출과 함께 사용됩니다. JSR-330 전반에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/standard-annotations.html">여기</a>를 참조하세요.</p>
</blockquote>
<p>다음 예의 구성은 한 줄에 불과하지만 그 뒤에 있는 "이유"와 "방법"을 이해하는 것이 중요합니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>aop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/aop<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- an HTTP Session-scoped bean exposed as a proxy --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>session<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- instructs the container to proxy the surrounding bean --&gt;</span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>scoped-proxy</span><span class="token punctuation">/&gt;</span></span> <span class="token comment">&lt;!-- (1)--&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- a singleton-scoped bean injected with a proxy to the above bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.SimpleUserService<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- a reference to the proxied userPreferences bean --&gt;</span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
(1) 프록시를 정의하는 줄
<p>그러한 프록시를 생성하려면 범위가 지정된 Bean 정의에 하위 <code>&lt;aop:scoped-proxy/&gt;</code> 요소를 삽입합니다(<a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other-injection-proxies">생성할 프록시 유형 선택</a> 및 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/xsd-schemas.html">XML 스키마 기반 구성</a> 참조).</p>
<p><code>request</code>, <code>session</code> 및 사용자 정의 범위 수준에서 범위가 지정된 Bean 정의에서 <code>&lt;aop:scoped-proxy/&gt;</code> 요소가 공통적으로 필요한 이유는 무엇입니까? 다음 싱글톤 빈 정의를 고려하고 이를 앞서 언급한(aforementioned) 범위들에 대해 정의해야 하는 것과 대조하십시오(다음 <code>userPreferences</code> 빈 정의는 현재 상태로는(as it stands) 불완전하다는 점에 유의하십시오).</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>session<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userManager<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserManager<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이전 예제에서 싱글톤 Bean(<code>userManager</code>)은 HTTP <code>Session</code> 범위 Bean(<code>userPreferences</code>)에 대한 참조와 함께 주입됩니다. 여기서 중요한(현저한, salient) 점은 <code>userManager</code> 빈이 싱글톤이라는 것입니다. 이는 컨테이너당 정확히 한 번 인스턴스화되고 해당 종속성(이 경우 <code>userPreferences</code> 빈, 단 하나)도 한 번만 주입됩니다. 이는 <code>userManager</code> 빈이 정확히 동일한 <code>userPreferences</code> 객체(즉, 원래 주입되었던 그 객체)에서만 작동한다는 것을 의미합니다.</p>
<p>이는 수명이 짧은 범위의 Bean을 수명이 긴 범위의 Bean에 주입할 때 당신이 원하는 동작이 아닙니다(예: HTTP <code>Session</code> 범위의 공동 작업 Bean을 싱글톤 Bean에 대한 종속성으로 주입). 오히려 단일 <code>userManager</code> 객체가 필요하며, HTTP <code>Session</code>의 수명 동안 HTTP <code>Session</code>에 특정된 <code>userPreferences</code> 객체가 필요합니다. 따라서 컨테이너는 범위 지정(scoping) 메커니즘(HTTP 요청, <code>Session</code> 등)에서 실제 <code>UserPreferences</code> 객체를 가져올(fetch) 수 있는 <code>UserPreferences</code> 클래스(이상적으로는 <code>UserPreferences</code> 인스턴스인 객체)와 정확히 동일한 public 인터페이스를 노출하는 객체를 생성합니다. 컨테이너는 이 프록시 객체를 <code>userManager</code> 빈에 주입하는데, 이 빈은 이 <code>UserPreferences</code> 참조가 프록시라는 것을 인식하지 못합니다. 이 예에서 <code>UserManager</code> 인스턴스는 종속성이 주입된 <code>UserPreferences</code> 객체에 대한 메서드를 호출할 때 실제로는 프록시에 대한 메서드를 호출하는 것입니다. 그런 다음 프록시는 (이 경우) HTTP <code>Session</code>에서 실제 <code>UserPreferences</code> 개체를 가져오고(fetch) 검색된(retrieved) 실제 <code>UserPreferences</code> 개체에 메서드 호출(invocation)을 위임합니다.</p>
<p>따라서 다음 예제에서 볼 수 있듯이 <code>request-</code> 및 <code>session-scoped</code>의 Bean을 공동 작업 개체에 주입할 때 다음(올바르고 완전한) 구성(configuration)이 필요합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>session<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>scoped-proxy</span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userManager<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.UserManager<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="choosing-the-type-of-proxy-to-create">Choosing the Type of Proxy to Create</h2>
<p>기본적으로 Spring 컨테이너가 <code>&lt;aop:scoped-proxy/&gt;</code> 요소로 표시되는 Bean에 대한 프록시를 생성할 때 CGLIB 기반 클래스 프록시가 생성됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
CGLIB 프록시는 private 메소드를 가로채지 않습니다. 그러한 프록시에서 private 메소드를 호출하려고 시도하면 실제 범위가 지정된(scoped) 대상(target) 객체에 위임되지 않습니다.</p>
</blockquote>
<p>또는 <code>&lt;aop:scoped-proxy/&gt;</code> 요소의 <code>proxy-target-class</code> 속성(attribute) 값에 <code>false</code>를 지정하여 범위가 지정된 Bean에 대한 표준 JDK 인터페이스 기반 프록시를 생성하도록 Spring 컨테이너를 구성할 수 있습니다. JDK 인터페이스 기반 프록시를 사용한다는 것은 이러한 프록시에 영향을 주기 위해 애플리케이션 classpath에 추가 라이브러리가 필요하지 않음을 의미합니다. 그러나 이는 또한 범위가 지정된 Bean의 클래스가 최소한 하나의 인터페이스를 구현해야 하며 범위가 지정된 Bean이 삽입되는 모든 협력자(종속성)가 해당 인터페이스 중 하나를 통해 Bean을 참조해야 함을 의미합니다. 다음 예에서는 인터페이스 기반 프록시를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token comment">&lt;!-- DefaultUserPreferences implements the UserPreferences interface --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.stuff.DefaultUserPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>session<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>scoped-proxy</span> <span class="token attr-name">proxy-target-class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>false<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userManager<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.stuff.UserManager<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>userPreferences<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>클래스 기반 또는 인터페이스 기반 프록시 선택에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/proxying.html">프록시 메커니즘</a>을 참조하세요.</p>
<h2 id="injecting-requestsession-references-directly">Injecting Request/Session References Directly</h2>
<p>팩토리 범위의 대안으로 Spring <code>WebApplicationContext</code>는 <code>HttpServletRequest</code>, <code>HttpServletResponse</code>, <code>HttpSession</code>, <code>WebRequest</code> 및 (JSF가 있는 경우) <code>FacesContext</code> 및 <code>ExternalContext</code>를 Spring 관리 빈에 삽입하는 것을 지원합니다. 단순히 다른 빈의 regular 주입 지점 옆의 type-based autowiring을 통해서요.<br>
Spring이 request, session 객체에 대한 프록시를 singleton 빈과 직렬화 가능한 빈 내부에 주입하는 방식은 Factory scope bean에 대한 scoped 프록시와 유사합니다.</p>
<hr>
<p><strong>Factory scope</strong><br>
Factory scope는 스프링에서 기본적으로 제공되지 않는 표준 스코프입니다. 대신에 사용자 정의 스코프를 만들 수 있습니다. Factory scope는 빈을 생성하는 특정 팩토리 메서드를 호출함으로써 빈을 얻을 때마다 새로운 인스턴스를 반환합니다. 일반적으로 Spring의 <code>@Scope</code> 어노테이션을 사용하여 사용자 정의 스코프를 정의하고 구현합니다.</p>
<hr>
<h1 id="custom-scopes">Custom Scopes</h1>
<p>빈 범위 지정 메커니즘은 확장 가능합니다. 자신만의 범위를 정의하거나 기존 범위를 재정의(redefine)할 수도 있지만 후자는 나쁜 습관으로 간주되며 내장된 <code>singleton</code> 및 <code>prototype</code> 범위를 재정의(override)할 수 없습니다.</p>
<h2 id="creating-custom-scope">Creating Custom Scope</h2>
<p>사용자 정의 범위를 Spring 컨테이너에 통합하려면 이 섹션에 설명된 <code>org.springframework.beans.factory.config.Scope</code> 인터페이스를 구현해야 합니다. 자신만의 <code>Scope</code>를 구현하는 방법에 대한 아이디어는 Spring Framework 자체와 함께 제공되는 <code>Scope</code> 구현과, 구현해야 하는 메서드를 더 자세히 설명하는 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/config/Scope.html"><code>Scope</code></a> javadoc를 참조하세요.</p>
<p><code>Scope</code> 인터페이스에는 범위에서 객체를 가져오고(get), 제거(remove)하고, 파괴(destory)하는 네 가지 메서드가 있습니다.</p>
<p>예를 들어, 세션 범위 구현은, 세션 범위(session-scoped) Bean을 반환합니다(존재하지 않는 경우 메소드는 나중에 참조(future reference)할 수 있도록 세션에 바인딩한 후 Bean의 새 인스턴스를 반환합니다). 다음 메서드는 주어진(underlying) 범위에서 객체를 반환합니다.</p>
<pre><code class="language-java"><span class="token class-name">Object</span> <span class="token function">get</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token class-name">ObjectFactory</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span> objectFactory<span class="token punctuation">)</span></code></pre>
<p>예를 들어, 세션 범위 구현은 기본 세션에서 세션 범위 Bean을 제거(remove)합니다. 객체가 반환되어야 하지만, 지정된 이름의 객체가 없으면 <code>null</code>을 반환할 수 있습니다. 다음 메서드는 주어진(underlying) 범위에서 객체를 제거(remove)합니다.</p>
<pre><code class="language-java"><span class="token class-name">Object</span> <span class="token function">remove</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span></code></pre>
<p>다음 메서드는 범위가 소멸(destroy)되거나 범위의 지정된 객체가 소멸(destroy)될 때 호출(invoke)해야 하는 콜백을 등록(register)합니다.</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">registerDestructionCallback</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token class-name">Runnable</span> destructionCallback<span class="token punctuation">)</span></code></pre>
<p>소멸(destruction) 콜백에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/config/Scope.html#registerDestructionCallback">javadoc</a> 또는 Spring 범위 구현을 참조하세요. 다음 메서드는 기본(underlying) 범위에 대한 대화 식별자를 가져옵니다.</p>
<pre><code class="language-java"><span class="token class-name">String</span> <span class="token function">getConversationId</span><span class="token punctuation">(</span><span class="token punctuation">)</span></code></pre>
<p>이 식별자는 범위마다 다릅니다. 세션 범위 구현의 경우 이 식별자는 세션 식별자가 될 수 있습니다.</p>
<hr>
<p><strong>callback</strong><br>
콜백(callback)은 프로그래밍에서 다른 코드에 의해 호출되는 함수 또는 코드 블록을 가리킵니다. 콜백은 일반적으로 이벤트가 발생했을 때 호출되는데, 이벤트는 보통 사용자의 동작이나 시스템에서 발생한 변화 등을 의미합니다. </p>
<p>스프링에서의 콜백은 주로 객체의 라이프사이클 관리나 이벤트 처리와 관련이 있습니다. 예를 들어, 스프링 빈의 초기화나 소멸 단계에서 특정 작업을 수행하기 위해 콜백을 등록할 수 있습니다. 또한, 스프링에서는 이벤트 기반의 프로그래밍을 지원하여 특정 이벤트가 발생했을 때 등록된 콜백을 호출할 수 있습니다.</p>
<p><strong>conversation</strong><br>
대화(Conversation)는 웹 애플리케이션에서 특정 작업이나 작업 그룹을 나타내는 개념입니다. 대화는 일련의 요청과 응답 사이에서 상태를 유지하고 사용자의 상호 작용을 추적하는 데 사용됩니다. 예를 들어, 사용자가 웹 애플리케이션에서 여러 단계의 폼을 작성하는 경우, 각 단계에서 사용자가 입력한 데이터를 유지하고 이전 상태를 추적하는 데 대화를 사용할 수 있습니다.</p>
<p>스프링에서 스코프의 대화는 일반적으로 HTTP 세션과 관련이 있습니다. 예를 들어, 세션 스코프의 빈은 HTTP 세션과 관련이 있으며, 사용자의 세션을 통해 상태를 유지하고 사용자 간의 상호 작용을 추적하는 데 사용됩니다. 이렇게 하면 여러 요청 간에 데이터를 공유하고 유지할 수 있으며, 사용자가 웹 애플리케이션을 탐색하는 동안 데이터를 유지할 수 있습니다.</p>
<hr>
<h2 id="using-a-custom-scope">Using a Custom Scope</h2>
<p>하나 이상의 사용자 정의 <code>Scope</code> 구현을 작성하고 테스트한 후에는 Spring 컨테이너가 새 범위를 인식하도록 해야 합니다. 다음 방법은 Spring 컨테이너에 새 <code>Scope</code>를 등록하는 주요 방법입니다.</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">registerScope</span><span class="token punctuation">(</span><span class="token class-name">String</span> scopeName<span class="token punctuation">,</span> <span class="token class-name">Scope</span> scope<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이 메소드는 Spring과 함께 제공되는 대부분의 구체(concrete) <code>ApplicationContext</code> 구현에서 <code>BeanFactory</code> 속성(property)을 통해 사용할 수 있는 <code>ConfigurableBeanFactory</code> 인터페이스에서 선언됩니다.</p>
<p><code>registerScope(..)</code> 메소드의 첫 번째 인수는 범위와 연관된 고유 이름입니다. Spring 컨테이너 자체에 있는 이러한 이름의 예로는 <code>singleton</code>과 <code>prototype</code>이 있습니다. <code>registerScope(..)</code> 메소드의 두 번째 인수는 등록하고 사용하려는 사용자 정의 <code>Scope</code> 구현의 실제 인스턴스입니다.</p>
<p>사용자 정의 <code>Scope</code> 구현을 작성한 후 다음 예제와 같이 등록한다고 가정합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
다음 예제에서는 Spring에 포함되어 있지만 기본적으로 등록되지는 않는 <code>SimpleThreadScope</code>를 사용합니다. 사용자 정의 <code>Scope</code> 구현에 대한 지침은 동일합니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token class-name">Scope</span> threadScope <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleThreadScope</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
beanFactory<span class="token punctuation">.</span><span class="token function">registerScope</span><span class="token punctuation">(</span><span class="token string">"thread"</span><span class="token punctuation">,</span> threadScope<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>그런 다음 다음과 같이 사용자 정의 <code>Scope</code>의 범위 지정 규칙을 준수하는(adhere to) Bean 정의를 생성할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thread<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span></code></pre>
<p>사용자 정의 <code>Scope</code> 구현을 사용하면 프로그래밍 방식의 범위 등록에만 국한되지 않습니다. 다음 예제와 같이 <code>CustomScopeConfigurer</code> 클래스를 사용하여 <code>Scope</code> 등록을 선언적으로 수행할 수도 있습니다.</p>
<pre><code class="language-null"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>aop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/aop<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.beans.factory.config.CustomScopeConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>scopes<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>map</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>entry</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thread<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
					<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.support.SimpleThreadScope<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>entry</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>map</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thing2<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.Thing2<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thread<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>name<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Rick<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>scoped-proxy</span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thing1<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.Thing1<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thing2<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>thing2<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>FactoryBean</code> 구현을 위한 <code>&lt;bean&gt;</code> 선언 내에 <code>&lt;aop:scoped-proxy/&gt;</code>를 배치하면 범위가 지정된 것은 <code>getObject()</code>에서 반환된 객체가 아니라 팩토리 빈 자체입니다.</p>
</blockquote>