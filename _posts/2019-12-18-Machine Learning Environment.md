---
title: "Machine Learning Environment"
last_modified_at: 2019-12-18T21:00:00+09:00
categories:
  - environment
tags:
  - ML
toc: true
toc_sticky: true
toc_label: "shortcut"
---

머신 러닝을 위한 셋팅

## bash

`venv` 가상환경 생성

```bash
conda create -n venv python=3
```

`venv` 가상환경 활성화

```bash
conda activate venv
pip install --upgrade pip
```

`requirement.txt` 프로젝트의 라이브러리 설치

```bash
pip install -r requirements.txt
```

현재 프로젝트 라이브러리 `requirements.txt` 저장

```bash
pip freeze > requirements.txt
```

## Docker
