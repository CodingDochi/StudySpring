<p>이 가이드는 Spring Security로 보호되는 리소스를 사용하여 간단한 웹 애플리케이션을 만드는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>고정된 사용자 목록이 지원되는 로그인 양식으로 페이지를 보호하는 Spring MVC 애플리케이션을 빌드합니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/2ab0e13d-5a41-4932-9a06-df13fcf4fe89/image.png"></p>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-null">plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.0'
	id 'io.spring.dependency-management' version '1.1.4'
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
	implementation 'org.springframework.boot:spring-boot-starter-security'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.security:spring-security-test'
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h2 id="create-an-unsecured-web-application">Create an Unsecured Web Application</h2>
<p>웹 애플리케이션에 보안을 적용하려면 먼저 보안을 설정할 웹 애플리케이션이 필요합니다. 이 섹션에서는 간단한 웹 애플리케이션을 만드는 과정을 안내합니다. 그런 다음 다음 섹션에서 Spring Security를 사용하여 이를 보호합니다.</p>
<p>웹 애플리케이션에는 홈 페이지와 "Hello, World" 페이지라는 두 가지 간단한 보기가 포함되어 있습니다. 홈 페이지는 다음 Thymeleaf 템플릿(src/main/resources/templates/home.html)에 정의되어 있습니다:</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/1999/xhtml<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Spring Security Example<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Welcome!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>

        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>p</span><span class="token punctuation">&gt;</span></span>Click <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>a</span> <span class="token attr-name"><span class="token namespace">th:</span>href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>@{/hello}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>here<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>a</span><span class="token punctuation">&gt;</span></span> to see a greeting.<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>p</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 간단한 보기에는 다음 Thymeleaf 템플릿(src/main/resources/templates/hello.html)에 정의된 /hello 페이지에 대한 링크가 포함되어 있습니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/1999/xhtml<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Hello World!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Hello world!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>웹 애플리케이션은 Spring MVC를 기반으로 합니다. 결과적으로 Spring MVC를 구성하고 이러한 템플릿을 노출하도록 뷰 컨트롤러를 설정해야 합니다. 다음 목록(src/main/java/guides/securingweb/MvcConfig.java)은 애플리케이션에서 Spring MVC를 구성하는 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>securingweb</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ViewControllerRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">WebMvcConfigurer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MvcConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addViewControllers</span><span class="token punctuation">(</span><span class="token class-name">ViewControllerRegistry</span> registry<span class="token punctuation">)</span><span class="token punctuation">{</span>
        registry<span class="token punctuation">.</span><span class="token function">addViewController</span><span class="token punctuation">(</span><span class="token string">"/home"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setViewName</span><span class="token punctuation">(</span><span class="token string">"home"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        registry<span class="token punctuation">.</span><span class="token function">addViewController</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setViewName</span><span class="token punctuation">(</span><span class="token string">"home"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        registry<span class="token punctuation">.</span><span class="token function">addViewController</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setViewName</span><span class="token punctuation">(</span><span class="token string">"hello"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        registry<span class="token punctuation">.</span><span class="token function">addViewController</span><span class="token punctuation">(</span><span class="token string">"/login"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setViewName</span><span class="token punctuation">(</span><span class="token string">"login"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p><code>addViewControllers()</code> 메서드(<code>WebMvcConfigurer</code>에서 동일한 이름의 메서드를 재정의함)는 4개의 뷰 컨트롤러를 추가합니다. 뷰 컨트롤러 중 두 개는 이름이 <code>home</code>(<code>home.html</code>에 정의됨)인 뷰를 참조하고, 다른 하나는 <code>hello</code>(<code>hello.html</code>에 정의됨)라는 뷰를 참조합니다. 네 번째 뷰 컨트롤러는 <code>login</code>이라는 또 다른 뷰를 참조합니다. 다음 섹션에서 해당 뷰를 만듭니다.</p>
<p>이 시점에서 "애플리케이션 실행"으로 이동하여 로그인할 필요 없이 애플리케이션을 실행할 수 있습니다.</p>
<p>이제 보안되지 않은 웹 애플리케이션이 있으므로 보안을 추가할 수 있습니다.</p>
<h2 id="스프링-보안-설정">스프링 보안 설정</h2>
<p>권한이 없는 사용자가 <code>/hello</code>의 인사말 페이지를 보는 것을 방지한다고 가정해 보겠습니다. 지금처럼 방문자가 홈페이지의 링크를 클릭하면 아무런 장애 없이 인사말을 볼 수 있습니다. 방문자가 해당 페이지를 보기 전에 로그인하도록 강제하는 장벽을 추가해야 합니다.</p>
<p>애플리케이션에서 Spring Security를 구성하면 됩니다. Spring Security가 classpath에 있는 경우 Spring Boot는 "기본" 인증을 사용하여 모든 HTTP 엔드포인트를 자동으로 보호합니다. 그러나 보안 설정을 추가로 사용자 정의할 수 있습니다. 가장 먼저 해야 할 일은 classpath에 Spring Security를 추가하는 것입니다.</p>
<p>Gradle을 사용하면 다음 목록에 표시된 것처럼 <code>build.gradle</code>의 <code>dependencies</code> 클로저에 세 줄(애플리케이션용 하나, Thymeleaf 및 Spring Security 통합용 하나, 테스트용 하나)을 추가해야 합니다.</p>
<pre><code class="language-null">implementation 'org.springframework.boot:spring-boot-starter-security'
//  Temporary explicit version to fix Thymeleaf bug
implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6:3.1.1.RELEASE'
testImplementation 'org.springframework.security:spring-security-test'</code></pre>
<p>다음 보안 구성(src/main/java/guides/securingweb/WebSecurityConfig.java)은 인증된 사용자만 비밀 인사말을 볼 수 있도록 보장합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>securingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>web<span class="token punctuation">.</span>builders</span><span class="token punctuation">.</span><span class="token class-name">HttpSecurity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>web<span class="token punctuation">.</span>configuration</span><span class="token punctuation">.</span><span class="token class-name">EnableWebSecurity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>core<span class="token punctuation">.</span>userdetails</span><span class="token punctuation">.</span><span class="token class-name">User</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>core<span class="token punctuation">.</span>userdetails</span><span class="token punctuation">.</span><span class="token class-name">UserDetails</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>core<span class="token punctuation">.</span>userdetails</span><span class="token punctuation">.</span><span class="token class-name">UserDetailsService</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>provisioning</span><span class="token punctuation">.</span><span class="token class-name">InMemoryUserDetailsManager</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>web</span><span class="token punctuation">.</span><span class="token class-name">SecurityFilterChain</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSecurity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSecurityConfig</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">SecurityFilterChain</span> <span class="token function">securityFilterChain</span><span class="token punctuation">(</span><span class="token class-name">HttpSecurity</span> http<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        http
                <span class="token punctuation">.</span><span class="token function">authorizeHttpRequests</span><span class="token punctuation">(</span><span class="token punctuation">(</span>requests<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> requests<span class="token punctuation">.</span><span class="token function">requestMatchers</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">,</span> <span class="token string">"/home"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">permitAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">anyRequest</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">authenticated</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">formLogin</span><span class="token punctuation">(</span><span class="token punctuation">(</span>form<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> form<span class="token punctuation">.</span><span class="token function">loginPage</span><span class="token punctuation">(</span><span class="token string">"/login"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">permitAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">logout</span><span class="token punctuation">(</span><span class="token punctuation">(</span>logout<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> logout<span class="token punctuation">.</span><span class="token function">permitAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                
        <span class="token keyword">return</span> http<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">UserDetailsService</span> <span class="token function">userDetailsService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
        <span class="token class-name">UserDetails</span> user <span class="token operator">=</span> <span class="token class-name">User</span><span class="token punctuation">.</span><span class="token function">withDefaultPasswordEncoder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">username</span><span class="token punctuation">(</span><span class="token string">"user"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">password</span><span class="token punctuation">(</span><span class="token string">"password"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">roles</span><span class="token punctuation">(</span><span class="token string">"USER"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">InMemoryUserDetailsManager</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>WebSecurityConfig</code> 클래스는 <code>@EnableWebSecurity</code>로 주석 처리되어 Spring Security의 웹 보안 지원을 활성화하고 Spring MVC 통합을 제공합니다. 또한 웹 보안 구성에 대한 몇 가지 세부 사항을 설정하기 위해 두 개의 Bean을 노출합니다.</p>
<p><code>SecurityFilterChain</code> 빈은 어떤 URL 경로를 보호해야 하는지, 어떤 경로를 보호하지 말아야 하는지 정의합니다. 특히 <code>/</code> 및 <code>/home</code> 경로는 인증이 필요하지 않도록 구성됩니다. 다른 모든 경로는 인증되어야 합니다.</p>
<p>사용자가 성공적으로 로그인하면 인증이 필요한 이전에 요청한 페이지로 리디렉션됩니다. 사용자 정의 <code>/login</code> 페이지(<code>loginPage()</code>에 의해 지정됨)가 있으며 모든 사람이 이를 볼 수 있습니다.</p>
<p><code>UserDetailsService</code> 빈은 단일 사용자로 메모리 내 사용자 저장소를 설정합니다. 해당 사용자에게는 <code>user</code> 이름, 비밀번호 <code>password</code>, role <code>USER</code>가 부여됩니다.</p>
<p>이제 로그인 페이지를 생성해야 합니다. <code>login</code> 뷰를 위한 뷰 컨트롤러가 이미 있으므로 다음 목록(src/main/resources/templates/login.html)에서 볼 수 있듯이 로그인 뷰 자체만 생성하면 됩니다.</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/1999/xhtml<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Spring Security Example <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name"><span class="token namespace">th:</span>if</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${param.error}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            Invalid username and password.
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name"><span class="token namespace">th:</span>if</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${param.logout}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            You have been logged out.
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name"><span class="token namespace">th:</span>action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>@{/login}<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>post<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>label</span><span class="token punctuation">&gt;</span></span> User Name : <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span> <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>label</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>label</span><span class="token punctuation">&gt;</span></span> Password: <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span> <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>label</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Sign In<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 Thymeleaf 템플릿은 사용자 이름과 비밀번호를 캡처하여 <code>/login</code>에 게시하는 양식을 제공합니다. 구성된 대로 Spring Security는 해당 요청을 가로채고 사용자를 인증하는 필터를 제공합니다. 사용자가 인증에 실패하면 페이지가 <code>/login?error</code>로 리디렉션되고 페이지에 적절한 오류 메시지가 표시됩니다. 로그아웃에 성공하면 애플리케이션이 <code>/login?logout</code>으로 전송되고 페이지에 적절한 성공 메시지가 표시됩니다.</p>
<p>마지막으로 방문자에게 현재 사용자 이름을 표시하고 로그아웃할 수 있는 방법을 제공해야 합니다. 이렇게 하려면 <code>hello.html</code>을 업데이트하여 현재 사용자에게 인사하고 <code>Sign Out</code> 양식을 포함하십시오(src/main/resources/templates/hello.html의 다음 목록 참조).</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/1999/xhtml<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">xmlns:</span>th</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org<span class="token punctuation">"</span></span>
      <span class="token attr-name"><span class="token namespace">xmlns:</span>sec</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://www.thymeleaf.org/thymeleaf-extras-springsecurity6<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Hello World!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span> <span class="token attr-name"><span class="token namespace">th:</span>inline</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Hello <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>span</span> <span class="token attr-name"><span class="token namespace">th:</span>remove</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tag<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">sec:</span>authentication</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>name<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>thymeleaf<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>span</span><span class="token punctuation">&gt;</span></span>!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name"><span class="token namespace">th:</span>action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>@{/logout}<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>post<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Sign Out<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>Thymeleaf와 Spring Security의 통합을 사용하여 사용자 이름을 표시합니다. "로그아웃" 양식은 <code>/logout</code>에 POST를 제출합니다. 성공적으로 로그아웃되면 사용자를 <code>/login?logout</code>으로 리디렉션합니다.</p>
<blockquote>
<p>Thymeleaf 3.1은 더 이상 <code>HttpServletRequest</code>에 대한 액세스를 제공하지 않으므로 <code>HttpServletRequest#getRemoteUser()</code>를 사용하여 현재 인증된 사용자에 액세스할 수 없습니다.</p>
</blockquote>
<h2 id="애플리케이션-실행">애플리케이션 실행</h2>
<p>Spring 초기화는 당신을 위해 애플리케이션 클래스를 생성합니다. 이 경우 클래스를 수정할 필요가 없습니다. 다음 목록(src/main/java/guides/securingweb/SecuringWebApplication.java)은 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>securingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SecuringWebApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">SecuringWebApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/82489ab5-0be1-4200-938a-332ca433909c/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a2cab710-1f69-496f-a76f-94c6d4d88f9b/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/dd6f1a0b-1f62-49ef-8ee4-e73ed8bbfc09/image.png"></p>
<p>링크를 클릭하면 <code>/hello</code>의 인사말 페이지로 이동하려고 시도합니다. 그러나 해당 페이지는 보안되어 있고 아직 로그인하지 않았으므로 다음 이미지와 같이 로그인 페이지로 이동합니다.</p>
<p>로그인 페이지에서 사용자 이름과 비밀번호 필드에 각각 사용자와 비밀번호를 입력하여 테스트 사용자로 로그인합니다. 로그인 양식을 제출하면 인증을 받은 후 다음 이미지와 같이 인사말 페이지로 이동됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9eadf76e-1010-46d5-992e-5efabab0738c/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/13528374-f0b3-4ec8-9269-16e976014c5b/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0f9089be-460c-47b1-acdd-49f95ce08317/image.png"></p>
<p>로그아웃 버튼을 클릭하면 인증이 취소되며, 로그아웃되었다는 메시지와 함께 로그인 페이지로 돌아갑니다.</p>