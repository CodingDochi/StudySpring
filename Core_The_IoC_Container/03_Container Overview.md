<p><code>org.springframework.context.ApplicationContext</code> 인터페이스는 Spring IoC 컨테이너를 나타내며 빈의 인스턴스화, 구성 및 조립을 담당합니다. 컨테이너는 구성 메타데이터(configuration metadata)를 읽어 어떤 개체를 인스턴스화하고, 구성하고, 어셈블할지에 대한 지침을 얻습니다. 구성 메타데이터는 XML, Java annotation 또는 Java 코드로 표시됩니다. 이를 통해 애플리케이션을 구성하는 개체와 해당 개체 간의 풍부한 상호 종속성을 표현할 수 있습니다.</p>
<p><code>ApplicationContext</code> 인터페이스의 여러 구현이 Spring과 함께 제공됩니다. 독립형 애플리케이션에서는 <code>ClassPathXmlApplicationContext</code> 또는 <code>FileSystemXmlApplicationContext</code>의 인스턴스를 생성하는 것이 일반적입니다. XML은 구성 메타데이터를 정의하기 위한 전통적인 형식이었지만, Java annotation이나 코드를 메타데이터 형식으로 사용하도록 컨테이너에 지시하기 위해 소량의 XML 구성을 제공함으로써 이러한 추가 메타데이터 형식에 대한 지원을 선언적으로 활성화 할 수 있습니다.</p>
<p>대부분의 애플리케이션 시나리오에서는 Spring IoC 컨테이너의 인스턴스를 인스턴스화하는 데에 명시적인 사용자 코드가 필요하지 않습니다. 예를 들어, 웹 애플리케이션 시나리오에서는 일반적으로 애플리케이션의 <code>web.xml</code> 파일에 있는 상용구(boilerplate) web descriptor XML의 간단한 8줄이면 충분합니다(<a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-create">웹 애플리케이션을 위한 편리한 ApplicationContext 인스턴스화</a> 참조). Eclipse용 Spring Tools(Eclipse 기반 개발 환경)를 사용하는 경우 몇 번의 마우스 클릭이나 키 입력만으로 이러한 상용구 구성을 쉽게 생성할 수 있습니다.</p>
<p>다음 다이어그램은 Spring이 작동하는 방식에 대한 고수준 개요(high-level view)를 보여줍니다. 애플리케이션 클래스는 구성 메타데이터와 결합(combined with)되므로, <code>ApplicationContext</code>가 생성되고 초기화된 후, 완전히 구성되고(configured) 실행 가능한 시스템 또는 애플리케이션을 갖게 됩니다.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/container-magic.png"></p>
<h1 id="configuration-metadata">Configuration Metadata</h1>
<p>이전 다이어그램에서 볼 수 있듯이 Spring IoC 컨테이너는 구성 메타데이터 형식을 사용(consume)합니다. 이 구성 메타데이터는 애플리케이션 개발자가 Spring 컨테이너에 애플리케이션의 객체를 인스턴스화, 구성 및 어셈블하도록 지시하는 방법을 나타냅니다.</p>
<p>구성 메타데이터는 전통적으로 간단하고 직관적인(intuitive) XML 형식으로 제공되며, 이 장의 대부분은 Spring IoC 컨테이너의 주요 개념과 기능을 전달(convey)하는 데 사용됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
XML 기반 메타데이터는 구성 메타데이터의 유일한 허용 형식이 아닙니다. Spring IoC 컨테이너 자체는 이 구성 메타데이터가 실제로 작성되는 형식과 완전히 분리되어 있습니다. 요즘에는 많은 개발자가 Spring 애플리케이션에 대해 <a href="https://docs.spring.io/spring-framework/reference/core/beans/java.html">Java 기반 구성</a>을 선택합니다.</p>
</blockquote>
<p>Spring 컨테이너에서 다른 형태의 메타데이터를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config.html">어노테이션 기반 구성</a> : 어노테이션 기반 구성 메타데이터를 사용하여 Bean을 정의합니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/java.html">Java 기반 구성</a> : XML 파일이 아닌 Java를 사용하여 <span style="background-color:#ffdce0">애플리케이션 클래스 외부</span>에 Bean을 정의합니다. 이러한 기능을 사용하려면 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/Configuration.html">@Configuration</a>, <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/Bean.html">@Bean</a>, <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/Import.html">@Import</a> 및 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/DependsOn.html">@DependsOn</a> 어노테이션을 참조하세요.</p>
</li>
</ul>
<p>Spring 구성(configuration)은 컨테이너가 관리해야 하는 적어도 하나, 일반적으로 하나 이상의 Bean 정의로 구성(consist of)됩니다. XML 기반 구성 메타데이터는 이러한 Bean을 최상위 <code>&lt;beans/&gt;</code> 요소 내의 <code>&lt;bean/&gt;</code> 요소로 구성합니다. Java 구성은 일반적으로 <code>@Configuration</code> 클래스 내에서 <code>@Bean</code> 어노테이션이 달린 메서드를 사용합니다.</p>
<p>이러한 빈 정의는 애플리케이션을 구성하는 실제 객체에 해당합니다. 일반적으로 서비스 계층(service layer) 객체, 저장소(repositories)나 DAO(데이터 액세스 객체)와 같은 지속성 계층(persistence layer) 객체, 웹 컨트롤러(controller)와 같은 프리젠테이션 객체(presentation object), JMS queue, JPA <code>EntityManagerFactory</code>와 같은 인프라 객체를 정의합니다. </p>
<p>일반적으로 도메인 객체를 생성하고 로드하는 것은 리포지토리와 비즈니스 로직의 책임이기 때문에 컨테이너에서는 세분화된(fine-grained) 도메인 객체를 구성하지 않습니다.</p>
<p>다음 예에서는 XML 기반 구성 메타데이터의 기본 구조를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span> <span class="token comment">&lt;!--(1) (2)--&gt;</span>
		<span class="token comment">&lt;!-- collaborators and configuration for this bean go here --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- collaborators and configuration for this bean go here --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- more bean definitions go here --&gt;</span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<table>
<tbody><tr>
<td><i class="conum"></i><b>1</b></td>
<td><code>id</code> 속성은 개별 빈 정의를 식별하는 문자열입니다.</td>
</tr>
<tr>
<td><i class="conum"></i><b>2</b></td>
<td><code>class</code> 속성은 빈의 유형을 정의하고 정규화된 클래스 이름을 사용합니다.
</td>
</tr>
</tbody></table>
<p><code>id</code> 속성의 값은 공동 작업 개체를 참조하는 데 사용될 수 있습니다. 이 예에는 공동 작업 개체를 참조하기 위한 XML이 표시되지 않습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies.html">종속성</a>을 참조하세요.</p>
<h1 id="instantiating-a-container">Instantiating a Container</h1>
<hr>
<p><img src="https://programming.vip/images/doc/40cd9dc7c95f0ce5d2047f2ae97b8c86.jpg"><img src="https://programming.vip/images/doc/d3b66db55373bda053bc58e8fde2e52f.jpg"></p>
<hr>
<p><code>ApplicationContext</code> 생성자에 제공(supplied)된 위치 경로(location path or paths)는 컨테이너가 로컬 파일 시스템, Java <code>CLASSPATH</code> 등과 같은 다양한 외부 리소스(resource)에서 구성 메타데이터를 로드할 수 있도록 하는 리소스 문자열(resource string)입니다.</p>
<pre><code class="language-java"><span class="token class-name">ApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"services.xml"</span><span class="token punctuation">,</span> <span class="token string">"daos.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
Spring의 IoC 컨테이너에 대해 배운 후에는 URI 구문(syntax)에 정의된 위치에서 InputStream을 읽기 위한 편리한 메커니즘을 제공하는 Spring의 <code>Resource</code> 추상화(<a href="https://docs.spring.io/spring-framework/reference/core/resources.html">Resource</a>에 설명됨)에 대해 더 알고 싶을 수도 있습니다. 특히 <code>Resource</code> 경로(paths)는 <a href="https://docs.spring.io/spring-framework/reference/core/resources.html#resources-app-ctx">애플리케이션 컨텍스트 및 리소스 경로</a>에 설명된 대로 애플리케이션 컨텍스트를 구성하는 데 사용됩니다.</p>
</blockquote>
<p>다음 예에서는 서비스 계층 객체<code>(services.xml)</code> 구성 파일을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- services --&gt;</span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>petStore<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.jpetstore.services.PetStoreServiceImpl<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountDao<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountDao<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>itemDao<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>itemDao<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token comment">&lt;!-- additional collaborators and configuration for this bean go here --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- more bean definitions for services go here --&gt;</span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 예에서는 데이터 액세스 객체 <code>daos.xml</code> 파일을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountDao<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- additional collaborators and configuration for this bean go here --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>itemDao<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.jpetstore.dao.jpa.JpaItemDao<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- additional collaborators and configuration for this bean go here --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- more bean definitions for data access objects go here --&gt;</span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예에서 서비스 계층은 <code>PetStoreServiceImpl</code> 클래스와 <code>JpaAccountDao</code> 및 <code>JpaItemDao</code> 타입(JPA 객체 관계형 매핑 표준 기반)의 두 데이터 액세스 객체로 구성됩니다. <code>property name</code> 요소는 JavaBean 속성의 이름을 참조하고, <code>ref</code> 요소는 다른 빈 정의의 이름을 참조합니다. <code>id</code>와 <code>ref</code> 요소 간의 이러한 연결은 공동 작업 개체 간의 종속성을 표현합니다. 개체의 종속성 구성에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies.html">종속성</a>을 참조하세요.</p>
<h1 id="composing-xml-based-configuration-metadata">Composing XML-based Configuration Metadata</h1>
<p>여러 XML 파일에 걸쳐 빈 정의를 갖는 것이 유용할 수 있습니다. 각 개별 XML 구성 파일은 아키텍처의 논리적 계층이나 모듈을 나타내는 경우가 많습니다.</p>
<p>애플리케이션 컨텍스트 생성자를 사용하여 이러한 모든 XML 조각(fragment)에서 Bean 정의를 로드할 수 있습니다. 이 생성자는 이전 섹션에 표시된 대로 여러 <code>Resource</code> 위치(location)를 사용합니다. 대안으로, 하나 이상의 <code>&lt;import/&gt;</code> 요소를 사용하여 다른 파일에서 Bean 정의를 로드하십시오. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>import</span> <span class="token attr-name">resource</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>services.xml<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>import</span> <span class="token attr-name">resource</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>resources/messageSource.xml<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>import</span> <span class="token attr-name">resource</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/resources/themeSource.xml<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>bean1<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>bean2<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이전 예제에서 외부 Bean 정의는 <code>services.xml</code>, <code>messageSource.xml</code> 및 <code>themeSource.xml</code>의 세 가지 파일에서 로드됩니다. 모든 위치 경로는 가져오기를 수행하는 정의 파일에 상대적이므로 <code>services.xml</code>은 가져오기를 수행하는 파일과 동일한 디렉터리 또는 클래스 경로 위치에 있어야 하고 <code>messageSource.xml</code> 및 <code>themeSource.xml</code>은 해당 가져오기 파일 위치 아래의 <code>resources</code> 위치에 있어야 합니다. 보시다시피 선행 슬래시(leading slash)는 무시됩니다. 그러나 이러한 경로는 상대적이므로 슬래시를 전혀 사용하지 않는 것이 더 좋습니다. 최상위 레벨 <code>&lt;beans/&gt;</code> 요소를 포함하여 가져오는 파일의 컨텐츠는 Spring 스키마에 따라 유효한 XML Bean 정의여야 합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
상대 "../" 경로를 사용하여 상위 디렉터리의 파일을 참조하는 것이 가능하지만 권장되지는 않습니다. 이렇게 하면 현재 애플리케이션 외부에 있는 파일에 대한 종속성이 생성됩니다. 특히 이 참조는 런타임 확인(resolution) 프로세스가 "가장 가까운" 클래스 경로 루트를 선택한 다음 해당 상위 디렉터리를 조사하는 <code>classpath:</code> URL(예: <code>classpath:../services.xml</code>)에는 권장되지 않습니다. 클래스 경로 구성 변경으로 인해 다른 잘못된 디렉터리가 선택될 수 있습니다.</p>
<p>상대 경로 대신 항상 정규화된 리소스 위치를 사용할 수 있습니다(예: <code>file:C:/config/services.xml</code> 또는 <code>classpath:/config/services.xml</code>). 그러나 애플리케이션 구성을 특정 절대 위치에 결합(coupling)하고 있다는 점에 유의하세요. 일반적으로 런타임 시 JVM 시스템 속성에 대해 확인(resolve)되는 "${…​}" 자리 표시자(placeholder)를 통해 이러한 절대 위치에 대한 간접 참조(indirection)를 유지하는 것이 좋습니다.</p>
</blockquote>
<p>네임스페이스 자체는 import 지시문 기능을 제공합니다. 일반 빈 정의 이상의 추가 구성 기능은 Spring이 제공하는 XML 네임스페이스 selection(예: <code>context</code> 및 <code>util</code> 네임스페이스)에서 사용할 수 있습니다.</p>
<h1 id="the-groovy-bean-definition-dsl">The Groovy Bean Definition DSL</h1>
<p>외부화된 구성 메타데이터에 대한 추가 예로서, Grails 프레임워크에서 알려진 것처럼 Bean 정의는 Spring의 Groovy Bean 정의 DSL로 표현될 수도 있습니다. 일반적으로 이러한 구성은 다음 예에 표시된 구조를 사용하여 ".groovy" 파일에 있습니다.</p>
<pre><code class="language-groovy">beans {
	dataSource(BasicDataSource) {
		driverClassName = "org.hsqldb.jdbcDriver"
		url = "jdbc:hsqldb:mem:grailsDB"
		username = "sa"
		password = ""
		settings = [mynew:"setting"]
	}
	sessionFactory(SessionFactory) {
		dataSource = dataSource
	}
	myService(MyService) {
		nestedBean = { AnotherBean bean -&gt;
			dataSource = dataSource
		}
	}
}</code></pre>
<p>이 구성 스타일은 XML Bean 정의와 거의 동일하며 Spring의 XML 구성 네임스페이스도 지원합니다. 또한 <code>importBeans</code> 지시어를 통해 XML 빈 정의 파일을 가져올 수 있습니다.</p>
<h1 id="using-the-container">Using the Container</h1>
<p><code>ApplicationContext</code>는 다양한 빈의 레지스트리와 해당 종속성을 유지 관리할 수 있는 고급 팩토리를 위한 인터페이스입니다. <code>T getBean(String name, Class&lt;T&gt; requiredType)</code> 메소드를 사용하여 Bean의 인스턴스를 검색할 수 있습니다.</p>
<p><code>ApplicationContext</code>를 사용하면 다음 예제와 같이 Bean 정의를 읽고 액세스할 수 있습니다.</p>
<pre><code class="language-java"><span class="token comment">// create and configure beans</span>
<span class="token class-name">ApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"services.xml"</span><span class="token punctuation">,</span> <span class="token string">"daos.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// retrieve configured instance</span>
<span class="token class-name">PetStoreService</span> service <span class="token operator">=</span> context<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token string">"petStore"</span><span class="token punctuation">,</span> <span class="token class-name">PetStoreService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// use configured instance</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> userList <span class="token operator">=</span> service<span class="token punctuation">.</span><span class="token function">getUsernameList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>Groovy 구성을 사용하면 부트스트래핑이 매우 유사해 보입니다. 이것은 Groovy를 인식하는(그러나 XML Bean 정의도 이해하는) 다른 컨텍스트 구현 클래스를 가지고 있습니다. 다음 예에서는 Groovy 구성을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">ApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GenericGroovyApplicationContext</span><span class="token punctuation">(</span><span class="token string">"services.groovy"</span><span class="token punctuation">,</span> <span class="token string">"daos.groovy"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>가장 유연한 변형은 판독기 대리자(reader delegates)와 결합된 <code>GenericApplicationContext</code>입니다. 예를 들어 다음 예제에서 볼 수 있듯이 XML 파일용 <code>XmlBeanDefinitionReader</code>를 사용합니다.</p>
<pre><code class="language-java"><span class="token class-name">GenericApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GenericApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">new</span> <span class="token class-name">XmlBeanDefinitionReader</span><span class="token punctuation">(</span>context<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">loadBeanDefinitions</span><span class="token punctuation">(</span><span class="token string">"services.xml"</span><span class="token punctuation">,</span> <span class="token string">"daos.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
context<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 Groovy 파일에 <code>GroovyBeanDefinitionReader</code>를 사용할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">GenericApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GenericApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">new</span> <span class="token class-name">GroovyBeanDefinitionReader</span><span class="token punctuation">(</span>context<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">loadBeanDefinitions</span><span class="token punctuation">(</span><span class="token string">"services.groovy"</span><span class="token punctuation">,</span> <span class="token string">"daos.groovy"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
context<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다양한 구성 소스에서 Bean 정의를 읽어 동일한 <code>ApplicationContext</code>에서 이러한 판독기 대리자(reader delegates)를 혼합하고 일치시킬 수 있습니다.</p>
<p>그런 다음 <code>getBean</code>을 사용하여 Bean의 인스턴스를 검색할 수 있습니다. <code>ApplicationContext</code> 인터페이스에는 Bean을 검색하기 위한 몇 가지 다른 메소드가 있지만 이상적으로는 애플리케이션 코드에서 이를 사용해서는 안 됩니다. 실제로 애플리케이션 코드에는 <code>getBean()</code> 메서드에 대한 호출이 전혀 없어야 하며 따라서 Spring API에 전혀 종속성이 없어야 합니다. 예를 들어 Spring의 웹 프레임워크 통합(integration)은 컨트롤러 및 JSF 관리 빈과 같은 다양한 웹 프레임워크 구성요소에 대한 종속성 주입을 제공하여 메타데이터(예: autowrire 어노테이션)를 통해 특정 빈에 대한 종속성을 선언할 수 있게 해줍니다.</p>