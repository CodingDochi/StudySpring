<p>데이터 바인딩은 사용자 입력이 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans-conventions">JavaBeans 규칙</a>에 따라 속성(property) 경로를 키로 사용하는 맵인 대상 객체에 사용자 입력을 바인딩하는 데 유용합니다. <code>DataBinder</code>는 이를 지원하는 기본 클래스이며 사용자 입력을 바인딩하는 두 가지 방법을 제공합니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-constructor-binding">생성자 바인딩(Constructor binding)</a> - 사용자 입력을 공용 데이터 생성자에 바인딩하여 사용자 입력에서 생성자 인수 값을 찾습니다.</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans">속성 바인딩(Property binding)</a> - 사용자 입력을 setter에 바인딩하여 사용자 입력의 키를 대상 개체 구조의 속성(property)과 일치시킵니다.</p>
</li>
</ul>
<p>생성자와 속성(property) 바인딩을 모두 적용하거나 하나만 적용할 수 있습니다.</p>
<h1 id="constructor-binding">Constructor Binding</h1>
<p>생성자 바인딩을 사용하려면 다음을 수행하세요.</p>
<ol>
<li>
<p><code>null</code>을 대상 객체로 사용하여 <code>DataBinder</code>를 만듭니다.</p>
</li>
<li>
<p><code>targetType</code>을 대상 클래스로 설정합니다.</p>
</li>
<li>
<p><code>construct</code> 호출.</p>
</li>
</ol>
<p>대상 클래스에는 단일 public 생성자 또는 인수가 있는 단일 non-public 생성자가 있어야 합니다. 생성자가 여러 개 있는 경우 기본 생성자가 있는 경우 해당 생성자가 사용됩니다.</p>
<p>기본적으로 생성자 매개변수 이름은 인수 값을 조회하는 데 사용되지만 <code>NameResolver</code>를 구성(configure)할 수 있습니다. Spring MVC와 WebFlux는 모두 생성자 매개변수의 <code>@BindParam</code> 어노테이션을 통해 바인딩할 값의 이름을 사용자 정의할 수 있도록 허용합니다.</p>
<p>사용자 입력을 변환하기 위해 필요에 따라 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans-conventions">타입 변환</a>이 적용됩니다. 생성자 매개변수가 객체인 경우 동일한 방식으로 재귀적으로 생성되지만 중첩된 속성(property) 경로를 통해 생성됩니다. 이는 생성자 바인딩이 대상 객체와 여기에 포함된 모든 객체를 모두 생성한다는 의미입니다.</p>
<p>바인딩 및 변환 오류는 <code>DataBinder</code>의 <code>BindingResult</code>에 반영됩니다. 대상이 성공적으로 생성되면 <code>construct</code> 호출 후 <code>target</code>이 생성된 인스턴스로 설정됩니다.</p>
<h1 id="property-binding-with-beanwrapper">Property Binding with <code>BeanWrapper</code></h1>
<p><code>org.springframework.beans</code> 패키지는 JavaBeans 표준을 준수합니다. JavaBean은 인수가 없는 기본 생성자를 가진 클래스이며, 예를 들어 <code>bingoMadness</code>라는 속성에 setter 메서드 <code>setBingoMadness(..)</code>와 getter 메서드 <code>getBingoMadness()</code>가 있는 명명 규칙을 따릅니다. JavaBeans 및 사양에 대한 자세한 내용은 <a href="https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/java/beans/package-summary.html">javabeans</a>를 참조하세요.</p>
<p>Bean 패키지에서 매우 중요한 클래스 중 하나는 <code>BeanWrapper</code> 인터페이스와 해당 구현(<code>BeanWrapperImpl</code>)입니다. javadoc에서 인용된 대로 <code>BeanWrapper</code>는 속성(property) 값(개별적으로 또는 대량으로(in bulk))을 설정(set) 및 가져오고(get), 속성 설명자(property descriptor)를 가져오고, 속성(property)을 쿼리하여 읽기 또는 쓰기 가능한지 확인(determine)하는 기능을 제공합니다. 또한 <code>BeanWrapper</code>는 중첩된 속성(property)에 대한 지원을 제공하여 하위 속성에 대한 속성을 무제한으로 설정할 수 있습니다. <code>BeanWrapper</code>는 또한 대상 클래스의 지원 코드 없이도 표준 JavaBeans <code>PropertyChangeListeners</code> 및 <code>VetoableChangeListeners</code>를 추가하는 기능을 지원합니다. 마지막으로 <code>BeanWrapper</code>는 색인화된 속성(indexed property) 설정을 지원합니다. <code>BeanWrapper</code>는 일반적으로 애플리케이션 코드에서 직접 사용되지 않고 <code>DataBinder</code> 및 <code>BeanFactory</code>에서 사용됩니다.</p>
<p><code>BeanWrapper</code>가 작동하는 방식은 부분적으로 이름으로 표시됩니다. 즉, <code>BeanWrapper</code>는 속성 설정 및 검색과 같은 해당 Bean에 대한 작업을 수행하기 위해 Bean을 래핑합니다.</p>
<h2 id="setting-and-getting-basic-and-nested-properties">Setting and Getting Basic and Nested Properties</h2>
<p>속성(property) 설정 및 가져오기는 <code>BeanWrapper</code>의 <code>setPropertyValue</code> 및 <code>getPropertyValue</code> 오버로드된 메서드 변형을 통해 수행됩니다. 자세한 내용은 해당 Javadoc을 참조하세요. 아래 표는 이러한 규칙의 몇 가지 예를 보여줍니다.</p>
<table id="beans-beans-conventions-properties-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">속성 예시 표</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">표현식</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>name</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>getName()</code> 또는 <code>isName()</code> 및 <code>setName(..)</code> 메서드에 해당하는 <code>name</code> 속성을 나타냅니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>account.name</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>account</code> 속성의 중첩된 <code>name</code> 속성을 나타냅니다. (예: <code>getAccount().setName()</code> 또는 <code>getAccount().getName()</code> 메서드에 해당)</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>account[2]</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">인덱스 속성 <code>account</code>의 <em>세 번째</em> 요소를 나타냅니다. 인덱스 속성은 <code>array</code>, <code>list</code> 또는 기타 자연스러운 순서의 컬렉션일 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>account[COMPANYNAME]</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>account</code> <code>Map</code> 속성의 <code>COMPANYNAME</code> 키로 인덱싱된 맵 항목의 값입니다.</p></td>
</tr>
</tbody>
</table>
<p>(<code>BeanWrapper</code>를 직접 사용하여 작업할 계획이 없다면 다음 섹션은 그다지 중요하지 않습니다. <code>DataBinder</code>와 <code>BeanFactory</code> 및 기본 구현만 사용하는 경우 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans-conversion"><code>PropertyEditors</code> 섹션</a>으로 건너뛰어야 합니다.)</p>
<p>다음 두 예제 클래스는 <code>BeanWrapper</code>를 사용하여 속성을 가져오고 설정합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Company</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">Employee</span> managingDirector<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">Employee</span> <span class="token function">getManagingDirector</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>managingDirector<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setManagingDirector</span><span class="token punctuation">(</span><span class="token class-name">Employee</span> managingDirector<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>managingDirector <span class="token operator">=</span> managingDirector<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Employee</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">float</span> salary<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">float</span> <span class="token function">getSalary</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> salary<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setSalary</span><span class="token punctuation">(</span><span class="token keyword">float</span> salary<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>salary <span class="token operator">=</span> salary<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 코드 조각은 인스턴스화된 <code>Company</code> 및 <code>Employee</code>의 일부 속성을 검색하고 조작하는 방법에 대한 몇 가지 예를 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">BeanWrapper</span> company <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BeanWrapperImpl</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Company</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// setting the company name..</span>
company<span class="token punctuation">.</span><span class="token function">setPropertyValue</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">,</span> <span class="token string">"Some Company Inc."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// ... can also be done like this:</span>
<span class="token class-name">PropertyValue</span> value <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PropertyValue</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">,</span> <span class="token string">"Some Company Inc."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
company<span class="token punctuation">.</span><span class="token function">setPropertyValue</span><span class="token punctuation">(</span>value<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// ok, let's create the director and tie it to the company:</span>
<span class="token class-name">BeanWrapper</span> jim <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BeanWrapperImpl</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Employee</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
jim<span class="token punctuation">.</span><span class="token function">setPropertyValue</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">,</span> <span class="token string">"Jim Stravinsky"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
company<span class="token punctuation">.</span><span class="token function">setPropertyValue</span><span class="token punctuation">(</span><span class="token string">"managingDirector"</span><span class="token punctuation">,</span> jim<span class="token punctuation">.</span><span class="token function">getWrappedInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// retrieving the salary of the managingDirector through the company</span>
<span class="token class-name">Float</span> salary <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Float</span><span class="token punctuation">)</span> company<span class="token punctuation">.</span><span class="token function">getPropertyValue</span><span class="token punctuation">(</span><span class="token string">"managingDirector.salary"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h1 id="propertyeditors"><code>PropertyEditor</code>'s</h1>
<p>Spring은 <code>Object</code>와 <code>String</code> 사이의 변환에 영향을 주기 위해 <code>PropertyEditor</code>의 개념을 사용합니다. 객체 자체와는 다른 방식으로 속성(property)을 표현하는 것이 편리할 수 있습니다. 예를 들어, <code>Date</code>는 사람이 읽을 수 있는 방식(<code>String</code>: <code>'2007-14-09'</code>)으로 표시할 수 있지만 사람이 읽을 수 있는 형식을 다시 원래 날짜로 변환할 수 있습니다(또는 더 나은 방법으로, 사람이 읽을 수 있는 형식으로 입력된 임의의 날짜를 다시 <code>Date</code> 객체로 변환할 수 있음). 이 동작은 <code>java.beans.PropertyEditor</code> 타입의 사용자 정의 편집기를 등록하여 수행할 수 있습니다. <code>BeanWrapper</code> 또는 특정 IoC 컨테이너(이전 장에서 언급한 대로)에 사용자 정의 편집기를 등록하면, 속성(property)을 원하는 타입으로 변환하는 방법에 대한 지식을 얻을 수 있습니다. <code>PropertyEditor</code>에 대한 자세한 내용은 <a href="https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/java/beans/package-summary.html">Oracle의 <code>java.beans</code> 패키지에 대한 javadoc</a>를 참조하세요.</p>
<p>Spring에서 속성(property) 편집이 사용되는 몇 가지 예:</p>
<ul>
<li>
<p>Bean의 속성 설정은 <code>PropertyEditor</code> 구현을 사용하여 수행됩니다. XML 파일에서 선언한 일부 빈의 속성(property) 값으로 <code>String</code>을 사용하면 Spring(해당 속성(property)의 설정자(setter)가 <code>Class</code> 매개변수를 갖는 경우)은 <code>ClassEditor</code>를 사용하여 매개변수를 <code>Class</code> 객체로 해석하려고 시도합니다.</p>
</li>
<li>
<p>Spring의 MVC 프레임워크에서 HTTP 요청 매개변수를 구문 분석하는 것은 <code>CommandController</code>의 모든 하위 클래스에서 수동으로 바인딩할 수 있는 모든 종류의 <code>PropertyEditor</code> 구현을 사용하여 수행됩니다.</p>
</li>
</ul>
<p>Spring에는 작업을 쉽게 하기 위해 다수의 내장 <code>PropertyEditor</code> 구현이 있습니다. 그것들은 모두 <code>org.springframework.beans.propertyeditors</code> 패키지에 있습니다. 대부분(다음 표에 표시된 대로 전부는 아님)은 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다. 속성 편집기를 어떤 방식으로 구성(configurable)할 수 있는 경우에도 고유한 변형을 등록하여 기본 변형을 재정의할 수 있습니다. 다음 표는 Spring이 제공하는 다양한 <code>PropertyEditor</code> 구현을 설명합니다.</p>
<table id="beans-beans-property-editors-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 2. 내장된 <code>PropertyEditor</code> 구현 예시</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">클래스</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ByteArrayPropertyEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">바이트 배열에 대한 편집기입니다. 문자열을 해당하는 바이트 표현으로 변환합니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 자동 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ClassEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">클래스를 나타내는 문자열을 실제 클래스로 파싱하고 그 반대로 수행합니다. 클래스를 찾을 수 없는 경우에는 <code>IllegalArgumentException</code>이 발생합니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CustomBooleanEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Boolean</code> 속성을 사용자 정의할 수 있는 속성 편집기입니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록되지만 사용자 정의 편집기의 사용자 정의 인스턴스로 재등록할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CustomCollectionEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">컬렉션에 대한 속성 편집기로, 어떤 소스 <code>Collection</code>을 주어진 대상 <code>Collection</code> 유형으로 변환합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CustomDateEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Date</code>에 대한 사용자 정의 속성 편집기로, 사용자 지정 <code>DateFormat</code>을 지원합니다. 기본적으로 등록되지 않습니다. 필요한 형식으로 사용자가 직접 등록해야 합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>CustomNumberEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">어떤 <code>Number</code> 하위 클래스에 대한 사용자 정의 속성 편집기로, 예를 들어 <code>Integer</code>, <code>Long</code>, <code>Float</code>, 또는 <code>Double</code>입니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록되지만 사용자 정의 편집기의 사용자 정의 인스턴스로 재등록할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>FileEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열을 <code>java.io.File</code> 객체로 변환합니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>InputStreamEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">한 방향 속성 편집기로, 문자열을 받아 중간 <code>ResourceEditor</code> 및 <code>Resource</code>를 통해 <code>InputStream</code>을 생성하여 <code>InputStream</code> 속성을 직접 문자열로 설정할 수 있습니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LocaleEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열을 <code>Locale</code> 객체로 해석하고 그 반대로 수행할 수 있습니다(문자열 형식은 <code>[언어]_[국가]_[변형]</code>입니다). 언더스코어 대신 공백을 구분자로 사용할 수도 있습니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>PatternEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열을 <code>java.util.regex.Pattern</code> 객체로 해석하고 그 반대로 수행할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>PropertiesEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열을 (<code>java.util.Properties</code> 클래스의 javadoc에서 정의한 형식으로 포맷된) <code>Properties</code> 객체로 변환할 수 있습니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>StringTrimmerEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">문자열을 자르는 속성 편집기입니다. 선택적으로 빈 문자열을 <code>null</code> 값으로 변환할 수 있습니다. 기본적으로 등록되지 않으며 사용자가 직접 등록해야 합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>URLEditor</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">URL의 문자열 표현을 실제 <code>URL</code> 객체로 해석합니다. 기본적으로 <code>BeanWrapperImpl</code>에 의해 등록됩니다.</p></td>
</tr>
</tbody>
</table>
<p>Spring은 필요할 수 있는 속성 편집기에 대한 검색 경로를 설정하기 위해 <code>java.beans.PropertyEditorManager</code>를 사용합니다. 검색 경로에는 <code>Font</code>, <code>Color</code> 및 대부분의 기본(primitive) 타칩과 같은 타입에 대한 <code>PropertyEditor</code> 구현이 포함된 <code>sun.bean.editors</code>도 포함됩니다. 표준 JavaBeans 인프라는, <code>PropertyEditor</code> 클래스가 처리하는 클래스와 동일한 패키지에 있고 해당 클래스와 동일한 이름을 가지며 <code>Editor</code>가 추가된 경우, 자동으로 <code>PropertyEditor</code> 클래스를 검색합니다(명시적으로 등록할 필요 없음). 예를 들어, 다음과 같은 클래스 및 패키지 구조가 있을 수 있으며, 이는 <code>SomethingEditor</code> 클래스가 <code>Something</code> 타입 속성(properties)에 대한 <code>PropertyEditor</code>로 인식되고 사용되는 데 충분합니다.</p>
<pre><code class="language-null">com
  chank
    pop
      Something
      SomethingEditor // the PropertyEditor for the Something class</code></pre>
<p>여기서도 표준 <code>BeanInfo</code> JavaBeans 메커니즘을 사용할 수도 있습니다(<a href="https://docs.oracle.com/javase/tutorial/javabeans/advanced/customization.html">여기서</a> 어느 정도 설명됨). 다음 예제에서는 <code>BeanInfo</code> 메커니즘을 사용하여 연관된 클래스의 속성으로 하나 이상의 <code>PropertyEditor</code> 인스턴스를 명시적으로 등록합니다.</p>
<pre><code class="language-null">com
  chank
    pop
      Something
      SomethingBeanInfo // the BeanInfo for the Something class</code></pre>
<p>참조된 <code>SomethingBeanInfo</code> 클래스에 대한 다음 Java 소스 코드는 <code>CustomNumberEditor</code>를 <code>Something</code> 클래스의 <code>age</code> 속성(property)과 연결합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SomethingBeanInfo</span> <span class="token keyword">extends</span> <span class="token class-name">SimpleBeanInfo</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">PropertyDescriptor</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getPropertyDescriptors</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">try</span> <span class="token punctuation">{</span>
			<span class="token keyword">final</span> <span class="token class-name">PropertyEditor</span> numberPE <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">CustomNumberEditor</span><span class="token punctuation">(</span><span class="token class-name">Integer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token class-name">PropertyDescriptor</span> ageDescriptor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PropertyDescriptor</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">,</span> <span class="token class-name">Something</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
				<span class="token annotation punctuation">@Override</span>
				<span class="token keyword">public</span> <span class="token class-name">PropertyEditor</span> <span class="token function">createPropertyEditor</span><span class="token punctuation">(</span><span class="token class-name">Object</span> bean<span class="token punctuation">)</span> <span class="token punctuation">{</span>
					<span class="token keyword">return</span> numberPE<span class="token punctuation">;</span>
				<span class="token punctuation">}</span>
			<span class="token punctuation">}</span><span class="token punctuation">;</span>
			<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">PropertyDescriptor</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> ageDescriptor <span class="token punctuation">}</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">IntrospectionException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">Error</span><span class="token punctuation">(</span>ex<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="custom-propertyeditors">Custom <code>PropertyEditor</code>'s</h2>
<p>Bean 속성을 문자열 값으로 설정할 때 Spring IoC 컨테이너는 궁극적으로 표준 JavaBeans <code>PropertyEditor</code> 구현을 사용하여 이러한 문자열을 속성의 복잡한 타입으로 변환합니다. Spring은 다수의 사용자 정의 <code>PropertyEditor</code> 구현을 사전 등록합니다(예를 들어 문자열로 표현된 클래스 이름을 <code>Class</code> 객체로 변환하기 위해). 또한 Java의 표준 JavaBeans <code>PropertyEditor</code> 조회 메커니즘을 사용하면 클래스에 대한 <code>PropertyEditor</code>의 이름을 적절하게 지정하고, 지원을 제공하는 클래스와 동일한 패키지에 배치하여, 자동으로 찾을 수 있습니다.</p>
<p>다른 사용자 정의 <code>PropertyEditor</code>를 등록해야 하는 경우 몇 가지 메커니즘을 사용할 수 있습니다. 일반적으로 편리하지 않거나 권장되지 않는 가장 수동적인 접근 방식은, <code>BeanFactory</code> 참조가 있다고 가정하고 <code>ConfigurableBeanFactory</code> 인터페이스의 <code>RegisterCustomEditor()</code> 메서드를 사용하는 것입니다. 또 다른 (약간 더 편리한) 메커니즘은 <code>CustomEditorConfigurer</code>라는 특별한 빈 팩토리 후처리기(post-processor)를 사용하는 것입니다. <code>BeanFactory</code> 구현과 함께 빈 팩토리 후처리기를 사용할 수 있지만, <code>CustomEditorConfigurer</code>에는 중첩된 속성(property) 설정(setup)이 있으므로, 다른 빈에 비슷한 방식으로 배포할 수 있고, 자동으로 감지되어 적용될 수 있는, <code>ApplicationContext</code>와 함께 사용하는 것이 좋습니다. </p>
<p>모든 빈 팩토리와 애플리케이션 컨텍스트는 속성 변환(property conversion)을 처리하기 위해 <code>BeanWrapper</code>를 사용하여 자동으로 여러 내장 속성 편집기를 사용합니다. <code>BeanWrapper</code>가 등록하는 표준 속성 편집기는 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans-conversion">이전 섹션</a>에 나열되어 있습니다. 또한 <code>ApplicationContext</code>는 특정 애플리케이션 컨텍스트 타입에 적합한 방식으로 리소스 조회(lookup)를 처리하기 위해 추가 편집기를 재정의하거나 추가합니다.</p>
<p>표준 JavaBeans <code>PropertyEditor</code> 인스턴스는 문자열로 표현된 속성(property) 값을 속성의 실제 복합 타입으로 변환하는 데 사용됩니다. Bean 팩토리 후처리 프로세서인 <code>CustomEditorConfigurer</code>를 사용하여 추가 <code>PropertyEditor</code> 인스턴스에 대한 지원을 <code>ApplicationContext</code>에 편리하게 추가할 수 있습니다.</p>
<p><code>ExoticType</code>이라는 사용자 클래스와 속성으로 설정된 <code>ExoticType</code>이 필요한 다른 클래스인 <code>DefinesOnExoticType</code>을 정의하는 다음 예제를 살펴보세요.</p>
<pre><code class="language-java"><span class="token keyword">package</span> example<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExoticType</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">ExoticType</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DependsOnExoticType</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">ExoticType</span> type<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">ExoticType</span> type<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>type <span class="token operator">=</span> type<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>모든 것이 제대로 설정되면 타입 속성(property)을 문자열로 할당하고, <code>PropertyEditor</code>가 이를 실제 <code>ExoticType</code> 인스턴스로 변환할 수 있기를 원합니다. 다음 Bean 정의는 이 관계를 설정하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>sample<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.DependsOnExoticType<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>type<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aNameForExoticType<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>PropertyEditor</code> 구현은 다음과 유사할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> example<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>beans</span><span class="token punctuation">.</span><span class="token class-name">PropertyEditorSupport</span><span class="token punctuation">;</span>

<span class="token comment">// converts string representation to ExoticType object</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExoticTypeEditor</span> <span class="token keyword">extends</span> <span class="token class-name">PropertyEditorSupport</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setAsText</span><span class="token punctuation">(</span><span class="token class-name">String</span> text<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token function">setValue</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">ExoticType</span><span class="token punctuation">(</span>text<span class="token punctuation">.</span><span class="token function">toUpperCase</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 다음 예제에서는 <code>CustomEditorConfigurer</code>를 사용하여 <code>ApplicationContext</code>에 새 <code>PropertyEditor</code>를 등록한 다음 필요에 따라 사용할 수 있는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.beans.factory.config.CustomEditorConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>customEditors<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>map</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>entry</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.ExoticType<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.ExoticTypeEditor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>map</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="propertyeditorregistrar"><code>PropertyEditorRegistrar</code></h2>
<p>Spring 컨테이너에 속성 편집기를 등록하는 또 다른 메커니즘은 <code>PropertyEditorRegistrar</code>를 생성하고 사용하는 것입니다. 이 인터페이스는 여러 가지 상황에서 동일한 속성 편집기 세트를 사용해야 할 때 특히 유용합니다. 해당 등록자(registrar)를 작성하고 각 경우에 재사용할 수 있습니다. <code>PropertyEditorRegistrar</code> 인스턴스는 Spring <code>BeanWrapper</code>(및 <code>DataBinder</code>)에 의해 구현되는 인터페이스인 <code>PropertyEditorRegistry</code>라는 인터페이스와 함께 작동합니다. <code>PropertyEditorRegistrar</code> 인스턴스는 <code>setPropertyEditorRegistrars(..)</code>라는 속성(property)을 노출하는 <code>CustomEditorConfigurer</code>(<a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-beans-conversion-customeditor-registration">여기</a>에 설명됨)와 함께 사용할 때 특히 편리합니다. 이러한 방식으로 <code>CustomEditorConfigurer</code>에 추가된 <code>PropertyEditorRegistrar</code> 인스턴스는 <code>DataBinder</code> 및 Spring MVC 컨트롤러와 쉽게 공유될 수 있습니다. 또한 사용자 정의 편집기에서 동기화할 필요가 없습니다. <code>PropertyEditorRegistrar</code>는 각 빈 생성 시도에 대해 새로운 <code>PropertyEditor</code> 인스턴스를 생성할 것으로 예상됩니다.</p>
<p>다음 예에서는 자신만의 <code>PropertyEditorRegistrar</code> 구현을 만드는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>foo<span class="token punctuation">.</span>editors<span class="token punctuation">.</span>spring</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">CustomPropertyEditorRegistrar</span> <span class="token keyword">implements</span> <span class="token class-name">PropertyEditorRegistrar</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerCustomEditors</span><span class="token punctuation">(</span><span class="token class-name">PropertyEditorRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token comment">// it is expected that new PropertyEditor instances are created</span>
		registry<span class="token punctuation">.</span><span class="token function">registerCustomEditor</span><span class="token punctuation">(</span><span class="token class-name">ExoticType</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">ExoticTypeEditor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// you could register as many custom property editors as are required here...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>PropertyEditorRegistrar</code> 구현 예는 <code>org.springframework.beans.support.ResourceEditorRegistrar</code>를 참조하세요. <code>registerCustomEditors(..)</code> 메소드 구현에서 각 속성 편집기의 새 인스턴스를 생성하는 방법에 주목하세요.</p>
<p>다음 예는 <code>CustomEditorConfigurer</code>를 구성하고 <code>CustomPropertyEditorRegistrar</code>의 인스턴스를 여기에 삽입하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.beans.factory.config.CustomEditorConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>propertyEditorRegistrars<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>list</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>ref</span> <span class="token attr-name">bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>customPropertyEditorRegistrar<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>list</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>customPropertyEditorRegistrar<span class="token punctuation">"</span></span>
	<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.foo.editors.spring.CustomPropertyEditorRegistrar<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>마지막으로(그리고 이 장의 초점에서 약간 벗어남) <a href="https://docs.spring.io/spring-framework/reference/web/webmvc.html#mvc">Spring의 MVC 웹 프레임워크</a>를 사용하는 사람들에게는 데이터 바인딩 웹 컨트롤러와 함께 <code>PropertyEditorRegistrar</code>를 사용하는 것이 매우 편리할 수 있습니다. 다음 예제에서는 <code>@InitBinder</code> 메서드 구현에 <code>PropertyEditorRegistrar</code>를 사용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RegisterUserController</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">PropertyEditorRegistrar</span> customPropertyEditorRegistrar<span class="token punctuation">;</span>

	<span class="token class-name">RegisterUserController</span><span class="token punctuation">(</span><span class="token class-name">PropertyEditorRegistrar</span> propertyEditorRegistrar<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customPropertyEditorRegistrar <span class="token operator">=</span> propertyEditorRegistrar<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@InitBinder</span>
	<span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customPropertyEditorRegistrar<span class="token punctuation">.</span><span class="token function">registerCustomEditors</span><span class="token punctuation">(</span>binder<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// other methods related to registering a User</span>
<span class="token punctuation">}</span></code></pre>
<p>이러한 스타일의 <code>PropertyEditor</code> 등록은 간결한 코드로 이어질 수 있으며(<code>@InitBinder</code> 메소드의 구현은 단 한 줄 길이임) 공통 <code>PropertyEditor</code> 등록 코드를 클래스에 캡슐화한 다음 필요한 만큼 많은 컨트롤러 간에 공유할 수 있습니다.</p>