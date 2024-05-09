<p>참조 문서의 이 부분에서는 원시 WebSocket 상호 작용을 포함하는 WebSocket 메시징, SockJS를 통한 WebSocket 에뮬레이션, WebSocket을 통한 하위 프로토콜인 STOMP를 통한 게시-구독 메시징에 대한 지원을 다룹니다.</p>
<h1 id="introduction-to-websocket">Introduction to WebSocket</h1>
<p>WebSocket 프로토콜인 <a href="https://datatracker.ietf.org/doc/html/rfc6455">RFC 6455</a>는 단일 TCP 연결을 통해 클라이언트와 서버 간에 전이중 양방향 통신 채널을 설정하는 표준화된 방법을 제공합니다. 이는 HTTP와 다른 TCP 프로토콜이지만 포트 80 및 443을 사용하고 기존 방화벽 규칙을 재사용할 수 있도록 HTTP를 통해 작동하도록 설계되었습니다.</p>
<p>WebSocket 상호 작용은 HTTP <code>Upgrade</code> 헤더를 사용하여 업그레이드하거나 이 경우 WebSocket 프로토콜로 전환하는 HTTP 요청으로 시작됩니다. 다음 예에서는 이러한 상호 작용을 보여줍니다.</p>
<pre><code class="language-yaml">GET /spring<span class="token punctuation">-</span>websocket<span class="token punctuation">-</span>portfolio/portfolio HTTP/1.1
<span class="token key atrule">Host</span><span class="token punctuation">:</span> localhost<span class="token punctuation">:</span><span class="token number">8080</span>
<span class="token key atrule">Upgrade</span><span class="token punctuation">:</span> websocket  // (1)
<span class="token key atrule">Connection</span><span class="token punctuation">:</span> Upgrade  // (2)
<span class="token key atrule">Sec-WebSocket-Key</span><span class="token punctuation">:</span> Uc9l9TMkWGbHFD2qnFHltg==
<span class="token key atrule">Sec-WebSocket-Protocol</span><span class="token punctuation">:</span> v10.stomp<span class="token punctuation">,</span> v11.stomp
<span class="token key atrule">Sec-WebSocket-Version</span><span class="token punctuation">:</span> <span class="token number">13</span>
<span class="token key atrule">Origin</span><span class="token punctuation">:</span> http<span class="token punctuation">:</span>//localhost<span class="token punctuation">:</span><span class="token number">8080</span></code></pre>
<p>(1) <code>Upgrade</code> 헤더입니다.<br>
(2) <code>Upgrade</code> 연결을 사용합니다. </p>
<p>일반적인 200 상태 코드 대신 WebSocket을 지원하는 서버는 다음과 유사한 출력을 반환합니다.</p>
<pre><code class="language-yaml">HTTP/1.1 101 Switching Protocols  // (1)
<span class="token key atrule">Upgrade</span><span class="token punctuation">:</span> websocket
<span class="token key atrule">Connection</span><span class="token punctuation">:</span> Upgrade
<span class="token key atrule">Sec-WebSocket-Accept</span><span class="token punctuation">:</span> 1qVdfYHU9hPOl4JYYNXF623Gzn0=
<span class="token key atrule">Sec-WebSocket-Protocol</span><span class="token punctuation">:</span> v10.stomp</code></pre>
<p>(1) 프로토콜 스위치</p>
<p>성공적인 핸드셰이크 후에는 HTTP 업그레이드 요청의 기반이 되는 TCP 소켓이 클라이언트와 서버 모두에 대해 계속 열려 메시지를 계속 보내고 받을 수 있습니다.</p>
<p>WebSocket 작동 방식에 대한 완전한 소개는 이 문서의 범위를 벗어납니다. RFC 6455, HTML5의 WebSocket 장 또는 웹의 많은 소개 및 자습서를 참조하세요.</p>
<p>WebSocket 서버가 웹 서버(예: nginx) 뒤에서 실행 중인 경우 WebSocket 업그레이드 요청을 WebSocket 서버로 전달하도록 구성해야 할 수 있습니다. 마찬가지로 애플리케이션이 클라우드 환경에서 실행되는 경우 WebSocket 지원과 관련된 클라우드 제공업체의 지침을 확인하세요.</p>
<h2 id="http-versus-websocket">HTTP Versus WebSocket</h2>
<p>WebSocket은 HTTP와 호환되도록 설계되었으며 HTTP 요청으로 시작되지만 두 프로토콜이 매우 다른 아키텍처와 애플리케이션 프로그래밍 모델로 이어진다는 점을 이해하는 것이 중요합니다.</p>
<p>HTTP와 REST에서 애플리케이션은 많은 URL로 모델링됩니다. 애플리케이션과 상호 작용하기 위해 클라이언트는 요청-응답 스타일로 해당 URL에 액세스합니다. 서버는 HTTP URL, 메소드 및 헤더를 기반으로 적절한 핸들러로 요청을 라우팅합니다.</p>
<p>이와 대조적으로 WebSocket에는 일반적으로 초기 연결에 대한 URL이 하나만 있습니다. 이후에는 모든 애플리케이션 메시지가 동일한 TCP 연결을 통해 전달됩니다. 이는 완전히 다른 비동기식 이벤트 중심 메시징 아키텍처를 나타냅니다.</p>
<p>WebSocket은 HTTP와 달리 메시지 내용에 어떤 의미도 규정하지 않는 낮은 수준의 전송 프로토콜이기도 합니다. 즉, 클라이언트와 서버가 메시지 의미에 동의하지 않으면 메시지를 라우팅하거나 처리할 방법이 없습니다.</p>
<p>WebSocket 클라이언트와 서버는 HTTP 핸드셰이크 요청의 <code>Sec-WebSocket-Protocol</code> 헤더를 통해 더 높은 수준의 메시징 프로토콜(예: STOMP) 사용을 협상할 수 있습니다. 그렇지 않은 경우에는 그들만의 컨벤션을 마련해야 합니다.</p>
<h2 id="when-to-use-websockets">When to Use WebSockets</h2>
<p>WebSocket은 웹 페이지를 동적이고 대화형으로 만들 수 있습니다. 그러나 많은 경우 AJAX와 HTTP 스트리밍 또는 긴 폴링을 결합하면 간단하고 효과적인 솔루션을 제공할 수 있습니다.</p>
<p>예를 들어 뉴스, 메일, 소셜 피드는 동적으로 업데이트되어야 하지만 몇 분마다 업데이트해도 괜찮습니다. 반면 협업, 게임, 금융 앱은 실시간에 훨씬 더 가까워져야 합니다.</p>
<p>지연 시간만으로는 결정적인 요소가 아닙니다. 메시지의 양이 상대적으로 적은 경우(예: 네트워크 오류 모니터링) HTTP 스트리밍 또는 폴링이 효과적인 솔루션을 제공할 수 있습니다. WebSocket 사용에 가장 적합한 사례는 낮은 대기 시간, 높은 빈도 및 높은 볼륨의 조합입니다.</p>
<p>또한 인터넷을 통해 통제할 수 없는 제한적인 프록시는 <code>Upgrade</code> 헤더를 전달하도록 구성되지 않았거나 유휴 상태로 나타나는 장기 연결을 닫기 때문에 WebSocket 상호 작용을 방해할 수 있다는 점을 명심하십시오. 이는 방화벽 내의 내부 애플리케이션에 WebSocket을 사용하는 것이 공용 애플리케이션에 사용하는 것보다 더 간단한 결정임을 의미합니다.</p>
<h1 id="section-summary">Section Summary</h1>
<ul>
<li><a href="https://docs.spring.io/spring-framework/reference/web/websocket/server.html">WebSocket API</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/websocket/fallback.html">SockJS Fallback</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/web/websocket/stomp.html">STOMP</a></li>
</ul>