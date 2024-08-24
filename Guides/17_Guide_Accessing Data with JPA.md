<p>이 가이드는 Spring Data JPA를 사용하여 관계형 데이터베이스에 데이터를 저장하고 검색하는 애플리케이션을 구축하는 과정을 안내합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>메모리 기반 데이터베이스에 <code>Customer</code> POJO(Plain Old Java Objects)를 저장하는 애플리케이션을 구축합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/eb4eb8d6-4560-4984-9b4b-79a6bac57da3/image.png"></p>
<h2 id="define-a-simple-entity">Define a Simple Entity</h2>
<p>이 예에서는 각각 JPA 엔터티로 주석이 달린 <code>Customer</code> 개체를 저장합니다. 다음 목록은 Customer 클래스(src/main/java/guides/accessingdatajpa/Customer.java에 있음)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatajpa</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Entity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GeneratedValue</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GenerationType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Entity</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Customer</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Long</span> id<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>

    <span class="token keyword">protected</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span><span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">,</span> <span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>
                <span class="token string">"Customer[id = %d, firstName ='%s', lastName = '%s']"</span><span class="token punctuation">,</span>
                id<span class="token punctuation">,</span> firstName<span class="token punctuation">,</span> lastName
        <span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Long</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getFirstName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> firstName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getLastName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>여기에는 <code>id</code>, <code>firstName</code> 및 <code>lastName</code>의 세 가지 속성이 있는 <code>Customer</code> 클래스가 있습니다. 또한 두 개의 생성자가 있습니다. 기본(default) 생성자는 JPA를 위해서만 존재합니다. 직접 사용하지 않으니 보호됨(<code>protected</code>)으로 지정되어 있습니다. 다른 생성자는 데이터베이스에 저장할 <code>Customer</code> 인스턴스를 만드는 데 사용하는 생성자입니다.</p>
