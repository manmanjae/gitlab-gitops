# 구현 범위와 후속 과제

## 코드로 확인할 수 있는 설계

| 항목 | 현재 선언과 의미 |
|---|---|
| 변경 조건 | 앱 변경 시 build/update, GitOps 변경 시 렌더링 검사. UI 수동 실행은 validate 중심 |
| Sonar 조건 | default branch push에서 관련 경로 변경 시 실행. 실패 허용이므로 강제 gate가 아님 |
| 검증 수준 | `kubectl kustomize`는 렌더링 검사. cluster schema·정책·기능 테스트를 보장하지 않음 |
| 이미지 추적 | pipeline IID로 `imageN` tag 생성. SHA tag나 digest 고정 방식은 아님 |
| 자동 commit | `deploy api-tester imageN` 제목의 push pipeline 생성 제외 |
| HPA | 최소 2·최대 5, CPU request 대비 60%. Argo CD replicas 비교·sync 제외 |
| PDB | 자발적 중단에 최소 1개 유지 선언. 비자발적 노드 장애 HA 보장이 아님 |
| 모니터링 prune | kube-prometheus-stack child는 prune false. root Application 자체에는 prune true |
| 로깅 | Kubernetes API Pod 로그 수집·라벨링→Loki gateway. 보관 168h 선언 |

## 미완료 또는 ZIP만으로 검증할 수 없는 부분

1. **Gateway 전환**: base의 `ingress.yaml`은 base kustomization에 포함되지 않습니다. 현재 dev 렌더링은 HTTPRoute를 사용합니다. 별도 파일 존재를 Ingress/Gateway 동시 배포로 해석하지 않습니다.
2. **Harbor TLS 경로**: Gateway listener는 있지만 Harbor HTTPRoute·ReferenceGrant는 이 사본에 없습니다. 해당 선언이 외부에 있는지 또는 미완료인지 확인이 필요합니다.
3. **Sonar 경로**: Sonar Gateway 선언과 scanner `--add-host`는 있으나 Sonar용 HTTPRoute는 포함되지 않습니다.
4. **실제 실행 증거**: ZIP에는 pipeline 로그·Grafana 화면·장애 전후 자료가 없습니다. 과거 실습 경험과 이번 정적 검사를 구분합니다.
5. **기능 검증**: 정적 Tomcat 화면이며 자동 기능 테스트·취약점 검출·점진 배포·자동 롤백은 확인하지 않았습니다.
6. **가용성과 운영**: 단일 Control Plane·Loki LocalPV/단일 replica, 알림 수신·SLO·백업 복구 검증은 후속 과제입니다.

## 별도 개선 commit 후보

- 원본 실험의 HTTP Git push와 Docker 인증 잔존 처리 개선, HTTPS·CA 구성과 최소 권한 token 사용
- pipeline 동시 실행 시 overlay commit 충돌 처리와 branch/rules 보호, runner 격리
- Helm dependency lock과 lint/template 검사, API schema·정책 검증 추가
- 테스트 모드와 강제 Quality Gate 모드 구분 및 실제 분석 성공 확인
- 누락된 Gateway 참조·Route 구성 확인, TLS 및 registry push/pull 재검증
- 메트릭·로그 조회 화면의 비식별 실행 증거, 장애 기록과 재발 방지 검증 추가

위 항목은 공개용 치환 과정에서 완료한 기능이 아닙니다.
