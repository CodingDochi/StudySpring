<p><img src="https://codedestine.com/wp-content/uploads/2016/09/TopicExchange.png"></p>
<p>이 가이드는 메시지를 게시하고 구독하는 RabbitMQ AMQP 서버를 설정하고 해당 RabbitMQ 서버와 상호 작용할 Spring Boot 애플리케이션을 만드는 과정을 안내합니다.</p>
<blockquote>
<p>AMQP는 Advanced Message Queuing Protocol(고급 메시지 큐잉 프로토콜)의 약자로, 메시지 지향 미들웨어를 위한 개방형 표준 프로토콜입니다. AMQP는 다양한 메시지 지향 미들웨어 시스템 간의 상호 운용성을 제공하는 역할을 합니다. 이 프로토콜은 메시지 전송, 큐잉, 라우팅 및 기타 다양한 메시징 패턴을 지원하여 분산 시스템에서의 효율적인 통신을 돕는 것이 목적입니다.</p>
<p>RabbitMQ는 AMQP의 오픈 소스 구현체 중 하나로, 다양한 프로그래밍 언어와 플랫폼에서 메시지 지향 애플리케이션을 구축하고 운영할 수 있는 유연하고 강력한 메시징 브로커입니다. AMQP 표준을 준수하여 데이터를 안전하고 신속하게 전달할 수 있도록 도와줍니다.</p>
</blockquote>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring AMQP의 <code>RabbitTemplate</code>을 사용하여 메시지를 게시하고 <code>MessageListenerAdapter</code>를 사용하여 POJO에서 메시지를 구독하는 애플리케이션을 구축합니다.</p>
<h2 id="rabbitmq-브로커-설정">RabbitMQ 브로커 설정</h2>
<p>메시징 애플리케이션을 구축하기 전에 메시지 수신 및 전송을 처리할 서버를 설정해야 합니다.</p>
<p>RabbitMQ는 AMQP 서버입니다. 서버는 <code>https://www.rabbitmq.com/download.html</code>에서 무료로 사용할 수 있습니다. 수동으로 다운로드하거나 Homebrew가 설치된 Mac을 사용하는 경우 터미널 창에서 다음 명령을 실행하여 다운로드할 수 있습니다.</p>
<p><strong>ubuntu 설치</strong></p>
<p><a href="https://www.rabbitmq.com/install-debian.html#supported-distributions">https://www.rabbitmq.com/install-debian.html#supported-distributions</a></p>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> <span class="token function">curl</span> gnupg apt-transport-https -y

<span class="token comment">## Team RabbitMQ's main signing key</span>
<span class="token function">curl</span> -1sLf <span class="token string">"https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA"</span> <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /usr/share/keyrings/com.rabbitmq.team.gpg <span class="token operator">&gt;</span> /dev/null
<span class="token comment">## Community mirror of Cloudsmith: modern Erlang repository</span>
<span class="token function">curl</span> -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg <span class="token operator">&gt;</span> /dev/null
<span class="token comment">## Community mirror of Cloudsmith: RabbitMQ repository</span>
<span class="token function">curl</span> -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /usr/share/keyrings/rabbitmq.9F4587F226208342.gpg <span class="token operator">&gt;</span> /dev/null

