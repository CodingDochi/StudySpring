<p>이 가이드는 Spring Data JPA 기반 백엔드에서 Vaadin 기반 UI를 사용하는 애플리케이션을 구축하는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>간단한 JPA 저장소를 위한 Vaadin UI를 빌드합니다. 당신이 얻게 될 것은 완전한 CRUD(생성, 읽기, 업데이트 및 삭제) 기능을 갖춘 애플리케이션과 사용자 정의 저장소 방법을 사용하는 필터링 예제입니다.</p>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-groovy">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.3'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'com.vaadin' version '24.3.5'
}

group = 'guide'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

ext {
    set('vaadinVersion', "24.3.5")
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'com.vaadin:vaadin-spring-boot-starter'
    runtimeOnly 'com.h2database:h2'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
    imports {
        mavenBom "com.vaadin:vaadin-bom:${vaadinVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h2 id="create-the-backend-services">Create the Backend Services</h2>
<p>이 가이드는 <a href="https://spring.io/guides/gs/accessing-data-jpa">Accessing Data with JPA</a>의 연속입니다. 유일한 차이점은 엔터티 클래스에 getter 및 setter가 있고 저장소의 사용자 정의 검색 방법이 최종 사용자에게 좀 더 적합하다는 점입니다. 이 가이드를 살펴보기 위해 해당 가이드를 읽을 필요는 없지만 원할 경우 읽을 수 있습니다.</p>
<p>새로운 프로젝트로 시작한 경우 엔터티 및 저장소 개체를 추가해야 합니다. <code>initial</code> 프로젝트에서 시작한 경우 이러한 개체가 이미 존재합니다.</p>
<p>다음 목록(src/main/java/com/example/crudwithvaadin/Customer.java)은 고객 엔터티를 정의합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Entity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GeneratedValue</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Customer</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

    <span class="token keyword">protected</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">,</span> <span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setLastName</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>


    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"Customer[id=%d, firstName='%s', lastName='%s']"</span><span class="token punctuation">,</span> id<span class="token punctuation">,</span> firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 목록(src/main/java/com/example/crudwithvaadin/CustomerRepository.java)은 고객 저장소를 정의합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>jpa<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">JpaRepository</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CustomerRepository</span> <span class="token keyword">extends</span> <span class="token class-name">JpaRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastNameStartsWithIgnoreCase</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
<span class="token punctuation">}</span></code></pre>
<p>다음 목록(src/main/java/com/example/crudwithvaadin/CrudWithVaadinApplication.java)은 일부 데이터를 생성하는 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CrudWithVaadinApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> log <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">CrudWithVaadinApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>


    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">CrudWithVaadinApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">loadData</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span><span class="token punctuation">(</span>args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token comment">// save a couple of customers</span>
            repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Jack"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Chloe"</span><span class="token punctuation">,</span> <span class="token string">"O'Brian"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Kim"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"David"</span><span class="token punctuation">,</span> <span class="token string">"Palmer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Michelle"</span><span class="token punctuation">,</span> <span class="token string">"Dessler"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            
            <span class="token comment">// fetch all customers</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customers found with findAll():"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">Customer</span> customer<span class="token operator">:</span> repository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            
            <span class="token comment">// fetch an individual customer by ID</span>
            <span class="token class-name">Customer</span> customer <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findById</span><span class="token punctuation">(</span><span class="token number">1L</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findOne(1L):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            
            <span class="token comment">// fetch customers by last name</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findByLastNameStartsWithIgnoreCase('Bauer'):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"-----------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">Customer</span> bauer <span class="token operator">:</span> repository<span class="token punctuation">.</span><span class="token function">findByLastNameStartsWithIgnoreCase</span><span class="token punctuation">(</span><span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>bauer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="define-the-main-view-class">Define the Main View class</h2>
<p>메인 뷰 클래스(이 가이드에서는 <code>MainView</code>라고 함)는 Vaadin UI 로직의 진입점입니다. Spring Boot 애플리케이션에서 <code>@Route</code>로 주석을 달면 자동으로 선택되어 웹 애플리케이션의 루트(root)에 표시됩니다. <code>@Route</code> 주석에 매개변수를 제공하여 view가 표시되는 URL을 사용자 정의할 수 있습니다. 다음 목록(src/main/java/com/example/crudwithvaadin/MainView.java의 <code>initial</code> 프로젝트)은 간단한 "Hello, World" view를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>button</span><span class="token punctuation">.</span><span class="token class-name">Button</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>notification</span><span class="token punctuation">.</span><span class="token class-name">Notification</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">VerticalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>router</span><span class="token punctuation">.</span><span class="token class-name">Route</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Route</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MainView</span> <span class="token keyword">extends</span> <span class="token class-name">VerticalLayout</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">public</span> <span class="token class-name">MainView</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Button</span><span class="token punctuation">(</span><span class="token string">"Click me"</span><span class="token punctuation">,</span> e <span class="token operator">-&gt;</span> <span class="token class-name">Notification</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token string">"Hello, Spring+Vaadin user!"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1df51529-e0a1-4fe1-ab42-9982244ddb08/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/757cd239-fbc7-4a83-95be-f200d688a997/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/b98a02f3-7731-4fb6-bb46-26f78a8011ea/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/0c30b2c3-02ef-4261-ac3c-deecb624107c/image.png"></p>
<h2 id="list-entities-in-a-data-grid">List Entities in a Data Grid</h2>
<p>멋진 레이아웃을 위해서는 <code>Grid</code> component를 사용할 수 있습니다. <code>setItems</code> 메소드를 사용하여 생성자 주입 <code>CustomerRepository</code>의 엔터티 목록을 <code>Grid</code>로 전달할 수 있습니다. <code>MainView</code>의 본문은 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>grid</span><span class="token punctuation">.</span><span class="token class-name">Grid</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">VerticalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>router</span><span class="token punctuation">.</span><span class="token class-name">Route</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Route</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MainView</span> <span class="token keyword">extends</span> <span class="token class-name">VerticalLayout</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">;</span>
    <span class="token keyword">final</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> grid<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MainView</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>repo <span class="token operator">=</span> repo<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>grid <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>grid<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">listCustomer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">listCustomer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fa0cfc95-8eb2-4b00-97ac-158c850cfdad/image.png"></p>
<blockquote>
<p>테이블이 크거나 동시 사용자가 많은 경우 전체 데이터 세트를 UI 구성 요소에 바인딩하고 싶지 않을 가능성이 높습니다.<br>
Vaadin Grid는 서버에서 브라우저로 데이터를 지연 로드하지만 이전 접근 방식은 전체 데이터 목록을 서버 메모리에 유지합니다. 메모리를 절약하기 위해 페이징을 사용하거나 지연 로딩을 사용하여(예: <code>grid.setItems(VaadinSpringDataHelpers.fromPagingRepository(repo))</code> 메서드를 사용하여 최상위 결과만 표시할 수 있습니다.</p>
</blockquote>
<h2 id="filtering-the-data">Filtering the Data</h2>
<p>대규모 데이터 세트가 서버에 문제가 되기 전에 사용자가 편집할 관련 행을 찾으려고 할 때 골치 아픈 일이 될 수 있습니다. <code>TextField</code> 구성 요소를 사용하여 필터 항목을 만들 수 있습니다. 이렇게 하려면 먼저 필터링을 지원하도록 <code>listCustomer()</code> 메서드를 수정하세요. 다음 예제(src/main/java/com/example/crudwithvaadin/MainView.java의 전체 프로젝트)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span><span class="token class-name">String</span> filterText<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token class-name">StringUtils</span><span class="token punctuation">.</span><span class="token function">hasText</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findByLastNameStartsWithIgnoreCase</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
		grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p>filterText로 "Bauer"를 입력하고 애플리케이션을 실행하면 필터링된 결과가 출력됩니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/a30ac1ac-0419-4e65-8513-9a2d3cb59f62/image.png"></p>
<hr>
<blockquote>
<p>이것이 Spring Data의 선언적 쿼리가 유용한 곳입니다. <code>findByLastNameStartsWithIgnoringCase</code>를 작성하는 것은 <code>CustomerRepository</code> 인터페이스의 한 줄 정의입니다.</p>
</blockquote>
<p><code>TextField</code> 구성 요소에 리스너를 연결하고 해당 값을 해당 필터 메서드에 연결할 수 있습니다. <code>ValueChangeListener</code>는 필터 텍스트 필드에 <code>ValueChangeMode.LAZY</code>를 정의하기 때문에 사용자 유형에 따라 자동으로 호출됩니다. 다음 예에서는 이러한 리스너를 설정하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">TextField</span> filter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TextField</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
filter<span class="token punctuation">.</span><span class="token function">setPlaceholder</span><span class="token punctuation">(</span><span class="token string">"Filter by last name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
filter<span class="token punctuation">.</span><span class="token function">setValueChangeMode</span><span class="token punctuation">(</span><span class="token class-name">ValueChangeMode</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">)</span><span class="token punctuation">;</span>
filter<span class="token punctuation">.</span><span class="token function">addValueChangeListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span>e<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">add</span><span class="token punctuation">(</span>filter<span class="token punctuation">,</span> grid<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<hr>
<p>전체 코드</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>grid</span><span class="token punctuation">.</span><span class="token class-name">Grid</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">VerticalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>textfield</span><span class="token punctuation">.</span><span class="token class-name">TextField</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>data<span class="token punctuation">.</span>value</span><span class="token punctuation">.</span><span class="token class-name">ValueChangeMode</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>router</span><span class="token punctuation">.</span><span class="token class-name">Route</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">StringUtils</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Route</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MainView</span> <span class="token keyword">extends</span> <span class="token class-name">VerticalLayout</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">;</span>
    <span class="token keyword">final</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> grid<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MainView</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>repo <span class="token operator">=</span> repo<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>grid <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>grid<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">//        listCustomers("Bauer");</span>

        <span class="token class-name">TextField</span> filter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TextField</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        filter<span class="token punctuation">.</span><span class="token function">setPlaceholder</span><span class="token punctuation">(</span><span class="token string">"Filter by last name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        filter<span class="token punctuation">.</span><span class="token function">setValueChangeMode</span><span class="token punctuation">(</span><span class="token class-name">ValueChangeMode</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">)</span><span class="token punctuation">;</span>
        filter<span class="token punctuation">.</span><span class="token function">addValueChangeListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span>e<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>filter<span class="token punctuation">,</span> grid<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span><span class="token class-name">String</span> filterText<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token class-name">StringUtils</span><span class="token punctuation">.</span><span class="token function">hasText</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findByLastNameStartsWithIgnoreCase</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c3ba46ee-156f-4163-adba-26c183b20760/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/30f6d808-961d-4bb5-86eb-dd57d0b6c321/image.png"></p>
<h2 id="define-the-editor-component">Define the Editor Component</h2>
<p>Vaadin UI는 일반 Java 코드이므로 처음부터 재사용 가능한 코드를 작성할 수 있습니다. 이렇게 하려면 <code>Customer</code> 엔터티에 대한 편집기 구성 요소를 정의하세요. <code>CustomerRepository</code>를 편집기에 직접 삽입하고 부분 생성, 업데이트 및 삭제 또는 CRUD 기능을 처리할 수 있도록 이를 Spring 관리 빈으로 만들 수 있습니다. 다음 예제(src/main/java/com/example/crudwithvaadin/CustomerEditor.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component</span><span class="token punctuation">.</span><span class="token class-name">Key</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component</span><span class="token punctuation">.</span><span class="token class-name">KeyNotifier</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>button</span><span class="token punctuation">.</span><span class="token class-name">Button</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>button</span><span class="token punctuation">.</span><span class="token class-name">ButtonVariant</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>icon</span><span class="token punctuation">.</span><span class="token class-name">VaadinIcon</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">HorizontalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">VerticalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>textfield</span><span class="token punctuation">.</span><span class="token class-name">TextField</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>data<span class="token punctuation">.</span>binder</span><span class="token punctuation">.</span><span class="token class-name">Binder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>spring<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">SpringComponent</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>spring<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">UIScope</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * A simple example to introduce building forms. As your real application is probably much
 * more complicated than this example, you could re-use this form in multiple places. This
 * example component is only used in MainView.
 * &lt;p&gt;
 * In a real world application you'll most likely using a common super class for all your
 * forms - less code, better UX.
 */</span>

<span class="token annotation punctuation">@SpringComponent</span>
<span class="token annotation punctuation">@UIScope</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CustomerEditor</span> <span class="token keyword">extends</span> <span class="token class-name">VerticalLayout</span> <span class="token keyword">implements</span> <span class="token class-name">KeyNotifier</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">;</span>
    <span class="token comment">/**
     * The currently edited customer
     */</span>
    <span class="token keyword">private</span> <span class="token class-name">Customer</span> customer<span class="token punctuation">;</span>

    <span class="token comment">/* Fields to edit properties in Customer entity */</span>
    <span class="token class-name">TextField</span> firstName <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TextField</span><span class="token punctuation">(</span><span class="token string">"First name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">TextField</span> lastName <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TextField</span><span class="token punctuation">(</span><span class="token string">"Last name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">/* Action buttons */</span>
    <span class="token class-name">Button</span> save <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Button</span><span class="token punctuation">(</span><span class="token string">"Save"</span><span class="token punctuation">,</span> <span class="token class-name">VaadinIcon</span><span class="token punctuation">.</span>CHECK<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Button</span> cancel <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Button</span><span class="token punctuation">(</span><span class="token string">"Cancel"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Button</span> delete <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Button</span><span class="token punctuation">(</span><span class="token string">"Delete"</span><span class="token punctuation">,</span> <span class="token class-name">VaadinIcon</span><span class="token punctuation">.</span>TRASH<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">HorizontalLayout</span> actions <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HorizontalLayout</span><span class="token punctuation">(</span>save<span class="token punctuation">,</span> cancel<span class="token punctuation">,</span> delete<span class="token punctuation">)</span><span class="token punctuation">;</span>


    <span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">ChangeHandler</span> <span class="token punctuation">{</span>
        <span class="token keyword">void</span> <span class="token function">onChange</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setChangeHandler</span><span class="token punctuation">(</span><span class="token class-name">ChangeHandler</span> h<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// ChangeHandler is notified when either save or delete</span>
        <span class="token comment">// is clicked</span>
        changeHandler <span class="token operator">=</span> h<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token class-name">Binder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> binder <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Binder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">ChangeHandler</span> changeHandler<span class="token punctuation">;</span>

    <span class="token keyword">void</span> <span class="token function">delete</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        repository<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">;</span>
        changeHandler<span class="token punctuation">.</span><span class="token function">onChange</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">void</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">;</span>
        changeHandler<span class="token punctuation">.</span><span class="token function">onChange</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">void</span> <span class="token function">editCustomer</span><span class="token punctuation">(</span><span class="token class-name">Customer</span> c<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>c <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token function">setVisible</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">final</span> <span class="token keyword">boolean</span> persisted <span class="token operator">=</span> c<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">!=</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>persisted<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token comment">// Find fresh entity for editing</span>
            <span class="token comment">// In a more complex app, you might want to load</span>
            <span class="token comment">// the entity/DTO with lazy loaded relations for editing</span>
            customer <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findById</span><span class="token punctuation">(</span>c<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">else</span> <span class="token punctuation">{</span>
            customer <span class="token operator">=</span> c<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        cancel<span class="token punctuation">.</span><span class="token function">setVisible</span><span class="token punctuation">(</span>persisted<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Bind customer properties to similarly named fields</span>
        <span class="token comment">// Could also use annotation or "manual binding" or programmatically</span>
        <span class="token comment">// moving values from fields to entities before saving</span>
        binder<span class="token punctuation">.</span><span class="token function">setBean</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">setVisible</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Focus first name initially</span>
        firstName<span class="token punctuation">.</span><span class="token function">focus</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token class-name">CustomerEditor</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>repository <span class="token operator">=</span> repository<span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">,</span> actions<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">//bind using naming convention</span>
        binder<span class="token punctuation">.</span><span class="token function">bindInstanceFields</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">//Configure and style components;</span>
        <span class="token function">setSpacing</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        save<span class="token punctuation">.</span><span class="token function">addThemeVariants</span><span class="token punctuation">(</span><span class="token class-name">ButtonVariant</span><span class="token punctuation">.</span>LUMO_PRIMARY<span class="token punctuation">)</span><span class="token punctuation">;</span>
        delete<span class="token punctuation">.</span><span class="token function">addThemeVariants</span><span class="token punctuation">(</span><span class="token class-name">ButtonVariant</span><span class="token punctuation">.</span>LUMO_ERROR<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">addKeyPressListener</span><span class="token punctuation">(</span><span class="token class-name">Key</span><span class="token punctuation">.</span>ENTER<span class="token punctuation">,</span> e <span class="token operator">-&gt;</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// wire action buttons to save, delete and reset</span>
        save<span class="token punctuation">.</span><span class="token function">addClickListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        delete<span class="token punctuation">.</span><span class="token function">addClickListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">delete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        cancel<span class="token punctuation">.</span><span class="token function">addClickListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">editCustomer</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">setVisible</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>대규모 애플리케이션에서는 이 편집기 구성 요소를 여러 위치에서 사용할 수 있습니다. 또한 대규모 애플리케이션에서는 몇 가지 일반적인 패턴(예: MVP)을 적용하여 UI 코드를 구조화할 수도 있습니다.</p>
<h2 id="wire-the-editor">Wire the Editor</h2>
<p>이전 단계에서는 component 기반 프로그래밍의 몇 가지 기본 사항을 이미 살펴보았습니다. <code>Button</code>을 사용하고 선택 리스너를 <code>Grid</code>에 추가하면 편집기를 기본 보기에 완전히 통합할 수 있습니다. 다음 목록(src/main/java/com/example/crudwithvaadin/MainView.java)은 <code>MainView</code> 클래스의 최종 버전을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>button</span><span class="token punctuation">.</span><span class="token class-name">Button</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>grid</span><span class="token punctuation">.</span><span class="token class-name">Grid</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>icon</span><span class="token punctuation">.</span><span class="token class-name">VaadinIcon</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">HorizontalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>orderedlayout</span><span class="token punctuation">.</span><span class="token class-name">VerticalLayout</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>component<span class="token punctuation">.</span>textfield</span><span class="token punctuation">.</span><span class="token class-name">TextField</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>data<span class="token punctuation">.</span>value</span><span class="token punctuation">.</span><span class="token class-name">ValueChangeMode</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>vaadin<span class="token punctuation">.</span>flow<span class="token punctuation">.</span>router</span><span class="token punctuation">.</span><span class="token class-name">Route</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">StringUtils</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Route</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MainView</span> <span class="token keyword">extends</span> <span class="token class-name">VerticalLayout</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerEditor</span> editor<span class="token punctuation">;</span>
    <span class="token keyword">final</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> grid<span class="token punctuation">;</span>
    <span class="token keyword">final</span> <span class="token class-name">TextField</span> filter<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Button</span> addNewBtn<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MainView</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repo<span class="token punctuation">,</span> <span class="token class-name">CustomerEditor</span> editor<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>repo <span class="token operator">=</span> repo<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>editor <span class="token operator">=</span> editor<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>grid <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Grid</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token class-name">Customer</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>filter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TextField</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>addNewBtn <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Button</span><span class="token punctuation">(</span><span class="token string">"New customer"</span><span class="token punctuation">,</span> <span class="token class-name">VaadinIcon</span><span class="token punctuation">.</span>PLUS<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// build layout</span>
        <span class="token class-name">HorizontalLayout</span> actions <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HorizontalLayout</span><span class="token punctuation">(</span>filter<span class="token punctuation">,</span> addNewBtn<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>actions<span class="token punctuation">,</span> grid<span class="token punctuation">,</span> editor<span class="token punctuation">)</span><span class="token punctuation">;</span>

        grid<span class="token punctuation">.</span><span class="token function">setHeight</span><span class="token punctuation">(</span><span class="token string">"300px"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        grid<span class="token punctuation">.</span><span class="token function">setColumns</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">,</span> <span class="token string">"firstName"</span><span class="token punctuation">,</span> <span class="token string">"lastName"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        grid<span class="token punctuation">.</span><span class="token function">getColumnByKey</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setWidth</span><span class="token punctuation">(</span><span class="token string">"50px"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setFlexGrow</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        filter<span class="token punctuation">.</span><span class="token function">setPlaceholder</span><span class="token punctuation">(</span><span class="token string">"Filter by last name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Hook logic to components</span>

        <span class="token comment">// Replace listing with filtered content when user changes filter</span>
        filter<span class="token punctuation">.</span><span class="token function">setValueChangeMode</span><span class="token punctuation">(</span><span class="token class-name">ValueChangeMode</span><span class="token punctuation">.</span>LAZY<span class="token punctuation">)</span><span class="token punctuation">;</span>
        filter<span class="token punctuation">.</span><span class="token function">addValueChangeListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span>e<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">add</span><span class="token punctuation">(</span>filter<span class="token punctuation">,</span> grid<span class="token punctuation">)</span><span class="token punctuation">;</span>
    

        <span class="token comment">// Connect selected Customer to editor or hide if none is selected</span>
		grid<span class="token punctuation">.</span><span class="token function">asSingleSelect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">addValueChangeListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
        editor<span class="token punctuation">.</span><span class="token function">editCustomer</span><span class="token punctuation">(</span>e<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Instantiate and edit new Customer the new button is clicked</span>
            addNewBtn<span class="token punctuation">.</span><span class="token function">addClickListener</span><span class="token punctuation">(</span>e <span class="token operator">-&gt;</span> editor<span class="token punctuation">.</span><span class="token function">editCustomer</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">,</span> <span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
        <span class="token comment">// Listen changes made by the editor, refresh data from backend</span>
            editor<span class="token punctuation">.</span><span class="token function">setChangeHandler</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            editor<span class="token punctuation">.</span><span class="token function">setVisible</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token function">listCustomers</span><span class="token punctuation">(</span>filter<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
        <span class="token comment">// Initialize listing</span>
        <span class="token function">listCustomers</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token comment">// tag::listCustomers[]</span>
    <span class="token keyword">void</span> <span class="token function">listCustomers</span><span class="token punctuation">(</span><span class="token class-name">String</span> filterText<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token class-name">StringUtils</span><span class="token punctuation">.</span><span class="token function">hasText</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findByLastNameStartsWithIgnoreCase</span><span class="token punctuation">(</span>filterText<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            grid<span class="token punctuation">.</span><span class="token function">setItems</span><span class="token punctuation">(</span>repo<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    <span class="token comment">// end::listCustomers[]</span>

<span class="token punctuation">}</span></code></pre>
<h3 id="build-an-executable-jar">Build an executable JAR</h3>
<p>radle을 사용하는 경우 <code>./gradlew bootRun</code>을 사용하여 애플리케이션을 실행할 수 있습니다. 또는 다음과 같이 <code>./gradlew build</code>를 사용하여 JAR 파일을 빌드한 후 JAR 파일을 실행할 수 있습니다.</p>
<pre><code class="language-shell">java -jar build/libs/gs-crud-with-vaadin-0.1.0.jar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/759d985b-e428-46e9-b843-b9a05a8e80d6/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/edefbf14-d342-4928-8f96-16a51250d1bf/image.png"></p>
<p>프론트엔드 폴더가 생겼다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/358227e5-d692-4f18-88d2-593c4f4a6e63/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/6241a5bf-3d5a-48f7-8590-8f698ad67a1f/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/7698afbb-1443-430c-b6ab-f49b35d601bb/image.png"> <img src="https://velog.velcdn.com/images/dev_hammy/post/c6d169c0-f077-4368-a65e-c93aa4bf7917/image.png"></p>