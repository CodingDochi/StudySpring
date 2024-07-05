<p>이 가이드는 MySQL 데이터베이스(대부분의 다른 가이드와 많은 샘플 애플리케이션에서 사용하는 메모리 내 임베디드 데이터베이스와 반대)에 연결된 Spring 애플리케이션을 생성하는 과정을 안내합니다. 이는 데이터베이스에 액세스하기 위해 Spring Data JPA를 사용하지만 이는 가능한 많은 선택 중 하나일 뿐입니다(예를 들어 일반 Spring JDBC를 사용할 수 있음).</p>
<h2 id="mysql-설치">mysql 설치</h2>
<p><a href="https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/">https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/</a></p>
<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04">https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04</a></p>
<p><a href="https://www.ionos.com/digitalguide/websites/web-development/mysql-on-ubuntu-2204/">https://www.ionos.com/digitalguide/websites/web-development/mysql-on-ubuntu-2204/</a></p>
<h3 id="mysql-server-설치">mysql-server 설치</h3>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt</span> update
<span class="token function">sudo</span> <span class="token function">apt</span> <span class="token function">install</span> mysql-server</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2b5fa63d-b197-4181-a398-6ea07189a1b9/image.png"></p>
<h3 id="서버가-자동-실행-되었는지-확인">서버가 자동 실행 되었는지 확인</h3>
<pre><code class="language-shell">systemctl status mysql</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/cd108a47-5b7b-48e7-9b14-be12f09d47c9/image.png"></p>
<h3 id="사용자-비밀번호-설정">사용자 비밀번호 설정</h3>
<pre><code class="language-shell">$ <span class="token function">sudo</span> mysql</code></pre>
<pre><code class="language-sql"><span class="token keyword">ALTER</span> <span class="token keyword">USER</span> <span class="token string">'root'</span><span class="token variable">@'localhost'</span> IDENTIFIED <span class="token keyword">WITH</span> mysql_native_password <span class="token keyword">BY</span> <span class="token string">'비밀번호'</span><span class="token punctuation">;</span></code></pre>
<pre><code class="language-shell">mysql <span class="token operator">&gt;</span> <span class="token builtin class-name">exit</span></code></pre>
<h3 id="보안-스크립트-호출-및-설정">보안 스크립트 호출 및 설정</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6d36d53f-496d-440c-86cb-e46bb90aa605/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/286461db-6490-43fb-86ed-0add3522038a/image.png"></p>
<p>보안 스크립트를 실행하면 익명 유저, 테스트 데이터베이스, 루트 권한 사용자의 원격 이용 등 여러 보안 관련 설정에 대한 문답이 이어진다. 모든 변경사항을 적용하려면 mysql을 닫고 다시 실행한다. </p>
<h3 id="인증-프로세스-변경">인증 프로세스 변경</h3>
<pre><code class="language-shell">$ mysql -u root -p
ALTER <span class="token environment constant">USER</span> <span class="token string">'root'</span>@<span class="token string">'localhost'</span> IDENTIFIED WITH auth_socket<span class="token punctuation">;</span></code></pre>
<p><code>IDENTIFIED WITH auth_socket</code>은 MySQL의 사용자 계정이 운영 체제의 인증 소켓(authentication socket)을 통해 인증되도록 하는 것을 의미합니다. 이 설정은 사용자가 MySQL에 암호 없이 로그인할 수 있게 만듭니다. 즉, 해당 운영 체제에 로그인한 사용자는 MySQL에 암호 입력 없이 자동으로 접속할 수 있게 됩니다. 즉, <code>sudo mysql</code>로 접속할 수 있다.</p>
<h3 id="root-외에-데이터베이스-작업용-계정-만들기">root 외에 데이터베이스 작업용 계정 만들기</h3>
<pre><code class="language-shell">$ <span class="token function">sudo</span> mysql</code></pre>
<pre><code class="language-sql"><span class="token keyword">CREATE</span> <span class="token keyword">USER</span> <span class="token string">'username'</span><span class="token variable">@'host'</span> IDENTIFIED <span class="token keyword">WITH</span> authentication_plugin <span class="token keyword">BY</span> <span class="token string">'password'</span><span class="token punctuation">;</span></code></pre>
<p>사용자 인증 플러그인을 선택할 때 여러 가지 옵션이 있습니다. 앞서 언급한 <code>auth_socket</code> 플러그인은 유효한 사용자가 데이터베이스에 액세스하기 위해 비밀번호를 입력하지 않고도 강력한 보안을 제공하므로 편리할 수 있습니다. 그러나 이는 외부 프로그램이 MySQL과 상호 작용해야 할 때 상황을 복잡하게 만들 수 있는 원격 연결도 방지합니다.</p>
<p>대안으로, 사용자가 MySQL의 기본 플러그인인 <code>caching_sha2_password</code>를 사용하여 인증하도록 구문의 <code>WITH authentication_plugin</code> 부분을 완전히 생략할 수 있습니다. MySQL 문서에서는 강력한 보안 기능으로 인해 비밀번호로 로그인하려는 사용자에게 이 플러그인을 권장합니다.</p>
<p><code>caching_sha2_password</code>로 인증하는 사용자를 생성하려면 다음 명령을 실행합니다. <code>sammy</code>를 원하는 사용자 이름으로 변경하고 비밀번호를 선택한 강력한 비밀번호로 변경하세요.</p>
<pre><code class="language-sql"><span class="token keyword">CREATE</span> <span class="token keyword">USER</span> <span class="token string">'sammy'</span><span class="token variable">@'localhost'</span> IDENTIFIED <span class="token keyword">BY</span> <span class="token string">'password'</span><span class="token punctuation">;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/19e2656b-23f7-46d2-8d38-b01eb22fb073/image.png"></p>
<pre><code class="language-sql"><span class="token keyword">GRANT</span> PRIVILEGE <span class="token keyword">ON</span> <span class="token keyword">database</span><span class="token punctuation">.</span><span class="token keyword">table</span> <span class="token keyword">TO</span> <span class="token string">'username'</span><span class="token variable">@'host'</span><span class="token punctuation">;</span></code></pre>
<p>작업용 계정에 권한을 부여한다. 이를테면 아래와 같이 부여할 수 있다.</p>
<pre><code class="language-sql"><span class="token keyword">GRANT</span> <span class="token keyword">CREATE</span><span class="token punctuation">,</span> <span class="token keyword">ALTER</span><span class="token punctuation">,</span> <span class="token keyword">DROP</span><span class="token punctuation">,</span> <span class="token keyword">INSERT</span><span class="token punctuation">,</span> <span class="token keyword">UPDATE</span><span class="token punctuation">,</span> <span class="token keyword">INDEX</span><span class="token punctuation">,</span> <span class="token keyword">DELETE</span><span class="token punctuation">,</span> <span class="token keyword">SELECT</span><span class="token punctuation">,</span> <span class="token keyword">REFERENCES</span><span class="token punctuation">,</span> RELOAD <span class="token keyword">on</span> <span class="token operator">*</span><span class="token punctuation">.</span><span class="token operator">*</span> <span class="token keyword">TO</span> <span class="token string">'sammy'</span><span class="token variable">@'localhost'</span> <span class="token keyword">WITH</span> <span class="token keyword">GRANT</span> <span class="token keyword">OPTION</span><span class="token punctuation">;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/dde27a9a-6b33-429c-add8-bee089f5d833/image.png"></p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>MySQL 데이터베이스를 생성하고 Spring 애플리케이션을 빌드한 후 새로 생성된 데이터베이스에 연결합니다.</p>
<blockquote>
<p>MySQL은 GPL 라이선스를 받았으므로 MySQL과 함께 배포하는 모든 프로그램 바이너리도 GPL을 사용해야 합니다. GNU 일반 공중 사용 허가서를 참조하세요.</p>
</blockquote>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9f1691c6-4520-4788-9b30-fa5742382488/image.png"></p>
<h2 id="create-the-database">Create the Database</h2>
<p>터미널(Microsoft Windows의 명령 프롬프트)을 열고 새 사용자를 생성할 수 있는 사용자로 MySQL 클라이언트를 엽니다.</p>
<p>예를 들어 Linux 시스템에서는 다음 명령을 사용합니다.</p>
<pre><code class="language-shell">$ <span class="token function">sudo</span> mysql --password</code></pre>
<blockquote>
<p>이는 루트로 MySQL에 연결하고 모든 호스트에서 사용자에 대한 액세스를 허용합니다. 이는 프로덕션 서버에 권장되는 방법이 아닙니다.</p>
</blockquote>
<p>새 데이터베이스를 생성하려면 mysql 프롬프트에서 다음 명령을 실행합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/14af5894-00b7-4d77-873f-6e5bc3da403c/image.png"></p>
<pre><code class="language-shell">mysql<span class="token operator">&gt;</span> create database db_example<span class="token punctuation">;</span> -
mysql<span class="token operator">&gt;</span> create user <span class="token string">'springuser'</span>@<span class="token string">'%'</span> identified by <span class="token string">'ThePassword'</span><span class="token punctuation">;</span> 
mysql<span class="token operator">&gt;</span> grant all on db_example.* to <span class="token string">'springuser'</span>@<span class="token string">'%'</span><span class="token punctuation">;</span> </code></pre>
<h2 id="create-the-applicationproperties-file">Create the <code>application.properties</code> File</h2>
<p>Spring Boot는 모든 것에 대한 기본값을 제공합니다. 예를 들어 기본 데이터베이스는 <code>H2</code>입니다. 따라서 다른 데이터베이스를 사용하려면 <code>application.properties</code> 파일에서 연결 속성을 정의해야 합니다.</p>
<p>다음 목록에 표시된 대로 <code>src/main/resources/application.properties</code>라는 리소스 파일을 만듭니다.</p>
<pre><code class="language-null">spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/db_example
spring.datasource.username=springuser
spring.datasource.password=ThePassword
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#spring.jpa.show-sql: true</code></pre>
<p>여기서 <code>spring.jpa.hibernate.ddl-auto</code>는 <code>none</code>, <code>update</code>, <code>create</code> 또는 <code>create-drop</code>일 수 있습니다. 자세한 내용은 Hibernate 문서를 참조하세요.</p>
<ul>
<li>
<p><code>none</code>: MySQL의 기본값입니다. 데이터베이스 구조는 변경되지 않습니다.</p>
</li>
<li>
<p><code>update</code>: Hibernate는 주어진 엔터티 구조에 따라 데이터베이스를 변경합니다.</p>
</li>
<li>
<p><code>create</code>: 매번 데이터베이스를 생성하지만 닫을 때 삭제하지 않습니다.</p>
</li>
<li>
<p><code>create-drop</code>: 데이터베이스를 생성하고 <code>SessionFactory</code>가 닫힐 때 삭제합니다.</p>
</li>
</ul>
<p>아직 데이터베이스 구조가 없기 때문에 <code>create</code> 또는 <code>update</code>부터 시작해야 합니다. 첫 번째 실행 후 프로그램 요구 사항에 따라 <code>update</code> 또는 <code>none</code>으로 전환할 수 있습니다. 데이터베이스 구조를 일부 변경하려면 <code>update</code>를 사용하십시오.</p>
<p><code>H2</code> 및 기타 내장 데이터베이스의 기본값은 <code>create-drop</code>입니다. <code>MySQL</code>과 같은 다른 데이터베이스의 경우 기본값은 <code>none</code>입니다.</p>
<blockquote>
<p>데이터베이스가 프로덕션 상태가 된 후에는 이를 <code>none</code>으로 설정하고 Spring 애플리케이션에 연결된 MySQL 사용자의 모든 권한을 취소하고 MySQL 사용자에게 <code>SELECT</code>, <code>UPDATE</code>, <code>INSERT</code> 및 <code>DELETE</code>만 제공하는 것이 좋은 보안 관행입니다. 이에 대한 자세한 내용은 이 가이드 끝부분에서 확인할 수 있습니다.</p>
</blockquote>
<h2 id="entity-모델-만들기"><code>@Entity</code> 모델 만들기</h2>
<p>다음 목록(src/main/java/guides/accessingdatamysql/User.java)에 표시된 대로 엔터티 모델을 생성해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamysql</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Entity</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GeneratedValue</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">GenerationType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>persistence</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Entity</span> <span class="token comment">// Hibernate에게 이 클래스로부터 테이블을 만들라고 말함</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Id</span>
    <span class="token annotation punctuation">@GeneratedValue</span><span class="token punctuation">(</span>strategy <span class="token operator">=</span> <span class="token class-name">GenerationType</span><span class="token punctuation">.</span>AUTO<span class="token punctuation">)</span>
    <span class="token keyword">private</span> <span class="token class-name">Integer</span> id<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> email<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Integer</span> <span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setId</span><span class="token punctuation">(</span><span class="token class-name">Integer</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>id <span class="token operator">=</span> id<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getEmail</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> email<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setEmail</span><span class="token punctuation">(</span><span class="token class-name">String</span> email<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>email <span class="token operator">=</span> email<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Hibernate는 자동으로 엔터티를 테이블로 변환합니다.</p>
<h2 id="저장소-생성">저장소 생성</h2>
<p>다음 목록(src/main/java/repository/accessingdatamysql/UserRepository.java)에 표시된 대로 사용자 레코드를 보관하는 저장소를 생성해야 합니다.</p>
<pre><code class="language-java">
<span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamysql</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">CrudRepository</span><span class="token punctuation">;</span>

<span class="token comment">// This will be AUTO IMPLEMENTED by Spring into a Bean called userRepository</span>
<span class="token comment">// CRUD refers Create, Read, Update, Delete</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">UserRepository</span> <span class="token keyword">extends</span> <span class="token class-name">CrudRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span> <span class="token punctuation">}</span></code></pre>
<p>Spring은 동일한 이름을 가진 빈에서 이 저장소 인터페이스를 자동으로 구현합니다(대소문자가 변경되어 <code>userRepository</code>라고 함).</p>
<h2 id="컨트롤러-생성">컨트롤러 생성</h2>
<p>다음 목록(src/main/java/guides/accessingdatamysql/MainController.java)에 표시된 대로 애플리케이션에 대한 HTTP 요청을 처리하는 컨트롤러를 생성해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamysql</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span> <span class="token comment">// This means that this class is a Controller</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span>path<span class="token operator">=</span><span class="token string">"/demo"</span><span class="token punctuation">)</span>  <span class="token comment">// This means URL's start with /demo (after Application path)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MainController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span> <span class="token comment">// This means to get the bean called userRepository</span>
    <span class="token comment">// Which is auto-generated by Spring, we will use it to handle the data</span>
    <span class="token keyword">private</span> <span class="token class-name">UserRepository</span> userRepository<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span>path<span class="token operator">=</span><span class="token string">"/add"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token annotation punctuation">@ResponseBody</span> <span class="token class-name">String</span> addNewUser <span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span> <span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token annotation punctuation">@RequestParam</span> <span class="token class-name">String</span> email<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// @ResponseBody means the returned String is the response, not a view name</span>
        <span class="token comment">// @RequestParam means it is a parameter from the GET or POST request</span>

        <span class="token class-name">User</span> n <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        n<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">;</span>
        n<span class="token punctuation">.</span><span class="token function">setEmail</span><span class="token punctuation">(</span>email<span class="token punctuation">)</span><span class="token punctuation">;</span>
        userRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>n<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token string">"Saved"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span>path<span class="token operator">=</span><span class="token string">"/all"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token annotation punctuation">@ResponseBody</span> <span class="token class-name">Iterable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">User</span><span class="token punctuation">&gt;</span></span> <span class="token function">getAllUsers</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// This returns a JSON or XML with the users</span>
        <span class="token keyword">return</span> userRepository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>앞의 예에서는 두 엔드포인트에 대해 <code>POST</code> 및 <code>GET</code>을 명시적으로 지정합니다. 기본적으로 <code>@RequestMapping</code>은 모든 HTTP 작업을 매핑합니다.</p>
</blockquote>
<h2 id="애플리케이션-클래스-생성">애플리케이션 클래스 생성</h2>
<p>Spring 초기화는 애플리케이션을 위한 간단한 클래스를 생성합니다. 다음 목록은 이 예제를 위해 초기화가 생성한 클래스(src/main/java/guides/accessingdatamysql/AccessingDataMysqlApplication.java)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamysql</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccessingDataMysqlApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataMysqlApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9aa2e905-d1b0-412f-b608-418dc19eb9fa/image.png"></p>
<p>이제 애플리케이션이 실행되고 있으므로 <code>curl</code>이나 유사한 도구를 사용하여 테스트할 수 있습니다. 테스트할 수 있는 두 개의 HTTP 엔드포인트가 있습니다.</p>
<ul>
<li><code>GET localhost:8080/demo/all</code>: 모든 데이터를 가져옵니다. </li>
<li><code>POST localhost:8080/demo/add</code>: 데이터에 한 명의 사용자를 추가합니다.</li>
</ul>
<p>다음 curl 명령은 사용자를 추가합니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> http://localhost:8080/demo/add -d <span class="token assign-left variable">name</span><span class="token operator">=</span>First -d <span class="token assign-left variable">email</span><span class="token operator">=</span>someemail@someemailprovider.com</code></pre>
<p>응답은 다음과 같아야 합니다.</p>
<pre><code class="language-shell">Saved</code></pre>
<p>다음 명령은 모든 사용자를 표시합니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> http://localhost:8080/demo/all</code></pre>
<p>응답은 다음과 같아야 합니다.</p>
<pre><code class="language-shell"><span class="token punctuation">[</span><span class="token punctuation">{</span><span class="token string">"id"</span>:1,<span class="token string">"name"</span><span class="token builtin class-name">:</span><span class="token string">"첫번째"</span>,<span class="token string">"email"</span><span class="token builtin class-name">:</span><span class="token string">"someemail@someemailprovider.com"</span><span class="token punctuation">}</span><span class="token punctuation">]</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/654dd8d9-3cb5-4aed-a432-dec1b3def8e3/image.png"></p>
<h3 id="데이터베이스-조회해보기">데이터베이스 조회해보기</h3>
<pre><code class="language-sql">sudo mysql
<span class="token keyword">show</span> <span class="token keyword">databases</span><span class="token punctuation">;</span>
<span class="token keyword">show</span> <span class="token keyword">tables</span><span class="token punctuation">;</span>
<span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> <span class="token keyword">user</span><span class="token punctuation">;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4d059966-147e-488a-9b12-d1023a43649d/image.png"></p>
<h2 id="일부-보안-변경">일부 보안 변경</h2>
<p>프로덕션 환경에서는 SQL 주입 공격에 노출될 수 있습니다. 해커는 <code>DROP TABLE</code> 또는 기타 파괴적인 SQL 명령을 주입할 수 있습니다. 따라서 보안을 위해 애플리케이션을 사용자에게 공개하기 전에 데이터베이스를 일부 변경해야 합니다.</p>
<p>다음 명령은 Spring 애플리케이션과 관련된 사용자의 모든 권한을 취소합니다.</p>
<pre><code class="language-sql"><span class="token keyword">revoke</span> <span class="token keyword">all</span> <span class="token keyword">on</span> db_example<span class="token punctuation">.</span><span class="token operator">*</span> <span class="token keyword">from</span> <span class="token string">'springuser'</span><span class="token variable">@'%'</span><span class="token punctuation">;</span></code></pre>
<p>이제 Spring 애플리케이션은 데이터베이스에서 아무 것도 할 수 없습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d9d84711-f703-432b-b7e1-51cd536316a5/image.png"></p>
<p>애플리케이션에는 일부 권한이 있어야 하므로 다음 명령을 사용하여 애플리케이션에 필요한 최소 권한을 부여하세요.</p>
<pre><code class="language-sql"><span class="token keyword">grant</span> <span class="token keyword">select</span><span class="token punctuation">,</span> <span class="token keyword">insert</span><span class="token punctuation">,</span> <span class="token keyword">delete</span><span class="token punctuation">,</span> <span class="token keyword">update</span> <span class="token keyword">on</span> db_example<span class="token punctuation">.</span><span class="token operator">*</span> <span class="token keyword">to</span> <span class="token string">'springuser'</span><span class="token variable">@'%'</span><span class="token punctuation">;</span></code></pre>
<p>모든 권한을 제거하고 일부 권한을 부여하면 Spring 애플리케이션에 구조(스키마)가 아닌 데이터베이스의 데이터만 변경하는 데 필요한 권한이 제공됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1bab77c0-82f7-4c09-8dfe-ff4c8e0cbf70/image.png"></p>
<p>데이터베이스를 변경하려는 경우:<br>
1. 권한을 다시 부여합니다.<br>
2. <code>spring.jpa.hibernate.ddl-auto</code>를 변경하여 <code>update</code>하세요.<br>
3. 응용 프로그램을 다시 실행하십시오.</p>
<p>그런 다음 여기에 표시된 두 명령을 반복하여 애플리케이션을 프로덕션 용도로 다시 안전하게 만듭니다. 더 나은 방법은 Flyway 또는 Liquibase와 같은 전용 마이그레이션 도구를 사용하는 것입니다.</p>