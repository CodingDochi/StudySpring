<p>Spring Framework JDBC 추상화에서 제공하는 값은 아마도 아래 표에 설명된 일련의 작업으로 가장 잘 표시될 것입니다. 표는 Spring이 처리하는 작업과 사용자의 책임인 작업을 보여줍니다.</p>
<table id="jdbc-who-does-what" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. Spring JDBC - 누가 무엇을 하는가?</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">동작</th>
<th class="tableblock halign-left valign-top">Spring</th>
<th class="tableblock halign-left valign-top">당신</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">연결(connection) 매개변수 정의</p></td>
<td class="tableblock halign-left valign-top"></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">연결 (connection) 열기</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">SQL 문장(statement) 지정</p></td>
<td class="tableblock halign-left valign-top"></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">매개변수 선언 및 매개변수 값 제공</p></td>
<td class="tableblock halign-left valign-top"></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">문장 준비 및 실행</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">결과 반복을 위한 루프 설정 (필요시)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">각 반복에 대한 작업 수행</p></td>
<td class="tableblock halign-left valign-top"></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">예외 처리</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">트랜잭션 처리</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">연결, 문장 및 결과 집합 닫기</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">X</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
</tbody>
</table>
<p>Spring Framework는 JDBC를 지루한 API로 만들 수 있는 모든 하위 수준 세부 사항을 처리합니다.</p>