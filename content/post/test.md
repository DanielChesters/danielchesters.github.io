+++
description = ""
tags = ["",""]
date = "2017-03-12T23:39:36+01:00"
title = "test"
categories = [""]
draft = true

+++
Test, tu peux pas test!

{{< highlight html >}}
<section id="main">
  <div>
   <h1 id="title">{{ .Title }}</h1>
    {{ range .Data.Pages }}
        {{ .Render "summary"}}
    {{ end }}
  </div>
</section>
{{< /highlight >}}

{{< figure src="/media/spf13.jpg" title="Steve Francia" >}}

[Neat]({{< ref "blog/neat.md" >}})
[Who]({{< relref "about.md#who" >}})

{{< tweet 666616452582129664 >}}

{{< youtube w7Ft2ymGmfc >}}

{{< gist spf13 7896402 >}}

{{< instagram BMokmydjG-M >}}

