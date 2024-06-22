<p>이 가이드는 문서 기반 데이터베이스인 <code>MongoDB</code>에 데이터를 저장하고 검색하는 애플리케이션을 구축하기 위해 <code>Spring Data MongoDB</code>를 사용하는 과정을 안내합니다.</p>
<h2 id="mongodb-설치하기">MongoDB 설치하기</h2>
<p><a href="https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/#std-label-install-mdb-community-ubuntu">https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/#std-label-install-mdb-community-ubuntu</a></p>
<h3 id="mongodb-구성">MongoDB 구성</h3>
<ol>
<li>
<p><strong>mongod</strong>: <code>mongod</code>는 몽고디비의 핵심 서버 프로세스입니다. 이 프로세스는 실제 데이터베이스 서버를 실행하며, 데이터 저장, 쓰기, 읽기 작업을 처리합니다. 일반적으로 이 프로세스를 시작하여 몽고디비 서버를 구동합니다.</p>
</li>
<li>
<p><strong>mongos</strong>: <code>mongos</code>는 몽고DB 샤딩을 관리하는 프로세스로, 샤딩된 환경에서 쿼리를 처리하고 샤드 간의 데이터 라우팅을 담당합니다. 여러 개의 <code>mongod</code> 인스턴스로 구성된 샤딩된 환경에서 사용됩니다.</p>
</li>
<li>
<p><strong>mongo</strong>: <code>mongo</code>는 몽고DB의 기본 콘솔 클라이언트입니다. 몽고디비 서버에 접속하여 쿼리를 실행하거나 관리 작업을 수행할 수 있는 인터랙티브한 콘솔 환경을 제공합니다.</p>
</li>
<li>
<p><strong>mongosh</strong>: <code>mongosh</code>는 <code>mongo</code> 셸의 새로운 버전으로, 더 많은 기능과 개선된 사용자 경험을 제공합니다. 몽고디비 쿼리, 관리 작업, JavaScript 사용 등을 지원합니다. <code>mongosh</code>는 이전 버전의 <code>mongo</code>보다 더 많은 기능을 갖추고 있습니다.</p>
</li>
</ol>
<h4 id="샤딩이란">샤딩이란?</h4>
<p>샤딩(Sharding)은 대량의 데이터를 처리하고 저장하기 위해 데이터베이스를 분할하는 기술입니다. 이 기술은 데이터베이스에 수평적 확장을 가능하게 하여 단일 서버가 아닌 여러 서버에 데이터를 분산하여 저장함으로써 성능과 확장성을 향상시킵니다.</p>
<p>일반적으로 데이터베이스에는 수많은 데이터가 쌓이게 되면서 단일 서버로는 그 처리와 저장이 어려워집니다. 이 때문에 데이터베이스를 여러 대의 서버에 분산하여 저장하고, 쿼리를 분산하여 처리함으로써 데이터베이스의 확장성과 성능을 유지하면서 대규모의 데이터를 효율적으로 관리할 수 있게 됩니다.</p>
<p>샤딩은 데이터를 분할하는 과정에서 특정 기준에 따라 데이터를 나누는데, 이를 통해 각각의 샤드에 저장된 데이터는 서로 다른 서버에 분산 저장됩니다. 이렇게 나뉜 데이터를 쿼리할 때는 샤드 키(Shard Key)를 기반으로 해당 데이터가 어느 샤드에 저장되어 있는지를 찾아내고, 필요한 경우 여러 샤드에 분산된 데이터를 효과적으로 조합하여 결과를 반환합니다.</p>
<h3 id="mongodb-커뮤니티-에디션-설치">MongoDB 커뮤니티 에디션 설치</h3>
<p>적절한 패키지 관리자를 사용하여 MongoDB Community Edition을 설치하려면 다음 단계를 따르세요.</p>
<ol>
<li>패키지 관리 시스템에서 사용되는 공개 키 가져오기</li>
</ol>
<p>아직 사용할 수 없는 경우 터미널에서 <code>gnupg</code> 및 <code>curl</code>을 설치합니다.</p>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> gnupg <span class="token function">curl</span></code></pre>
<p><code>https://pgp.mongodb.com/server-7.0.asc</code> 에서 MongoDB 공개 GPG 키를 가져오려면 다음 명령을 실행하십시오.</p>
<pre><code class="language-shell"><span class="token function">curl</span> -fsSL https://pgp.mongodb.com/server-7.0.asc <span class="token operator">|</span> <span class="token punctuation">\</span>
   <span class="token function">sudo</span> gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg <span class="token punctuation">\</span>
   --dearmor
   </code></pre>
