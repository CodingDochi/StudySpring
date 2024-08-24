<p><a href="https://docs.spring.io/spring-ldap/docs/current/reference/">https://docs.spring.io/spring-ldap/docs/current/reference/</a></p>
<p>이 가이드는 애플리케이션을 생성하고 Spring Security LDAP 모듈로 보안을 유지하는 프로세스를 안내합니다.</p>
<h2 id="ldap">LDAP</h2>
<p><strong>LDAP란?</strong></p>
<ul>
<li>네트워크 프로토콜로, 디렉터리 서비스에 접근하는 데 사용</li>
<li>디렉터리 서비스: 사용자, 컴퓨터, 리소스와 같은 데이터를 저장하고 관리</li>
<li>표준화된 방식으로 데이터 읽기, 변경</li>
</ul>
<p><strong>LDAP의 주요 용도</strong></p>
<ul>
<li>사용자 인증</li>
<li>디렉터리 검색</li>
<li>이메일 클라이언트 설정</li>
<li>인터넷 서비스</li>
</ul>
<p><strong>LDAP의 특징</strong></p>
<ul>
<li>계층적인 구조로 정보 저장</li>
<li>간단하고 확장 가능</li>
<li>기업 환경에서 인프라로 사용</li>
<li>유연, 보안 강화, 중앙 집중식 디렉터리 서비스 제공</li>
</ul>
<p><strong>LDAP의 장점</strong></p>
<ul>
<li>표준화된 방식으로 디렉터리 서비스에 접근 가능</li>
<li>다양한 용도로 사용 가능</li>
<li>계층적인 구조로 정보 관리 효율적</li>
<li>보안 강화</li>
<li>중앙 집중식 디렉터리 서비스 제공</li>
</ul>
<p><strong>LDAP의 단점</strong></p>
<ul>
<li>데이터 저장에 비용이 많이 들 수 있음</li>
<li>성능이 저하될 수 있음</li>
</ul>
<p><strong>LDAP의 활용 사례</strong></p>
<ul>
<li>Active Directory</li>
<li>OpenLDAP</li>
<li>Red Hat Directory Server</li>
<li>389 Directory Server</li>
</ul>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Security에 내장된 Java 기반 LDAP 서버로 보호되는 간단한 웹 애플리케이션을 구축합니다. 사용자 집합이 포함된 데이터 파일을 사용하여 LDAP 서버를 로드합니다.</p>
<h2 id="first-build-an-unsecured-web-application">First, Build an Unsecured Web Application</h2>
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
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h3 id="create-a-simple-web-controller">Create a Simple Web Controller</h3>
<p>Spring에서 REST 엔드포인트는 Spring MVC 컨트롤러입니다. 다음 Spring MVC 컨트롤러(src/main/java/guides/authenticatingldap/HomeController.java)는 간단한 메시지를 반환하여 GET / 요청을 처리합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>authenticatingldap</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HomeController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">index</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Welcome to the home page!"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>전체 클래스는 <code>@RestController</code>로 표시되어 Spring MVC가 컨트롤러를 자동 감지하고(내장된 검색 기능을 사용하여) 필요한 웹 경로를 자동으로 구성할 수 있습니다.</p>
<p><code>@RestController</code>는 또한 뷰가 없기 때문에 Spring MVC에게 텍스트를 HTTP 응답 본문에 직접 쓰라고 지시합니다. 대신 페이지를 방문하면 브라우저에 간단한 메시지가 표시됩니다(이 가이드의 초점은 LDAP로 페이지를 보호하는 것이기 때문입니다).</p>
<h3 id="build-the-unsecured-web-application">Build the Unsecured Web Application</h3>
<p>보안되지 않은 웹 애플리케이션 구축<br>
웹 애플리케이션을 보호하기 전에 웹 애플리케이션이 작동하는지 확인해야 합니다. 그렇게 하려면 몇 가지 핵심 Bean을 정의해야 하며, 이는 <code>Application</code> 클래스를 생성하여 수행할 수 있습니다. 다음 목록(src/main/java/guides/authenticatingldap/AuthenticatingLdapApplication.java)은 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>authenticatingldap</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AuthenticatingLdapApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AuthenticatingLdapApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>브라우저를 열고 <code>http://localhost:8080</code>을 방문하면 다음 일반 텍스트가 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a4e4c5b0-3182-4248-b079-66df6209e803/image.png"></p>
<h2 id="set-up-spring-security">Set up Spring Security</h2>
<p>Spring Security를 구성하려면 먼저 빌드에 몇 가지 추가 종속성을 추가해야 합니다.</p>
<pre><code class="language-null">implementation("org.springframework.boot:spring-boot-starter-security")
implementation("org.springframework.ldap:spring-ldap-core")
implementation("org.springframework.security:spring-security-ldap")
implementation("com.unboundid:unboundid-ldapsdk")</code></pre>
<blockquote>
<p>Gradle의 아티팩트 해결 문제로 인해 <code>spring-tx</code>를 가져와야 합니다. 그렇지 않으면 Gradle이 작동하지 않는 이전 버전을 가져옵니다.</p>
<pre><code class="language-null">implementation 'org.springframework:spring-tx:6.1.0'</code></pre>
</blockquote>
<p>이러한 종속성은 Spring Security와 오픈 소스 LDAP 서버인 <code>UnboundId</code>를 추가합니다. 이러한 종속성이 적용되면 다음 예제(src/main/java/guides/authenticatingldap/WebSecurityConfig.java)에 표시된 대로 순수 Java를 사용하여 보안 정책을 구성할 수 있습니다.</p>
<h3 id="websecurityconfig">WebSecurityConfig</h3>
<p>또한 LDAP 서버가 필요합니다. Spring Boot은 순수 Java로 작성된 내장 서버에 대한 자동 구성을 제공하며, 이 가이드에서 사용됩니다. <code>ldapAuthentication()</code> 메서드는 로그인 폼의 사용자 이름을 <code>{0}</code>에 삽입하여 <code>uid={0},ou=people,dc=springframework,dc=org</code> 형태로 LDAP 서버에서 검색하도록 설정합니다. 또한 <code>passwordCompare()</code> 메서드는 인코더와 암호 속성의 이름을 구성합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>authenticatingldap</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>authentication<span class="token punctuation">.</span>builders</span><span class="token punctuation">.</span><span class="token class-name">AuthenticationManagerBuilder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>web<span class="token punctuation">.</span>builders</span><span class="token punctuation">.</span><span class="token class-name">HttpSecurity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>crypto<span class="token punctuation">.</span>bcrypt</span><span class="token punctuation">.</span><span class="token class-name">BCryptPasswordEncoder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>security<span class="token punctuation">.</span>web</span><span class="token punctuation">.</span><span class="token class-name">SecurityFilterChain</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSecurityConfig</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">SecurityFilterChain</span> <span class="token function">securityFilterChain</span><span class="token punctuation">(</span><span class="token class-name">HttpSecurity</span> http<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        http
                <span class="token punctuation">.</span><span class="token function">authorizeRequests</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">anyRequest</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">fullyAuthenticated</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">formLogin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> http<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configure</span><span class="token punctuation">(</span><span class="token class-name">AuthenticationManagerBuilder</span> auth<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        auth
                <span class="token punctuation">.</span><span class="token function">ldapAuthentication</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">userDnPatterns</span><span class="token punctuation">(</span><span class="token string">"uid={0},ou=people"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">groupSearchBase</span><span class="token punctuation">(</span><span class="token string">"ou=groups"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">contextSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">url</span><span class="token punctuation">(</span><span class="token string">"ldap://localhost:8389/dc=springframework,dc=org"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">passwordCompare</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">passwordEncoder</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">BCryptPasswordEncoder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">passwordAttribute</span><span class="token punctuation">(</span><span class="token string">"userPassword"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>

<span class="token comment">//@Configuration</span>
<span class="token comment">//public class WebSecurityConfig {</span>

<span class="token comment">//  @Bean</span>
<span class="token comment">//  public SecurityFilterChain configure(HttpSecurity http) throws Exception {</span>
<span class="token comment">//    return http</span>
<span class="token comment">//      .authorizeRequests()</span>
<span class="token comment">//      .anyRequest().authenticated()</span>
<span class="token comment">//      .and()</span>
<span class="token comment">//      .formLogin(Customizer.withDefaults())</span>
<span class="token comment">//      .build();</span>
<span class="token comment">//  }</span>
<span class="token comment">//}</span></code></pre>
<p>주석 처리된 코드는 Spring Security 5.7 이전 버전에서 사용되던 코드입니다. Spring Security 5.7부터는 <code>SecurityFilterChain</code> 인터페이스를 사용하여 컨피규레이션해야 하므로 기존 코드는 더 이상 사용되지 않습니다.</p>
<p>주석 처리된 코드와 현재 코드의 차이점은 다음과 같습니다:</p>
<ol>
<li>
<p>인증 방식:</p>
<ul>
<li>주석 처리된 코드: <code>anyRequest().authenticated()</code></li>
<li>현재 코드: <code>anyRequest().fullyAuthenticated()</code></li>
</ul>
</li>
<li>
<p>formLogin 사용:</p>
<ul>
<li>주석 처리된 코드: <code>formLogin(Customizer.withDefaults())</code></li>
<li>현재 코드: 명시적으로 설정하지 않음</li>
</ul>
</li>
<li>
<p>LDAP 인증 설정:</p>
<ul>
<li>주석 처리된 코드: <code>ldapAuthentication()</code>에 설정 포함</li>
<li>현재 코드: <code>configure(AuthenticationManagerBuilder)</code> 메서드에 설정 분리</li>
</ul>
</li>
</ol>
<p>현재 코드는 <code>SecurityFilterChain</code> 인터페이스를 사용하여 컨피규레이션을 구성하고, <code>anyRequest().fullyAuthenticated()</code> 메서드를 사용하여 모든 요청에 대해 완전한 인증(인증 및 권한 부여)을 요구합니다. 또한, <code>formLogin</code> 설정을 명시적으로 설정하지 않아 Spring Security의 기본 폼 로그인 기능을 사용합니다.</p>
<p>주석 처리된 코드는 <code>anyRequest().authenticated()</code> 메서드를 사용하여 모든 요청에 대해 단순히 인증만 요구합니다. 또한, <code>formLogin(Customizer.withDefaults())</code> 메서드를 사용하여 Spring Security의 기본 폼 로그인 기능을 사용합니다.</p>
<p>따라서, Spring Security 5.7 이후 버전에서는 주석 처리된 코드 대신 현재 코드를 사용하는 것이 좋습니다.</p>
<h3 id="add-application-properties">Add Application Properties</h3>
<p>Spring LDAP에서는 <code>application.properties</code> 파일에 세 가지 애플리케이션 속성을 설정해야 합니다.</p>
<pre><code class="language-null">spring.ldap.embedded.ldif=classpath:test-server.ldif
spring.ldap.embedded.base-dn=dc=springframework,dc=org
spring.ldap.embedded.port=8389</code></pre>
<h2 id="set-up-user-data">Set up User Data</h2>
<p>LDAP 서버는 LDIF(LDAP Data Interchange Format) 파일을 사용하여 사용자 데이터를 교환할 수 있습니다. <code>application.properties</code> 내의 <code>spring.ldap.embedded.ldif</code> 속성을 사용하면 Spring Boot가 LDIF 데이터 파일을 가져올 수 있습니다. 이를 통해 데모 데이터를 쉽게 사전 로드할 수 있습니다. 다음 목록(src/main/resources/test-server.ldif에 있음)은 이 예에서 작동하는 LDIF 파일을 보여줍니다.</p>
<pre><code class="language-null">dn: dc=springframework,dc=org
objectclass: top
objectclass: domain
objectclass: extensibleObject
dc: springframework

dn: ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=subgroups,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: subgroups

dn: ou=people,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: people

dn: ou=space cadets,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: space cadets

dn: ou=\"quoted people\",dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: "quoted people"

dn: ou=otherpeople,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: otherpeople

dn: uid=ben,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Ben Alex
sn: Alex
uid: ben
userPassword: $2a$10$c6bSeWPhg06xB1lvmaWNNe4NROmZiSpYhlocU/98HNr2MhIOiSt36

dn: uid=bob,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Bob Hamilton
sn: Hamilton
uid: bob
userPassword: bobspassword

dn: uid=joe,ou=otherpeople,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Joe Smeth
sn: Smeth
uid: joe
userPassword: joespassword

dn: cn=mouse\, jerry,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Mouse, Jerry
sn: Mouse
uid: jerry
userPassword: jerryspassword

dn: cn=slash/guy,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: slash/guy
sn: Slash
uid: slashguy
userPassword: slashguyspassword

dn: cn=quote\"guy,ou=\"quoted people\",dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: quote\"guy
sn: Quote
uid: quoteguy
userPassword: quoteguyspassword

dn: uid=space cadet,ou=space cadets,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Space Cadet
sn: Cadet
uid: space cadet
userPassword: spacecadetspassword



dn: cn=developers,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: developers
ou: developer
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org
uniqueMember: uid=bob,ou=people,dc=springframework,dc=org

dn: cn=managers,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: managers
ou: manager
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org
uniqueMember: cn=mouse\, jerry,ou=people,dc=springframework,dc=org

dn: cn=submanagers,ou=subgroups,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfUniqueNames
cn: submanagers
ou: submanager
uniqueMember: uid=ben,ou=people,dc=springframework,dc=org</code></pre>
<blockquote>
<p>LDIF 파일 사용은 프로덕션 시스템의 표준 구성이 아닙니다. 그러나 테스트 목적이나 가이드에는 유용합니다.</p>
</blockquote>
<h2 id="실행해보기">실행해보기</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/21a4d87e-aecf-460a-8744-bf18c709fad9/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f81ed59c-3938-4b4f-8d46-a87d043d2265/image.png"></p>
<p><code>http://localhost:8080</code> 사이트에 접속하면 Spring Security에서 제공하는 로그인 페이지로 이동하게 된다.</p>
<p>사용자 이름 <code>ben</code>과 비밀번호 <code>benspassword</code>를 입력합니다. 브라우저에 다음 메시지가 표시되어야 합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/84e0d558-fb9e-42cc-909d-5fe37d72033a/image.png"></p>