<p><a href="https://spring.io/guides/gs/multi-module/#scratch">https://spring.io/guides/gs/multi-module/#scratch</a></p>
<p>이 가이드에서는 Spring Boot를 사용하여 다중 모듈 프로젝트를 만드는 방법을 보여줍니다. 프로젝트에는 라이브러리 jar와 라이브러리를 사용하는 기본 애플리케이션이 있습니다. 또한 이를 사용하여 라이브러리(즉, 애플리케이션이 아닌 jar 파일)를 자체적으로 빌드하는 방법을 확인할 수도 있습니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>You will set up a library jar that exposes a service for simple “Hello, World” messages and then include the service in a web application that uses the library as a dependency.</p>
<h2 id="프로젝트-생성">프로젝트 생성</h2>
<p>sdk java 17<br>
language java<br>
build gradle-groovy<br>
gradle Wrapper 8.5<br>
group 'guides'<br>
project name 'multi-module'</p>
<h3 id="buildgradle-root">build.gradle [root]</h3>
<pre><code class="language-groovy">plugins {
    id 'java'
}

group = 'guides'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation platform('org.junit:junit-bom:5.9.1')
    testImplementation 'org.junit.jupiter:junit-jupiter'
}

test {
    useJUnitPlatform()
}</code></pre>
<h3 id="settinggradle-root">setting.gradle [root]</h3>
<pre><code class="language-groovy">rootProject.name = 'multi-module'

include 'library'
include 'application'</code></pre>
<p><code>rootProject.name</code>만 있던 setting.gradle 파일에 include할 디렉터리 이름 두 개를 추가합니다.</p>
<h2 id="루트-디렉터리-아래-library-디렉터리-추가">루트 디렉터리 아래 library 디렉터리 추가</h2>
<p>모듈이 아닌 디렉터리를 추가합니다. 가이드와는 달리 저는 경로를 'library/src/main/java/guides/multimodule/service'라고 지정했습니다.<br>
library 디렉터리의 루트 즉, src와 같은 깊이에 <code>build.gradle</code>을 추가합니다.</p>
<h3 id="buildgradle-library">build.gradle [library]</h3>
<pre><code class="language-groovy">plugins {
    id 'org.springframework.boot' version '3.1.5' apply false
    id 'io.spring.dependency-management' version '1.1.3'
    id 'java'
}

group = 'guides'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

dependencyManagement {
    imports {
        mavenBom org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES
    }
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'

//튜토리얼에는 위 두개의 의존성만 기재하고 있지만 스프링 3에서는 junit4가 아닌 junit5를 사용하므로 아래 두개의 의존성과 test {use JUnitPlatform()} 도 그대로 기재합니다.

    testImplementation platform('org.junit:junit-bom:5.9.1')
    testImplementation 'org.junit.jupiter:junit-jupiter'
}

test {
    useJUnitPlatform()
}</code></pre>
<p>주어진 Gradle 스크립트는 Spring Boot 플러그인을 적용하지 않도록 설정되어 있습니다. <code>apply false</code>로 지정된 부분에서 Spring Boot 플러그인을 적용하지 않도록 설정하고 있습니다. 이 설정 때문에 해당 Gradle 빌드 스크립트를 실행하여 실행 가능한 JAR 파일을 생성하는 기능이 비활성화될 수 있습니다. </p>
<p>Spring Boot 플러그인은 <code>org.springframework.boot</code> 플러그인 ID로 선언되며, 이것이 JAR 파일을 만들기 위한 많은 기능을 활성화합니다. 따라서 해당 설정을 비활성화하면 Spring Boot의 기능을 사용할 수 없게 됩니다. </p>
<p>예를 들어, <code>bootJar</code> 또는 <code>jar</code> 태스크를 사용하여 실행 가능한 JAR 파일을 생성하는 등의 작업이 Spring Boot 플러그인을 통해 수행될 수 있습니다. 설정에서 <code>apply false</code>로 Spring Boot 플러그인을 적용하지 않으면 이러한 태스크가 활성화되지 않을 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ee13502d-2879-4d53-a86b-3d5fa8f47b79/image.png"></p>
<p>디렉터리의 마지막 레벨을 보면 main 클래스가 생성되지 않은 것을 확인할 수 있습니다.</p>
<h2 id="bombill-of-materials">BOM(Bill of Materials)</h2>
<p>Maven과 Gradle의 BOM(Bill of Materials)은 프로젝트에서 사용하는 종속성의 버전을 관리하는 방법입니다.</p>
<ol>
<li>
<p><strong>Maven BOM</strong>:</p>
<ul>
<li>Maven 프로젝트에서 사용하는 방법으로, POM 파일에 종속성을 정의하고, 이러한 종속성들의 버전을 일관되게 유지할 수 있도록 BOM을 사용합니다.</li>
<li>Maven BOM은 일련의 종속성 그룹을 정의하고, 해당 그룹에 속하는 모든 종속성의 버전을 명시합니다. 이를 통해 프로젝트 전체에서 일관된 버전을 사용할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>Gradle Native BOM</strong>:</p>
<ul>
<li>Gradle 빌드 시스템에서 사용하는 방법으로, Maven BOM과 유사한 개념을 가지고 있습니다. Gradle에서도 BOM을 활용하여 종속성의 버전을 관리할 수 있습니다.</li>
<li>Gradle Native BOM은 Gradle의 특정 플러그인이나 기능에서 사용하는 종속성의 버전을 관리하는 데 사용됩니다. Maven BOM과 유사한 목적으로 사용되지만 Gradle의 문법과 구조에 맞게 적용됩니다.</li>
</ul>
</li>
</ol>
<p>이러한 BOM은 종속성 버전을 일관되게 유지하고, 프로젝트 전체에서 특정 종속성 그룹의 버전을 간편하게 업데이트하거나 변경할 수 있도록 도와줍니다. Maven과 Gradle은 각각의 빌드 도구에서 사용되는 BOM의 개념을 지원하며, 프로젝트의 특정 요구 사항에 따라 선택하여 활용할 수 있습니다.</p>
<hr>
<p><a href="https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/">https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/</a></p>
<p><a href="https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/#managing-dependencies.dependency-management-plugin.using-in-isolation">https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/#managing-dependencies.dependency-management-plugin.using-in-isolation</a></p>
<p><strong>3.1.2. Spring Boot의 종속성 관리를 독립적으로 사용하기</strong></p>
<p>Spring Boot의 종속성 관리는 해당 프로젝트에 Spring Boot 플러그인을 적용하지 않고도 사용할 수 있습니다. SpringBootPlugin 클래스는 BOM의 그룹 ID, artifact ID, 또는 버전을 알 필요 없이 BOM을 가져올 수 있는 <code>BOM_COORDINATES</code> 상수를 제공합니다.</p>
<p>먼저, 프로젝트를 Spring Boot 플러그인에 의존하도록 구성하되 적용하지 않습니다.</p>
<pre><code class="language-groovy">plugins {
    id 'org.springframework.boot' version '3.2.0' apply false
}</code></pre>
<p>Spring Boot 플러그인의 종속성 관리 플러그인 의존성은 의존성 관리 플러그인을 선언하지 않고도 사용할 수 있게 합니다. 또한, Spring Boot가 사용하는 의존성 관리 플러그인과 동일한 버전을 자동으로 사용하게 됩니다.</p>
<p>의존성 관리 플러그인을 적용한 다음 Spring Boot의 bom을 가져올 수 있도록 구성합니다.</p>
<pre><code class="language-groovy">apply plugin: 'io.spring.dependency-management'