<ol start="2">
<li>MongoDB용 목록 파일 만들기<br>
사용 중인 Ubuntu 버전에 대한 목록 파일 <code>/etc/apt/sources.list.d/mongodb-org-7.0.list</code>를 생성합니다.</li>
</ol>
<pre><code class="language-shell"><span class="token builtin class-name">echo</span> <span class="token string">"deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse"</span> <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /etc/apt/sources.list.d/mongodb-org-7.0.list</code></pre>
<ol start="3">
<li>로컬 패키지 데이터베이스 다시 로드<br>
로컬 패키지 데이터베이스를 다시 로드하려면 다음 명령을 실행하십시오.</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt-get</span> update</code></pre>
<ol start="4">
<li>MongoDB 패키지 설치<br>
MongoDB의 최신 안정 버전이나 MongoDB의 특정 버전을 설치할 수 있습니다.</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> -y mongodb-org</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/185f0474-dee3-417b-8c18-6f4eebc7d99d/image.png"></p>
<ul>
<li>
<p>디렉터리<br>
패키지 매니저를 통해 설치한 경우, 설치 중에 데이터 디렉터리인 <code>/var/lib/mongodb</code>와 로그 디렉터리인 <code>/var/log/mongodb</code>가 생성됩니다.</p>
</li>
<li>
<p>기본적으로 MongoDB는 <code>mongodb</code> 사용자 계정으로 실행됩니다. MongoDB 프로세스를 실행하는 사용자를 변경하는 경우, 이 사용자가 해당 디렉터리에 액세스할 수 있도록 데이터 및 로그 디렉터리의 권한을 수정해야 합니다.</p>
</li>
<li>
<p>구성 파일<br>
공식 MongoDB 패키지에는 구성 파일(<code>/etc/mongod.conf</code>)이 포함되어 있습니다. 이러한 설정(데이터 디렉터리 및 로그 디렉터리 지정 등)은 시작할 때 적용됩니다. 즉, MongoDB 인스턴스가 실행 중인 동안 구성 파일을 변경하는 경우, 변경 사항이 적용되려면 인스턴스를 다시 시작해야 합니다.</p>
</li>
<li>
<p>절차<br>
시스템에서 MongoDB Community Edition을 실행하는 방법은 다음과 같습니다. 이 지침은 공식 <code>mongodb-org</code> 패키지를 사용하는 것을 전제로 하며, Ubuntu에서 제공하는 비공식 <code>mongodb</code> 패키지가 아닌 것으로 가정합니다.</p>
</li>
<li>
<p>초기화 시스템<br>
<code>mongod</code> 프로세스를 실행하고 관리하기 위해 운영 체제의 내장된 초기화 시스템을 사용합니다. 최근 버전의 Linux는 주로 <code>systemctl</code> 명령을 사용하는 <code>systemd</code>를 사용하고, 이전 버전의 Linux는 주로 <code>service</code> 명령을 사용하는 <code>System V init</code>을 사용하는 경향이 있습니다.</p>
</li>
</ul>
<p>플랫폼이 어떤 초기화 시스템을 사용하는지 확실하지 않은 경우 다음 명령을 실행하십시오.</p>
<pre><code class="language-shell"><span class="token function">ps</span> --no-headers -o <span class="token function">comm</span> <span class="token number">1</span></code></pre>
<p>그런 다음 결과에 따라 아래에서 적절한 탭을 선택합니다.</p>
<ul>
<li><code>systemd</code> - 아래 <code>systemd(systemctl)</code> 탭을 선택합니다.</li>
<li><code>init</code> - 아래 <code>System V Init(service)</code> 탭을 선택합니다.</li>
</ul>
<p>저는 <code>systemd</code>를 따라 설치하겠습니다.</p>
<h3 id="초기화-시스템-사용하기">초기화 시스템 사용하기</h3>
<ol>
<li>몽고디비 시작하기<br>
몽고디비 프로세스를 시작하려면 다음 명령을 사용하세요:</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> systemctl start mongod</code></pre>
<ul>
<li>몽고디비를 시작할 때 다음과 유사한 오류가 발생하는 경우:<br>
<code>Failed to start mongod.service: Unit mongod.service not found.</code><br>
아래 명령을 실행하세요:<pre><code class="language-shell"><span class="token function">sudo</span> systemctl daemon-reload</code></pre>그런 다음 위의 시작 명령을 다시 실행하세요.</li>
</ul>
<ol start="2">
<li>몽고디비가 성공적으로 시작되었는지 확인하세요.</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> systemctl status mongod</code></pre>
<ul>
<li>몽고디비를 시스템 재부팅 후에도 자동으로 시작하도록 하려면 다음 명령을 사용하세요:<pre><code class="language-shell"><span class="token function">sudo</span> systemctl <span class="token builtin class-name">enable</span> mongod</code></pre></li>
</ul>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/333c28d7-dc92-40c0-8cef-cc6c84f97808/image.png"></p>
<ol start="3">
<li>몽고디비를 중지할 수도 있습니다:</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> systemctl stop mongod</code></pre>
<ol start="4">
<li>몽고디비 프로세스를 다시 시작하려면 다음 명령을 사용하세요:</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> systemctl restart mongod</code></pre>
<p>몽고디비 프로세스의 상태를 확인하거나 중요한 메시지 및 오류를 확인하려면 <code>/var/log/mongodb/mongod.log</code> 파일의 출력을 확인할 수 있습니다.</p>
<ol start="5">
<li>몽고디비 사용하기</li>
</ol>
<p>동일한 호스트 머신에서 몽고디비와 <code>mongosh</code> 세션을 시작하세요. <code>mongosh</code>를 실행할 때는 명령줄 옵션이 없이 <code>localhost</code>에서 기본 포트 27017로 실행 중인 몽고디비에 연결할 수 있습니다.</p>
<pre><code class="language-shell">mongosh</code></pre>
<p><code>mongosh</code>를 사용하여 다른 호스트 및/또는 포트에서 실행 중인 몽고디비 인스턴스에 연결하는 등 <code>mongosh</code>를 사용하여 연결하는 방법에 대한 자세한 내용은 <code>mongosh</code> 문서를 참조하세요.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Data MongoDB를 사용하여 MongoDB 데이터베이스에 <code>Customer</code> POJO(Plain Old Java Objects)를 저장합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e2365167-d49d-4cc8-b22c-a7f35d761112/image.png"></p>
<h2 id="단순-엔터티-정의">단순 엔터티 정의</h2>
<p>MongoDB는 NoSQL 문서 저장소입니다. 이 예에서는 <code>Customer</code> 개체를 저장합니다. 다음 목록은 <code>Customer</code> 클래스(src/main/java/guides/accessingdatamongodb/Customer.java)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamongodb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Id</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Customer</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Id</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> id<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> lastName<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">,</span> <span class="token class-name">String</span> lastName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>lastName <span class="token operator">=</span> lastName<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span>
                <span class="token string">"Customer[id=%s, firstName='%s', lstName='%s']"</span><span class="token punctuation">,</span>
                id<span class="token punctuation">,</span> firstName<span class="token punctuation">,</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>여기에는 <code>id</code>, <code>firstName</code> 및 <code>lastName</code>의 세 가지 속성이 있는 <code>Customer</code> 클래스가 있습니다. <code>id</code>는 대부분 MongoDB에서 내부용으로 사용됩니다. 또한 새 인스턴스를 생성할 때 엔터티를 채우는 단일 생성자가 있습니다.</p>
