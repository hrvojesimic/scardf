<table>
<tt><td><b>Jena</b></td><td><b>Scardf</b></td></tt>
<tr><td><pre><code><br>
Model model = !ModelFactory.createDefaultModel();<br>
model.createResource( "http://somewhere/JohnSmith" )<br>
.addProperty( N,<br>
model.createResource()<br>
.addProperty( Given, "John" )<br>
.addProperty( Family, "Smith" ) );<br>
</code></pre></td>
<td><pre><code><br>
val graph = Graph.build(<br>
!UriRef( "http://somewhere/JohnSmith" ) -N-&gt; Branch(<br>
Given -&gt; "John", Family -&gt; "Smith"<br>
)<br>
)<br>
</code></pre></td>
</tr>
</table>