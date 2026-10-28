# 설치 전제와 예시 값

## GitHub 열람과 GitLab 실행 구분

이 저장소를 GitHub에 올려도 `.gitlab-ci.yml`은 GitHub Actions로 실행되지 않습니다. 현재 코드는 GitLab CI가 같은 GitLab 저장소의 overlay를 갱신하고 Argo CD도 그 저장소를 읽는 흐름입니다.

재현하려면 별도 GitLab 프로젝트에 가져온 뒤 root·child Application의 `repoURL`과 CI의 Git push 대상을 **같은 GitLab 저장소**로 맞추세요. GitHub를 GitOps 원본으로 사용하는 변경은 별도의 설계·구현·검증 작업입니다.

## 외부 준비 항목

- Kubernetes, GitLab 및 `onprem` tag의 Shell Runner와 Docker 접근 환경
- runner에 Git·Docker·kubectl, 필요 시 Ruby. Ruby가 없으면 CI는 보조 YAML 검사를 생략합니다.
- Harbor 프로젝트, SonarQube, Argo CD와 Git 접근 인증
- NGINX Gateway Fabric 및 이 선언을 처리할 Gateway API·ProxySettingsPolicy CRD, GatewayClass `nginx`
- MetalLB의 `default-pool`과 실제 LoadBalancer IP, 클라이언트/노드/runner의 DNS와 CA 신뢰
- `openebs-hostpath` StorageClass와 Metrics Server
- 각 namespace의 imagePullSecret·Grafana Secret·TLS Secret 등 참조 리소스

GitLab·Harbor·SonarQube·Argo CD 자체 설치 manifest 및 전체 bootstrap 스크립트는 제공하지 않습니다. 설치된 제품과 chart/CRD의 호환성도 재현 환경에서 확인해야 합니다.

## 반드시 교체할 예시

| 예시 | 의미 |
|---|---|
| `gitlab.example.test/portfolio/gitlab-gitops.git` | CI 쓰기와 Argo CD 읽기 대상 GitLab 저장소 |
| `harbor.example.test/dev/api-tester` | 이미지 저장소. CI 변수의 registry/project와 동일하게 맞춤 |
| `api-tester-dev.example.test` | 앱 HTTPRoute host |
| `grafana.example.test` | Grafana HTTPRoute host |
| `sonarqube.example.test` | scanner hosts 및 Sonar 접속 주소 |
| `192.0.2.10`, `192.0.2.11` | 문서용 주소. 실제 실습망에 할당 가능한 IP로 교체 |
| `onprem_gitops_observability_lab` | Sonar 프로젝트 key |

`example.test`와 문서용 IP는 공개용 치환값입니다. DNS/hosts 설정은 코드 공개 준비에서 변경하지 않았습니다.

## GitLab CI 변수

| 변수 | 용도 | 관리 |
|---|---|---|
| `HARBOR_REGISTRY` | registry host | 환경 설정 |
| `HARBOR_PROJECT` | Harbor project | overlay의 이미지 경로와 일치 |
| `HARBOR_USERNAME` | push 계정 | 최소 권한 계정 |
| `HARBOR_PASSWORD` | registry credential | 민감 변수 |
| `SONAR_HOST_URL` | Sonar endpoint | hosts 설정·CA와 일치 |
| `SONAR_TOKEN` | 분석 credential | 민감 변수 |
| `GITOPS_TOKEN` | GitOps commit push | repository 쓰기 최소 권한, 민감 변수 |

실제 값은 문서·YAML에 쓰지 않습니다. 기본 branch 보호와 token push 허용 조건도 GitLab에서 별도로 구성합니다.

## 코드에서 참조하는 Secret

- `app-dev/harbor-regcred`: Pod 이미지 pull 인증
- `monitoring/kube-prometheus-stack-grafana`: `admin-user`, `admin-password` key
- `harbor/harbor-tls`: Gateway TLS certificate reference
- Argo CD의 repository 접근 credential은 Argo CD에서 별도로 등록

TLS Secret 참조는 Gateway와 namespace가 달라 `ReferenceGrant`가 필요합니다. 이 ZIP에는 해당 선언이 없습니다. Secret을 같은 namespace에 둘지 허용 참조를 만들지는 실제 구성에 맞게 결정해야 합니다. [Gateway API TLS 문서](https://gateway-api.sigs.k8s.io/guides/user-guides/tls/)

## 로컬 정적 검증 명령

```sh
kubectl kustomize k8s-gitops/applications
kubectl kustomize k8s-gitops/apps/api-tester/overlays/dev
kubectl kustomize k8s-gitops/addons/nginx-gateway
```

위 명령은 렌더링 검사이며 cluster에 적용하지 않습니다. Helm wrapper는 의존 chart를 내려받아 렌더링해야 전체 출력 검증이 가능합니다. root Application을 적용하면 child Applications의 자동 동기화와 일부 prune이 동작하므로 필요한 인증·저장소·CRD와 환경 격리를 먼저 준비해야 합니다.
