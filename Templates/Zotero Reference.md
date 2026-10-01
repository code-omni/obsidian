---
type: reference
citekey: {{citekey}}
title: "{{title}}"
authors: [{% for c in creators %}"{{c.firstName}} {{c.lastName}}{{c.name}}"{% if not loop.last %}, {% endif %}{% endfor %}]
year: {{date | format("YYYY")}}
item_type: {{itemType}}
zotero: {{desktopURI}}
aliases: ["{{title}}"]
tags: [{% for t in tags %}"{{t.tag | replace(" ", "-")}}"{% if not loop.last %}, {% endif %}{% endfor %}]
---
# {{title}}
{% for c in creators %}{{c.firstName}} {{c.lastName}}{{c.name}}{% if not loop.last %}, {% endif %}{% endfor %} ({{date | format("YYYY")}}) · [Open in Zotero]({{desktopURI}}){% if url %} · [Source]({{url}}){% endif %}

{% persist "synopsis" %}
## Synopsis
<!-- Your words: what is this source claiming, and why did it matter to you? Survives re-import. -->

## My notes
<!-- Thoughts while reading, links to [[Common]] notes, page-less quotes (e.g. from a physical book or a video timestamp). Survives re-import. -->

{% endpersist %}

## Annotations
{% persist "annotations" %}
{% set newAnnotations = annotations | filterby("date", "dateafter", lastImportDate) %}
{% if newAnnotations.length > 0 %}
### Imported {{importDate | format("YYYY-MM-DD")}}
{% for a in newAnnotations %}
{% if a.annotatedText %}> {{a.annotatedText}}{% endif %}
{% if a.imageRelativePath %}![[{{a.imageRelativePath}}]]{% endif %}
{% if a.comment %}
{{a.comment}}
{% endif %}
— [p. {{a.pageLabel}}](zotero://open-pdf/library/items/{{a.attachment.itemKey}}?page={{a.pageLabel}}&annotation={{a.id}})

{% endfor %}
{% endif %}
{% endpersist %}
