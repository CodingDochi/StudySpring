<p>이 가이드는 Spring Vault를 사용하여 비밀 관리 도구인 HashiCorp Vault에서 비밀을 로드하는 애플리케이션을 구축하는 과정을 안내합니다.</p>
<h2 id="hashicorp-vault">HashiCorp Vault</h2>
<p>HashiCorp Vault는 보안을 위한 비밀 관리 도구로, 암호, API 키, 암호화된 데이터 등을 안전하게 보관하고 액세스 관리를 제어하는 데 사용됩니다.</p>
<p>일반적으로 소프트웨어 애플리케이션에서 중요한 보안 정보(비밀 키, 패스워드, API 토큰 등)는 안전하게 저장되어야 합니다. Vault는 이러한 중요한 정보를 안전하게 저장하고 관리하기 위한 솔루션으로 사용됩니다. </p>
<p>Vault는 다양한 보안 시나리오에 대응하기 위해 다음과 같은 기능을 제공합니다:</p>
<ol>
<li>
<p><strong>보안 저장소(Secure Storage)</strong>: Vault는 안전한 저장소 역할을 하며, 암호화된 형태로 중요한 데이터를 저장합니다.</p>
</li>
<li>
<p><strong>액세스 제어(Access Control)</strong>: 사용자 및 애플리케이션에 대한 액세스를 엄격하게 제어합니다. 정책을 사용하여 누가 어떤 데이터에 액세스할 수 있는지를 관리합니다.</p>
</li>
<li>
<p><strong>다양한 보안 및 암호화 기능</strong>: 데이터 암호화, 동적 시크릿 생성, 시크릿 회전 등 다양한 보안 기능을 제공하여 데이터 보호를 강화합니다.</p>
</li>
<li>
<p><strong>인증 관리(Authentication Management)</strong>: 다양한 인증 메커니즘을 지원하고, 인증된 사용자에게만 데이터 액세스 권한을 부여합니다.</p>
</li>
<li>
<p><strong>보안 감사(Security Auditing)</strong>: Vault는 시스템의 보안 상태를 감사하고 모니터링할 수 있도록 로깅과 감사 기능을 제공합니다.</p>
</li>
</ol>
<p>Vault는 클라우드 환경이나 온프레미스에서도 사용 가능하며, 보안 요구사항이 높은 환경에서 데이터 보호와 보안 관리를 강화하기 위해 사용됩니다. 개발자, 운영팀 및 보안 전문가들이 애플리케이션에서 사용하는 중요한 보안 정보를 안전하게 관리할 수 있도록 도와줍니다.</p>
<h2 id="hashicorp-vault-설치">HashiCorp Vault 설치</h2>
<p><a href="https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install">https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install</a></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/81e11c40-c25b-4219-b0eb-bf2c54e9d0b8/image.png"></p>
<ol>
<li>Update the package manager and install GPG and wget.</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt</span> update <span class="token operator">&amp;&amp;</span> <span class="token function">sudo</span> <span class="token function">apt</span> <span class="token function">install</span> gpg <span class="token function">wget</span></code></pre>
<ol start="2">
<li>Download the keyring</li>
</ol>
<pre><code class="language-shell"><span class="token function">wget</span> -O- https://apt.releases.hashicorp.com/gpg <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg</code></pre>
<ol start="3">
<li>Verify the keyring</li>
</ol>
<pre><code class="language-shell">gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint</code></pre>
<ol start="4">
<li>Add the HashiCorp repository.</li>
</ol>
<pre><code class="language-shell"><span class="token builtin class-name">echo</span> <span class="token string">"deb [arch=<span class="token variable"><span class="token variable">$(</span>dpkg --print-architecture<span class="token variable">)</span></span> signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com <span class="token variable"><span class="token variable">$(</span>lsb_release -cs<span class="token variable">)</span></span> main"</span> <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /etc/apt/sources.list.d/hashicorp.list</code></pre>
<ol start="5">
<li>Install Vault</li>
</ol>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt</span> update <span class="token operator">&amp;&amp;</span> <span class="token function">sudo</span> <span class="token function">apt</span> <span class="token function">install</span> vault</code></pre>
<ol start="6">
<li>Verifying the Installation<br>
새 터미널을 열고 아래 명령어를 실행하여 바이너리가 실행되는지 확인하세요</li>
</ol>
<pre><code class="language-shell">vault</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c56569ce-3b16-4cd9-84d0-96fc487c24b4/image.png"></p>
<p>이런 화면이 나온다면 정상적으로 설치된 것입니다.</p>
<h2 id="무엇을-만들-것인가">무엇을 만들 것인가</h2>
<p>Vault에 저장된 비밀을 로드하고 전송 암호화 백엔드를 사용합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/b2d9c972-603e-4304-9252-a3e48de6a720/image.png"></p>
<h2 id="개발용-vault-server-시작">개발용 vault server 시작</h2>
<pre><code class="language-shell">vault server --dev --dev-root-token-id<span class="token operator">=</span><span class="token string">"00000000-0000-0000-0000-000000000000"</span></code></pre>
<hr>
<p>이 명령어는 HashiCorp Vault를 개발용(Dev mode)으로 실행하는 데 사용됩니다. </p>
<p>여기서 각 옵션은 다음과 같은 역할을 합니다:</p>
<ul>
<li><code>vault server</code>: Vault 서버를 시작하는 명령어입니다.</li>
<li><code>--dev</code>: Vault를 개발용 모드로 실행하도록 지시합니다. 개발 모드에서는 데이터를 영속적으로 보관하지 않고, 메모리에 보관하며, 토큰이 고정되어 있습니다.</li>
<li><code>--dev-root-token-id="00000000-0000-0000-0000-000000000000"</code>: 개발용 모드에서는 루트 토큰을 설정해야 합니다. 이 옵션은 개발용 모드에서 사용할 루트 토큰의 값을 설정합니다. 여기서는 <code>00000000-0000-0000-0000-000000000000</code>으로 설정되었습니다.</li>
</ul>
<p>개발용 모드는 보안을 강조하는 것보다는 Vault의 기능과 API를 테스트하고 익히는 데 주로 사용됩니다. 이 모드에서 생성된 데이터는 Vault 서버가 종료되면 사라지므로, 실제 운영 환경에서는 사용되지 않습니다. 보안 요소가 포함되지 않고 단순히 기능 및 API를 테스트할 때 유용합니다.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/59a424f4-2454-4f9d-886a-dae4021f894c/image.png"></p>
<p>여러 정보가 포함된 Vault 서버의 시작 로그가 출력됩니다. 로그의 마지막 즈음에 다음과 같은 메세지가 출력되었는지 확인해보세요</p>
<pre><code class="language-shell"><span class="token punctuation">[</span>INFO <span class="token punctuation">]</span> core: post-unseal setup complete</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/36e8b42c-1895-4fc7-aac9-39b85c995a3e/image.png"></p>
<hr>
<p>Vault은 일종의 보안 상자로, 보안 정보를 안전하게 저장하고 암호화하는데 사용됩니다. Vault이 시작될 때 봉인(sealed)된 상태로 시작되는데, 이는 보안을 유지하기 위해 중요한 단계입니다. </p>
<p>Vault이 봉인된 상태에서는 저장된 데이터에 접근할 수 없습니다. Vault을 사용하려면 봉인 해제(unseal) 과정을 거쳐야 합니다. 봉인을 해제 하면 Vault 내부의 암호화 키를 해독하여 데이터에 액세스할 수 있게 됩니다.</p>
<p>unseal은 일종의 보안 절차로, 특정 조건이나 규칙에 따라 여러 명의 사용자가 복합적으로 작동하여 Vault의 보안을 해제합니다. 이 과정에서 복호화 키를 생성하거나 복원하여 Vault의 데이터에 접근할 수 있게 됩니다. unseal은 Vault의 보안성을 유지하고 민감한 정보를 안전하게 보호하기 위한 중요한 단계 중 하나입니다.</p>
<p>개발 모드(dev mode)에서는 Vault가 메모리 상에만 존재하며, 단일 unseal 키로 이미 봉인 해제 상태로 시작됩니다. </p>
<hr>
<blockquote>
<p>위 명령은 전송 암호화 없이 인메모리 저장소를 사용하여 개발 모드에서 Vault를 시작합니다. 이는 Vault를 로컬에서 평가하는 데 적합합니다. 프로덕션 사용을 위해서는 적절한 SSL 인증서와 안정적인 스토리지 백엔드를 사용해야 합니다. 자세한 내용은 Vault의 프로덕션 강화 가이드를 참조하세요.</p>
</blockquote>
<h2 id="vault에-비밀secret-저장">Vault에 비밀(secret) 저장</h2>
<p>Vault는 미사용 시(at rest) 암호화된 민감한(sensitive) 데이터를 저장할 수 있는 비밀 관리 시스템입니다. 비밀번호, 암호화 키, API 키와 같은 민감한 구성 세부 정보를 저장하는 것이 이상적입니다.</p>
<p>Vault 명령줄을 사용하여 Vault에 응용프로그램 구성을 저장하려면 다른 콘솔 창을 실행하세요.</p>
<p>먼저, Vault CLI가 Vault 엔드포인트를 가리키도록 하고 인증 토큰을 제공하도록 두 가지 환경 변수를 설정해야 합니다.</p>
<pre><code class="language-shell">$ <span class="token builtin class-name">export</span> <span class="token assign-left variable">VAULT_TOKEN</span><span class="token operator">=</span><span class="token string">"00000000-0000-0000-0000-000000000000"</span>
$ <span class="token builtin class-name">export</span> <span class="token assign-left variable">VAULT_ADDR</span><span class="token operator">=</span><span class="token string">"http://127.0.0.1:8200"</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2076e86e-4523-4a65-b445-6ca68640ac0f/image.png"></p>
<p>이제 Vault 내에 구성 키-값 쌍을 저장할 수 있습니다.</p>
<pre><code class="language-shell">$ vault kv put secret/github github.oauth2.key<span class="token operator">=</span>foobar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4efdb648-3d1c-4b47-a8cf-410b2acdf668/image.png"></p>
<h3 id="configure-your-application">configure your application</h3>
<p>여기서는 <code>application.properties</code>를 사용하여 애플리케이션을 구성합니다. Spring Cloud Vault는 부트스트랩 컨텍스트로 구성됩니다.<code>src/main/resources/application.properties</code></p>
<pre><code class="language-null">spring.cloud.vault.token=00000000-0000-0000-0000-000000000000
spring.cloud.vault.scheme=http</code></pre>
<h2 id="애플리케이션-클래스-생성">애플리케이션 클래스 생성</h2>
<p>여기에서는 모든 구성 요소가 포함된 Application 클래스를 만듭니다.</p>
<p>src/main/java/hello/Application.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">VaultKeyValueOperationsSupport</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">VaultSysOperations</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">VaultTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">VaultTransitOperations</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">VaultMount</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>vault<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">VaultResponse</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Application</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">VaultTemplate</span> vaultTemplate<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">Application</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> strings<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>

        <span class="token comment">//You usually would not print a secret to stdout</span>
        <span class="token class-name">VaultResponse</span> response <span class="token operator">=</span> vaultTemplate
                <span class="token punctuation">.</span><span class="token function">opsForKeyValue</span><span class="token punctuation">(</span><span class="token string">"secret"</span><span class="token punctuation">,</span> <span class="token class-name">VaultKeyValueOperationsSupport</span><span class="token punctuation">.</span><span class="token class-name">KeyValueBackend</span><span class="token punctuation">.</span>KV_2<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"github"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Value of github.oauth2.key"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"github.oauth2.key"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">//Let's encrypt some data using the Transit backend.</span>
        <span class="token class-name">VaultTransitOperations</span> transitOperations <span class="token operator">=</span> vaultTemplate<span class="token punctuation">.</span><span class="token function">opsForTransit</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">//We need to setup transit first (assuming you didn't set up it yet)</span>
        <span class="token class-name">VaultSysOperations</span> sysOperations <span class="token operator">=</span> vaultTemplate<span class="token punctuation">.</span><span class="token function">opsForSys</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>sysOperations<span class="token punctuation">.</span><span class="token function">getMounts</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">containsKey</span><span class="token punctuation">(</span><span class="token string">"transit/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            sysOperations<span class="token punctuation">.</span><span class="token function">mount</span><span class="token punctuation">(</span><span class="token string">"transit"</span><span class="token punctuation">,</span> <span class="token class-name">VaultMount</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token string">"transit"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            transitOperations<span class="token punctuation">.</span><span class="token function">createKey</span><span class="token punctuation">(</span><span class="token string">"foo-key"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">//Encrypt a plain-text value</span>
        <span class="token class-name">String</span> ciphertext <span class="token operator">=</span> transitOperations<span class="token punctuation">.</span><span class="token function">encrypt</span><span class="token punctuation">(</span><span class="token string">"foo-key"</span><span class="token punctuation">,</span> <span class="token string">"Secure message"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Encrypted value"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>ciphertext<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">//Decrypt</span>

        <span class="token class-name">String</span> plaintext <span class="token operator">=</span> transitOperations<span class="token punctuation">.</span><span class="token function">decrypt</span><span class="token punctuation">(</span><span class="token string">"foo-key"</span><span class="token punctuation">,</span> ciphertext<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Decrypted value"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>plaintext<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"-------------------------------"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring Cloud Vault는 <code>VaultOperations</code>를 사용하여 Vault와 상호작용합니다. 유형이 안전한 액세스를 위해 Vault의 특성이 <code>MyConfiguration</code>에 매핑됩니다. <code>@EnableConfigurationProperties(MyConfiguration.class)</code>는 구성 속성 매핑을 활성화하고 <code>MyConfiguration</code> Bean을 등록합니다.</p>
<p><code>Application</code>에는 <code>MyConfiguration</code> 인스턴스를 자동 연결하는 <code>main()</code> 메서드가 포함되어 있습니다.</p>
<hr>
<ol>
<li>
<p><strong>VaultTemplate</strong>: Spring에서 Vault와 상호 작용하기 위한 간편한 API를 제공하는 클래스입니다. Vault 서버와 통신하고, 다양한 Vault 기능을 실행할 수 있도록 도와줍니다.</p>
<ul>
<li><strong>VaultResponse</strong>: Vault 서버로부터의 응답을 나타내는 클래스입니다. 주로 Vault 서버에서 얻은 데이터를 담고 있습니다.</li>
<li><strong>VaultTransitOperations</strong>: Vault의 Transit 백엔드와 상호 작용하기 위한 클래스로, 데이터를 암호화하고 복호화하는 등의 작업을 수행합니다.</li>
<li><strong>VaultSysOperations</strong>: Vault 시스템 작업을 수행하는데 사용되며, Vault 서버의 설정 변경 및 초기화 등을 처리합니다.</li>
</ul>
</li>
<li>
<p><strong>VaultKeyValueOperationsSupport</strong>: 이는 Vault의 Key-Value 백엔드와 상호 작용하는데 사용되며, Key-Value 데이터를 읽고 쓰는 등의 작업을 수행합니다. 주요 기능으로는 데이터 검색, 쓰기, 수정, 삭제 등이 있습니다.</p>
</li>
<li>
<p><code>KeyValueBackend.KV_1</code>과 <code>KV_2</code>는 Vault의 Key-Value 백엔드 버전을 가리킵니다. <code>KV_1</code>은 오래된 버전의 Key-Value 백엔드이고, <code>KV_2</code>는 더 최신 버전입니다. 이들 간에는 데이터 저장 방식, 기능, 설정 등에서 차이가 있을 수 있습니다.</p>
</li>
<li>
<p><code>CommandLineRunner</code>를 구현하고 <code>run</code>을 오버라이드하는 것은 애플리케이션이 실행될 때 특정한 로직을 수행하기 위한 방법 중 하나입니다. 이 코드에서는 Vault와의 연동 작업을 애플리케이션이 시작될 때 수행하고 있습니다. 애플리케이션이 실행될 때 Vault와의 연결 및 설정 초기화를 자동으로 수행하기 위해서 사용되었을 수 있습니다. 이를 통해 Vault와의 초기 설정을 애플리케이션 시작과 함께 자동으로 수행할 수 있습니다.</p>
</li>
</ol>
<hr>
<h3 id="vault-서버-백엔드-클라이언트">vault 서버, 백엔드, 클라이언트</h3>
<p>Vault 서버는 Vault 애플리케이션 자체를 가리킵니다. 이것은 보안을 위한 중앙 집중식 저장소로 작동하며, 보안 토큰, 비밀 키, 인증 정보 등을 안전하게 저장하고 관리합니다.</p>
<p>Vault 백엔드는 Vault 서버가 실제로 사용하는 데이터 스토리지 엔진입니다. Vault는 여러 종류의 백엔드를 지원하며, 이러한 백엔드는 데이터를 저장하고 보호하기 위해 사용됩니다. 예를 들어, Key-Value 백엔드는 간단한 데이터 저장을 제공하고, Transit 백엔드는 데이터 암호화와 관련된 서비스를 제공합니다.</p>
<p>Vault 클라이언트는 Vault 서버와 상호 작용하는 애플리케이션 또는 도구입니다. Vault 클라이언트는 Vault 서버에 인증하고, 비밀 키를 가져오거나 저장소에서 데이터를 읽거나 쓰는 등의 작업을 수행할 수 있습니다. 클라이언트는 Vault API를 사용하여 서버와 통신하고 보안 데이터를 관리합니다.</p>
<p>이러한 구성요소들은 Vault 시스템에서 보안과 데이터 관리를 위해 함께 작동하여 전체 보안 솔루션을 형성합니다.</p>
<hr>
<h3 id="vault-백엔드">vault 백엔드</h3>
<ul>
<li>
<p><strong>Transit 백엔드</strong> : Vault의 암호화 서비스를 제공하는 백엔드입니다. 이를 통해 데이터를 암호화하고, 암호화된 데이터를 복호화할 수 있습니다. 주로 보안 관련 작업에 사용됩니다.</p>
</li>
<li>
<p><strong>Key-Value 백엔드</strong> 간단한 데이터를 저장하고 관리하는 데 사용됩니다. 파일 시스템, 데이터베이스 또는 다른 저장소와 유사한 방식으로 데이터를 저장합니다.</p>
</li>
<li>
<p><strong>AWS 백엔드</strong>: AWS와 상호 작용하여 AWS 자격 증명을 관리하고, AWS 리소스에 대한 액세스를 제어합니다.</p>
</li>
<li>
<p><strong>Database 백엔드</strong>: 데이터베이스 자격 증명을 동적으로 생성하고 데이터베이스 접근을 제어합니다.</p>
</li>
<li>
<p><strong>Token 백엔드</strong>: 다양한 종류의 토큰을 생성하고 관리합니다. 주로 인증 및 권한 부여에 사용됩니다.</p>
</li>
</ul>
<p>이러한 백엔드들은 Vault의 다양한 기능과 서비스를 제공하며, 각각의 백엔드는 특정한 용도에 맞게 설계되어 있습니다.</p>
<hr>
<p><code>Application</code>이 <code>CommandLineRunner</code>를 구현하므로 부팅이 시작될 때 <code>run</code> 메서드가 자동으로 호출됩니다. 다음과 같은 내용이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/30e98bc1-138d-469e-a463-792a5c3c67d3/image.png"></p>
<blockquote>
<p>Vault의 비밀(secret) 백엔드는 URI를 사용하여 문서를 식별하는 문서 저장소와 잘 비교됩니다. 문서는 JSON 기반이므로 Vault 데이터의 편리한 객체 매핑이 가능합니다.</p>
</blockquote>