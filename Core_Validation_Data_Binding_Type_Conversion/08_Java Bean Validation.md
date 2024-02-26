<p>Spring Framework는 <a href="https://beanvalidation.org/">Java Bean 유효성 검사</a> API에 대한 지원을 제공합니다.</p>
<h1 id="overview-of-bean-validation">Overview of Bean Validation</h1>
<p>Bean 유효성 검사는 Java 애플리케이션에 대한 제약 조건 선언 및 메타데이터를 통해 일반적인 유효성 검사 방법을 제공합니다. 이를 사용하려면 런타임에 의해 적용되는 선언적 유효성 검사 제약 조건으로 도메인 모델 속성에 어노테이션을 답니다. 기본 제공 제약 조건이 있으며 사용자 지정 제약 조건을 정의할 수도 있습니다.</p>
<p>두 가지 속성이 있는 간단한 <code>PersonForm</code> 모델을 보여주는 다음 예를 살펴보세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonForm</span> <span class="token punctuation">{</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">int</span> age<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Bean 유효성 검사를 사용하면 다음 예제와 같이 제약 조건을 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonForm</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@NotNull</span>
	<span class="token annotation punctuation">@Size</span><span class="token punctuation">(</span>max<span class="token operator">=</span><span class="token number">64</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Min</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token keyword">int</span> age<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 Bean 유효성 검사 유효성 검사기는 선언된 제약 조건을 기반으로 이 클래스의 인스턴스를 유효성 검사합니다. API에 대한 일반 정보는 <a href="https://beanvalidation.org/">Bean 유효성 검증</a>을 참조하십시오. 특정 제약 조건에 대해서는 <a href="https://hibernate.org/validator/">Hibernate Validator 문서</a>를 참조하세요. Bean 유효성 검증 제공자를 Spring Bean으로 설정하는 방법을 배우려면 계속 읽으십시오.</p>
<h1 id="configuring-a-bean-validation-provider">Configuring a Bean Validation Provider</h1>
<p>Spring은 Bean 유효성 검증 제공자를 Spring Bean으로 부트스트래핑하는 것을 포함하여 Bean 유효성 검증 API에 대한 완전한 지원을 제공합니다. 이를 통해 애플리케이션에서 검증이 필요할 때마다 <code>jakarta.validation.ValidatorFactory</code> 또는 <code>jakarta.validation.Validator</code>를 삽입할 수 있습니다.</p>
<p>다음 예제와 같이 <code>LocalValidatorFactoryBean</code>을 사용하여 기본 유효성 검사기를 Spring 빈으로 구성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>beanvalidation</span><span class="token punctuation">.</span><span class="token class-name">LocalValidatorFactoryBean</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">LocalValidatorFactoryBean</span> <span class="token function">validator</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">LocalValidatorFactoryBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 예제의 기본 구성은 기본 부트스트랩 메커니즘을 사용하여 초기화하기 위해 Bean 유효성 검증을 트리거합니다. Hibernate Validator와 같은 Bean Validation 제공자(provider)는 클래스패스에 존재할 것으로 예상되며 자동으로 감지됩니다.</p>
<h2 id="inject-jakarta-validator">Inject jakarta Validator</h2>
<p><code>LocalValidatorFactoryBean</code>은 <code>jakarta.validation.ValidatorFactory</code>와 <code>jakarta.validation.Validator</code>를 모두 구현하므로 다음 예제와 같이 Bean Validation API로 직접 작업하려는 경우 후자에 대한 참조를 주입하여 유효성 검사 논리를 적용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>validation</span><span class="token punctuation">.</span><span class="token class-name">Validator</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyService</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">Validator</span> validator<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="inject-spring-validator">Inject Spring Validator</h2>
<p><code>jakarta.validation.Validator</code> 구현 외에도 <code>LocalValidatorFactoryBean</code>은 <code>org.springframework.validation.Validator</code>에도 적응하므로 Bean에 Spring Validation API가 필요한 경우 후자에 대한 참조를 주입할 수 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>validation</span><span class="token punctuation">.</span><span class="token class-name">Validator</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyService</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">Validator</span> validator<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>org.springframework.validation.Validator</code>로 사용될 때 <code>LocalValidatorFactoryBean</code>은 기본 <code>jakarta.validation.Validator</code>를 호출한 다음 <code>ContraintViolations</code>를 <code>FieldErrors</code>에 적용하고 이를 유효성 검사 메서드에 전달된 <code>Errors</code> 개체에 등록합니다.</p>
<h2 id="configure-custom-constraints">Configure Custom Constraints</h2>
<p>각 Bean 유효성 검증 제약조건은 두 부분으로 구성됩니다.</p>
<ul>
<li>
<p>제약 조건과 해당 구성 가능한 속성(configurable property)을 선언하는 <code>@Constraint</code> 어노테이션입니다.</p>
</li>
<li>
<p>제약 조건의 동작을 구현하는 <code>jakarta.validation.ConstraintValidator</code> 인터페이스의 구현입니다.</p>
</li>
</ul>
<p>선언을 구현과 연결하기 위해 각 <code>@Constraint</code> 어노테이션은 해당 <code>ConstraintValidator</code> 구현 클래스를 참조합니다. 런타임 시 <code>ConstraintValidatorFactory</code>는 도메인 모델에서 제약 조건 어노테이션이 발견될 때 참조된 구현을 인스턴스화합니다.</p>
<p>기본적으로 <code>LocalValidatorFactoryBean</code>은 <code>ConstraintValidator</code> 인스턴스를 생성하기 위해 Spring을 사용하는 <code>SpringConstraintValidatorFactory</code>를 구성합니다. 이를 통해 사용자 정의 <code>ConstraintValidator</code>는 다른 Spring 빈과 마찬가지로 종속성 주입의 이점을 누릴 수 있습니다.</p>
<p>다음 예제는 종속성 주입을 위해 Spring을 사용하는 연관된 <code>ConstraintValidator</code> 구현이 뒤따르는 사용자 정의 <code>@Constraint</code> 선언을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>METHOD<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>FIELD<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Constraint</span><span class="token punctuation">(</span>validatedBy<span class="token operator">=</span><span class="token class-name">MyConstraintValidator</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">MyConstraint</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>validation</span><span class="token punctuation">.</span><span class="token class-name">ConstraintValidator</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyConstraintValidator</span> <span class="token keyword">implements</span> <span class="token class-name">ConstraintValidator</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">Foo</span> aDependency<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 예제에서 볼 수 있듯이 <code>ConstraintValidator</code> 구현은 다른 Spring 빈과 마찬가지로 <code>@Autowired</code> 종속성을 가질 수 있습니다.</p>
<h1 id="spring-driven-method-validation">Spring-driven Method Validation</h1>
<p><code>MethodValidationPostProcessor</code> 빈 정의를 통해 Bean 유효성 검사의 메서드 유효성 검사 기능을 Spring 컨텍스트에 통합할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>beanvalidation</span><span class="token punctuation">.</span><span class="token class-name">MethodValidationPostProcessor</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">MethodValidationPostProcessor</span> <span class="token function">validationPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MethodValidationPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring 기반 메서드 유효성 검사에 적합하려면, 대상 클래스에 Spring의 <code>@Validated</code> 어노테이션을 달아야 하며, 선택적으로 사용할 유효성 검사 그룹을 선언할 수도 있습니다. Hibernate 유효성 검사기 및 Bean 유효성 검사 공급자에 대한 설정 세부 사항은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/validation/beanvalidation/MethodValidationPostProcessor.html">MethodValidationPostProcessor</a>를 참조하세요.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
메소드 검증은 대상 클래스 주변의 <a href="https://docs.spring.io/spring-framework/reference/core/aop/introduction-proxies.html">AOP 프록시</a>(인터페이스의 메소드에 대한 JDK 동적 프록시 또는 CGLIB 프록시)에 의존합니다. 프록시 사용에는 특정 제한 사항이 있으며 그 중 일부는 <a href="https://docs.spring.io/spring-framework/reference/core/aop/proxying.html#aop-understanding-aop-proxies">AOP 프록시 이해</a>에 설명되어 있습니다. 또한 프록시된 클래스에서는 항상 메서드와 접근자를 사용하는 것을 기억하세요. 직접 현장 액세스는 작동하지 않습니다.</p>
</blockquote>
<p>Spring MVC와 WebFlux는 동일한 기본 메서드 유효성 검사를 기본적으로 지원하지만 AOP는 필요하지 않습니다. 따라서 이 섹션의 나머지 부분을 확인하고 Spring MVC <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html">유효성 검사</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html">오류 응답 섹션</a>과 WebFlux <a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-validation.html">유효성 검사</a> 및 오류 응답 섹션도 참조하세요.</p>
<h2 id="method-validation-exceptions">Method Validation Exceptions</h2>
<p>기본적으로 <code>jakarta.validation.ConstraintViolationException</code>은 <code>jakarata.validation.Validator</code>에서 반환된 <code>ConstraintViolations</code> 집합으로 발생합니다. 대안으로 <code>MessageSourceResolvable</code> 오류에 맞게 조정된 <code>ConstraintViolations</code>를 사용하여 대신 <code>MethodValidationException</code>을 발생시킬 수 있습니다. 활성화하려면 다음 플래그를 설정하십시오.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>validation<span class="token punctuation">.</span>beanvalidation</span><span class="token punctuation">.</span><span class="token class-name">MethodValidationPostProcessor</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">MethodValidationPostProcessor</span> <span class="token function">validationPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">MethodValidationPostProcessor</span> processor <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MethodValidationPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		processor<span class="token punctuation">.</span><span class="token function">setAdaptConstraintViolations</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> processor<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>MethodValidationException</code>에는 메서드 매개 변수별로 오류를 그룹화하는 <code>ParameterValidationResults</code> 목록이 포함되어 있으며, 각각은 <code>MethodParameter</code>, 인수 값 및 <code>ConstraintViolations</code>에서 조정된 <code>MessageSourceResolvable</code> 오류 목록을 노출합니다. 필드 및 속성에 계단식 위반이 있는 <code>@Valid</code> 메서드 매개 변수의 경우 <code>ParameterValidationResult</code>는 <code>org.springframework.validation.Errors</code>를 구현하고 유효성 검사 오류를 <code>FieldErrors</code>로 노출하는 <code>ParameterErrors</code>입니다.</p>
<h2 id="customizing-validation-errors">Customizing Validation Errors</h2>
<p>조정된 <code>MessageSourceResolvable</code> 오류는 오류 메시지로 변환되어 로케일 및 언어별 리소스 번들과 함께 구성된 <code>MessageSource</code>를 통해 사용자에게 표시될 수 있습니다. 이 섹션에서는 예시를 제공합니다.</p>
<p>다음과 같은 클래스 선언이 있다고 가정해 보겠습니다.</p>
<pre><code class="language-java">record <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Size</span><span class="token punctuation">(</span>min <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">,</span> max <span class="token operator">=</span> <span class="token number">10</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Validated</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyService</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">addStudent</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Valid</span> <span class="token class-name">Person</span> person<span class="token punctuation">,</span> <span class="token annotation punctuation">@Max</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span> <span class="token keyword">int</span> degrees<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Person.name()</code>의 <code>ConstraintViolation</code>은 다음과 같이 <code>FieldError</code>에 맞게 조정됩니다.</p>
<ul>
<li>
<p>오류 코드 <code>"Size.student.name"</code>, <code>"Size.name"</code>, <code>"Size.java.lang.String"</code> 및 <code>"Size"</code></p>
</li>
<li>
<p>메시지 인수 <code>"name"</code>, <code>10</code> 및 <code>1</code>(필드 이름 및 제약 조건 속성)</p>
</li>
<li>
<p>기본 메시지 "크기는 1에서 10 사이여야 합니다."</p>
</li>
</ul>
<p>기본 메시지를 사용자 정의하려면, 위의 오류 코드 및 메시지 인수를 사용하여 <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-functionality-messagesource">MessageSource</a> 리소스 번들에 속성을 추가할 수 있습니다. 또한 메시지 인수 <code>"name"</code>은 오류 코드 <code>"student.name"</code> 및 <code>"name"</code>이 포함된 <code>MessagreSourceResolvable</code>이며 사용자 정의할 수도 있습니다. 예를 들어:</p>
<p>properties</p>
<pre><code class="language-properties">Size.student.name=Please, provide a {0} that is between {2} and {1} characters long
student.name=username</code></pre>
<p><code>degree</code> 메소드 매개변수의 <code>ConstraintViolation</code>은 다음을 통해 <code>MessageSourceResolvable</code>에 적용됩니다.</p>
<ul>
<li>
<p>오류 코드 <code>"Max.myService#addStudent.degrees"</code>, <code>"Max.degrees"</code>, <code>"Max.int"</code>, <code>"Max"</code></p>
</li>
<li>
<p>메시지 인수 "degrees2 및 2(필드 이름 및 제약 조건 속성)</p>
</li>
<li>
<p>기본 메시지는 "2보다 작거나 같아야 합니다"입니다.</p>
</li>
</ul>
<p>위의 기본 메시지를 사용자 정의하려면 다음과 같은 속성을 추가할 수 있습니다.</p>
<p>properties</p>
<pre><code class="language-properties">Max.degrees=You cannot provide more than {1} {0}</code></pre>
<h2 id="additional-configuration-options">Additional Configuration Options</h2>
<p>대부분의 경우 기본 <code>LocalValidatorFactoryBean</code> 구성(configuration)으로 충분합니다. 메시지 보간(interpolation)부터 순회(traversal) resolution까지 다양한 Bean 유효성 검사 구성에 대한 여러 구성(configuration) 옵션이 있습니다. 이러한 옵션에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html"><code>LocalValidatorFactoryBean</code></a> javadoc를 참조하세요.</p>
<h1 id="configuring-a-databinder">Configuring a <code>DataBinder</code></h1>
<p><code>Validator</code>를 사용하여 <code>DataBinder</code> 인스턴스를 구성할 수 있습니다. 구성한 후에는<code>binder.validate()</code>를 호출하여 <code>Validator</code>를 호출할 수 있습니다. 모든 유효성 검사 <code>Errors</code>는 바인더의 <code>BindingResult</code>에 자동으로 추가됩니다.</p>
<p>다음 예에서는 프로그래밍 방식으로 <code>DataBinder</code>를 사용하여 대상 객체에 바인딩한 후 유효성 검사 논리를 호출하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">Foo</span> target <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">DataBinder</span> binder <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DataBinder</span><span class="token punctuation">(</span>target<span class="token punctuation">)</span><span class="token punctuation">;</span>
binder<span class="token punctuation">.</span><span class="token function">setValidator</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FooValidator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// bind to the target object</span>
binder<span class="token punctuation">.</span><span class="token function">bind</span><span class="token punctuation">(</span>propertyValues<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// validate the target object</span>
binder<span class="token punctuation">.</span><span class="token function">validate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// get BindingResult that includes any validation errors</span>
<span class="token class-name">BindingResult</span> results <span class="token operator">=</span> binder<span class="token punctuation">.</span><span class="token function">getBindingResult</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>dataBinder.addValidators</code> 및 <code>dataBinder.replaceValidators</code>를 통해 여러 <code>Validator</code> 인스턴스로 <code>DataBinder</code>를 구성할 수도 있습니다. 이는 전역적으로 구성된 Bean 검증을 DataBinder 인스턴스에 로컬로 구성된 Spring <code>Validator</code>와 결합할 때 유용합니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/validation.html">Spring MVC 유효성 검사 구성</a>을 참조하세요.</p>
<h1 id="spring-mvc-3-validation">Spring MVC 3 Validation</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/validation.html">Spring MVC 장의 유효성 검사</a>를 참조하세요.</p>