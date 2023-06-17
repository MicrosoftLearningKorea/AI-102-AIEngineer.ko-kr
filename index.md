---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

# AI 엔지니어 연습

이러한 실습 랩 연습에서는 Microsoft 과정 [AI-102 Microsoft Azure AI 솔루션 디자인 및 구현과 Microsoft](https://docs.microsoft.com/learn/certifications/courses/ai-102t00) Learn의 동등한 [자기 주도적 모듈을 지원합니다](https://aka.ms/AzureLearn_AIEngineer). 연습은 학습 자료와 함께 진행할 수 있으며, 설명되어 있는 기술을 사용하여 연습할 수 있게 도와줍니다.

이러한 연습을 완료하려면 Microsoft Azure 구독이 필요합니다. [https://azure.microsoft.com](https://azure.microsoft.com)에서 무료 평가판에 등록할 수 있습니다.

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| 연습 |
| --- |
{% for activity in labs  %}| [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