<span class="token comment">## Add apt repositories maintained by Team RabbitMQ</span>
<span class="token function">sudo</span> <span class="token function">tee</span> /etc/apt/sources.list.d/rabbitmq.list <span class="token operator">&lt;&lt;</span><span class="token string">EOF
## Provides modern Erlang/OTP releases
##
deb [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main

# another mirror for redundancy
deb [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main

## Provides RabbitMQ
##
deb [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main

# another mirror for redundancy
deb [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.9F4587F226208342.gpg] https://ppa2.novemberain.com/rabbitmq/rabbitmq-server/deb/ubuntu jammy main
EOF</span>

<span class="token comment">## Update package indices</span>
<span class="token function">sudo</span> <span class="token function">apt-get</span> update -y

<span class="token comment">## Install Erlang packages</span>
<span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> -y erlang-base <span class="token punctuation">\</span>
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets <span class="token punctuation">\</span>
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key <span class="token punctuation">\</span>
                        erlang-runtime-tools erlang-snmp erlang-ssl <span class="token punctuation">\</span>
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl

<span class="token comment">## Install rabbitmq-server and its dependencies</span>
<span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> rabbitmq-server -y --fix-missing</code></pre>
<p><strong>docker compose 사용해보기</strong></p>
<p>Docker가 로컬에서 실행 중인 경우 Docker Compose를 사용하여 RabbitMQ 서버를 빠르게 시작할 수도 있습니다. Github의 전체 프로젝트 루트에 <code>docker-compose.yml</code>이 있습니다. 다음 목록과 같이 매우 간단합니다.</p>
<pre><code class="language-yaml"><span class="token key atrule">rabbitmq</span><span class="token punctuation">:</span>
  <span class="token key atrule">image</span><span class="token punctuation">:</span> rabbitmq<span class="token punctuation">:</span>management
  <span class="token key atrule">ports</span><span class="token punctuation">:</span>
    <span class="token punctuation">-</span> <span class="token string">"5672:5672"</span>
    <span class="token punctuation">-</span> <span class="token string">"15672:15672"</span></code></pre>
<p>현재 디렉터리에 이 파일이 있으면 <code>docker-compose up</code>을 실행하여 RabbitMQ를 컨테이너에서 실행할 수 있습니다.</p>
<h3 id="ubuntu에서-docker-설치">Ubuntu에서 Docker 설치</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/abff1aab-2848-4926-b222-7ca963ad4edc/image.png"></p>
<p><a href="https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository">https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository</a></p>
<ol>
<li>호스트 머신에 도커 엔진을 설치하기 전 도코 apt 레파지토리를 설정</li>
</ol>
<pre><code class="language-shell"><span class="token comment"># Add Docker's official GPG key:</span>
<span class="token function">sudo</span> <span class="token function">apt-get</span> update
<span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> ca-certificates <span class="token function">curl</span> gnupg
<span class="token function">sudo</span> <span class="token function">install</span> -m 0755 -d /etc/apt/keyrings
<span class="token function">curl</span> -fsSL https://download.docker.com/linux/ubuntu/gpg <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor -o /etc/apt/keyrings/docker.gpg
<span class="token function">sudo</span> <span class="token function">chmod</span> a+r /etc/apt/keyrings/docker.gpg

<span class="token comment"># Add the repository to Apt sources:</span>
<span class="token builtin class-name">echo</span> <span class="token punctuation">\</span>
  <span class="token string">"deb [arch=<span class="token variable"><span class="token variable">$(</span>dpkg --print-architecture<span class="token variable">)</span></span> signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  <span class="token variable"><span class="token variable">$(</span><span class="token builtin class-name">.</span> /etc/os-release <span class="token operator">&amp;&amp;</span> <span class="token builtin class-name">echo</span> <span class="token string">"<span class="token variable">$VERSION_CODENAME</span>"</span><span class="token variable">)</span></span> stable"</span> <span class="token operator">|</span> <span class="token punctuation">\</span>
  <span class="token function">sudo</span> <span class="token function">tee</span> /etc/apt/sources.list.d/docker.list <span class="token operator">&gt;</span> /dev/null
<span class="token function">sudo</span> <span class="token function">apt-get</span> update</code></pre>
<ol start="2">
<li>가장 최신 버전 설치하기</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>
<p>도커 컴포즈 플러그인과 CLI가 포함되어 있다. </p>
<ol start="3">
<li>잘 설치되었는지 <code>hello-world</code> 이미지 실행으로 확인해보기</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> docker run hello-world</code></pre>
<p>이 명령어는 컨테이너 안에서 실행될 테스트용 이미지를 다운 받습니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2fa813d5-eede-48a7-9fda-e481733d5fdc/image.png"></p>
<h3 id="인텔리제이에-docker-연결">인텔리제이에 Docker 연결</h3>
<p><a href="https://stackoverflow.com/questions/51191094/pycharm-docker-unix-tcp-socket-with-unix-var-run-docker-sock-permission/51194496#51194496">docker 그룹에 사용자 추가하여 unix socket 연결 권한 얻기</a></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/dc87e96b-8045-48cc-92d6-723497f807ff/image.png"></p>
<ol>
<li>
<p><code>id -nG</code>: 현재 사용자가 속한 그룹을 출력합니다. </p>
</li>
<li>
<p><code>sudo groupadd docker</code>: 'docker'라는 그룹을 만들려고 시도했으나 이미 존재하는 그룹이라고 알려줍니다.</p>
</li>
<li>
<p><code>sudo usermod -aG docker $USER</code>: 현재 사용자를 'docker' 그룹에 추가합니다. 이 명령어를 통해 현재 사용자에게 Docker 명령어를 실행할 권한을 주기 위해서입니다.</p>
</li>
</ol>
<p><code>sudo systemctl start docker</code> 명령을 실행하면 <code>docker</code> 프로세스가 생성됩니다. 해당 도커 프로세스에는 <code>dockerd</code> 데몬 스레드가 포함되어 있습니다. 이 명령은 기본 <code>docker.sock</code> Unix 소켓도 생성합니다. <code>docker.sock</code> 소켓은 <code>dockerd</code> 데몬 스레드에 의해 지속적으로 수신됩니다. 이를 통해 <code>docker.pid</code> 프로세스를 사용하여 커널 수준 IPC를 수행할 수 있습니다. 이 도커 소켓을 사용하려면 프로세스 수준(<code>docker.pid</code>)과 파일 수준(<code>docker.sock</code>)에서 적절한 권한이 필요합니다. 따라서 아래 두 명령을 실행하면 문제가 해결됩니다.</p>
<ol start="4">
<li>
<p><code>sudo chmod a+rwx /var/run/docker.sock</code> 및 <code>sudo chmod a+rwx /var/run/docker.pid</code>: Docker 소켓 파일과 PID 파일에 대한 권한을 변경하여 모든 사용자에게 읽기, 쓰기 및 실행 권한을 부여합니다. </p>
</li>
<li>
<p><code>sudo systemctl start docker</code>: Docker 서비스를 시작합니다. </p>
</li>
</ol>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/33cf30a5-5c00-4232-b952-477e3a3866c3/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d7284a5c-746f-4f5f-aa41-61c050a35eff/image.png"></p>
<p><code>compose.yaml</code>의 <code>service</code> 옆에 생긴 아이콘은 <code>docker-compose up</code>을 실행하여 RabbitMQ를 컨테이너 내부에서 실행한다.</p>
<h2 id="starting-with-springinitalizr">Starting with SpringInitalizr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/50808ce0-8b63-4b95-9e5a-c23ed3a303de/image.png"></p>
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
    implementation 'org.springframework.boot:spring-boot-starter-amqp'
    developmentOnly 'org.springframework.boot:spring-boot-docker-compose'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.amqp:spring-rabbit-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h3 id="composeyaml">compose.yaml</h3>
<pre><code class="language-yaml"><span class="token key atrule">services</span><span class="token punctuation">:</span>
  <span class="token key atrule">rabbitmq</span><span class="token punctuation">:</span>
    <span class="token key atrule">image</span><span class="token punctuation">:</span> <span class="token string">'rabbitmq:latest'</span>
    <span class="token key atrule">environment</span><span class="token punctuation">:</span>
      <span class="token punctuation">-</span> <span class="token string">'RABBITMQ_DEFAULT_PASS=secret'</span>
      <span class="token punctuation">-</span> <span class="token string">'RABBITMQ_DEFAULT_USER=myuser'</span>
    <span class="token key atrule">ports</span><span class="token punctuation">:</span>
      <span class="token punctuation">-</span> <span class="token string">'5672'</span></code></pre>
<h2 id="create-rabbitmq-message-receiver">Create RabbitMQ Message Receiver</h2>
<p>메시징 기반 애플리케이션을 사용하려면 게시된 메시지에 응답하는 수신자를 만들어야 합니다. 다음 목록(src/main/java/guides.messagingrabbitmq/Receiver.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingrabbitmq</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">CountDownLatch</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Receiver</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token class-name">CountDownLatch</span> latch <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">CountDownLatch</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">receiveMessage</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Received &lt;"</span> <span class="token operator">+</span> message <span class="token operator">+</span> <span class="token string">"&gt;"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        latch<span class="token punctuation">.</span><span class="token function">countDown</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">CountDownLatch</span> <span class="token function">getLatch</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> latch<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p><code>Receiver</code>는 메시지 수신 방법을 정의하는 POJO입니다. 메시지 수신을 위해 등록할 때 원하는 이름을 지정할 수 있습니다.</p>
<blockquote>
<p>편의를 위해 이 POJO에는 <code>CountDownLatch</code>도 있습니다. 이를 통해 메시지가 수신되었음을 알릴 수 있습니다. 이는 프로덕션 애플리케이션에서 구현할 가능성이 없는 것입니다.</p>
</blockquote>
<p>-<code>CountDownLatch</code>는 Java에서 동시성 및 다중 스레드 프로그래밍을 지원하는 유틸리티 클래스입니다. 일반적으로 <code>CountDownLatch</code>는 다음과 같은 상황에서 활용됩니다:</p>
<ol>
<li><strong>멀티스레드 환경에서 작업 동기화</strong>: 여러 스레드가 있는 상황에서 특정 작업이 시작되거나 완료될 때까지 스레드가 대기하도록 할 수 있습니다.</li>
<li><strong>병렬 작업 완료 시점 동기화</strong>: 병렬로 실행되는 작업이 모두 완료될 때까지 대기할 때 사용됩니다.</li>
</ol>
<ul>
<li>
<p><code>CountDownLatch</code>는 생성할 때 카운트를 지정하고, <code>countDown()</code> 메서드를 호출하여 카운트를 감소시킵니다. <code>await()</code> 메서드를 호출하여 카운트가 0이 될 때까지 대기하도록 설정할 수 있습니다.</p>
</li>
<li>
<p>일반적으로 프로덕션 환경에서 <code>CountDownLatch</code>를 사용하여 이벤트를 처리하거나 작업 상태를 추적하는 데에는 일반적으로 적용되지 않습니다. 이는 다음과 같은 몇 가지 이유로 설명될 수 있습니다:</p>
</li>
</ul>
<ol>
<li>
<p><strong>동기화 오버헤드</strong>: <code>CountDownLatch</code>를 사용하여 스레드 간에 작업을 동기화하는 것은 추가적인 오버헤드를 유발할 수 있습니다. 이것은 복잡성을 증가시키고 성능에 영향을 미칠 수 있습니다.</p>
</li>
<li>
<p><strong>디버깅 어려움</strong>: <code>CountDownLatch</code>를 사용하면 코드의 복잡성이 증가하고, 다중 스레드 환경에서 예측하기 어려운 상태 관리 문제가 발생할 수 있습니다.</p>
</li>
<li>
<p><strong>불안정성 및 잠재적인 오류</strong>: 이러한 동기화 메커니즘은 잘못 사용할 경우 데드락(Deadlock)이나 무한 대기 등의 문제를 야기할 수 있습니다.</p>
</li>
</ol>
<p>대신, 프로덕션 환경에서는 더 안정적이고 신뢰성 있는 방법을 사용하여 작업을 관리하고 상태를 추적하는 것이 좋습니다. 예를 들어, 메시지 수신 여부를 추적하는 대신 로깅, 이벤트 기반 알림 시스템, 또는 상태 관리를 위한 특정 데이터베이스를 사용하는 것이 더 일반적인 방법입니다.</p>
<h2 id="register-the-listener-and-send-a-message">Register the Listener and Send a Message</h2>
<p>Spring AMQP의 <code>RabbitTemplate</code>은 RabbitMQ로 메시지를 보내고 받는 데 필요한 모든 것을 제공합니다. 그러나 다음을 수행해야 합니다.</p>
<ul>
<li>메시지 리스너 컨테이너를 구성합니다.</li>
<li>대기열, 교환 및 이들 간의 바인딩을 선언합니다.</li>
<li>리스너를 테스트하기 위해 일부 메시지를 보내도록 구성 요소를 구성합니다.</li>
</ul>
<blockquote>
<p>Spring Boot는 연결 팩토리와 RabbitTemplate을 자동으로 생성하여 작성해야 하는 코드의 양을 줄입니다.</p>
</blockquote>
<p><code>RabbitTemplate</code>을 사용하여 메시지를 보내고, 메시지 수신자 컨테이너에 <code>Receiver</code>를 등록하여 메시지를 받습니다. 연결 팩토리는 둘 다 구동하여 RabbitMQ 서버에 연결할 수 있도록 합니다. 다음 목록(src/main/java/guides.messagingrabbitmq/MessagingRabbitmqApplication.java)은 애플리케이션 클래스를 생성하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingrabbitmq</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">Binding</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">BindingBuilder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">Queue</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">TopicExchange</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>rabbit<span class="token punctuation">.</span>connection</span><span class="token punctuation">.</span><span class="token class-name">ConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>rabbit<span class="token punctuation">.</span>listener</span><span class="token punctuation">.</span><span class="token class-name">SimpleMessageListenerContainer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>rabbit<span class="token punctuation">.</span>listener<span class="token punctuation">.</span>adapter</span><span class="token punctuation">.</span><span class="token class-name">MessageListenerAdapter</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MessagingRabbitmqApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> topicExchangeName <span class="token operator">=</span> <span class="token string">"spring-boot-exchange"</span><span class="token punctuation">;</span>

    <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">String</span> queueName <span class="token operator">=</span> <span class="token string">"spring-boot"</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">Queue</span> <span class="token function">queue</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Queue</span><span class="token punctuation">(</span>queueName<span class="token punctuation">,</span> <span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">TopicExchange</span> <span class="token function">exchange</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TopicExchange</span><span class="token punctuation">(</span>topicExchangeName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">Binding</span> <span class="token function">binding</span><span class="token punctuation">(</span><span class="token class-name">Queue</span> queue<span class="token punctuation">,</span> <span class="token class-name">TopicExchange</span> exchange<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">BindingBuilder</span><span class="token punctuation">.</span><span class="token function">bind</span><span class="token punctuation">(</span>queue<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">to</span><span class="token punctuation">(</span>exchange<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">with</span><span class="token punctuation">(</span><span class="token string">"foo.bar.#"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">SimpleMessageListenerContainer</span> <span class="token function">container</span><span class="token punctuation">(</span><span class="token class-name">ConnectionFactory</span> connectionFactory<span class="token punctuation">,</span>
                                             <span class="token class-name">MessageListenerAdapter</span> listenerAdapter<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SimpleMessageListenerContainer</span> container <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleMessageListenerContainer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        container<span class="token punctuation">.</span><span class="token function">setConnectionFactory</span><span class="token punctuation">(</span>connectionFactory<span class="token punctuation">)</span><span class="token punctuation">;</span>
        container<span class="token punctuation">.</span><span class="token function">setQueueNames</span><span class="token punctuation">(</span>queueName<span class="token punctuation">)</span><span class="token punctuation">;</span>
        container<span class="token punctuation">.</span><span class="token function">setMessageListener</span><span class="token punctuation">(</span>listenerAdapter<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> container<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">MessageListenerAdapter</span> <span class="token function">listenerAdapter</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span> receiver<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MessageListenerAdapter</span><span class="token punctuation">(</span>receiver<span class="token punctuation">,</span> <span class="token string">"receiveMessage"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">InterruptedException</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">MessagingRabbitmqApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><code>ListenerAdapter()</code> 메소드에 정의된 Bean은 컨테이너(<code>container()</code>에 정의됨)에 메시지 리스너로 등록됩니다. <code>spring-boot</code> 큐에서 메시지를 수신합니다. <code>Receiver</code> 클래스는 POJO이므로 <code>MessageListenerAdapter</code>에 래핑되어야 하며 여기서 <code>receiveMessage</code>를 호출하도록 지정해야 합니다.</p>
<blockquote>
<p>JMS 대기열과 AMQP 대기열은 의미가 다릅니다. 예를 들어 JMS는 대기열에 있는 메시지를 하나의 소비자에게만 보냅니다. AMQP 대기열은 동일한 작업을 수행하지만 AMQP 생산자는 메시지를 대기열에 직접 보내지 않습니다. 대신 단일 대기열로 이동하거나 여러 대기열로 팬아웃할 수 있는 교환으로 메시지가 전송되어 JMS 토픽의 개념을 에뮬레이션합니다.</p>
<ul>
<li>
<p>'팬아웃(Fanout)'은 메시지 라우팅과 관련된 개념으로, 메시지를 한 송신자가 여러 수신자에게 전달하는 것을 의미합니다. </p>
</li>
<li>
<p>AMQP의 팬아웃은 특정 대기열로 메시지를 보내는 것이 아니라, 메시지를 교환(exchange)에 보내고, 해당 교환에 연결된 모든 큐(queue)로 메시지를 전송하는 기능을 말합니다. AMQP에서는 팬아웃을 사용하여 메시지를 여러 대기열에 퍼뜨려 다수의 소비자가 해당 메시지를 받을 수 있도록 할 수 있습니다.</p>
</li>
<li>
<p>이를 JMS의 토픽(Topic) 개념과 유사하게 생각할 수 있습니다. JMS 토픽도 한 번의 메시지 발행으로 여러 구독자에게 메시지를 전송하는데 사용됩니다. AMQP의 팬아웃은 이러한 기능을 수행하면서도, 더 유연한 라우팅 및 교환을 통해 메시지를 효율적으로 관리하고 전달할 수 있는 장점이 있습니다.</p>
</li>
</ul>
</blockquote>
<p>메시지 리스너 컨테이너와 receiver Bean만 있으면 메시지를 수신할 수 있습니다. 메시지를 보내려면 Rabbit 템플릿도 필요합니다.</p>
<p><code>queue()</code> 메서드는 AMQP 대기열을 생성합니다. <code>exchange()</code> 메서드는 주제 교환을 생성합니다. <code>binding()</code> 메서드는 이 두 가지를 함께 바인딩하여 <code>RabbitTemplate</code>이 교환에 게시할 때 발생하는 동작을 정의합니다.</p>
<blockquote>
<p>Spring AMQP를 제대로 설정하려면 <code>Queue</code>, <code>TopicExchange</code> 및 <code>Binding</code>이 최상위 Spring 빈으로 선언되어야 합니다.</p>
</blockquote>
<p>이 경우 주제 교환을 사용하고 대기열은 <code>foo.bar.#</code>이라는 라우팅 키로 바인딩됩니다. 이는 <code>foo.bar</code>로 시작하는 라우팅 키로 전송되는 모든 메시지를 의미합니다. 대기열로 라우팅됩니다.</p>
<h3 id="빈-간의-의존성">빈 간의 의존성</h3>
<p>빈 간의 의존성을 확인하려면 스프링 컨테이너의 빈 구성 정보를 확인해야 합니다. 이를 위해 스프링 프레임워크는 다양한 방법을 제공합니다.</p>
<ol>
<li>
<p><strong>의존성 그래프 출력:</strong> 스프링 컨테이너는 내부적으로 빈의 의존성 그래프를 가지고 있습니다. 이를 출력하려면 <code>ApplicationContext</code>의 <code>getBeanFactory()</code> 메서드를 사용하여 <code>DefaultListableBeanFactory</code>를 가져온 다음, <code>getDependenciesForBean()</code> 등의 메서드를 사용하여 의존성을 출력할 수 있습니다. 하지만 이 방법은 비교적 복잡하고 직접적인 방법이 아닙니다.</p>
</li>
<li>
<p><strong>스프링 부트 Actuator 사용:</strong> Actuator의 <code>/actuator/dependencies</code> 엔드포인트를 호출하여 의존성 정보를 확인할 수 있습니다. 이 엔드포인트는 의존성 트리를 JSON 형태로 제공하여 각 빈 간의 의존성을 볼 수 있습니다.</p>
</li>
<li>
<p><strong>의존성 시각화 도구:</strong> 일부 도구는 스프링 애플리케이션의 빈 구성을 시각적으로 나타내는 기능을 제공합니다. 이를 통해 빈 간의 의존성을 쉽게 파악할 수 있습니다.</p>
</li>
<li>
<p><strong>IDE의 스프링 관련 플러그인:</strong> 일부 IDE는 스프링 빈 간의 의존성을 시각적으로 표시해주는 플러그인을 제공합니다. IntelliJ IDEA 등의 IDE에서는 이러한 플러그인을 통해 빈 간의 의존성을 쉽게 확인할 수 있습니다.</p>
</li>
</ol>
<p>이러한 방법들 중에서 선택하여 빈 간의 의존성을 확인할 수 있습니다. Actuator 엔드포인트나 의존성 시각화 도구를 사용하는 것이 더 직관적이고 편리할 수 있습니다.</p>
<hr>
<h2 id="send-a-test-message">Send a Test Message</h2>
<p>테스트 메시지 보내기<br>
이 샘플에서 테스트 메시지는 <code>CommandLineRunner</code>에 의해 전송됩니다. <code>CommandLineRunner</code>는 receiver의 래치를 기다리고 애플리케이션 컨텍스트를 닫습니다. 다음 목록(src/main/java/guides.messagingrabbitmq/Runner.java)은 작동 방식을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingrabbitmq</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent</span><span class="token punctuation">.</span><span class="token class-name">TimeUnit</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>amqp<span class="token punctuation">.</span>rabbit<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">RabbitTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Runner</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">RabbitTemplate</span> rabbitTemplate<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Receiver</span> receiver<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Runner</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span> receiver<span class="token punctuation">,</span> <span class="token class-name">RabbitTemplate</span> rabbitTemplate<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>receiver <span class="token operator">=</span> receiver<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>rabbitTemplate <span class="token operator">=</span> rabbitTemplate<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Sending message..."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        rabbitTemplate<span class="token punctuation">.</span><span class="token function">convertAndSend</span><span class="token punctuation">(</span><span class="token class-name">MessagingRabbitmqApplication</span><span class="token punctuation">.</span>topicExchangeName<span class="token punctuation">,</span> <span class="token string">"foo.bar.baz"</span><span class="token punctuation">,</span> <span class="token string">"Hello from RabbitMQ!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        receiver<span class="token punctuation">.</span><span class="token function">getLatch</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">await</span><span class="token punctuation">(</span><span class="token number">10000</span><span class="token punctuation">,</span> <span class="token class-name">TimeUnit</span><span class="token punctuation">.</span>MILLISECONDS<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>템플릿은 binding과 일치하는 <code>foo.bar.baz</code>의 라우팅 키를 사용하여 exchange로 메시지를 라우팅합니다.</p>
<p>테스트에서는 수신기를 별도로(독립적으로 isolation) 테스트할 수 있도록 실행기를 모의할 수 있습니다.</p>
<h2 id="run-the-application">Run the Application</h2>
<p><code>main()</code> 메소드는 Spring 애플리케이션 컨텍스트를 생성하여 해당 프로세스를 시작합니다. 그러면 메시지 수신(listening)을 시작하는 메시지 리스너 컨테이너가 시작됩니다. 자동으로 실행되는 <code>Runner</code> Bean이 있습니다. 애플리케이션 컨텍스트에서 <code>RabbitTemplate</code>을 검색하고 <code>Hello from RabbitMQ!</code>를 보냅니다. <code>spring-boot</code> 대기열에 메시지가 표시됩니다. 마지막으로 Spring 애플리케이션 컨텍스트를 닫고 애플리케이션이 종료됩니다.</p>
<pre><code class="language-shell">    Sending message<span class="token punctuation">..</span>.
    Received <span class="token operator">&lt;</span>Hello from RabbitMQ<span class="token operator">!</span><span class="token operator">&gt;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4647a82b-ef68-4df7-a87d-e0b98469df09/image.png"></p>