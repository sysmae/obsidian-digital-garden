---
publish: true
---

'원웨이 도어'에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘

# 원웨이 도어(One-Way Door) 의사결정 프레임워크 핸드북

## 핵심 요약

**원웨이 도어 결정**은 한 내리면 되돌리기 어렵거나 비용·시간 소모가 큰 중대한 결정으로, 신중한 분석과 협의를 거쳐야 한다. 반면 **투웨이 도어 결정**은 잘못되더라도 손쉽게 되돌릴 수 있어 빠르게 실행하고 학습하며 조정하는 데 집중한다[^1].

## 1. 탄생 배경

1997년 제프 베이조스는 “어떤 결정은 되돌릴 수 없을 만큼 중대하고, 또 되돌리기 힘들다”는 관점에서 의사결정을 구분했다. 2015년 주주서한에서는 이를 “원웨이 도어(One-Way Door)”와 “투웨이 도어(Two-Way Door)”로 정의하며, 조직 내 의사결정 속도와 효율성 제고를 강조했다[^1].

## 2. 프레임워크 구조

| 구분 | 원웨이 도어 결정 | 투웨이 도어 결정 |
| :-- | :-- | :-- |
| 정의 | 되돌리기 거의 불가능, 대규모 자원 투입 필요 | 쉽게 되돌릴 수 있어 실패 부담이 낮음 |
| 예시 | 장기 계약 체결, 대규모 인수·합병, AWS 인프라 구축[^2] | A/B 테스트, 새로운 서비스 시범 운영, 마케팅 채널 실험 |
| 의사결정 속도 | 느림: 충분한 정보 수집·전문가 검토 필요 | 빠름: 70% 정보 확보 시 즉시 실행 권장[^2] |
| 책임 주체 | 고위 경영진, 관련 전문가 그룹 | 소규모 팀 혹은 개별 담당자 |

## 3. 사용 방법

### 1) 결정 유형 식별

- **되돌릴 수 있는가?**
- **되돌리기 위한 비용·시간은 얼마인가?**
- **실패 시 조직 전체에 미치는 영향은?**


### 2) 접근 방식 조정

- **원웨이 도어 결정 시**
    - 충분한 데이터·시나리오 분석
    - 다양한 이해관계자와 심층 토의
    - 리스크 관리 도구(비용·편익 분석, 의사결정 트리) 활용
- **투웨이 도어 결정 시**
    - 70% 정보 확보 후 즉시 실행
    - 작은 단위 실험으로 빠른 피드백 수집
    - 실패 시 즉각 롤백 가능한 절차 마련


### 3) 조직 내 확산

- 결정 유형별 **권한 위임** 가이드라인 수립
- 의사결정 체크리스트와 **사전 분류 기준** 배포
- 시행 후 결과 모니터링 및 **학습 기록** 공유


## 4. 도입 효과

1. **의사결정 속도 향상**
불필요한 분석회의 축소, 투웨이 도어 결정의 즉시 실행으로 민첩성 강화[^2].
2. **자원 최적화**
중대한 결정에만 집중, 반복 가능 결정에는 최소한의 시간·인력 투입.
3. **의사결정 마비 해소**
위험과 리스크를 명확히 구분해 분석 마비 최소화.
4. **권한 부여 및 조직 문화 개선**
소규모 팀과 개인의 주도적 실행 촉진, 책임감과 실행력 강화.
5. **실패 학습 가속화**
투웨이 도어 결정 실패 시 빠른 학습과 재실행을 통한 지속 개선[^3].

## 5. 실제 적용 사례

- **Amazon Web Services 구축**: 대규모 자본·인프라 투입으로 원웨이 도어 결정[^1].
- **A/B 테스트**: 웹사이트 기능 비교 실험, 결과 불만족 시 즉시 롤백 가능한 투웨이 도어 결정[^2].
- **오피스 장기 임대 계약**: 계약 해지 어려움으로 원웨이 도어 결정.
- **신규 마케팅 채널 시범 운영**: 실패 시 쉽게 중단·재조정 가능한 투웨이 도어 결정.


## 6. 실행 팁

- “이 결정은 1-Way인가 2-Way인가?”라는 질문을 **습관화**할 것.
- 투웨이 도어 결정은 **70% 정보** 확보 시 실행, 90% 대기 금지[^2].
- 원웨이 도어 결정은 **다른 분석 방법**이 떠오르면 추가 검토 진행.
- 모든 결정 후 **회고 및 문서화**로 조직 학습 사이클 완성.

**핵심 권장 사항**: 조직의 일상 의사결정에 본 프레임워크를 내재화하여 중대한 결정에는 철저한 분석과 협의를, 반복 가능한 실험에는 신속한 실행과 학습을 동시에 추구하라.

<div style="text-align: center">⁂</div>

[^1]: https://www.cubthinktank.com/posts/article-two-door

[^2]: https://aws.amazon.com/executive-insights/content/how-amazon-defines-and-operationalizes-a-day-1-culture/

[^3]: https://thoughtbot.com/blog/one-way-vs-two-way-door-decisions

[^4]: https://shit.management/one-way-and-two-way-door-decisions/

[^5]: https://www.linkedin.com/pulse/power-decision-making-one-way-vs-two-way-doors-amazon-venkatesan-8kdkc

[^6]: https://www.linkedin.com/posts/jasonyoong_jeff-bezos-explains-the-difference-between-activity-7244164615729733632-tDdM

[^7]: https://brunch.co.kr/@sacony/136

[^8]: https://www.ideatovalue.com/inno/nickskillicorn/2024/08/one-way-door-decisions-or-two-way-door-decisions/

[^9]: https://www.linkedin.com/pulse/jeff-bezos-decision-making-rules-thomas-chua-qhqyc

[^10]: https://www.jameswarrick.com/one-way-door-decisions/

[^11]: https://sebastianhallqvist.substack.com/p/one-way-doors-two-way-doors

[^12]: https://www.scarletink.com/p/from-one-way-to-two-way-doors-rethinking

[^13]: https://www.inc.com/jeff-haden/amazon-founder-jeff-bezos-this-is-how-successful-people-make-such-smart-decisions.html

[^14]: https://blueprints.guide/posts/one-way-vs-two-way-doors

[^15]: https://www.youtube.com/watch?v=rxsdOQa_QkM

[^16]: https://www.reddit.com/r/coolguides/comments/18t1a92/a_cool_guide_to_jeff_bezoss_decisionmaking_model/

