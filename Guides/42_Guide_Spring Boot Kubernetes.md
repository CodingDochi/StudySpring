<p>이 가이드는 Kubernetes에 Spring Boot 애플리케이션을 배포하는 과정을 안내합니다. Spring Boot 및 Kubernetes를 사용하여 작업을 수행하는 다양한 방법 중에서 선택할 수 있습니다. 이 가이드의 목적은 모든 대안을 논의하거나 프로덕션에 도달하는 방법에 대한 모든 세부 사항을 설명하는 것이 아니라 가능한 한 빨리 진행하도록 하는 것입니다.</p>
<hr>
<h2 id="쿠버네티스의-주요-요소">쿠버네티스의 주요 요소</h2>
<p>쿠버네티스(Kubernetes)에서 애플리케이션을 배포하고 관리하기 위해 사용되는 주요 요소들과 그들 간의 상호작용 과정을 간략히 설명하겠습니다.</p>
<ol>
<li>
<p><strong>파드(Pod)</strong>:</p>
<ul>
<li>파드는 쿠버네티스에서 가장 작은 배포 가능한 단위입니다. </li>
<li>하나 이상의 컨테이너를 그룹화하며, 동일한 호스트에서 실행됩니다.</li>
<li>파드는 동일한 IP 주소와 네트워크 네임스페이스를 공유하므로 컨테이너 간에 네트워크 통신이 가능합니다.</li>
</ul>
</li>
<li>
<p><strong>서비스(Service)</strong>:</p>
<ul>
<li>서비스는 파드의 집합에 대한 네트워크 엔드포인트를 제공하는 추상화입니다.</li>
<li>클러스터 내의 다른 파드나 외부 클라이언트가 서비스를 통해 파드에 접근할 수 있습니다.</li>
<li>서비스 유형에는 ClusterIP, NodePort, LoadBalancer, ExternalName 등이 있습니다.</li>
</ul>
</li>
<li>
<p><strong>인그레스(Ingress)</strong>:</p>
<ul>
<li>인그레스는 클러스터 내부 또는 외부에서 애플리케이션에 접근할 수 있는 HTTP 및 HTTPS 라우팅 규칙을 정의하는 API 객체입니다.</li>
<li>서비스와 함께 사용되어 외부에서 애플리케이션으로의 트래픽을 라우팅합니다.</li>
<li>단일 IP 주소 및 포트를 사용하여 여러 서비스에 대한 가상 호스트 기반 라우팅을 제공합니다.</li>
</ul>
</li>
<li>
<p><strong>쿠버네티스 이벤트 순환 과정</strong>:</p>
<ul>
<li>사용자는 kubectl 또는 API를 통해 쿠버네티스 클러스터에 명령을 제출합니다.</li>
<li>API 서버는 요청을 받아들이고, 이를 etcd 데이터베이스에 저장합니다.</li>
<li>스케줄러는 새로운 파드를 클러스터 내의 어떤 노드에 배치할지 결정하고, API 서버에 배치 결과를 전송합니다.</li>
<li>kubelet은 노드에서 실행 중인 파드 상태를 확인하고, 필요에 따라 파드를 생성하거나 제거합니다.</li>
<li>생성된 파드는 클러스터 네트워크와 연결되고, 서비스와 인그레스 등의 다른 리소스에 의해 라우팅될 수 있습니다.</li>
</ul>
</li>
</ol>
<p>이러한 주요 요소들과 과정을 통해 쿠버네티스는 애플리케이션을 배포하고 관리하는 데 필요한 기능을 제공합니다.</p>
<hr>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>Kubernetes는 컨테이너화된 애플리케이션의 배포(deployment), 확장(scaling) 및 관리(management)를 자동화하기 위한 오픈 소스 시스템입니다. 쉽게 관리하고 검색할 수 있도록 애플리케이션을 구성하는 컨테이너를 논리 단위로 그룹화합니다. 이 가이드에서는 간단한 Spring 부팅 애플리케이션을 구축하고 배포합니다.</p>
<p>컨테이너 이미지 빌드에 대한 배경 지식을 다루는 시작 가이드 및 Docker 관련 주제 가이드도 찾을 수 있습니다.</p>
<h2 id="what-you-will-need">What You Will Need</h2>
<p>Linux 또는 Linux와 유사한 명령줄이 필요합니다. 이 가이드의 명령줄 예제는 Linux, 셸이 있는 MacOS 터미널 또는 Windows의 WSL에서 작동합니다.</p>
<hr>
<h3 id="install-kubectl-on-linux">Install kubectl on Linux</h3>
<p><a href="https://kubernetes.io/docs/tasks/tools/">https://kubernetes.io/docs/tasks/tools/</a></p>
<p><a href="https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/">https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/</a></p>
<h4 id="1-curl로-kubectl-binary-설치하기">1. curl로 kubectl binary 설치하기</h4>
<pre><code class="language-shell"><span class="token function">curl</span> -LO <span class="token string">"https://dl.k8s.io/release/<span class="token variable"><span class="token variable">$(</span><span class="token function">curl</span> -L -s https://dl.k8s.io/release/stable.txt<span class="token variable">)</span></span>/bin/linux/amd64/kubectl"</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4964fe43-de71-4ba7-a8f8-214f0f3e4794/image.png"></p>
<h4 id="2-binary-검증validate-하기">2. binary 검증(validate) 하기</h4>
<p>kubectll 체크섬 파일 다운로드</p>
<pre><code class="language-shell"> <span class="token function">curl</span> -LO <span class="token string">"https://dl.k8s.io/release/<span class="token variable"><span class="token variable">$(</span><span class="token function">curl</span> -L -s https://dl.k8s.io/release/stable.txt<span class="token variable">)</span></span>/bin/linux/amd64/kubectl.sha256"</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5d1b27f7-a926-4fd6-b7ea-71c7679f15b7/image.png"></p>
<p>체크섬 파일에 대해 kubectl 바이너리의 유효성을 검사합니다.</p>
<pre><code class="language-shell"><span class="token builtin class-name">echo</span> <span class="token string">"<span class="token variable"><span class="token variable">$(</span><span class="token function">cat</span> kubectl.sha256<span class="token variable">)</span></span>  kubectl"</span> <span class="token operator">|</span> sha256sum --check</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/74f8fd72-c5a6-4cf7-ad29-f6dd5c527439/image.png"></p>
<h4 id="3-kubectl-설치하기">3. kubectl 설치하기</h4>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">install</span> -o root -g root -m 0755 kubectl /usr/local/bin/kubectl</code></pre>
<h4 id="4-설치한-버전이-최신인지-테스트하십시오">4. 설치한 버전이 최신인지 테스트하십시오.</h4>
<pre><code class="language-shell">kubectl version --client</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/671b9630-c725-46b7-8274-376ae8322fde/image.png"></p>
<h3 id="verify-kubectl-configuration">Verify kubectl configuration</h3>
<p>kubectl이 Kubernetes 클러스터를 찾고 액세스하려면 <code>kube-up.sh</code>를 사용하여 클러스터를 생성하거나 Minikube 클러스터를 성공적으로 배포할 때 자동으로 생성되는 <code>kubeconfig</code> 파일이 필요합니다. 기본적으로 kubectl 구성(configuration)은 <code>~/.kube/config</code>에 있습니다.</p>
<p>클러스터 상태(cluster state)를 가져와서 kubectl이 올바르게 구성되었는지 확인하세요.</p>
<pre><code class="language-shell">kubectl cluster-info</code></pre>
<p>URL 응답이 표시되면 kubectl이 클러스터에 액세스하도록 올바르게 구성된 것입니다. 다음과 유사한 메시지가 표시되면 kubectl이 올바르게 구성되지 않았거나 Kubernetes 클러스터에 연결할 수 없는 것입니다.</p>
<pre><code class="language-shell">The connection to the server <span class="token operator">&lt;</span>server-name:port<span class="token operator">&gt;</span> was refused - did you specify the right <span class="token function">host</span> or port?</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/54a77d64-b81f-4084-9333-7fb91b7abc89/image.png"></p>
<p>예를 들어, 노트북에서 (로컬로) Kubernetes 클러스터를 실행하려는 경우 먼저 <code>Minikube</code>와 같은 도구를 설치한 다음 위에 설명된 명령을 다시 실행해야 합니다.</p>
<p>kubectl cluster-info가 URL 응답을 반환하지만 클러스터에 액세스할 수 없는 경우 클러스터가 올바르게 구성되었는지 확인하려면 다음을 사용하세요.</p>
<pre><code class="language-shell">kubectl cluster-info dump</code></pre>
<h3 id="minikube-설치">minikube 설치</h3>
<p>minikube는 Kubernetes를 쉽게 배우고 개발할 수 있도록 하는 데 중점을 둔 로컬 Kubernetes입니다. 필요한 것은 Docker(또는 이와 유사하게 호환되는) 컨테이너 또는 가상 머신 환경뿐이며 Kubernetes는 단 하나의 명령 <code>minikube start</code> 으로 사용할 수 있습니다.</p>
<h4 id="what-youll-need">What you’ll need</h4>
<ul>
<li>2 CPUs or more</li>
<li>2GB of free memory</li>
<li>20GB of free disk space</li>
<li>Internet connection</li>
<li>Container or virtual machine manager, such as: Docker, QEMU, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMware Fusion/Workstation</li>
</ul>
<p>바이너리 다운로드를 사용하여 x86-64 Linux에 최신 minikube 안정 릴리스를 설치하려면:</p>
<pre><code class="language-shell"><span class="token function">curl</span> -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
<span class="token function">sudo</span> <span class="token function">install</span> minikube-linux-amd64 /usr/local/bin/minikube</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3f7ffb83-b4c1-4d1a-a4bc-8e99b5ace4bf/image.png"></p>
<h4 id="start-your-cluster">start your cluster</h4>
<p>관리자 액세스 권한이 있는 터미널(root로 로그인하지 않음)에서 다음을 실행합니다.</p>
<pre><code class="language-shell">minikube start</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/09bf325a-7700-47a1-8e18-c5e11f8a9579/image.png"></p>
<p>Minikube가 시작되지 않으면 <a href="https://minikube.sigs.k8s.io/docs/drivers/">드라이버 페이지</a>에서 호환 가능한 컨테이너 또는 가상 머신 관리자 설정에 대한 도움말을 참조하세요.</p>
<h3 id="interact-with-your-cluster">Interact with your cluster</h3>
<p>이미 kubectl을 설치했다면(문서 참조) 이제 이를 사용하여 반짝이는 새 클러스터에 액세스할 수 있습니다.</p>
<pre><code class="language-shell">kubectl get po -A</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9c575a8e-93c4-4bb6-9b22-847eba4d8551/image.png"></p>
<hr>
<p>Kubernetes 클러스터와 명령줄 도구 <code>Kubectl</code>도 필요합니다. <code>Kind</code>(Docker에서) 또는 <code>Minikube</code>를 사용하여 로컬로 클러스터를 생성할 수 있습니다. 또는 <code>Google Cloud Platform</code>, <code>Amazon Web Services</code> 또는 <code>Microsoft Azure</code>와 같은 클라우드 공급자를 사용할 수 있습니다. 계속 진행하기 전에 셸에서 <code>kubectl</code> 명령을 실행할 수 있는지 확인하세요. 다음 예에서는 <code>kind</code>를 사용합니다.</p>
<pre><code class="language-shell">$ kubectl cluster-info
Kubernetes master is running at https://127.0.0.1:46253
KubeDNS is running at https://127.0.0.1:46253/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use <span class="token string">'kubectl cluster-info dump'</span><span class="token builtin class-name">.</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/479d9a15-f9c2-43c3-b06a-a06d09f22071/image.png"></p>
<p>또한 다음 명령을 실행해야 합니다.</p>
<pre><code class="language-shell">$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT<span class="token punctuation">(</span>S<span class="token punctuation">)</span>   AGE
service/kubernetes   ClusterIP   <span class="token number">10.43</span>.0.1    <span class="token operator">&lt;</span>none<span class="token operator">&gt;</span>        <span class="token number">443</span>/TCP   7m13s</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c99bc96e-9d63-403b-917e-927ca1223951/image.png"></p>
<h2 id="create-a-spring-boot-application">Create a Spring Boot Application</h2>
<p>먼저 Spring Boot 애플리케이션을 만듭니다. 이미 github에서 사용하고 싶은 것이 있다면 터미널에서 복제할 수 있습니다(<code>git</code> 및 <code>java</code>는 이미 설치되어 있습니다). 또는 <code>start.spring.io</code>를 사용하여 처음부터 애플리케이션을 생성할 수 있습니다.</p>
<pre><code class="language-shell"><span class="token function">curl</span> https://start.spring.io/starter.tgz -d <span class="token assign-left variable">dependencies</span><span class="token operator">=</span>webflux,actuator <span class="token operator">|</span> <span class="token function">tar</span> -xzvf -</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d9f4a6fc-8755-4725-b4d8-30b93aebc705/image.png"></p>
<p>그런 다음 애플리케이션을 빌드할 수 있습니다.</p>
<hr>
<p>그레이들(Gradle)을 사용하여 애플리케이션을 빌드할 수 있어요! <code>./gradlew</code> 명령어는 프로젝트 루트 디렉토리에서 Gradle 래퍼(wrapper)를 실행하여 빌드하는 데 사용됩니다. 이 명령어를 실행하면 프로젝트의 Gradle 스크립트가 실행되어 프로젝트를 빌드하고 필요한 의존성을 다운로드합니다. 만약 Gradle을 사용하여 Java 또는 Kotlin 기반의 애플리케이션을 빌드하려면 <code>./gradlew build</code> 명령어를 사용할 수 있어요.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/37b571fc-4de6-4d23-b27f-b403e5af1ee9/image.png"></p>
<p>그러면 빌드 결과를 볼 수 있습니다. 빌드가 성공하면 다음과 유사한 JAR 파일이 표시됩니다.</p>
<pre><code class="language-shell"><span class="token function">ls</span> -l target/*.jar
-rw-r--r-- <span class="token number">1</span> root root <span class="token number">19463334</span> Nov <span class="token number">15</span> <span class="token number">11</span>:54 target/demo-0.0.1-SNAPSHOT.jar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fbfe40c8-f000-49b8-83c7-2c6a180d4092/image.png"></p>
<hr>
<p><code>./gradlew build</code>로 빌드한 파일은 <code>build/libs</code> 디렉토리에 저장되어있습니다.</p>
<hr>
<p>JAR은 실행 가능합니다.</p>
<pre><code class="language-shell">java -jar 파일이름.jar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/534099be-7b24-42ba-aa72-dacb7fba172c/image.png"></p>
<p>프로젝트를 다운로드할 때 추가한 액추에이터 종속성 때문에 애플리케이션에는 몇 가지 기본 제공 HTTP 엔드포인트가 있습니다. 시작 시 로그에 다음과 유사한 출력이 표시됩니다.</p>
<pre><code class="language-shell"><span class="token punctuation">..</span>.
<span class="token number">2019</span>-11-15 <span class="token number">12</span>:12:35.333  INFO <span class="token number">13912</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> o.s.b.a.e.web.EndpointLinksResolver      <span class="token builtin class-name">:</span> Exposing <span class="token number">2</span> endpoint<span class="token punctuation">(</span>s<span class="token punctuation">)</span> beneath base path <span class="token string">'/actuator'</span>
<span class="token number">2019</span>-11-15 <span class="token number">12</span>:12:36.448  INFO <span class="token number">13912</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> o.s.b.web.embedded.netty.NettyWebServer  <span class="token builtin class-name">:</span> Netty started on port<span class="token punctuation">(</span>s<span class="token punctuation">)</span>: <span class="token number">8080</span>
<span class="token punctuation">..</span>.</code></pre>
<p>그런 다음 다른 터미널에서 endpoint을 curl할 수 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> localhost:8080/actuator <span class="token operator">|</span> jq <span class="token builtin class-name">.</span>
<span class="token punctuation">{</span>
  <span class="token string">"_links"</span><span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
    <span class="token string">"self"</span><span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
      <span class="token string">"href"</span><span class="token builtin class-name">:</span> <span class="token string">"http://localhost:8080/actuator"</span>,
      <span class="token string">"templated"</span><span class="token builtin class-name">:</span> <span class="token boolean">false</span>
    <span class="token punctuation">}</span>,
    <span class="token string">"health-path"</span><span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
      <span class="token string">"href"</span><span class="token builtin class-name">:</span> <span class="token string">"http://localhost:8080/actuator/health/{*path}"</span>,
      <span class="token string">"templated"</span><span class="token builtin class-name">:</span> <span class="token boolean">true</span>
    <span class="token punctuation">}</span>,
    <span class="token string">"health"</span><span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
      <span class="token string">"href"</span><span class="token builtin class-name">:</span> <span class="token string">"http://localhost:8080/actuator/health"</span>,
      <span class="token string">"templated"</span><span class="token builtin class-name">:</span> <span class="token boolean">false</span>
    <span class="token punctuation">}</span>,
    <span class="token string">"info"</span><span class="token builtin class-name">:</span> <span class="token punctuation">{</span>
      <span class="token string">"href"</span><span class="token builtin class-name">:</span> <span class="token string">"http://localhost:8080/actuator/info"</span>,
      <span class="token string">"templated"</span><span class="token builtin class-name">:</span> <span class="token boolean">false</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<ul>
<li><code>curl</code>: 커맨드 라인에서 URL을 통해 데이터를 전송하고 받을 수 있는 도구입니다.</li>
<li><code>localhost:8080/actuator</code>: 로컬 호스트의 8080 포트에서 액추에이터 엔드포인트에 요청을 보냅니다. 액추에이터는 스프링 부트 애플리케이션의 모니터링 및 관리를 위한 엔드포인트를 제공합니다.</li>
<li><code>jq .</code>: curl로 받은 JSON을 파싱하고, 보기 좋게 출력하는데 사용되는 도구입니다. <code>.</code>은 jq에게 입력을 그대로 출력하도록 지시합니다.</li>
</ul>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2c726354-3bef-4c0a-8e1c-c5a76eaf9208/image.png"></p>
<p>이 단계를 완료하려면 Ctrl+C를 눌러 애플리케이션을 중지하세요.</p>
<h2 id="containerize-the-application">Containerize the Application</h2>
<p>Spring Boot 애플리케이션을 컨테이너화하는 데는 여러 가지 옵션이 있습니다. 이미 Spring Boot jar 파일을 빌드하고 있는 경우에는 플러그인을 직접 호출하기만 하면 됩니다.</p>
<pre><code class="language-shell">$ ./gradlew bootBuildImage</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f697a2dc-e5bf-4ab8-af74-343876c0e4aa/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/70f5179e-2ad5-4317-b2f7-c2bee7b0e1f8/image.png"></p>
<p>컨테이너를 로컬로 실행할 수 있습니다.</p>
<pre><code class="language-shell">$ docker run -p <span class="token number">8080</span>:8080 demo:0.0.1-SNAPSHOT</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1db62a9f-fbd3-4a4f-88ad-76a45d6f1096/image.png"></p>
<p>그런 다음 다른 터미널에서 작동하는지 확인할 수 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> localhost:8080/actuator/health</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4d5cb90b-f3ba-4072-ad46-f8d6d55eb795/image.png"></p>
<p>컨테이너를 중지하여 완료합니다.<br>
Dockerhub(<code>docker login</code>)로 인증하지 않으면 이미지를 푸시할 수 없지만 작동해야 하는 이미지가 이미 있습니다. 인증을 받은 경우 다음을 수행할 수 있습니다.</p>
<pre><code class="language-shell">$ docker tag demo:0.0.1-SNAPSHOT springguides/demo
$ docker push springguides/demo</code></pre>
<p>실제로는 Kubernetes가 일반적으로 로컬 docker 데몬에 연결되지 않는 Kubelets(노드) 내부에서 이미지를 가져오기 때문에 이미지를 Dockerhub(또는 다른 액세스 가능한 저장소)에 푸시해야 합니다. 이 시나리오의 목적에 따라 푸시를 생략하고 이미 있는 이미지를 사용할 수 있습니다.</p>
<blockquote>
<p>테스트를 위해 안전하지 않은 (예를 들어,)로컬 레지스트리에서 <code>docker push</code>가 작동하도록 하는 해결 방법이 있지만 이는 이 가이드의 범위를 벗어납니다.</p>
</blockquote>
<h2 id="deploy-the-application-to-kubernetes">Deploy the Application to Kubernetes</h2>
<hr>
<p>쿠버네티스에 애플리케이션을 배포하는 것은 당신이 개발한 애플리케이션을 쿠버네티스 클러스터에서 실행할 수 있도록 설정하는 과정을 의미합니다. 이는 애플리케이션을 컨테이너 이미지로 패키징하고, 쿠버네티스가 이 이미지를 사용하여 클러스터 내에서 여러 노드에 분산하여 실행하도록 지시하는 것을 포함합니다.</p>
<p>일반적으로 쿠버네티스에서 애플리케이션을 배포하는 단계는 다음과 같습니다:</p>
<ol>
<li>
<p><strong>컨테이너 이미지 준비</strong>: 먼저 애플리케이션을 실행할 수 있는 컨테이너 이미지를 준비합니다. 이 이미지에는 애플리케이션 코드, 실행에 필요한 종속성 및 설정이 포함됩니다.</p>
</li>
<li>
<p><strong>쿠버네티스 리소스 정의</strong>: 쿠버네티스는 YAML 또는 JSON 포맷의 매니페스트 파일을 사용하여 애플리케이션 배포를 정의합니다. 이 매니페스트 파일은 애플리케이션을 실행할 파드, 서비스, 인그레스 등의 쿠버네티스 리소스를 정의합니다.</p>
</li>
<li>
<p><strong>쿠버네티스 클러스터에 배포</strong>: 정의된 쿠버네티스 리소스를 사용하여 쿠버네티스 클러스터에 애플리케이션을 배포합니다. 이 단계에서 쿠버네티스는 애플리케이션을 실행하고 네트워킹 및 로드 밸런싱을 구성합니다.</p>
</li>
<li>
<p><strong>상태 모니터링 및 관리</strong>: 배포된 애플리케이션의 상태를 모니터링하고 필요한 경우 스케일링, 롤링 업데이트 등의 작업을 수행하여 애플리케이션을 관리합니다.</p>
</li>
</ol>
<p>따라서 쿠버네티스에 애플리케이션을 배포한다는 것은 애플리케이션을 실행할 환경을 설정하고, 클러스터 내에서 안정적으로 실행되도록 하는 과정을 의미합니다.</p>
<hr>
<p>이제 포트 8080을 실행하고 노출하는 컨테이너가 있으므로 Kubernetes를 실행하는 데 필요한 것은 YAML뿐입니다. YAML을 보거나 편집할 필요가 없도록 지금은 <code>kubectl</code>에 YAML 생성을 요청할 수 있습니다. 여기서 변경될 수 있는 유일한 것은 <code>--image</code> 이름입니다. 컨테이너를 자체 리포지토리에 배포한 경우 다음 태그 대신 해당 태그를 사용하세요.</p>
<pre><code class="language-shell">$  kubectl create deployment demo --image<span class="token operator">=</span>springg--image<span class="token operator">=</span>springguides/demo --dry-run<span class="token operator">=</span>client -o<span class="token operator">=</span>yaml <span class="token operator">&gt;</span> deployment.yaml
$ <span class="token builtin class-name">echo</span> --- <span class="token operator">&gt;&gt;</span> deployment.yaml
$ kubectl create <span class="token function">service</span> clusterip demo --tcp<span class="token operator">=</span><span class="token number">8080</span>:8080 --dry-run -o<span class="token operator">=</span>yaml <span class="token operator">&gt;&gt;</span> deployment.yaml</code></pre>
<hr>
<p>해당 명령어는 먼저 쿠버네티스에 애플리케이션을 배포하기 위해 YAML 매니페스트 파일을 생성하는 과정을 수행합니다.</p>
<ol>
<li>
<p><code>kubectl create deployment demo --image=springg--image=springguides/demo --dry-run=client -o=yaml &gt; deployment.yaml</code>: 이 명령은 쿠버네티스 클러스터에 <code>demo</code>라는 이름의 배포(deployment)를 생성하고, 해당 배포에 <code>springguides/demo</code>라는 이미지를 사용하도록 지정합니다. <code>--dry-run=client -o=yaml</code> 옵션은 실제로 클러스터에 배포를 만들지 않고 YAML 형식으로 출력하도록 지시합니다. 이를 <code>deployment.yaml</code> 파일에 리다이렉션하여 파일로 저장합니다.</p>
</li>
<li>
<p><code>echo --- &gt;&gt; deployment.yaml</code>: 이 명령은 <code>deployment.yaml</code> 파일에 구분을 위해 <code>---</code>를 추가합니다. 이는 다음 명령어의 결과를 구분하기 위한 것입니다.</p>
</li>
<li>
<p><code>kubectl create service clusterip demo --tcp=8080:8080 --dry-run=client -o=yaml &gt;&gt; deployment.yaml</code>: 이 명령은 <code>demo</code>라는 이름의 ClusterIP 서비스를 생성하며, 이 서비스는 8080 포트로 들어오는 트래픽을 배포된 파드의 8080 포트로 전달합니다. 이 또한 <code>--dry-run=client -o=yaml</code> 옵션을 사용하여 YAML 형식으로 출력하고, 이를 <code>deployment.yaml</code> 파일에 추가합니다.</p>
</li>
</ol>
<p>따라서 위 명령어를 실행하면 <code>deployment.yaml</code> 파일에 배포 및 서비스에 대한 정의가 YAML 형식으로 저장됩니다. 이 파일을 사용하여 쿠버네티스 클러스터에 애플리케이션을 배포할 수 있습니다.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/cef6bb31-08a9-4267-a564-20c72af97f85/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5edf882c-11fd-44cc-8437-858c9f3d2642/image.png"></p>
<p>위에서 생성된 YAML을 가져와 원하는 경우 편집하거나 그대로 적용할 수 있습니다.</p>
<pre><code class="language-shell">$ kubectl apply -f deployment.yaml
deployment.apps/demo created
service/demo created</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6bc84579-ccc5-438e-ba1b-c17a1014efa9/image.png"></p>
<p>애플리케이션이 실행 중인지 확인합니다.</p>
<pre><code class="language-shell">$ kubectl get all
NAME                             READY     STATUS      RESTARTS   AGE
pod/demo-658b7f4997-qfw9l        <span class="token number">1</span>/1       Running     <span class="token number">0</span>          146m

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT<span class="token punctuation">(</span>S<span class="token punctuation">)</span>    AGE
service/kubernetes   ClusterIP   <span class="token number">10.43</span>.0.1       <span class="token operator">&lt;</span>none<span class="token operator">&gt;</span>        <span class="token number">443</span>/TCP    2d18h
service/demo         ClusterIP   <span class="token number">10.43</span>.138.213   <span class="token operator">&lt;</span>none<span class="token operator">&gt;</span>        <span class="token number">8080</span>/TCP   21h

NAME                   READY     UP-TO-DATE   AVAILABLE   AGE
deployment.apps/demo   <span class="token number">1</span>/1       <span class="token number">1</span>            <span class="token number">1</span>           21h

NAME                              DESIRED   CURRENT   READY     AGE
replicaset.apps/demo-658b7f4997   <span class="token number">1</span>         <span class="token number">1</span>         <span class="token number">1</span>         21h
d</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fdfd56f2-fb4c-46f2-8bcd-24c408208bd7/image.png"></p>
<blockquote>
<p>데모 팟(Pod)이 <code>Running</code> 상태로 표시될 때까지 <code>kubectl get all</code>을 반복하십시오.</p>
</blockquote>
<p>이제 Kubernetes에서 서비스로 노출한 애플리케이션에 연결할 수 있어야 합니다. 개발 시에 잘 작동하는 한 가지 방법은 SSH 터널을 만드는 것입니다.</p>
<pre><code class="language-shell">$ kubectl port-forward svc/demo <span class="token number">8080</span>:8080</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7aa09713-974b-4002-a4f0-bcff0e876374/image.png"></p>
<p>이제 다른 터미널에서 해당 앱이 실행 중인지 확인할 수 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> localhost:8080/actuator/health
<span class="token punctuation">{</span><span class="token string">"status"</span><span class="token builtin class-name">:</span><span class="token string">"UP"</span><span class="token punctuation">}</span></code></pre>