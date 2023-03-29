---
title: 'Reducing Jekyll build times'
tags:
 - jekyll
 - development
---

This post contains information about reducing the build times for my personal Jekyll GitHub website.
<!--more-->

## Starting build

Using Docker with the command:
``docker run --rm --volume="$($PWD):/srv/jekyll" -it nexus.dev-space.duckdns.org:5000/jekyll/jekyll:4.2.2 jekyll build --profile``

```shell
Filename                                             | Count |    Bytes |   Time
-----------------------------------------------------+-------+----------+-------
_layouts/default.html                                |    93 | 1041.55K | 96.557
_layouts/tagpage.html                                |    16 |   45.59K | 11.987
_layouts/language.html                               |    10 |   33.50K |  1.881
_languages/languages.html                            |     1 |    4.50K |  1.759
_layouts/page_with_tags.html                         |    54 |  280.13K |  1.544
_tools/tools.html                                    |     1 |    8.96K |  0.700
_posts/2022-06-04-Raspberry-Pi-custom-photo-frame.md |     1 |    3.80K |  0.359
_operating_systems/operating_systems.html            |     1 |    2.69K |  0.333
_languages/javascript/tools.md                       |     1 |    1.01K |  0.312
_languages/html.md                                   |     1 |    0.31K |  0.239
_tag/project.md                                      |     1 |    2.73K |  0.133
_includes/navigation.html                            |    93 |   61.32K |  0.108
_includes/head-custom.html                           |    93 |  100.90K |  0.103
_includes/toc.html                                   |    25 |   11.91K |  0.100
tags.html                                            |     1 |    3.71K |  0.032
_includes/page-tags.html                             |    54 |   16.12K |  0.021
_languages/javascript/syntax.md                      |     1 |   69.70K |  0.015
_languages/javascript/syntax.md/#excerpt             |     1 |   69.70K |  0.015
_tools/tools.html/#excerpt                           |     1 |    8.96K |  0.006
_languages/typescript/syntax.md                      |     1 |   14.14K |  0.005
_languages/typescript/syntax.md/#excerpt             |     1 |   14.14K |  0.004
_languages/javascript/arrays.md                      |     1 |   10.34K |  0.004
_languages/languages.html/#excerpt                   |     1 |    4.50K |  0.003
_languages/javascript/arrays.md/#excerpt             |     1 |   10.34K |  0.003
_languages/sql/sql_lobs.md                           |     1 |    5.86K |  0.002
_languages/javascript/node_js.md                     |     1 |    9.08K |  0.002
_tag/project.md/#excerpt                             |     1 |    2.73K |  0.002
_includes/site-tags.html                             |     1 |    1.99K |  0.002
_languages/javascript/node_js.md/#excerpt            |     1 |    9.08K |  0.002
_tools/aws_cli.md                                    |     1 |    9.32K |  0.002
_languages/javascript/libraries.md/#excerpt          |     1 |    2.23K |  0.001
_languages/sql/sql_lobs.md/#excerpt                  |     1 |    5.86K |  0.001
_languages/sql/sql_basics.md                         |     1 |    5.63K |  0.001
_languages/sql/sql_basics.md/#excerpt                |     1 |    5.63K |  0.001
_layouts/mainPage.html                               |     6 |   23.35K |  0.001
posts.html                                           |     1 |    1.02K |  0.001
_languages/java/javafx.md/#excerpt                   |     1 |    2.57K |  0.001
_languages/javascript/ui_components.md               |     1 |    3.07K |  0.001
_languages/javascript/classes.md                     |     1 |    3.64K |  0.001
_languages/javascript/ui_components.md/#excerpt      |     1 |    3.07K |  0.001
_includes/blog-tags.html                             |     1 |    1.51K |  0.001
_languages/javascript/classes.md/#excerpt            |     1 |    3.64K |  0.001
_tools/serverless.md                                 |     1 |    3.14K |  0.001
_operating_systems/operating_systems.html/#excerpt   |     1 |    2.69K |  0.001
_languages/java/concurrency.md/#excerpt              |     1 |    1.99K |  0.001
_operating_systems/debian.md/#excerpt                |     1 |    0.21K |  0.001
_operating_systems/ubuntu.md/#excerpt                |     1 |    0.37K |  0.001
_languages/javascript/libraries.md                   |     1 |    2.23K |  0.001
_languages/cmd.md                                    |     1 |    1.15K |  0.001
_tools/jekyll.md                                     |     1 |    1.27K |  0.001


                    done in 217.358 seconds.
```
done in 248.538 seconds

