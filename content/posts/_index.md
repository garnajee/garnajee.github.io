---
title: All posts
summary: Contains all posts I've written
description: You'll find here all posts I've written.
---

You can also search by [tags](/tags)

{{- $posts := sort .Site.RegularPages ".PublishDate" "desc" -}}

{{- range $post := $posts }}

<article>
  <h2>{{ $post.Title }}</h2>
  <p>{{ $post.Content }}</p>
</article>

{{- end }}