<blockquote>
<p>이 가이드에서는 간결성을 위해 일반적인 getter와 setter를 생략했습니다.</p>
</blockquote>
<p><code>id</code>는 MongoDB ID의 표준 이름에 적합하므로 Spring Data MongoDB에 태그를 지정하기 위해 특별한 주석이 필요하지 않습니다.</p>
<p>다른 두 속성인 <code>firstName</code>과 <code>lastName</code>은 주석이 추가되지 않은 상태로 유지됩니다. 속성 자체와 동일한 이름을 공유하는 필드에 매핑된 것으로 가정합니다.</p>
<p>편리한 <code>toString()</code> 메소드는 고객에 대한 세부 정보를 인쇄합니다.</p>
<blockquote>
<p>MongoDB는 데이터를 컬렉션에 저장합니다. Spring Data MongoDB는 <code>Customer</code> 클래스를 <code>customer</code>라는 컬렉션에 매핑합니다. 컬렉션 이름을 변경하려면 클래스에 Spring Data MongoDB의 <code>@Document</code> 주석을 사용할 수 있습니다.</p>
</blockquote>
<h2 id="간단한-쿼리-만들기">간단한 쿼리 만들기</h2>
<p>Spring Data MongoDB는 MongoDB에 데이터를 저장하는 데 중점을 둡니다. 또한 쿼리 파생 기능과 같은 Spring Data Commons 프로젝트의 기능을 상속합니다. 기본적으로 MongoDB의 쿼리 언어를 배울 필요는 없습니다. 몇 가지 메소드를 작성할 수 있으며 쿼리가 자동으로 작성됩니다.</p>
<p>이것이 어떻게 작동하는지 확인하려면 다음 목록(src/main/java/guides/accessingdatamongodb/CustomerRepository.java)에 표시된 대로 <code>Customer</code> 문서를 쿼리하는 저장소 인터페이스를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamongodb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>mongodb<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">MongoRepository</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">CustomerRepository</span> <span class="token keyword">extends</span> <span class="token class-name">MongoRepository</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Customer</span> <span class="token function">findByFirstName</span><span class="token punctuation">(</span><span class="token class-name">String</span> firstName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">public</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Customer</span><span class="token punctuation">&gt;</span></span> <span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token class-name">String</span> lastName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
<span class="token punctuation">}</span></code></pre>
<p><code>CustomerRepository</code>는 <code>MongoRepository</code> 인터페이스를 확장하고 작동하는 값 및 ID 유형(각각 <code>Customer</code> 및 <code>String</code>)을 연결합니다. 이 인터페이스에는 표준 CRUD 작업(생성, 읽기, 업데이트 및 삭제)을 포함한 많은 작업이 포함되어 있습니다.</p>
<p>메소드 signature를 선언하여 다른 쿼리를 정의할 수 있습니다. 이 경우 기본적으로 <code>Customer</code> 유형의 문서를 찾고 <code>firstName</code>과 일치하는 문서를 찾는 <code>findByFirstName</code>을 추가합니다.</p>
<p>또한 성으로 사람 목록을 찾는 <code>findByLastName</code>도 있습니다.</p>
<p>일반적인 Java 애플리케이션에서는 <code>CustomerRepository</code>를 구현하는 클래스를 작성하고 쿼리를 직접 작성합니다. Spring Data MongoDB를 매우 유용하게 만드는 것은 이 구현을 생성할 필요가 없다는 사실입니다. Spring Data MongoDB는 애플리케이션을 실행할 때 즉시 이를 생성합니다.</p>
<p>이제 이 애플리케이션을 연결하고 어떻게 보이는지 확인할 수 있습니다!</p>
<h2 id="애플리케이션-클래스-생성">애플리케이션 클래스 생성</h2>
<p>Spring Boot는 <code>@SpringBootApplication</code> 클래스의 동일한 패키지(또는 하위 패키지)에 포함된 리포지토리를 자동으로 처리합니다. 등록 프로세스를 더 효과적으로 제어하려면 <code>@EnableMongoRepositories</code> 주석을 사용할 수 있습니다.</p>
<blockquote>
<p>기본적으로 <code>@EnableMongoRepositories</code>는 현재 패키지에서 Spring Data의 저장소 인터페이스 중 하나를 확장하는 인터페이스를 검색합니다. 프로젝트 레이아웃에 여러 프로젝트가 있고 저장소를 찾을 수 없는 경우 <code>basePackageClasses=MyRepository.class</code>를 사용하여 Spring Data MongoDB에 유형별로 다른 루트 패키지를 스캔하도록 안전하게 지시할 수 있습니다.</p>
</blockquote>
<p>Spring Data MongoDB는 <code>MongoTemplate</code>을 사용하여 <code>find*</code> 메소드 뒤에 있는 쿼리를 실행합니다. 더 복잡한 쿼리에 템플릿을 직접 사용할 수 있지만 이 가이드에서는 이에 대해 다루지 않습니다. (Spring Data MongoDB 참조 가이드 참조)</p>
<p>이제 Initializr가 생성한 간단한 클래스를 수정해야 합니다. 일부 데이터를 설정하고 이를 사용하여 출력을 생성해야 합니다. 다음 목록은 완성된 <code>AccessingDataMongodbApplication</code> 클래스(src/main/java/guides/accessingdatamongodb/AccessingDataMongodbApplication.java)를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>accessingdatamongodb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccessingDataMongodbApplication</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">CustomerRepository</span> repository<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">AccessingDataMongodbApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>

        repository<span class="token punctuation">.</span><span class="token function">deleteAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// save a couple of customers</span>
        repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Alice"</span><span class="token punctuation">,</span> <span class="token string">"Smith"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        repository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Customer</span><span class="token punctuation">(</span><span class="token string">"Bob"</span><span class="token punctuation">,</span> <span class="token string">"Smith"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// fetch all customers</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Customers found with findAll():"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">Customer</span> customer <span class="token operator">:</span> repository<span class="token punctuation">.</span><span class="token function">findAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// fetch an individual customer</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Customer found with findByFirstName('Alice'):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"--------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>repository<span class="token punctuation">.</span><span class="token function">findByFirstName</span><span class="token punctuation">(</span><span class="token string">"Alice"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Customers found with findByLastName('Smith'):"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"--------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">Customer</span> customer <span class="token operator">:</span> repository<span class="token punctuation">.</span><span class="token function">findByLastName</span><span class="token punctuation">(</span><span class="token string">"Smith"</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>customer<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>AccessingDataMongodbApplication</code>에는 <code>CustomerRepository</code>의 인스턴스를 자동 연결하는 <code>main()</code> 메서드가 포함되어 있습니다. Spring Data MongoDB는 동적으로 프록시를 생성하고 거기에 주입합니다. 몇 가지 테스트를 통해 <code>CustomerRepository</code>를 사용합니다. 먼저, 몇 가지 <code>Customer</code> 개체를 저장하고 <code>save()</code> 메서드를 시연하고 사용할 일부 데이터를 설정합니다. 다음으로 <code>findAll()</code>을 호출하여 데이터베이스에서 모든 <code>Customer</code> 개체를 가져옵니다. 그런 다음 <code>findByFirstName()</code>을 호출하여 이름으로 단일 <code>Customer</code>를 가져옵니다. 마지막으로 <code>findByLastName()</code>을 호출하여 성이 <code>Smith</code>인 모든 고객을 찾습니다.</p>
<blockquote>
<p>기본적으로 Spring Boot는 로컬로 호스팅되는 MongoDB 인스턴스에 연결을 시도합니다. 애플리케이션을 다른 곳에 호스팅된 MongoDB 인스턴스로 지정하는 방법에 대한 자세한 내용은 참조 문서를 읽어보세요.</p>
</blockquote>
<p><code>AccessingDataMongodbApplication</code>은 <code>CommandLineRunner</code>를 구현하므로 Spring Boot가 시작될 때 <code>run</code> 메서드가 자동으로 호출됩니다. 다음과 같은 내용이 표시됩니다(쿼리와 같은 다른 출력도 포함).</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3f8ed03c-ab17-4e54-9ec3-8a35d05f6509/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3beb07e0-e5f0-4174-8a73-c7ec7b1a4661/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f43480e0-9a7c-4add-b0f9-8e6e7d2d3fcc/image.png"></p>