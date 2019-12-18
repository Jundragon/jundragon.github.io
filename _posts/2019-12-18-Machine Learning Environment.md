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

## Linux

`venv` 가상환경 생성

```bash
python3 -m virtualenv venv
```

`venv` 가상환경 활성화

```bash
source venv/bin/activate
```

`requirement.txt` 프로젝트의 라이브러리 설치

```bash
pip install -r requirements.txt
```

현재 프로젝트 라이브러리 `requirements.txt` 저장

```bash
pip freeze > requirements.txt
```

## Windows 10

Anaconda Prompt를 관리자 권한으로 실행

`venv` 가상환경 생성

```powershell
conda create -n venv python=3
```

`venv` 가상환경 활성화

```powershell
conda activate venv
pip install --upgrade pip
```

## Docker