dependencyManagement {
    imports {
        mavenBom org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES
    }
}</code></pre>
<p>위 코드는 조금 어색합니다. 그 이유는 우리가 의존성 관리 플러그인을 적용하는 명령형 방식을 사용하기 때문입니다.</p>
<p>코드를 좀 더 간결하게 만들기 위해 루트 부모 프로젝트에서 플러그인을 적용하거나 Spring Boot 플러그인과 같은 방식으로 plugins 블록을 사용할 수 있습니다. 이 방법의 단점은 의존성 관리 플러그인의 버전을 명시적으로 지정해야 한다는 것입니다.</p>
<pre><code class="language-groovy">plugins {
    java
    id("org.springframework.boot") version "3.2.0" apply false
    id("io.spring.dependency-management") version "1.1.4"
}

dependencyManagement {
    imports {
        mavenBom(org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES)
    }
}</code></pre>
<p><strong>3.2. Gradle의 BOM 지원을 통한 종속성 관리</strong></p>
<p>Gradle은 bom을 사용하여 프로젝트의 버전을 관리할 수 있도록 해줍니다. <code>platform</code> 또는 <code>enforcedPlatform</code> 종속성으로 선언하여 bom을 사용할 수 있습니다. <code>platform</code> 종속성은 bom의 버전을 권장사항으로 취급하며, 종속성 그래프의 다른 버전 및 제약 사항이 해당 bom에 선언된 버전 이외의 버전을 사용할 수 있도록 합니다. <code>enforcedPlatform</code> 종속성은 bom에 선언된 버전을 요구 사항으로 취급하며, 종속성 그래프에서 해당 버전 이외의 모든 버전을 무시합니다.</p>
<p>SpringBootPlugin 클래스는 <code>BOM_COORDINATES</code> 상수를 제공하여 Spring Boot의 bom에 대한 의존성을 선언할 수 있도록 합니다. 버전이나 그룹 ID, artifact ID를 알 필요 없이 사용할 수 있습니다.</p>
<pre><code class="language-groovy">dependencies {
    implementation platform(org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES)
}</code></pre>
<p><code>platform</code> 또는 <code>enforced platform</code>은 선언된 구성(Configuration)에서만 버전을 제약하며, 선언된 구성에서 확장된 구성으로만 적용됩니다. 따라서 하나 이상의 구성에서 동일한 종속성을 선언해야 할수도 있습니다.</p>
<p><strong>3.2.1. 관리되는 버전 사용자 정의</strong><br>
Gradle의 bom 지원을 사용할 때 <code>spring-boot-dependencies</code>에서 프로퍼티를 사용하여 버전을 제어할 수 없습니다. 대신 Gradle이 제공하는 메커니즘 중 하나인 해결 전략을 사용해야 합니다. 그 중 하나인 해결 전략을 사용하여 <code>org.slf4j</code> 그룹의 모듈들의 버전을 제어하는 방법은 다음과 같습니다.</p>
<pre><code class="language-groovy">configurations.all {
    resolutionStrategy.eachDependency { DependencyResolveDetails details -&gt;
        if (details.requested.group == 'org.slf4j') {
            details.useVersion '1.7.20'
        }
    }
}</code></pre>
<p>각 Spring Boot 릴리즈는 특정 집합의 서드파티 종속성과 설계, 테스트되었습니다. 버전을 재정의하면 호환성 문제가 발생할 수 있으므로 신중하게 사용해야 합니다.</p>
<hr>
<h3 id="library-디렉터리--myservice-serviceproperties-클래스-생성">library 디렉터리 : MyService, ServiceProperties 클래스 생성</h3>
<p>library는 application이 사용할 MyService.java를 제공할 것입니다.</p>
<pre><code class="language-java">
<span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>multimodule<span class="token punctuation">.</span>service</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">.</span><span class="token class-name">EnableConfigurationProperties</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Service</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token annotation punctuation">@EnableConfigurationProperties</span><span class="token punctuation">(</span><span class="token class-name">ServiceProperties</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyService</span> <span class="token punctuation">{</span>

  <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ServiceProperties</span> serviceProperties<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">MyService</span><span class="token punctuation">(</span><span class="token class-name">ServiceProperties</span> serviceProperties<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>serviceProperties <span class="token operator">=</span> serviceProperties<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">message</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>serviceProperties<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>MyService.java가 사용할 ServiceProperties 클래스도 같은 깊이에 생성합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>multimodule<span class="token punctuation">.</span>service</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">.</span><span class="token class-name">ConfigurationProperties</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@ConfigurationProperties</span><span class="token punctuation">(</span><span class="token string">"service"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceProperties</span> <span class="token punctuation">{</span>

    <span class="token comment">/**
     * A message for the service.
     */</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> message<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> message<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMessage</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>message <span class="token operator">=</span> message<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이런 식으로 할 필요는 없습니다. 라이브러리는 Spring 기능이 아닌 순수 Java API만 제공할 수 있습니다. 이 경우 라이브러리를 사용하는 애플리케이션은 구성 자체를 제공해야 합니다.</p>
<p><code>@ConfigurationProperties</code>는 스프링 부트에서 외부 설정 파일의 값을 자바 빈에 바인딩하기 위해 사용됩니다. 이를 통해 외부 설정을 해당 클래스의 인스턴스로 자동 매핑할 수 있습니다. 보통 <code>@ConfigurationProperties</code>를 사용할 때는 <code>@EnableConfigurationProperties</code>를 사용하여 해당 클래스를 활성화합니다.</p>
<p><code>@ConfigurationProperties("service")</code>는 속성 파일(<code>application.properties</code>)이나 환경 변수에서 <code>service.*</code>와 일치하는 속성을 찾습니다. 이 코드에서는<code>ServiceProperties</code> 클래스의 필드와 매핑하려고 시도합니다. 만약 <code>service.*</code>와 매치되는 속성이 없다면 그 필드들은 디폴트 값(null 또는 primitive type의 default 값)으로 초기화됩니다.</p>
<p>그러나 위 코드에서는 <code>MyService</code> 클래스의 생성자를 통해 <code>ServiceProperties</code>를 주입받고 있기 때문에, 외부 프로퍼티를 바로 사용하고 있습니다. 따라서 <code>@ConfigurationProperties</code>를 사용하지 않고 의존성 주입을 통해 값을 가져올 수 있습니다. 이러한 경우 <code>@ConfigurationProperties</code>나 <code>@EnableConfigurationProperties</code>를 사용할 필요는 없습니다.</p>
<h3 id="참고">참고</h3>
<h4 id="1-import">1. @Import</h4>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Documented</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ElementType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Retention</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Target</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * Indicates one or more &lt;em&gt;component classes&lt;/em&gt; to import &amp;mdash; typically
 * {@link Configuration @Configuration} classes.
 *
 * &lt;p&gt;Provides functionality equivalent to the {@code &lt;import/&gt;} element in Spring XML.
 * Allows for importing {@code @Configuration} classes, {@link ImportSelector} and
 * {@link ImportBeanDefinitionRegistrar} implementations, as well as regular component
 * classes (as of 4.2; analogous to {@link AnnotationConfigApplicationContext#register}).
 *
 * &lt;p&gt;{@code @Bean} definitions declared in imported {@code @Configuration} classes should be
 * accessed by using {@link org.springframework.beans.factory.annotation.Autowired @Autowired}
 * injection. Either the bean itself can be autowired, or the configuration class instance
 * declaring the bean can be autowired. The latter approach allows for explicit, IDE-friendly
 * navigation between {@code @Configuration} class methods.
 *
 * &lt;p&gt;May be declared at the class level or as a meta-annotation.
 *
 * &lt;p&gt;If XML or other non-{@code @Configuration} bean definition resources need to be
 * imported, use the {@link ImportResource @ImportResource} annotation instead.
 *
 * @author Chris Beams
 * @author Juergen Hoeller
 * @since 3.0
 * @see Configuration
 * @see ImportSelector
 * @see ImportBeanDefinitionRegistrar
 * @see ImportResource
 */</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Import</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * {@link Configuration @Configuration}, {@link ImportSelector},
	 * {@link ImportBeanDefinitionRegistrar}, or regular component classes to import.
	 */</span>
	<span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>해당 코드는 <code>@Import</code> 어노테이션을 정의하고 있습니다. 이 어노테이션은 하나 이상의 컴포넌트 클래스를 가져오는 데 사용됩니다. 주로 <code>@Configuration</code> 클래스들을 가져오는 데 사용되며, Spring XML의 <code>&lt;import/&gt;</code> 요소와 유사한 기능을 제공합니다.</p>
<p><code>@Import</code> 어노테이션을 통해 다음을 가져올 수 있습니다:</p>
<ul>
<li><code>@Configuration</code> 클래스들</li>
<li><code>ImportSelector</code></li>
<li><code>ImportBeanDefinitionRegistrar</code> 구현체</li>
<li>4.2부터는 일반 컴포넌트 클래스들 (<code>AnnotationConfigApplicationContext#register</code>와 유사)</li>
</ul>
<p>이 어노테이션은 클래스 수준이거나 메타 어노테이션으로 사용될 수 있습니다.</p>
<p>가져온 <code>@Configuration</code> 클래스에서 선언된 <code>@Bean</code> 정의는 <code>@Autowired</code> 주입을 통해 접근할 수 있습니다. 주입할 빈 자체를 <code>@Autowired</code>로 설정할 수 있거나, 빈을 선언하는 구성 클래스 인스턴스를 <code>@Autowired</code>로 설정할 수도 있습니다. 후자의 방법을 사용하면 <code>@Configuration</code> 클래스 메서드 간에 명시적이고 IDE에서 쉽게 이동할 수 있습니다.</p>
<p>XML 또는 다른 비 <code>@Configuration</code> 빈 정의 리소스를 가져와야 하는 경우에는 <code>@ImportResource</code> 어노테이션을 대신 사용해야 합니다.</p>
<h4 id="2-importbeandefinitionregistrarjava">2. ImportBeanDefinitionRegistrar.java</h4>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">BeanDefinitionRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">BeanDefinitionRegistryPostProcessor</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">BeanNameGenerator</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>type</span><span class="token punctuation">.</span><span class="token class-name">AnnotationMetadata</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * Interface to be implemented by types that register additional bean definitions when
 * processing @{@link Configuration} classes. Useful when operating at the bean definition
 * level (as opposed to {@code @Bean} method/instance level) is desired or necessary.
 *
 * &lt;p&gt;Along with {@code @Configuration} and {@link ImportSelector}, classes of this type
 * may be provided to the @{@link Import} annotation (or may also be returned from an
 * {@code ImportSelector}).
 *
 * &lt;p&gt;An {@link ImportBeanDefinitionRegistrar} may implement any of the following
 * {@link org.springframework.beans.factory.Aware Aware} interfaces, and their respective
 * methods will be called prior to {@link #registerBeanDefinitions}:
 * &lt;ul&gt;
 * &lt;li&gt;{@link org.springframework.context.EnvironmentAware EnvironmentAware}&lt;/li&gt;
 * &lt;li&gt;{@link org.springframework.beans.factory.BeanFactoryAware BeanFactoryAware}
 * &lt;li&gt;{@link org.springframework.beans.factory.BeanClassLoaderAware BeanClassLoaderAware}
 * &lt;li&gt;{@link org.springframework.context.ResourceLoaderAware ResourceLoaderAware}
 * &lt;/ul&gt;
 *
 * &lt;p&gt;Alternatively, the class may provide a single constructor with one or more of
 * the following supported parameter types:
 * &lt;ul&gt;
 * &lt;li&gt;{@link org.springframework.core.env.Environment Environment}&lt;/li&gt;
 * &lt;li&gt;{@link org.springframework.beans.factory.BeanFactory BeanFactory}&lt;/li&gt;
 * &lt;li&gt;{@link java.lang.ClassLoader ClassLoader}&lt;/li&gt;
 * &lt;li&gt;{@link org.springframework.core.io.ResourceLoader ResourceLoader}&lt;/li&gt;
 * &lt;/ul&gt;
 *
 * &lt;p&gt;See implementations and associated unit tests for usage examples.
 *
 * @author Chris Beams
 * @author Juergen Hoeller
 * @since 3.1
 * @see Import
 * @see ImportSelector
 * @see Configuration
 */</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">ImportBeanDefinitionRegistrar</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * Register bean definitions as necessary based on the given annotation metadata of
	 * the importing {@code @Configuration} class.
	 * &lt;p&gt;Note that {@link BeanDefinitionRegistryPostProcessor} types may &lt;em&gt;not&lt;/em&gt; be
	 * registered here, due to lifecycle constraints related to {@code @Configuration}
	 * class processing.
	 * &lt;p&gt;The default implementation delegates to
	 * {@link #registerBeanDefinitions(AnnotationMetadata, BeanDefinitionRegistry)}.
	 * @param importingClassMetadata annotation metadata of the importing class
	 * @param registry current bean definition registry
	 * @param importBeanNameGenerator the bean name generator strategy for imported beans:
	 * {@link ConfigurationClassPostProcessor#IMPORT_BEAN_NAME_GENERATOR} by default, or a
	 * user-provided one if {@link ConfigurationClassPostProcessor#setBeanNameGenerator}
	 * has been set. In the latter case, the passed-in strategy will be the same used for
	 * component scanning in the containing application context (otherwise, the default
	 * component-scan naming strategy is {@link AnnotationBeanNameGenerator#INSTANCE}).
	 * @since 5.2
	 * @see ConfigurationClassPostProcessor#IMPORT_BEAN_NAME_GENERATOR
	 * @see ConfigurationClassPostProcessor#setBeanNameGenerator
	 */</span>
	<span class="token keyword">default</span> <span class="token keyword">void</span> <span class="token function">registerBeanDefinitions</span><span class="token punctuation">(</span><span class="token class-name">AnnotationMetadata</span> importingClassMetadata<span class="token punctuation">,</span> <span class="token class-name">BeanDefinitionRegistry</span> registry<span class="token punctuation">,</span>
			<span class="token class-name">BeanNameGenerator</span> importBeanNameGenerator<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token function">registerBeanDefinitions</span><span class="token punctuation">(</span>importingClassMetadata<span class="token punctuation">,</span> registry<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">/**
	 * Register bean definitions as necessary based on the given annotation metadata of
	 * the importing {@code @Configuration} class.
	 * &lt;p&gt;Note that {@link BeanDefinitionRegistryPostProcessor} types may &lt;em&gt;not&lt;/em&gt; be
	 * registered here, due to lifecycle constraints related to {@code @Configuration}
	 * class processing.
	 * &lt;p&gt;The default implementation is empty.
	 * @param importingClassMetadata annotation metadata of the importing class
	 * @param registry current bean definition registry
	 */</span>
	<span class="token keyword">default</span> <span class="token keyword">void</span> <span class="token function">registerBeanDefinitions</span><span class="token punctuation">(</span><span class="token class-name">AnnotationMetadata</span> importingClassMetadata<span class="token punctuation">,</span> <span class="token class-name">BeanDefinitionRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>해당 코드는 <code>ImportBeanDefinitionRegistrar</code> 인터페이스를 정의하고 있습니다. 이 인터페이스는 <code>@Configuration</code> 클래스를 처리할 때 추가적인 빈 정의를 등록하는 데 사용됩니다. 주로 빈 정의 수준에서 작업해야 하는 경우에 유용합니다.</p>
<p>이 인터페이스를 구현하는 클래스들은 <code>@Import</code> 어노테이션에서 제공하거나 <code>ImportSelector</code>에서 반환될 수 있습니다.</p>
<p><code>ImportBeanDefinitionRegistrar</code>는 다음의 <code>Aware</code> 인터페이스 중 하나 이상을 구현할 수 있습니다:</p>
<ul>
<li><code>EnvironmentAware</code></li>
<li><code>BeanFactoryAware</code></li>
<li><code>BeanClassLoaderAware</code></li>
<li><code>ResourceLoaderAware</code></li>
</ul>
<p>또는 다음과 같은 단일 생성자를 제공할 수 있습니다:</p>
<ul>
<li><code>Environment</code></li>
<li><code>BeanFactory</code></li>
<li><code>ClassLoader</code></li>
<li><code>ResourceLoader</code></li>
</ul>
<p><code>registerBeanDefinitions</code> 메서드를 구현하여 <code>@Configuration</code> 클래스를 가져온다면 해당하는 어노테이션 메타데이터를 기반으로 빈 정의를 등록할 수 있습니다. 단, <code>BeanDefinitionRegistryPostProcessor</code> 유형의 빈은 여기서 등록할 수 없으며, 이는 <code>@Configuration</code> 클래스 처리와 관련된 라이프사이클 제약 때문입니다.</p>
<h4 id="3-enableconfigurationpropertiesjava">3. @EnableConfigurationProperties.java</h4>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Documented</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ElementType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Retention</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Target</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Import</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * Enable support for {@link ConfigurationProperties @ConfigurationProperties} annotated
 * beans. {@code @ConfigurationProperties} beans can be registered in the standard way
 * (for example using {@link Bean @Bean} methods) or, for convenience, can be specified
 * directly on this annotation.
 *
 * @author Dave Syer
 * @since 1.0.0
 */</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token class-name">EnableConfigurationPropertiesRegistrar</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">EnableConfigurationProperties</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * The bean name of the configuration properties validator.
	 * @since 2.2.0
	 */</span>
	<span class="token class-name">String</span> VALIDATOR_BEAN_NAME <span class="token operator">=</span> <span class="token string">"configurationPropertiesValidator"</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Convenient way to quickly register
	 * {@link ConfigurationProperties @ConfigurationProperties} annotated beans with
	 * Spring. Standard Spring Beans will also be scanned regardless of this value.
	 * @return {@code @ConfigurationProperties} annotated beans to register
	 */</span>
	<span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>이 코드는 <code>@EnableConfigurationProperties</code>라는 어노테이션의 정의를 보여줍니다. 이 어노테이션은 <code>@ConfigurationProperties</code>로 주석이 달린 빈을 지원하는 데 사용됩니다. <code>@ConfigurationProperties</code>로 주석이 달린 빈은 표준 방식(예: <code>@Bean</code> 메서드를 사용)으로 등록되거나 이 어노테이션에 직접 지정할 수 있습니다.</p>
<p>주요 속성은 다음과 같습니다:</p>
<ul>
<li><code>value()</code>: 이 속성은 <code>@ConfigurationProperties</code>로 주석이 달린 빈을 빠르게 Spring에 등록하는 편리한 방법입니다. 이 값을 설정하면 해당 클래스에 <code>@ConfigurationProperties</code>가 달린 빈을 자동으로 스캔하고 등록합니다. 또한, 이 속성을 설정하지 않아도 표준 Spring Beans은 스캔되어 등록됩니다.</li>
</ul>
<h4 id="4-configurationpropertiesjava">4. @ConfigurationProperties.java</h4>
<pre><code class="language-java">
<span class="token keyword">package</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Documented</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ElementType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Retention</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Target</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>context<span class="token punctuation">.</span>properties<span class="token punctuation">.</span>bind</span><span class="token punctuation">.</span><span class="token class-name">ConstructorBinding</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>core<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AliasFor</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Indexed</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * Annotation for externalized configuration. Add this to a class definition or a
 * {@code @Bean} method in a {@code @Configuration} class if you want to bind and validate
 * some external Properties (e.g. from a .properties file).
 * &lt;p&gt;
 * Binding is either performed by calling setters on the annotated class or, if
 * {@link ConstructorBinding @ConstructorBinding} is in use, by binding to the constructor
 * parameters.
 * &lt;p&gt;
 * Note that contrary to {@code @Value}, SpEL expressions are not evaluated since property
 * values are externalized.
 *
 * @author Dave Syer
 * @since 1.0.0
 * @see ConfigurationPropertiesScan
 * @see ConstructorBinding
 * @see ConfigurationPropertiesBindingPostProcessor
 * @see EnableConfigurationProperties
 */</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>METHOD <span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token annotation punctuation">@Indexed</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">ConfigurationProperties</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * The prefix of the properties that are valid to bind to this object. Synonym for
	 * {@link #prefix()}. A valid prefix is defined by one or more words separated with
	 * dots (e.g. {@code "acme.system.feature"}).
	 * @return the prefix of the properties to bind
	 */</span>
	<span class="token annotation punctuation">@AliasFor</span><span class="token punctuation">(</span><span class="token string">"prefix"</span><span class="token punctuation">)</span>
	<span class="token class-name">String</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * The prefix of the properties that are valid to bind to this object. Synonym for
	 * {@link #value()}. A valid prefix is defined by one or more words separated with
	 * dots (e.g. {@code "acme.system.feature"}).
	 * @return the prefix of the properties to bind
	 */</span>
	<span class="token annotation punctuation">@AliasFor</span><span class="token punctuation">(</span><span class="token string">"value"</span><span class="token punctuation">)</span>
	<span class="token class-name">String</span> <span class="token function">prefix</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">""</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Flag to indicate that when binding to this object invalid fields should be ignored.
	 * Invalid means invalid according to the binder that is used, and usually this means
	 * fields of the wrong type (or that cannot be coerced into the correct type).
	 * @return the flag value (default false)
	 */</span>
	<span class="token keyword">boolean</span> <span class="token function">ignoreInvalidFields</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">false</span><span class="token punctuation">;</span>

	<span class="token comment">/**
	 * Flag to indicate that when binding to this object unknown fields should be ignored.
	 * An unknown field could be a sign of a mistake in the Properties.
	 * @return the flag value (default true)
	 */</span>
	<span class="token keyword">boolean</span> <span class="token function">ignoreUnknownFields</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token boolean">true</span><span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>이 코드는 <code>@ConfigurationProperties</code>라는 어노테이션의 정의를 보여줍니다. 이 어노테이션은 외부 구성 파일(예: .properties 파일)에서 속성을 바인딩하고 유효성을 검사하기 위해 클래스 정의나 <code>@Configuration</code> 클래스의 <code>@Bean</code> 메서드에 추가할 수 있는 어노테이션입니다.</p>
<p>해당 어노테이션은 다음과 같은 속성을 가지고 있습니다:</p>
<ul>
<li><code>value()</code>: 해당 객체에 바인딩할 유효한 속성들의 접두사(prefix)를 정의합니다. 이는 <code>prefix()</code> 메서드와 동의어입니다. 유효한 접두사는 점으로 구분된 하나 이상의 단어로 정의됩니다. (예: <code>"acme.system.feature"</code>)</li>
<li><code>prefix()</code>: <code>value()</code> 메서드와 동일한 기능을 수행하며, 해당 객체에 바인딩할 유효한 속성들의 접두사(prefix)를 정의합니다.</li>
<li><code>ignoreInvalidFields()</code>: 해당 객체에 바인딩할 때 유효하지 않은 필드를 무시할지 여부를 지정합니다. 유효하지 않은 필드란, 일반적으로 올바른 형식이 아닌 필드(또는 올바른 형식으로 변환할 수 없는 필드)를 의미합니다. (기본값은 <code>false</code>)</li>
<li><code>ignoreUnknownFields()</code>: 해당 객체에 바인딩할 때 알 수 없는 필드를 무시할지 여부를 지정합니다. 알 수 없는 필드란 속성 파일에 오타 등의 실수로 나타난 필드를 의미합니다. (기본값은 <code>true</code>)</li>
</ul>
<h3 id="service-테스트">Service 테스트</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/40901557-0996-47d2-9979-1b831d3e088f/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/91fb855f-33af-4a0a-80bc-455ba05a1c97/image.png"></p>
<p><code>library/src/test/java/guides.multimodule.service</code> 디렉터리 생성</p>
<blockquote>
<p>이전 목록에서는 <code>@SpringBootTest</code> 주석의 기본 속성을 사용하여 테스트용 <code>service.message</code>를 구성했습니다. 라이브러리를 사용하는 애플리케이션과 런타임 시 충돌이 발생할 수 있으므로 <code>application.properties</code>를 라이브러리에 저장하지 않는 것이 좋습니다(클래스 경로에서 단 하나의 <code>application.properties</code>만 로드됨). <code>application.properties</code>를 테스트 클래스 경로에 넣을 수 있지만 <code>jar</code>에는 포함시키지 않을 수 있습니다(예를 들어 <code>src/test/resources</code>에 배치).</p>
</blockquote>
<p>라이브러리 구성 요소에 대한 단위 테스트를 작성하고 싶을 것입니다. 재사용 가능한 Spring 구성을 라이브러리의 일부로 제공하는 경우 구성이 작동하는지 확인하기 위해 통합 테스트를 작성할 수도 있습니다. 이를 위해 JUnit과 @SpringBootTest 주석을 사용할 수 있습니다.</p>
<p>MyServiceTest 클래스를 생성하고 아래 코드를 삽입하세요</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>multimodule<span class="token punctuation">.</span>service</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span><span class="token string">"service.message=Hello"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyServiceTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MyService</span> myService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">contextLoads</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>myService<span class="token punctuation">.</span><span class="token function">message</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotNull</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@SpringBootApplication</span>
    <span class="token keyword">static</span> <span class="token keyword">class</span> <span class="token class-name">TestConfiguration</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이 코드는 스프링 부트의 테스트 기능을 사용하여 <code>MyService</code> 클래스를 테스트하는 코드입니다.</p>
<ol>
<li>
<p><code>@SpringBootTest("service.message=Hello")</code>: <code>@SpringBootTest</code> 어노테이션은 테스트를 위해 스프링 애플리케이션 컨텍스트를 설정합니다. 여기서 <code>"service.message=Hello"</code>는 외부 속성으로 설정되는 내용입니다. 이것은 테스트 환경에서 <code>service.message</code> 속성에 <code>Hello</code> 값을 주입하는 역할을 합니다.</p>
</li>
<li>
<p><code>private MyService myService;</code>: <code>MyService</code>를 주입받을 필드입니다. <code>@Autowired</code> 어노테이션을 사용하여 스프링이 <code>MyService</code> 빈을 주입하도록 지시합니다.</p>
</li>
<li>
<p><code>@Test</code>: JUnit의 테스트 메서드를 나타내는 어노테이션입니다. </p>
</li>
<li>
<p><code>assertThat(myService.message()).isNotNull();</code>: <code>myService.message()</code>를 호출하여 반환된 값이 <code>null</code>이 아닌지를 검증합니다.</p>
</li>
<li>
<p><code>@SpringBootApplication static class TestConfiguration { }</code>: 테스트에서 사용하는 임시 스프링 부트 애플리케이션 컨텍스트를 정의하는 내부 정적 클래스입니다. <code>@SpringBootApplication</code>은 스프링 부트 애플리케이션을 정의할 때 사용하는 메타 어노테이션으로, 여기서는 테스트 환경을 구성하기 위해 사용되었습니다.</p>
</li>
</ol>
<p>이 코드는 <code>MyService</code> 클래스의 <code>message()</code> 메서드를 호출하고, 반환된 값이 <code>null</code>이 아닌지를 확인하는 단순한 테스트를 수행합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/261481e8-f0f0-45a2-bd3b-c78e58336eb7/image.png"></p>
<p>MyService를 찾지 못해서 빨간 글씨로 표시되고 있습니다.</p>
<p>이제 루트 디렉터리의 <code>build.gradle</code>, <code>src</code> 디렉터리는 삭제합니다. 삭제 후 library 디렉터리의 <code>build.gradle</code>에서 다시 빌드 작업을 진행합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7a35aea1-c23b-4194-b5d0-a94063b10f86/image.png"></p>
<p>성공적으로 resolve 되는 것을 확인할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8bb3bc0a-4991-48a2-a23d-93b1f6a4cc60/image.png"></p>
<p>테스트 실행 시 위와 같은 에러가 난다면 스프링 부트 버전에 맞는 테스트 의존성을 사용하고 있는지 확인합니다. build.gradle을 튜토리얼에서 명시한대로 작성했다면 제가 작성한 build.gradle을 참고하여 JUnit5로 변경합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7a97367a-7d63-41ad-afcb-73463c91284a/image.png"></p>
<p>이번엔 테스트 및 빌드에 성공합니다.</p>
<h2 id="루트-디렉터리-아래-application-디렉터리-추가">루트 디렉터리 아래 application 디렉터리 추가</h2>
<p>'application/src/main/java/guides/multimodule/application' 경로를 생성하고 build.gradle을 추가한다. </p>
<h3 id="buildgradle-application">build.gradle [application]</h3>
<pre><code class="language-groovy">
plugins {
    id 'org.springframework.boot' version '3.1.5'
    id 'io.spring.dependency-management' version '1.1.3'
    id 'java'
}

group = 'guides'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}


dependencies {
    implementation project(':library')
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation platform('org.junit:junit-bom:5.9.1')
    testImplementation 'org.junit.jupiter:junit-jupiter'
}

test {
    useJUnitPlatform()
}</code></pre>
<p>의존성에 <code>implementation project(':library')</code> 로 앞서 만든 library 디렉터리를 추가했습니다. </p>
<h3 id="application-디렉터리--demoapplication-클래스-생성">application 디렉터리 : DemoApplication 클래스 생성</h3>
<p>경로의 끝에 <code>DemoApplcation</code> 클래스를 생성합니다. 이 main 클래스는 <code>library</code>의 <code>Service</code>를 사용하여 메시지를 렌더링하는 <code>@RestController</code>가 될 것입니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>multimodule<span class="token punctuation">.</span>application</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">guides<span class="token punctuation">.</span>multimodule<span class="token punctuation">.</span>service</span><span class="token punctuation">.</span><span class="token class-name">MyService</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span><span class="token punctuation">(</span>scanBasePackages <span class="token operator">=</span> <span class="token string">"guides.multimodule"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DemoApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">MyService</span> myService<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">DemoApplication</span><span class="token punctuation">(</span><span class="token class-name">MyService</span> myService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>myService <span class="token operator">=</span> myService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">home</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> myService<span class="token punctuation">.</span><span class="token function">message</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">DemoApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@SpringBootApplication</code>은 다음을 추가하는 편리한 어노테이션이에요:</p>
<ul>
<li><code>@Configuration</code>: 해당 클래스를 애플리케이션 컨텍스트의 빈 정의 소스로 태그합니다.</li>
<li><code>@EnableAutoConfiguration</code>: Spring Boot에게 클래스패스 설정, 다른 빈, 여러 속성 설정을 기반으로 빈을 추가하도록 지시합니다. 예를 들어, <code>spring-webmvc</code>가 클래스패스에 있으면 이 어노테이션은 애플리케이션을 웹 애플리케이션으로 표시하고 <code>DispatcherServlet</code> 설정과 같은 주요 동작을 활성화합니다.</li>
<li><code>@ComponentScan</code>: Spring에게 <code>guides</code> 패키지에서 다른 컴포넌트, 설정, 서비스를 찾도록 지시하여 컨트롤러를 찾을 수 있게 합니다.</li>
</ul>
<p><code>main()</code> 메서드는 Spring Boot의 <code>SpringApplication.run()</code> 메서드를 사용하여 애플리케이션을 시작합니다. XML 한 줄도 없었죠? <code>web.xml</code> 파일도 없었습니다. 이 웹 애플리케이션은 100% 순수 Java로 구성되었으며 어떠한 플러밍이나 인프라 구성도 건드리지 않았어요.</p>
<p><code>DemoApplication</code>이 <code>MyService</code>(guides.multimodule.service)와 다른 패키지(guides.multimodule.application)에 있기 때문에 <code>@SpringBootApplication</code>은 자동으로 인식할 수 없습니다. <code>MyService</code>를 선택하게 하는 여러 방법이 있어요:</p>
<ol>
<li>직접 <code>@Import(MyService.class)</code>로 가져옵니다.</li>
<li><code>@SpringBootApplication(scanBasePackageClasses={...})</code>로 해당 패키지에서 모든 것을 가져옵니다.</li>
<li>패키지 이름(guides.multimodule)을 명시적으로 지정합니다. (이 가이드에서는 이 방법을 사용합니다)</li>
</ol>
<blockquote>
<p>만약 애플리케이션이 JPA 또는 Spring Data를 사용한다면, <code>@EntityScan</code> 및 <code>@EnableJpaRepositories</code> (관련된 것들도 포함됨) 어노테이션은 <code>@SpringBootApplication</code>에서 기본 패키지만 상속받습니다. 즉, <code>scanBasePackageClasses</code>나 <code>scanBasePackages</code>를 명시한 경우 <code>@EntityScan</code> 및 <code>@EnableJpaRepositories</code>를 해당 패키지 스캔이 명시적으로 구성된 상태에서 사용해야 할 수 있습니다.</p>
</blockquote>
<h3 id="resource-디렉터리-생성-후-applicationproperties-추가">resource 디렉터리 생성 후 application.properties 추가</h3>
<p><code>src/main/resources</code> 디렉터리를 생성하고 <code>application.properties</code> 파일을 생성합니다. 이 파일에 <code>library</code>의 <code>service</code>에서 사용할 <code>messsage</code>를 제공할 것입니다.</p>
<pre><code class="language-null">service.message=Hello, World</code></pre>
<h3 id="application-테스트">application 테스트</h3>
<p>애플리케이션을 시작하여 엔드투엔드 결과를 테스트합니다. IDE에서 애플리케이션을 시작하거나 명령줄을 사용할 수 있습니다. 애플리케이션이 실행되면 브라우저의 <code>http://localhost:8080/</code>에서 클라이언트 애플리케이션을 방문하십시오. 거기에서 응답에 Hello, World가 반영된 것을 볼 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e73513dd-51e4-4dbd-a45a-53ad25aadcc7/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/93d409cf-f06d-4846-bdbb-32fcc31e4112/image.png"></p>
<p>Gradle을 사용하는 경우 다음 명령(실제로는 두 개의 명령이 순서대로 실행됨)을 사용하여 먼저 라이브러리를 빌드한 다음 애플리케이션을 실행합니다.</p>
<pre><code class="language-shell">$ ./gradlew build <span class="token operator">&amp;&amp;</span> ./gradlew :application:bootRun</code></pre>