<h1>Precise Explanation of permission bits (e.g, 0644)</h1>
<p>When you use:</p>
<pre><code class="c">open("file", O_CREAT | O_RDWR, 0.644)</code></pre>
<p>In Basic words the 0644 is an octal literal (if you don't know what is an octal you would have to look up that up its pretty straightforward). the leading 0 means the number is in base 8.</p>
<p>This is just a way to give permission using: read(r), write(w) and execute(w) bits. the permissions are 9 bits divided into 3 groups: Owner, Group, Others</p>
<p>Each permission corresponds to one bit:</p>
<table>
  <thead>
    <tr>
      <th>Permissions</th>
      <th>Binary</th>
      <th>Octal Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Read(r)</td>
      <td>100</td>
      <td>4</td>
    </tr>
    <tr>
      <td>Write(w)</td>
      <td>010</td>
      <td>2</td>
    </tr>
    <tr>
      <td>Execute(x)</td>
      <td>001</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>Each group sums its bits</p>

<h3>0644 Breakdown</h3>

<pre>
  0   6   4   4
      |   |   |
      |   |   └── Others
      |   └──────── Group
      └──────────── Owner
</pre>

<h3>Owner = 6</h3>
<pre>
  6 = 4 + 2
    = read + write
</pre>
<h3>Group = 4</h3>
<pre>4 = read only</pre>
<h3>Others = 4</h3>
<pre>4 = read only</pre>
<h3>So all together:</h3>
<pre>
  Owner : rw-
  Group : r--
  Others : r--
</pre>

<h3>Binary Representation:</h3>
<pre>
  0644 (octal)
  = 110 100 100 (binary)
</pre>
<h4>Which maps to:</h4>
<pre>
  rw- r-- r--
</pre>
