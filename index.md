---
layout: default
title: Home
nav_order: 0
description: "CloudMsg/SendTalk 멀티 프로바이더 설계 + 관리자 화면 설계 + LLM 활용 방법론"
---

# 과제 정리: 멀티 메시지 업체 전환 설계 + 운영자 설정 UI

이 문서는 기존 **CloudMsg 단일 발송 구조**를 **조직(Organization)별 CloudMsg/SendTalk 선택 구조**로 확장하기 위한 설계를 정리한 결과물입니다.  
추가로, 서비스 운용자가 조직별 업체를 변경할 수 있는 **관리자 화면 설계**와, 기업 요구사항에 맞춘 **LLM 활용 방법론(프롬프트 의도/결과/의사결정 근거)**를 함께 기록합니다.

## **이 문서에서 다루는 것**

- **설계 전제(가정) 정의**: 애매한 요구사항을 “확정/가정”으로 분리하고 기준선을 고정
- **API 차이의 설계 영향**: 인증/엔드포인트/포맷 차이를 Provider 추상화로 흡수
- **서버 작업 계획**: 데이터 모델 → Provider → Webhook → 관리 API → 테스트/배포 순서
- **운영자 화면 설계**: 실수 방지(연결 테스트, 마스킹, 확인 모달, 변경 이력, 충돌 방지)

## **빠른 이동**

- 가장 먼저 읽을 페이지(추천): **[LLM 활용 방법론](./llm-collaboration)**
- 설계 개요를 빠르게 파악: **[문제 분석](./problem-analysis)**
- 구현 관점 핵심: **[서버 설계 및 작업 계획](./server-design)**
- 운영 관점 핵심: **[관리자 화면 설계](./admin-ui-design)**
- 샘플 관리자 화면: <a href="{{ site.baseurl }}/sample-ui.html" target="_blank" rel="noopener noreferrer">
  샘플 UI 보기 (HTML)
</a>

## **전체 목차**

1. **[LLM 활용 방법론](./llm-collaboration)**  
2. **[문제 분석](./problem-analysis)**  
3. **[API 비교 분석](./api-comparison)**  
4. **[가정 세트](./assumptions)**  
5. **[서버 설계 및 작업 계획](./server-design)**  
6. **[관리자 화면 설계](./admin-ui-design)**  
7. **[부록](./appendix)**

## **접근 방식 요약**

핵심 전략은 3가지입니다.

첫째, **가정부터 고정**합니다. 업체 선택 단위/운영 권한/Webhook 처리처럼 설계를 좌우하는 전제를 먼저 명시합니다.  
둘째, **Provider 추상화로 의존성을 분리**합니다. 발송 서비스는 공통 인터페이스만 알고, 업체별 차이는 구현체로 격리합니다.  
셋째, **운영 안전장치**를 설계에 포함합니다. 연결 테스트, 마스킹, 변경 이력, 낙관적 락 등으로 운영 실수를 줄입니다.
