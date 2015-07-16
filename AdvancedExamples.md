<table cellpadding='5'>
<tt><td><b>English</b></td><td><b>Scardf</b></td></tt>
<tr>
<td><i>John's children are Anna and Bob.</i></td>
<td><code>john -children-&gt; List( anna, bob )</code></td>
</tr>
<tr>
<td><i>Family members are resources of type "person".</i></td>
<td><code>val familyMembers = g/-/having( RDF.Type -&gt; person )</code></td>
</tr>
<tr>
<td><i>All family members who have a male spouse.</i></td>
<td><code>familyMembers/where( _/spouse/isMale? )</code></td>
</tr>
<tr>
<td><i>John's weight is not stated in graph g.</i></td>
<td><code>g/john has( weight -&gt; None )</code></td>
</tr>
<tr>
<td><i>Family members which like science.</i></td>
<td><code>familyMembers/where( _/likes contains science )</code></td>
</tr>
<tr>
<td><i>Triples stating that someone's height is less than 100.</i></td>
<td><code>g.triplesLike( Node, height, { h: Literal =&gt; asInt(h) &lt; 100 } )</code></td>
</tr>
</table>