## Updating github-pages gem

No significant change.
<details>
<summary>About the same @ 239.907 seconds</summary>

```shell
Filename                                             | Count |    Bytes |    Time
-----------------------------------------------------+-------+----------+--------
_layouts/default.html                                |    93 | 1043.92K | 108.824
_layouts/tagpage.html                                |    16 |   45.68K |  12.691
_layouts/language.html                               |    11 |   35.31K |   1.992
_languages/languages.html                            |     1 |    5.00K |   1.830
_layouts/page_with_tags.html                         |    53 |  279.54K |   1.727
_tools/tools.html                                    |     1 |    8.59K |   0.610
_operating_systems/operating_systems.html            |     1 |    2.69K |   0.381
_posts/2022-06-04-Raspberry-Pi-custom-photo-frame.md |     1 |    3.80K |   0.359
tags.html                                            |     1 |    3.71K |   0.294
_languages/javascript/tools.md                       |     1 |    1.01K |   0.242
_tag/project.md                                      |     1 |    2.73K |   0.205
_languages/html.md                                   |     1 |    0.31K |   0.161
_includes/navigation.html                            |    93 |   61.32K |   0.110
_includes/toc.html                                   |    25 |   11.91K |   0.110
_includes/head-custom.html                           |    93 |  100.90K |   0.105
_includes/page-tags.html                             |    53 |   16.07K |   0.022
_languages/javascript/syntax.md/#excerpt             |     1 |   69.70K |   0.015
_languages/javascript/syntax.md                      |     1 |   69.70K |   0.014
_languages/typescript/syntax.md/#excerpt             |     1 |   14.14K |   0.005
_languages/typescript/syntax.md                      |     1 |   14.14K |   0.005
_languages/languages.html/#excerpt                   |     1 |    5.00K |   0.005
_languages/javascript/arrays.md                      |     1 |   10.34K |   0.004
_tools/aws_cli.md                                    |     1 |    9.32K |   0.003
_tools/tools.html/#excerpt                           |     1 |    8.59K |   0.003
_languages/javascript/arrays.md/#excerpt             |     1 |   10.34K |   0.002
posts.html                                           |     1 |    1.02K |   0.002
_languages/javascript/node_js.md/#excerpt            |     1 |    9.08K |   0.002
_includes/site-tags.html                             |     1 |    1.99K |   0.002
_languages/javascript/node_js.md                     |     1 |    9.08K |   0.002
_tag/project.md/#excerpt                             |     1 |    2.73K |   0.002
_languages/sql/sql_basics.md/#excerpt                |     1 |    5.63K |   0.001
_languages/sql/sql_basics.md                         |     1 |    5.63K |   0.001
_languages/sql/sql_lobs.md/#excerpt                  |     1 |    5.86K |   0.001
_languages/sql/sql_lobs.md                           |     1 |    5.86K |   0.001
_languages/javascript/classes.md/#excerpt            |     1 |    3.64K |   0.001
_languages/javascript/classes.md                     |     1 |    3.64K |   0.001
_layouts/mainPage.html                               |     6 |   23.48K |   0.001
_tools/serverless.md                                 |     1 |    3.14K |   0.001
_languages/javascript/ui_components.md               |     1 |    3.07K |   0.001
_operating_systems/operating_systems.html/#excerpt   |     1 |    2.69K |   0.001
_includes/blog-tags.html                             |     1 |    1.51K |   0.001
_languages/javascript/libraries.md/#excerpt          |     1 |    2.23K |   0.001
_languages/javascript/libraries.md                   |     1 |    2.23K |   0.001
_languages/javascript/ui_components.md/#excerpt      |     1 |    3.07K |   0.001
_languages/java/jpa.md/#excerpt                      |     1 |    1.65K |   0.001
_operating_systems/debian.md/#excerpt                |     1 |    0.21K |   0.001
_operating_systems/ubuntu.md/#excerpt                |     1 |    0.37K |   0.001
_languages/javascript/imports.md/#excerpt            |     1 |    1.04K |   0.001
_languages/cmd.md                                    |     1 |    1.15K |   0.001
_languages/java/javafx.md/#excerpt                   |     1 |    2.57K |   0.001


                    done in 239.907 seconds.
```
</details>








