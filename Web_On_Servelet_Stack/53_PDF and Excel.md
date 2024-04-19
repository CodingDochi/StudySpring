<p>Spring은 PDF 및 Excel 스프레드시트를 포함하여 HTML 이외의 출력을 반환하는 방법을 제공합니다. 이 섹션에서는 이러한 기능을 사용하는 방법을 설명합니다.</p>
<h1 id="introduction-to-document-views">Introduction to Document Views</h1>
<p>HTML 페이지가 항상 사용자가 모델 출력을 보는 가장 좋은 방법은 아니며 Spring을 사용하면 모델 데이터에서 동적으로 PDF 문서나 Excel 스프레드시트를 간단하게 생성할 수 있습니다. 문서는 보기이며 올바른 콘텐츠 유형으로 서버에서 스트리밍되어 클라이언트 PC가 응답으로 스프레드시트 또는 PDF 뷰어 응용 프로그램을 실행할 수 있도록 합니다.</p>
<p>Excel 보기를 사용하려면 클래스 경로에 Apache POI 라이브러리를 추가해야 합니다. PDF를 생성하려면 OpenPDF 라이브러리를 추가하는 것이 좋습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
가능하다면 기본 문서 생성 라이브러리의 최신 버전을 사용해야 합니다. 특히 OpenPDF는 적극적으로 유지 관리되고 신뢰할 수 없는 PDF 콘텐츠에 대한 중요한 취약점을 수정하므로 오래된 원본 iText 2.1.7 대신 OpenPDF(예: OpenPDF 1.2.12)를 강력히 권장합니다.</p>
</blockquote>
<h1 id="pdf-views">PDF Views</h1>
<p>단어 목록에 대한 간단한 PDF 보기는 다음 예제와 같이 <code>org.springframework.web.servlet.view.document.AbstractPdfView</code>를 확장하고 <code>buildPdfDocument()</code> 메서드를 구현할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PdfWordList</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractPdfView</span> <span class="token punctuation">{</span>

	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">buildPdfDocument</span><span class="token punctuation">(</span><span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> model<span class="token punctuation">,</span> <span class="token class-name">Document</span> doc<span class="token punctuation">,</span> <span class="token class-name">PdfWriter</span> writer<span class="token punctuation">,</span>
			<span class="token class-name">HttpServletRequest</span> request<span class="token punctuation">,</span> <span class="token class-name">HttpServletResponse</span> response<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>

		<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> words <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">)</span> model<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"wordList"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">String</span> word <span class="token operator">:</span> words<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			doc<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Paragraph</span><span class="token punctuation">(</span>word<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>컨트롤러는 외부 뷰 정의(이름으로 참조)에서 또는 핸들러 메서드의 <code>View</code> 인스턴스로 이러한 뷰를 반환할 수 있습니다.</p>
<h1 id="excel-views">Excel Views</h1>
<p>Spring Framework 4.2부터 <code>org.springframework.web.servlet.view.document.AbstractXlsView</code>가 Excel 뷰의 기본 클래스로 제공됩니다. 이는 오래된 <code>AbstractExcelView</code> 클래스를 대체하는 특수 하위 클래스(<code>AbstractXlsxView</code> 및 <code>AbstractXlsxStreamingView</code>)가 있는 Apache POI를 기반으로 합니다.</p>
<p>프로그래밍 모델은 <code>AbstractPdfView</code>와 비슷합니다. <code>buildExcelDocument()</code>가 중앙 템플릿 메서드이고 컨트롤러가 외부 정의(by name)에서 이러한 뷰를 반환하거나 핸들러 메서드에서 <code>View</code> 인스턴스로 반환할 수 있다는 점입니다.</p>