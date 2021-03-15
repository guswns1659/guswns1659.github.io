---
title: "Pandoc) Wiki"
header:
  teaser: /assets/linux.jpg
  overlay_image: /assets/linux.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Tool
---

# pandoc
markdown 파일을 다른 파일 형식으로 바꿔주는 프로그램이다. 터미널 기반 프로그램으로 쉘스크립트를 작성하면 여러 파일을 동시에 바꿀 수 있다. [공식 홈페이지](https://pandoc.org/getting-started.html)에 가면 튜로리얼을 할 수 있다. 마크다운 파일을 구글 독스로 바로 변환할 수 있는 방법을 찾았는데 없었다. 그래서 pandoc으로 docx로 바꾸고 구글독스에 업로드하는 과정으로 변경했다.

## markdown을 docx로 바꾸는 명령어

```java
pandoc test.md -f markdown -t docx -o test.docx
```
