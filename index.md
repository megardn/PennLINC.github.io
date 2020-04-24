---
layout: default
title: Home
nav_order: 1
description: "Information and tutorials on how to go from DICOMS to dataframes."
permalink: /
---
# NeuroInformatics Tutorials and Information
{: .fs-9 }


{: .fs-6 .fw-300 }

[Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/PennLINC/PennLINC.github.io){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## Getting started

This site documents knowledge and practices in informatics, engineering, and systems administration for members of LINC. If you need to know:

- How to get a new Flywheel account
- How to transfer DICOM files from place to place
- How to use `QSIPrep` or `XCPEngine`
- How to set python environment management
- Who to turn to for additional help

This site will help you navigate informatics at LINC.

This effort's success is dependent on your involvement as lab members and a community, so if you have edits to suggest, or documentation requests, please make them known by submitting an [issue](https://github.com/PennLINC/PennLINC.github.io/issues) or a [pull request](https://github.com/PennLINC/PennLINC.github.io/pulls). If you are a LINC member and are documenting one of your tools/processes, please create your documentation according to the [LINC documentation guidelines](docs/Contributing/contributing) and submit a [Pull Request](https://github.com/PennLINC/PennLINC.github.io/pulls), so it can be reviewed and added to the site.

---

This documentation website was created using [Jekyll and Github pages](https://help.github.com/en/github/working-with-github-pages/setting-up-a-github-pages-site-with-jekyll); the template is [`just-the-docs`](https://pmarsceill.github.io/just-the-docs/).

#### Thank you to the contributors for LINC tutorials!

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"/></a>
  </li>
{% endfor %}
</ul>
