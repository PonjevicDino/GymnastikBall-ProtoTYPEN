---
title: Formatting
weight: 10
---

# How to format?

[Hugo](https://gohugo.io/) uses **Markdown** to format *text*. Find out more on how to format [Markdown files](https://www.markdownguide.org/basic-syntax/).
The raw markdown file of this page can be seen [here](https://raw.githubusercontent.com/pretoms/dpp_docs/main/content/docs/projects/our_project/subpage_1/_index.md).

The hugo book theme enables the use of [Mermaid](https://mermaid.js.org/intro/) diagrams ... 

{{< mermaid >}}
stateDiagram-v2
    idea --> prototype: 🛠
{{< /mermaid >}}

... and [KaTeX](https://katex.org/) math typesetting.

{{< katex >}}
e^{\pi i} + 1 = 0
{{< /katex >}}

```python
print("Format code in your Markdown file.")
```

Try to always add a descriptive caption to an included image. Also make sure images are not unnecessarily large.
{{< figure src="bears.gif" caption="Two care bears putting on their seat belts.">}}

You can also embed videos. For more details see the [hugo-video](https://github.com/martignoni/hugo-video) theme component.

More details on how to use the Hugo book theme can be found [here](https://github.com/alex-shpak/hugo-book).

{{< video src="test.mp4" autoplay="true" loop="true" muted="true" >}}

You can also create:
* unordered lists
* and tables:

| Milestone   | Date      |
| :----       | ----:     |
| ~~Idea~~        | ~~1.1.1979~~  |
| New idea        | 2.1.1979 |
| Prototype   | 3.1.1979  |

Also task lists can be formatted:
- [x] Prototype
- [ ] Documentation