<p><code>Customer</code> 클래스에는 <code>@Entity</code>라는 주석이 붙어 JPA 엔터티임을 나타냅니다. (<code>@Table</code> 주석이 존재하지 않으므로 이 엔터티는 <code>Customer</code>라는 테이블에 매핑된 것으로 가정합니다.)</p>
<p>JPA가 이를 객체의 ID로 인식할 수 있도록 <code>Customer</code> 객체의 <code>id</code> 속성에는 <code>@Id</code>라는 주석이 붙습니다. <code>id</code> 속성에는 <code>@GeneratedValue</code> 주석이 추가되어 ID가 자동으로 생성되어야 함을 나타냅니다.</p>
<p>다른 두 속성인 <code>firstName</code>과 <code>lastName</code>은 주석이 추가되지 않은 상태로 유지됩니다. 속성 자체와 동일한 이름을 공유하는 열에 매핑된다고 가정합니다.</p>
<p>편리한 <code>toString()</code> 메소드는 고객의 속성을 인쇄합니다.</p>
<hr>
<p>Java에서 클래스에 명시적 생성자가 없을 때, 컴파일러는 기본 생성자를 자동으로 추가합니다. 그러나 클래스에 다른 생성자가 이미 선언되어 있다면, 명시적으로 생성자를 선언하거나 기본 생성자를 추가해야 합니다.</p>
<p>여기서 <code>protected Customer() {}</code>는 기본 생성자를 명시적으로 선언한 것입니다. 이렇게 하지 않으면 기본적으로 제공되는 매개변수 없는 생성자인 기본 생성자가 public이 아니라 protected 또는 private으로 제한될 수 있습니다. 그렇게 되면 외부에서 해당 생성자를 사용할 수 없게 됩니다. </p>
<p>JPA에서는 엔티티 클래스가 매개변수 없는 public 또는 protected 생성자를 가져야 합니다. 이는 JPA가 객체를 올바르게 생성하고 관리하기 위해 필요합니다. 만약 명시적으로 생성자를 선언하지 않으면 기본 생성자를 컴파일러가 추가하지만, 여기서 <code>protected Customer() {}</code>와 같이 생성자를 선언하는 것이 좋은 습관입니다.</p>
<hr>
<h2 id="create-simple-queries">Create Simple Queries</h2>
<p>Spring Data JPA는 JPA를 사용하여 관계형 데이터베이스에 데이터를 저장하는 데 중점을 둡니다. 가장 강력한 기능은 저장소 인터페이스에서 런타임 시 자동으로 저장소 구현을 생성하는 기능입니다.</p>
<p>이것이 어떻게 작동하는지 보려면 다음 목록(src/main/java/guides/accessingdatajpa/CustomerRepository.java)에 표시된 대로 <code>Customer</code> 엔터티와 작동하는 저장소 인터페이스를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatajpa</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">CrudRepository</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CustomerRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">,</span> <span class="token class-name">Long</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    
    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Customer</span> <span class="token function">findById</span><span class="token punctuation">(</span><span class="token keyword">long</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>CustomerRepository</code>는 <code>CrudRepository</code> 인터페이스를 확장합니다. 작업하는 엔터티 유형과 ID인 <code>Customer</code> 및 <code>Long</code>은 <code>CrudRepository</code>의 generic 매개변수에 지정됩니다. <code>CrudRepository</code>를 확장함으로써 <code>CustomerRepository</code>는 <code>Customer</code> 엔터티를 저장, 삭제 및 찾는 방법을 포함하여 <code>Customer</code> persistence 작업을 위한 여러 방법을 상속합니다.</p>
<p>Spring Data JPA를 사용하면 메소드 signature를 선언하여 다른 쿼리 메소드를 정의할 수도 있습니다. 예를 들어 <code>CustomerRepository</code>에는 <code>findByLastName()</code> 메서드가 포함되어 있습니다.</p>
<p>일반적인 Java 애플리케이션에서는 <code>CustomerRepository</code>를 구현하는 클래스를 작성할 것으로 예상할 수 있습니다. 그러나 이것이 Spring Data JPA를 매우 강력하게 만드는 이유입니다. 저장소 인터페이스의 구현을 작성할 필요가 없습니다. Spring Data JPA는 애플리케이션을 실행할 때 구현을 생성합니다.</p>
<p>이제 이 예제를 연결하고 어떻게 보이는지 확인할 수 있습니다!</p>
<hr>
<p>Spring Data JPA에서 <code>CrudRepository</code>와 <code>JpaRepository</code>는 둘 다 JPA 기능을 제공하는 인터페이스입니다. 하지만 각각의 차이점이 있습니다.</p>
<ol>
<li>
<p><strong>CrudRepository:</strong> 기본적인 CRUD(Create, Read, Update, Delete) 기능을 제공하는 인터페이스입니다. 이 인터페이스는 CRUD 메서드를 제공하여 데이터베이스의 엔티티를 다루는 기본적인 작업을 할 수 있도록 도와줍니다.</p>
</li>
<li>
<p><strong>JpaRepository:</strong> <code>JpaRepository</code>는 <code>CrudRepository</code>의 기능을 상속하면서 추가적으로 JPA에 특화된 메서드들을 제공합니다. 이 인터페이스는 <code>PagingAndSortingRepository</code>를 상속하며, 페이징과 정렬 기능을 제공하고 일반적인 CRUD 외에도 JPA에서 제공하는 다양한 기능들을 사용할 수 있도록 합니다. <code>JpaRepository</code>는 보다 더 확장된 JPA 관련 기능을 사용할 수 있게 해줍니다.</p>
</li>
</ol>
<p>따라서 기본적인 CRUD 작업만 필요한 경우에는 <code>CrudRepository</code>를 사용할 수 있고, JPA와 관련된 더 많은 기능이 필요한 경우에는 <code>JpaRepository</code>를 사용하는 것이 좋습니다.</p>
<hr>
<h2 id="create-an-application-class">Create an Application Class</h2>
<p>Spring 초기화는 애플리케이션을 위한 간단한 클래스를 생성합니다. 다음 목록은 이 예제를 위해 Initializr가 생성한 클래스(src/main/java/guides/accessingdatajpa/AccessingDataJpaApplication.java)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatajpa</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccessingDataJpaApplication</span> <span class="token punctuation">{</span>

  <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> log <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataJpaApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataJpaApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">demo</span><span class="token punctuation">(</span><span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">(</span>args<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
      <span class="token comment">// save a few customers</span>
      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Jack"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Chloe"</span><span class="token punctuation">,</span> <span class="token string">"O'Brian"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Kim"</span><span class="token punctuation">,</span> <span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"David"</span><span class="token punctuation">,</span> <span class="token string">"Palmer"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Michelle"</span><span class="token punctuation">,</span> <span class="token string">"Dessler"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

      <span class="token comment">// fetch all customers</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customers found with findAll():"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>customer <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

      <span class="token comment">// fetch an individual customer by ID</span>
      <span class="token class-name">Customer</span> customer <span class="token operator">=</span> repository<span class="token punctuation">.</span><span class="token function">findById</span><span class="token punctuation">(</span><span class="token number">1L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findById(1L):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>customer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

      <span class="token comment">// fetch customers by last name</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Customer found with findByLastName('Bauer'):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"--------------------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      repository<span class="token punctuation">.</span><span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token string">"Bauer"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">forEach</span><span class="token punctuation">(</span>bauer <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>bauer<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><code>AccessingDataJpaApplication</code> 클래스에는 몇 가지 테스트를 통해 <code>CustomerRepository</code>를 실행하는 <code>demo()</code> 메서드가 포함되어 있습니다. 먼저 Spring 애플리케이션 컨텍스트에서 <code>CustomerRepository</code>를 가져옵니다. 그런 다음 소수의 <code>Customer</code> 개체를 저장하고 <code>save()</code> 메서드를 시연하고 작업할 일부 데이터를 설정합니다. 다음으로 <code>findAll()</code>을 호출하여 데이터베이스에서 모든 <code>Customer</code> 개체를 가져옵니다. 그런 다음 <code>findById()</code>를 호출하여 해당 ID로 단일 <code>Customer</code>를 가져옵니다. 마지막으로 <code>findByLastName()</code>을 호출하여 성이 "Bauer"인 모든 고객을 찾습니다. <code>demo()</code> 메소드는 애플리케이션이 시작될 때 자동으로 코드를 실행하는 <code>CommandLineRunner</code> Bean을 반환합니다.</p>
<blockquote>
<p>기본적으로 Spring Boot는 JPA 저장소 지원을 활성화하고 <code>@SpringBootApplication</code>이 있는 패키지(및 해당 하위 패키지)를 찾습니다. 구성에 표시되지 않는 패키지에 JPA 저장소 인터페이스 정의가 있는 경우 <code>@EnableJpaRepositories</code> 및 해당 type-safe <code>basePackageClasses=MyRepository.class</code> 매개변수를 사용하여 대체 패키지를 가리킬 수 있습니다.</p>
</blockquote>
<hr>
<p><code>main</code> 메서드의 <code>args</code> 매개변수는 프로그램이 실행될 때 외부에서 전달되는 인자를 담고 있는 문자열 배열입니다. 이 배열은 외부에서 프로그램에 전달된 인자들을 담고 있으며, 예를 들어 프로그램 실행 시에 커맨드 라인에서 전달되는 인자들이 이 배열에 저장됩니다.</p>
<ol>
<li>
<p><code>main</code> 함수에서 <code>args</code> 매개변수가 사용되지 않는 이유는 해당 애플리케이션이 외부에서 인자를 받아 처리하지 않는다는 것을 의미할 수 있습니다. Spring Boot 애플리케이션에서는 <code>SpringApplication.run</code>을 호출할 때 인자를 전달하지 않아도 정상적으로 애플리케이션이 실행됩니다. 그래서 여기서는 <code>args</code>를 사용하지 않았을 가능성이 있습니다.</p>
</li>
<li>
<p><code>CommandLineRunner</code>는 <code>run</code> 메서드를 정의한 함수형 인터페이스입니다. 여기서 람다 표현식 <code>(args) -&gt; { ... }</code>는 <code>CommandLineRunner</code>의 <code>run</code> 메서드를 오버라이드하는 함수이므로 내부적으로 이 매개변수를 사용하는 것이 일반적입니다. 그러나 여기서는 <code>args</code> 매개변수를 사용하지 않아도 람다 표현식이 잘 작동할 수 있습니다. 이는 <code>args</code>를 사용하지 않아도 될 때, 즉 해당 람다 표현식 내부에서 외부 매개변수를 참조할 필요가 없는 경우에 해당합니다. 따라서 <code>args</code> 매개변수를 사용하지 않더라도 <code>run</code> 메서드의 시그니처를 따르는 람다 표현식을 사용할 수 있습니다.</p>
</li>
</ol>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5d2f1dfc-cdb1-48e7-b935-f6843f284f24/image.png"><img src="https://velog.velcdn.com/images/dev_hammy/post/17f53da1-ba4a-461e-a409-6de0cd061f22/image.png"></p>