# 정적 검증 기록

검증일: 2026-10-28. 공개용 치환 후 로컬 파일을 검사했습니다. 원본 ZIP은 변경하지 않았습니다.

| 검사 | 결과 |
|---|---|
| App of Apps Kustomize 렌더링 | 통과, Application 5개 |
| 앱 dev overlay Kustomize 렌더링 | 통과, 리소스 7개 |
| Gateway 설정 Kustomize 렌더링 | 통과, Gateway 2개 |
| Tomcat server.xml XML 파싱 | 통과 |
| 치환 전 실습 도메인·조직 식별자·사설 IPv4 잔존 패턴 | 검출 없음 |
| 개인키·일부 token 형식·credential 대입 패턴 | 검출 없음 |
| 이미지 태그·HPA·PDB·Sonar 실패 허용 선언 유지 | 확인 |

패턴 검사는 모든 종류의 비밀을 탐지한다는 보장이 아닙니다. ZIP 밖의 Git 이력·CI 변수·runner credential·이미지 레이어·실행 로그는 검사하지 않았습니다.

## 이번에 실행하지 않은 검사

- GitLab CI Lint와 실제 pipeline 실행
- Helm dependency 다운로드·lock 생성·전체 chart 렌더링/호환성 검사
- Docker 이미지 빌드·Harbor push/pull
- 클러스터 적용, Gateway status·TLS·서비스 기능 통합 테스트
- 메트릭·로그 수집·알림 수신·부하·drain·복구 시험

Kustomize 렌더링 성공은 Kubernetes API schema, 외부 CRD, 설치된 chart 버전 또는 실제 동작의 성공을 의미하지 않습니다. 이 사본의 런타임 결과와 원본 환경의 과거 실습을 혼동하지 않습니다.
