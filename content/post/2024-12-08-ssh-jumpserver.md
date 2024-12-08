---
title: ssh jump
subtitle:  ssh jump zwischen mehreren Servern
date: 2024-12-08
tags: ["ssh", "ssh-jump"]
---



### Simple Left-Right Graph
```mermaid
graph LR;
A-->B;
A-->C;
B-->D;
C-->D;
```

```mermaid
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->>John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail!
    John-->>Alice: Great!
    John->>Bob: How about you?
    Bob-->>John: Jolly good!
```


The result is a diagram which is correctly rendered like this:

<pre class="mermaid">
flowchart TD
     A-->B
</pre>

If you want to see the raw source files for this, just take a look at the [source for this page](https://raw.githubusercontent.com/andykuszyk/andykuszyk.github.io/master/2023-05-03-yet-another-mermaid-in-github-pages-guide.md).

<script type="module">
	import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
	mermaid.initialize({
		startOnLoad: true,
		theme: 'dark'
	});
</script>


```goat
      .               .                .               .--- 1          .-- 1     / 1
     / \              |                |           .---+            .-+         +
    /   \         .---+---.         .--+--.        |   '--- 2      |   '-- 2   / \ 2
   +     +        |       |        |       |    ---+            ---+          +
  / \   / \     .-+-.   .-+-.     .+.     .+.      |   .--- 3      |   .-- 3   \ / 3
 /   \ /   \    |   |   |   |    |   |   |   |     '---+            '-+         +
 1   2 3   4    1   2   3   4    1   2   3   4         '--- 4          '-- 4     \ 4

```