# CLAUDE.md

이 파일은 `baby-diet` 프로젝트 전용 Claude Code 가이드입니다.
상위 폴더의 CLAUDE.md와 별개로, 이 프로젝트에만 적용됩니다.

## 프로젝트 개요

영유아(0~36개월)를 대상으로 한 식단 기록 및 추천 대시보드입니다.
부모가 아기의 일별 식단을 캘린더 형식으로 기록하고, 좋아하는 재료와 아직 시도하지 않은 재료를 기반으로 식단을 추천받을 수 있습니다.

## 서비스 대상 및 목적

- **대상**: 영유아를 양육 중인 양육자
- **목적**: 영유아의 균형 있는 영양을 위한 식단을 제공하고 제안하여, 궁극적으로 **아기에게는 건강함**을, **주양육자에게는 편리함**을 제공한다.

## 핵심 원칙

이 프로젝트는 영유아를 대상으로 한 식단표입니다.
참고하는 정보는 **WHO(세계보건기구)** 나 **국가 공식 문서** 등 공신력 있는 기관의 자료만을 기준으로 해야 합니다.

- 이유식 시작 시기, 재료 도입 순서, 영양 기준 등 모든 의학·영양 관련 내용은 반드시 WHO 또는 대한민국 식품의약품안전처, 보건복지부, 소아과학회 등 공식 기관 자료에 근거해야 합니다.
- 검증되지 않은 정보나 임의적인 추정을 식단 추천 로직에 포함하지 않습니다.
- 알레르기 관련 기능은 특히 신중하게 다루며, 의학적 진단을 대체하지 않음을 명시합니다.

## 참고 문서 기준

영양·의학 관련 내용을 구현하거나 수정할 때는 반드시 **각 기관이 발행한 가장 최신 버전**의 공식 문서를 기준으로 합니다.
구버전 지침이 최신 개정판으로 변경된 경우, 최신 개정판을 우선 적용합니다.

| 기관 | 문서명 | 현재 적용 버전 |
|------|--------|--------------|
| 보건복지부 | 한국인 영양소 섭취기준 (KDRIs) | 2020년 개정판 (최신) |
| WHO | Complementary Feeding of Young Children | 2023 Updated Guidelines |
| WHO | Infant and Young Child Feeding (IYCF) | 최신 버전 우선 |
| 농촌진흥청 | 국가표준식품성분표 | 제10개정판 (최신) |
| 대한소아과학회 | 소아청소년 표준성장도표 | 2017년판 (최신) |
| 식품의약품안전처 | 영유아 이유식 가이드라인 | 최신 고시 기준 |

> ⚠️ 새로운 기준이 발표될 경우 위 표를 업데이트하고, 코드 내 권장 수치도 함께 개정합니다.

## 기술 스택

- 단일 HTML 파일 (React 18 CDN + Babel Standalone + Tailwind CSS CDN)
- 데이터 저장: 브라우저 localStorage
- 별도 서버/빌드 불필요 — 파일을 브라우저에서 직접 열어 사용

## 실행 방법

```
# 브라우저에서 직접 열기
open baby-diet/index.html
```

## 아키텍처

```
index.html
├── Header          — 아기 정보 표시 및 탭 네비게이션
├── CalendarView    — 월별 캘린더, 날짜별 식사 현황 표시
├── DayPanel        — 선택된 날짜의 식사 목록 및 추가/수정
├── MealFormModal   — 식사 기록 입력 모달 (종류/시간/재료/제조량/섭취량)
├── NutritionView   — 일간·월간 탄단지 영양 분석 및 권장 비율 비교
├── RecommendView   — 식단 추천 (좋아하는 재료 + 미시도 재료 기반)
├── IngredientsView — 먹어본 전체 재료 목록 및 통계
└── SettingsView    — 아기 정보 및 알레르기 재료 관리
```

## 데이터 구조

```js
// localStorage 키
babyDiet_meals      // { 'YYYY-MM-DD': [meal, ...] }
babyDiet_liked      // string[]  — 좋아하는 재료명 목록
babyDiet_allergy    // string[]  — 알레르기/주의 재료명 목록
babyDiet_name       // string    — 아기 이름(별명)
babyDiet_birth      // string    — 생년월일 'YYYY-MM-DD'

// meal 객체
{
  id: string,
  type: '아침' | '점심' | '저녁' | '간식',
  time: string,          // 'HH:MM'
  liked: boolean,
  memo: string,
  ingredients: [
    {
      id: string,
      name: string,
      madeGrams: number,   // 제조량 (g)
      eatenGrams: number,  // 섭취량 (g) — 영양 계산 기준
    }
  ]
}
```
