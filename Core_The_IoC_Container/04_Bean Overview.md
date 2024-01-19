<p>Spring IoC 컨테이너는 하나 이상의 Bean을 관리합니다. 이러한 Bean은 컨테이너에 제공하는 구성 메타데이터(예: XML <code>&lt;bean/&gt;</code> 정의 형식)를 사용하여 생성됩니다.</p>
<p>컨테이너 자체 내에서 이러한 빈 정의는 다음 메타데이터를 포함하는 <code>BeanDefinition</code> 객체로 표시됩니다.</p>
<ul>
<li>
<p>패키지 한정(package-qualified) 클래스 이름: 일반적으로 정의되는 Bean의 실제 구현 클래스입니다.</p>
</li>
<li>
<p>Bean이 컨테이너에서 어떻게 작동해야 하는지를 명시하는 Bean 동작 구성(behavioral configuration) 요소(scope, lifecycle 콜백 등).</p>
</li>
<li>
<p>Bean이 작업을 수행하는 데 필요한 다른 Bean에 대한 참조입니다. 이러한 참조를 협력자(collaborators) 또는 종속성(dependencies)이라고도 합니다.</p>
</li>
<li>
<p>새로 생성된 객체에 설정할 기타 구성 설정(예: pool의 크기 제한 또는 connection pool을 관리하는 Bean에서 사용할 connection 수)</p>
</li>
</ul>
<p>이 메타데이터는 각 빈 정의를 구성하는 속성 집합(properties set)으로 변환됩니다. 다음 표에서는 이러한 속성을 설명합니다.</p>
<table id="beans-factory-bean-definition-tbl" class="tableblock frame-all grid-all stretch">
<caption class="title">Table 1. The bean definition</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">Property</th>
<th class="tableblock halign-left valign-top">Explained in…​</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Class</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-class">Instantiating Beans</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Name</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-beanname">Naming Beans</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scope</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="factory-scopes.html" class="xref page">Bean Scopes</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Constructor arguments</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="dependencies/factory-collaborators.html" class="xref page">Dependency Injection</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Properties</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="dependencies/factory-collaborators.html" class="xref page">Dependency Injection</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Autowiring mode</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="dependencies/factory-autowire.html" class="xref page">Autowiring Collaborators</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Lazy initialization mode</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="dependencies/factory-lazy-init.html" class="xref page">Lazy-initialized Beans</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Initialization method</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="factory-nature.html#beans-factory-lifecycle-initializingbean" class="xref page">Initialization Callbacks</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">Destruction method</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="factory-nature.html#beans-factory-lifecycle-disposablebean" class="xref page">Destruction Callbacks</a></p></td>
</tr>
</tbody>
</table>
<p>특정 bean을 생성하는 방법에 대한 정보를 포함하는 bean 정의 외에도 <code>ApplicationContext</code> 구현은 (사용자가) 컨테이너 외부에서 생성한 기존 객체의 등록(registration)을 허용합니다. 이는 <code>DefaultListableBeanFactory</code> 구현을 반환하는 <code>getBeanFactory()</code> 메서드를 통해 ApplicationContext의 <code>BeanFactory</code>에 액세스하여 수행됩니다. <code>DefaultListableBeanFactory</code>는 <code>registerSingleton(..)</code> 및 <code>registerBeanDefinition(..)</code> 메소드를 통해 이 등록을 지원합니다. 그러나 일반적인 애플리케이션은 일반 Bean 정의 메타데이터를 통해 정의된 Bean에서만 작동(work solely)합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Bean 메타데이터와 수동으로 제공(supplied)되는 싱글톤 인스턴스는 가능한 한 빨리 등록해야 컨테이너가 자동 연결(autowiring) 및 기타 내부 검사(introspection) 단계 중에 이에 대해 적절하게 추론(reason)할 수 있습니다. 기존 메타데이터 및 기존 싱글톤 인스턴스를 재정의하는 것은 어느 정도 지원되지만 런타임 시 새 Bean 등록(factory에 대한 실시간 액세스와 동시에)은 공식적으로 지원되지 않으며 동시 액세스 예외, Bean 컨테이너의 일관되지 않은 상태로 이어질 수 있습니다.</p>
</blockquote>
<h1 id="naming-beans">Naming Beans</h1>
<p>모든 Bean에는 하나 이상의 식별자(identifier)가 있습니다. 이러한 식별자는 Bean을 호스팅하는 컨테이너 내에서 고유해야 합니다. Bean에는 일반적으로 하나의 식별자만 있습니다. 그러나 둘 이상이 필요한 경우 추가 항목은 별칭(aliases)으로 간주될 수 있습니다.</p>
<p>XML 기반 구성 메타데이터에서는 <code>id</code> 속성(attribute), <code>name</code> 속성 또는 둘 다를 사용하여 Bean 식별자를 지정합니다. <code>id</code> 속성을 사용하면 정확히 하나의 <code>id</code>를 지정할 수 있습니다. 일반적으로 이러한 이름은 영숫자('myBean', 'someService' 등)이지만 특수 문자도 포함될 수 있습니다. Bean에 다른 별칭을 도입하려는 경우 쉼표(<code>,</code>), 세미콜론(<code>;</code>) 또는 공백으로 구분하여 <code>name</code> 속성에 이를 지정할 수도 있습니다. <code>id</code> 속성이 <code>xsd:string</code> 유형으로 정의되었지만 Bean <code>id</code> 고유성은 XML 파서가 아닌 컨테이너에 의해 강제됩니다.</p>
<p>Bean의 <code>name</code>이나 <code>id</code>를 제공(supply)할 필요는 없습니다. <code>name</code>이나 <code>id</code>를 명시적으로 제공하지 않으면 컨테이너는 해당 Bean에 대한 고유한 이름을 생성합니다. 그러나 <code>ref</code> 요소나 Service Locator 스타일 조회(lookup)를 사용하여 해당 Bean을 이름으로 참조하려면 이름을 제공해야 합니다. 이름을 제공하지 않는 동기는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-properties-detailed.html#beans-inner-beans">inner bean</a> 및 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html">autowiring collaborators</a>를 사용하는 것과 관련이 있습니다.</p>
<blockquote>
<h3 id="빈-명명-규칙convention">빈 명명 규칙(convention)</h3>
<p>규칙은 Bean의 이름을 지정할 때 인스턴스 필드 이름에 표준 Java 규칙을 사용하는 것입니다. 즉, 빈 이름은 소문자로 시작하고 거기서부터 카멜케이스로 표기됩니다. 이러한 이름의 예로는 <code>accountManager</code>, <code>accountService</code>, <code>userDao</code>, <code>loginController</code> 등이 있습니다.</p>
<p>Bean의 이름을 일관되게 지정하면 구성을 더 쉽게 읽고 이해할 수 있습니다. 또한 Spring AOP를 사용하면 이름별로 관련된 Bean 집합(set)에 Advice를 적용할 때 많은 도움이 된다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
classpath에서 컴포넌트 스캐닝을 통해 Spring은 앞서 설명한 규칙에 따라 명명되지 않은 컴포넌트에 대한 빈 이름을 생성합니다. 즉, 첫번째 문자를 소문자로 바꾸는 간단한 클래스 이름을 만듭니다. 그러나 두 개 이상의 문자가 있고 첫 번째 문자와 두 번째 문자가 모두 대문자인 (비정상적인) 특수한 경우에는 원래 대소문자가 유지됩니다. 이는 <code>java.beans.Introspector.decapitalize</code>(Spring이 여기서 사용하는)에 정의된 것과 동일한 규칙입니다.</p>
</blockquote>
<h2 id="aliasing-a-bean-outside-the-bean-definition">Aliasing a Bean outside the Bean Definition</h2>
<p>빈 정의 자체에서 <code>id</code> 속성에 지정된 최대 하나의(up to one) 이름과 <code>name</code> 속성에 있는 다른 이름의 조합을 사용하여 빈에 대해 두 개 이상의 이름을 제공(supply)할 수 있습니다. 이러한 이름은 동일한 Bean에 대한 동등한 별명(equivalent aliases)일 수 있으며 애플리케이션의 각 컴포넌트가 해당 컴포넌트 자체에 특정한 Bean 이름을 사용하여 공통 종속성을 참조하도록 하는 등의 일부 상황에 유용합니다.</p>
<p>그러나 Bean이 실제로 정의된 모든 alias을 지정하는 것이 항상 적절한(adequate) 것은 아닙니다. 때로는 다른 곳에서 정의된 Bean에 대한 별칭을 도입하는 것이 바람직할 때도 있습니다. 이는 일반적으로 구성이 각 하위 시스템으로 분할되고 각 하위 시스템이 자체 객체 정의 세트를 갖는 대규모 시스템의 경우입니다. XML 기반 구성 메타데이터에서는 <code>&lt;alias/&gt;</code> 요소를 사용하여 이를 수행할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>alias</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>fromName<span class="token punctuation">"</span></span> <span class="token attr-name">alias</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>toName<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>이 경우 <code>fromName</code>이라는 이름의 빈(동일한 컨테이너에 있음)은 이 별칭 정의를 사용한 후 toName으로 참조될 수도 있습니다.</p>
<p>예를 들어, 하위 시스템 A의 구성 메타데이터는 subsystemA-dataSource라는 이름으로 DataSource를 참조할 수 있습니다. 하위 시스템 B의 구성 메타데이터는 subsystemB-dataSource라는 이름으로 DataSource를 참조할 수 있습니다. 이 두 하위 시스템을 모두 사용하는 기본 애플리케이션을 구성할 때 기본 애플리케이션은 myApp-dataSource라는 이름으로 DataSource를 참조합니다. 세 가지 이름이 모두 동일한 개체를 참조하도록 하려면 구성 메타데이터에 다음 별칭 정의를 추가하면 됩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>alias</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myApp-dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">alias</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>subsystemA-dataSource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>alias</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myApp-dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">alias</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>subsystemB-dataSource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>이제 각 구성요소와 기본 애플리케이션은 고유한 이름을 통해 dataSource를 참조할 수 있으며 다른 정의(효과적으로 네임스페이스 생성)와 충돌하지 않는 것이 보장되지만 동일한 Bean을 참조합니다.</p>
<p>Java 구성<br>
Java 구성을 사용하는 경우 @Bean 주석을 사용하여 별칭을 제공할 수 있습니다. 자세한 내용은 @Bean 주석 사용을 참조하세요.</p>
<h1 id="instantiating-beans">Instantiating Beans</h1>
<p>Bean 정의는 본질적으로 하나 이상의 객체를 생성하기 위한 레시피입니다. 컨테이너는 요청 받았을 시 named Bean에 대한 레시피를 살펴보고 해당 Bean 정의로 캡슐화된 구성 메타데이터를 사용하여 실제 객체를 생성(또는 획득)합니다.</p>
<p>XML 기반 구성 메타데이터를 사용하는 경우 <code>&lt;bean/&gt;</code> 요소의 <code>class</code> 속성(attribute)에 인스턴스화할 객체의 type(또는 class)을 지정합니다. 이 <code>class</code> 속성(attribute)(내부적으로 <code>BeanDefinition</code> 인스턴스의 <code>Class</code> 속성(property))은 일반적으로 필수입니다. (예외에 대해서는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class-instance-factory-method">인스턴스 팩토리 메소드를 사용하여 인스턴스화</a> 및 <a href="https://docs.spring.io/spring-framework/reference/core/beans/child-bean-definitions.html">Bean 정의 상속</a>을 참조하십시오.) 다음 두 가지 방법 중 하나로 <code>Class</code> 특성을 사용할 수 있습니다.</p>
<ul>
<li>
<p>일반적으로 컨테이너 자체가 생성자를 reflectively 호출하여 Bean을 직접 생성하는 경우 생성될 Bean 클래스를 지정하는 것은 <code>new</code> 연산자를 사용하는 Java 코드와 다소 동등합니다.</p>
</li>
<li>
<p>객체를 생성하기 위해 호출되는 <code>static</code> 팩토리 메서드를 포함하는 실제 클래스를 지정하는 것은, 컨테이너가 Bean을 생성하기 위해 클래스에서 <code>static</code> 팩토리 메서드를 호출하는 흔하지 않은 경우입니다. <code>static</code> 팩토리 메소드 호출에서 반환된 객체 유형은 동일한 클래스일 수도 있고 완전히 다른 클래스일 수도 있습니다.</p>
</li>
</ul>
<blockquote>
<h3 id="중첩된-클래스-이름">중첩된 클래스 이름</h3>
<p>중첩 클래스에 대한 빈 정의를 구성하려면 바이너리 이름이나 중첩 클래스의 소스 이름을 사용할 수 있습니다.</p>
<p>예를 들어 <code>com.example</code> 패키지에 <code>SomeThing</code>이라는 클래스가 있고 이 <code>SomeThing</code> 클래스에 <code>OtherThing</code>이라는 정적 중첩 클래스가 있는 경우 달러 기호(<code>$</code>) 또는 점(<code>.</code>)으로 구분할 수 있습니다. 따라서 Bean 정의의 <code>class</code> 속성 값은 <code>com.example.SomeThing$OtherThing</code> 또는 <code>com.example.SomeThing.OtherThing</code>이 됩니다.</p>
</blockquote>
<h1 id="instantiation-with-a-constructor">Instantiation with a Constructor</h1>
<p>생성자 접근 방식으로 빈을 생성하면 모든 일반 클래스는 Spring에서 사용 가능하고 호환 가능합니다. 즉, 개발 중인 클래스는 특정 인터페이스를 구현하거나 특정 방식으로 코딩할 필요가 없습니다. 단순히 빈 클래스를 지정하는 것만으로도 충분(suffice)합니다. 그러나 특정 Bean에 사용하는 IoC 유형에 따라 기본(비어 있는) 생성자가 필요할 수 있습니다.</p>
<p>Spring IoC 컨테이너는 관리하려는 사실상(virtually) 모든 클래스를 관리할 수 있습니다. 이는 실제 JavaBeans 관리에만 국한되지 않습니다. 대부분의 Spring 사용자는 기본(인수 없음) 생성자와 컨테이너의 속성(property)을 따라 모델링된 적절한 setter 및 getter만 있는 실제 JavaBeans를 선호합니다. 컨테이너에 더 이국적인 non-bean 스타일 클래스를 가질 수도 있습니다. 예를 들어 JavaBean 사양을 전혀 준수(adhere)하지 않는 레거시 connection pool을 사용해야 하는 경우 Spring이 이를 관리할 수도 있습니다.</p>
<p>XML 기반 구성 메타데이터를 사용하면 다음과 같이 Bean 클래스를 지정할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExample<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBeanTwo<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>생성자에 인수를 제공하고(필요한 경우) 개체가 생성된 후 개체 인스턴스 속성을 설정하는 메커니즘에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html">종속성 주입</a>을 참조하세요.</p>
<hr>
<p><strong>Bean Factory</strong><br>
<img src="https://kospiotr.github.io/resources/wiki/spring/bean-factory.png"></p>
<p><strong>Application Context</strong><br>
<img src="https://kospiotr.github.io/resources/wiki/spring/application-context.png"></p>
<p><strong>BeanFactory and ApplicationContext</strong><br>
<img src="https://lh3.googleusercontent.com/-Gu2o72TfYQ8/YGXWe93O0QI/AAAAAAAAm0U/jslu8obsxcYQ0DcMBdtY2pqBRLh77MfuQCLcBGAsYHQ/w483-h308/image.png"></p>
<p><strong>Beans</strong><br>
<img src="https://kospiotr.github.io/resources/wiki/spring/beans.png"></p>
<p><strong>Factory Method Design Pattern</strong><br>
<img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*MftqA5sum-N1fnewmj4-Ow.png"></p>
<h1 id="instantiation-with-a-static-factory-method">Instantiation with a Static Factory Method</h1>
<p>정적 팩토리 메소드를 사용하여 생성한 Bean을 정의할 때 <code>class</code> 속성(attribute)을 사용하여 <code>static</code> 팩토리 메소드가 포함된 클래스를 지정하고, 팩토리 메소드 자체의 이름을 지정하려면 <code>factory-method</code>라는 속성(attribute)을 사용하십시오. 선택적 인수를 사용하여 이 메서드를 호출하고 살아있는 객체(live object)를 반환할 수 있어야 하며, 이후(subsequently) 이 개체는 생성자를 통해 생성된 것처럼 처리됩니다. 이러한 Bean 정의의 용도 중 하나는 레거시 코드에서 <code>static</code> 팩토리를 호출하는 것입니다.</p>
<p>다음 빈 정의는 빈이 팩토리 메소드를 호출하여 생성되도록 지정합니다. 정의는 반환된 객체의 타입(class)을 지정하는 것이 아니라 팩토리 메서드를 포함하는 클래스를 지정합니다. 이 예에서 <code>createInstance()</code> 메서드는 <code>static</code> 메서드여야 합니다. 다음 예에서는 팩토리 메서드를 지정하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>clientService<span class="token punctuation">"</span></span>
	<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ClientService<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createInstance<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예제는 이전 Bean 정의와 함께 작동하는 클래스를 보여줍니다.</p>
<p>팩토리 메서드에 (선택적) 인수를 제공하고 개체가 팩토리에서 반환된 후 개체 인스턴스 속성을 설정하는 메커니즘에 대한 자세한 내용은 세부 종속성 및 구성을 참조하세요.</p>
<h1 id="instantiation-by-using-an-instance-factory-method">Instantiation by Using an Instance Factory Method</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class-static-factory-method">정적 팩토리 메소드</a>를 통한 인스턴스화와 유사하게, 인스턴스 팩토리 메소드를 사용한 인스턴스화는 컨테이너에서 기존 Bean의 non-static 메소드를 호출하여 새 Bean을 생성합니다. 이 메커니즘을 사용하려면 <code>class</code> 속성(attribute)을 비워두고 <code>factory-bean</code> 속성(attribute)에서 객체를 생성하기 위해 호출될 인스턴스 메서드가 포함된 현재(또는 상위 또는 조상) 컨테이너의 Bean 이름을 지정합니다. <code>factory-method</code> 속성(attribute)을 사용하여 팩토리 메소드 자체의 이름을 설정합니다. 다음 예에서는 이러한 Bean을 구성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token comment">&lt;!-- the factory bean, which contains a method called createClientServiceInstance() --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceLocator<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.DefaultServiceLocator<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- inject any dependencies required by this locator bean --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token comment">&lt;!-- the bean to be created via the factory bean --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>clientService<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceLocator<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createClientServiceInstance<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예에서는 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultServiceLocator</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">ClientService</span> clientService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClientServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">ClientService</span> <span class="token function">createClientServiceInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> clientService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제에서 볼 수 있듯이 하나의 팩토리 클래스는 둘 이상의 팩토리 메서드를 보유할 수도 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceLocator<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.DefaultServiceLocator<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- inject any dependencies required by this locator bean --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>clientService<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceLocator<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createClientServiceInstance<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>accountService<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceLocator<span class="token punctuation">"</span></span>
	<span class="token attr-name">factory-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createAccountServiceInstance<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예에서는 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultServiceLocator</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">ClientService</span> clientService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClientServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">AccountService</span> accountService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AccountServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">ClientService</span> <span class="token function">createClientServiceInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> clientService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">AccountService</span> <span class="token function">createAccountServiceInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> accountService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 접근 방식은 DI(종속성 주입)를 통해 factory bean 자체를 관리하고 구성할 수 있음을 보여줍니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-properties-detailed.html">종속성 및 구성</a>을 참조하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring 문서에서 "factory bean"은 Spring 컨테이너에 구성되고 <a href="https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class-instance-factory-method">instance</a> 또는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class-static-factory-method">static</a> 팩토리 메소드를 통해 객체를 생성하는 빈을 의미합니다. 대조적으로, <code>FactoryBean</code>(대문자 사용에 주의)은 Spring 특정 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-factorybean"><code>FactoryBean</code></a> 구현 클래스를 참조합니다.</p>
</blockquote>
<h1 id="determining-a-beans-runtime-type">Determining a Bean's Runtime Type</h1>
<p>특정 Bean의 런타임 유형을 결정하는 것은 쉽지 않습니다(non-trivial). Bean 메타데이터 정의에 지정된 클래스는 잠재적으로 선언된 팩토리 메소드와 결합되거나 Bean의 다른 런타임 유형으로 이어질 수 있는 <code>FactoryBean</code> 클래스이거나  instance-level 팩토리 메서드(대신 지정된 <code>factory-bean</code> 이름을 통해 resolve 됨)일 경우 전혀 설정되지 않는 초기 클래스 참조일 뿐입니다. 또한 AOP 프록시는 대상 Bean의 실제 유형(구현된 인터페이스만)을 제한적으로 노출하여 인터페이스 기반 프록시로 Bean 인스턴스를 래핑할 수 있습니다.</p>
<hr>
<p><strong>AOP proxy</strong></p>
<p><img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*V89Ix8Xx0IfA8l6yUF2g7A.png"></p>
<hr>
<p>특정 빈의 실제 런타임 유형을 알아내는 권장 방법은 지정된 빈 이름에 대한 <code>BeanFactory.getType</code> 호출입니다. 이는 위의 모든 경우를 고려(take into accout)하고 <code>BeanFactory.getBean</code> 호출이 동일한 Bean 이름에 대해 리턴할 객체 유형을 리턴합니다.</p>