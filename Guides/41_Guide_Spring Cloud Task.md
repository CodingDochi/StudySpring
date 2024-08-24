<h2 id="getting-started-with-spring-cloud-task">Getting Started with Spring Cloud Task</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/76bea8e0-d4d5-431a-af5c-6d83815a2e69/image.png"></p>
<h3 id="스프링-클라우드-태스크란">스프링 클라우드 태스크란?</h3>
<p>Spring Cloud Task는 일괄 데이터 처리 작업(batch data processing)과 같은 단기 Spring Boot 마이크로서비스를 구축하기 위한 프레임워크입니다. 프로젝트 사이트, 문서 및 샘플에서 프레임워크에 대해 자세히 알아볼 수 있습니다.</p>
<h3 id="태스크-애플리케이션-개요">태스크 애플리케이션 개요</h3>
<p>이 가이드에서는 Spring Cloud Task를 사용하는 Spring Boot 애플리케이션을 개발하고 이를 로컬 머신에 배포합니다. 처음에는 Spring Cloud Task 기능을 시연하기 위해 H2 데이터베이스를 사용하지만 Spring Cloud Task의 가치는 실행된 작업의 결과를 영구 데이터 저장소(persistent data store)에 저장하는 기능에 있습니다. 이 예에서는 MariaDB를 영구 데이터 저장소로 사용하여 진행합니다.</p>
<h3 id="spring-cloud-task-using-in-memory-h2">Spring Cloud Task using In Memory H2</h3>
<p>첫 번째 단계에서는 H2 데이터 저장소가 내장된 Spring Cloud Task를 사용합니다. 프로젝트 초기화 시 <code>spring.datasource</code> 속성이 감지되지 않으면 Spring Cloud Task는 새 H2 인스턴스를 가동하여 실행된 작업 결과를 저장합니다. 이는 추가 구성(configuration) 없이 발생합니다. 이렇게 하면 빠르게 시작하고 실행할 수 있지만 애플리케이션이 완료되고 H2 데이터베이스가 종료되면 실행된 작업의 결과가 손실됩니다. 가이드의 이 단계에서는 <code>application.properites</code>의 <code>spring.datasource</code> 속성이 주석(comment) 처리되어 있는지 확인하세요.</p>
<h2 id="taskapplication">TaskApplication</h2>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>task<span class="token punctuation">.</span>configuration</span><span class="token punctuation">.</span><span class="token class-name">EnableTask</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>task<span class="token punctuation">.</span>listener<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AfterTask</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>task<span class="token punctuation">.</span>listener<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">BeforeTask</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>task<span class="token punctuation">.</span>repository</span><span class="token punctuation">.</span><span class="token class-name">TaskExecution</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@EnableTask</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TaskApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">TaskApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">commandLineRunner</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">HelloWorldCommandLineRunner</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@BeforeTask</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">beforeMethod</span><span class="token punctuation">(</span><span class="token class-name">TaskExecution</span> taskExecution<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Before task"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@AfterTask</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">afterMethod</span><span class="token punctuation">(</span><span class="token class-name">TaskExecution</span> taskExecution<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"After task"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">class</span> <span class="token class-name">HelloWorldCommandLineRunner</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
            <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Hello Spring Cloud Task!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="영구-mariadb를-사용하는-spring-cloud-작업">영구 MariaDB를 사용하는 Spring Cloud 작업</h3>
<p>단기 작업보다 오래 지속되는 데이터를 유지하려면 외부에서 사용 가능한 데이터 저장소가 필요합니다. 이 가이드에서는 Docker 컨테이너에서 실행되는 MariaDB 인스턴스를 사용합니다.</p>
<blockquote>
<p>메모<br>
이 가이드를 따르려면 Docker를 로컬에서 실행해야 합니다.</p>
</blockquote>
<p>MariaDB를 로컬로 실행하려면 다음 명령을 실행하십시오.</p>
<pre><code class="language-shell">docker run -p <span class="token number">3306</span>:3306 --name mariadb -e <span class="token assign-left variable">MYSQL_ROOT_PASSWORD</span><span class="token operator">=</span>password -e <span class="token assign-left variable">MYSQL_DATABASE</span><span class="token operator">=</span>task -d mariadb:latest</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/31d2ef83-0176-4e50-9fd5-e6d747125f66/image.png"></p>
<p>MariaDB 인스턴스가 실행 중이면 <code>application.properties</code>를 통해 애플리케이션에 구성을 제공합니다.</p>
<pre><code class="language-null">spring.datasource.url=jdbc:mariadb://localhost:3306/task
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f976cfbd-4430-4690-9d83-4143fbdbd394/image.png"></p>
<p>이제 애플리케이션을 다시 실행하면 MariaDB 인스턴스 내부에 작업 실행 결과가 저장됩니다. Docker 컨테이너에 셸을 가져와 이를 확인할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/10d5cd13-7c89-4a65-9b29-94710c71c939/image.png"></p>
<pre><code class="language-shell">docker <span class="token builtin class-name">exec</span> -it mariadb mariadb --user root -ppassword</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f333a32b-22ad-4e4b-947c-388d3e476d74/image.png"></p>
<p>그런 다음 명령을 실행하십시오.</p>
<pre><code class="language-shell">use task<span class="token punctuation">;</span>
show tables<span class="token punctuation">;</span> --show all tables that Spring Cloud Task created
<span class="token keyword">select</span> * from task_execution<span class="token punctuation">;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3abff91a-384a-4013-b36d-87857c73aee9/image.png"></p>
<p>MariaDB 인스턴스가 실행되는 동안 실행한 모든 프로그램 실행을 볼 수 있어야 합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f7409c00-45c6-4049-ae8a-2c35f072d485/image.png"></p>
<p>도커로 마리아 디비를 열어놓은 상태로 다시 애플리케이션을 실행하면 이미 테이블이 존재한다며 에러메시지가 나온다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8a27b4e3-9654-48d1-a53b-9fa69bf10691/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7ebc0b76-0205-4f0a-b42f-4ef9408ff48f/image.png"></p>
<p>도커에서 마리아디비를 확인하면 방금 실행한 내용이 반영되어 있음을 알 수 있다. </p>
<p>요약<br>
축하해요! Spring Cloud Task의 상위 수준 개요를 완료했으며 Spring Cloud Task 애플리케이션을 빌드, 테스트 및 시작할 수 있었습니다.</p>