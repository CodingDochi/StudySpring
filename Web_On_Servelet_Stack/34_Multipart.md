<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/multipart-forms.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>MultipartResolver</code>가 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/multipart.html">활성화되면</a> <code>multipart/form-data</code>가 포함된 POST 요청의 콘텐츠가 구문 분석되어 일반 요청 매개변수로 액세스됩니다. 다음 예에서는 하나의 일반 양식 필드와 하나의 업로드된 파일에 액세스합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileUploadController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/form"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handleFormUpload</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">,</span>
			<span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span><span class="token string">"file"</span><span class="token punctuation">)</span> <span class="token class-name">MultipartFile</span> file<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>file<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">byte</span><span class="token punctuation">[</span><span class="token punctuation">]</span> bytes <span class="token operator">=</span> file<span class="token punctuation">.</span><span class="token function">getBytes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token comment">// store the bytes somewhere</span>
			<span class="token keyword">return</span> <span class="token string">"redirect:uploadSuccess"</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> <span class="token string">"redirect:uploadFailure"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>인수 유형을 <code>List&lt;MultipartFile&gt;</code>로 선언하면 동일한 매개변수 이름에 대해 여러 파일을 확인할 수 있습니다.</p>
<p><code>@RequestParam</code> annotation이 annotation에 지정된 매개변수 이름 없이 <code>Map&lt;String, MultipartFile&gt;</code> 또는 <code>MultiValueMap&lt;String, MultipartFile&gt;</code>으로 선언되면 지정된 각 매개변수 이름에 대한 멀티파트 파일로 맵이 채워집니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Servlet 멀티파트 구문 분석을 사용하면 Spring의 <code>MultipartFile</code> 대신 <code>jakarta.servlet.http.Part</code>를 메소드 인수 또는 컬렉션 값 type으로 선언할 수도 있습니다.</p>
</blockquote>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/modelattrib-method-args.html">command 객체</a>에 대한 데이터 바인딩의 일부로 멀티파트 콘텐츠를 사용할 수도 있습니다. 예를 들어, 이전 예제의 양식 필드와 파일은 다음 예제와 같이 form 객체의 필드일 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">MyForm</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">MultipartFile</span> file<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FileUploadController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/form"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handleFormUpload</span><span class="token punctuation">(</span><span class="token class-name">MyForm</span> form<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> errors<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>form<span class="token punctuation">.</span><span class="token function">getFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">byte</span><span class="token punctuation">[</span><span class="token punctuation">]</span> bytes <span class="token operator">=</span> form<span class="token punctuation">.</span><span class="token function">getFile</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getBytes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token comment">// store the bytes somewhere</span>
			<span class="token keyword">return</span> <span class="token string">"redirect:uploadSuccess"</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> <span class="token string">"redirect:uploadFailure"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>RESTful 서비스 시나리오에서는 브라우저가 아닌 클라이언트에서도 멀티파트 요청을 제출할 수 있습니다. 다음 예에서는 JSON이 포함된 파일을 보여줍니다.</p>
<pre><code class="language-null">POST /someUrl
Content-Type: multipart/mixed

--edt7Tfrdusa7r3lNQc79vXuhIIMlatb7PQg7Vp
Content-Disposition: form-data; name="meta-data"
Content-Type: application/json; charset=UTF-8
Content-Transfer-Encoding: 8bit

{
	"name": "value"
}
--edt7Tfrdusa7r3lNQc79vXuhIIMlatb7PQg7Vp
Content-Disposition: form-data; name="file-data"; filename="file.properties"
Content-Type: text/xml
Content-Transfer-Encoding: 8bit
... File Data ...</code></pre>
<p><code>@RequestParam</code>을 <code>String</code>로 사용하여 "메타 데이터" 부분에 액세스할 수 있지만 아마도 JSON에서 역직렬화하고 싶을 것입니다(<code>@RequestBody</code>와 유사). <a href="https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-message-conversion">HttpMessageConverter</a>로 변환한 후 멀티파트에 액세스하려면 <code>@RequestPart</code> annotation을 사용하세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestPart</span><span class="token punctuation">(</span><span class="token string">"meta-data"</span><span class="token punctuation">)</span> <span class="token class-name">MetaData</span> metadata<span class="token punctuation">,</span>
		<span class="token annotation punctuation">@RequestPart</span><span class="token punctuation">(</span><span class="token string">"file-data"</span><span class="token punctuation">)</span> <span class="token class-name">MultipartFile</span> file<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>jakarta.validation.Valid</code>와 함께 <code>@RequestPart</code>를 사용하거나 Spring의 <code>@Validated</code> annotation을 사용할 수 있으며 두 가지 모두 표준 Bean 유효성 검사가 적용됩니다. 기본적으로 유효성 검사 오류로 인해 <code>MethodArgumentNotValidException</code>이 발생하고 이는 400(BAD_REQUEST) 응답으로 전환됩니다. 또는 다음 예제와 같이 <code>Errors</code> 또는 <code>BindingResult</code> 인수를 통해 컨트롤러 내에서 로컬로 유효성 검사 오류를 처리할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Valid</span> <span class="token annotation punctuation">@RequestPart</span><span class="token punctuation">(</span><span class="token string">"meta-data"</span><span class="token punctuation">)</span> <span class="token class-name">MetaData</span> metadata<span class="token punctuation">,</span> <span class="token class-name">Errors</span> errors<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다른 매개변수에 <code>@Constraint</code> annotation이 있어서 메서드 유효성 검사가 적용되는 경우 대신 <code>HandlerMethodValidationException</code>이 발생합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html">유효성</a> 검사 섹션을 참조하세요.</p>