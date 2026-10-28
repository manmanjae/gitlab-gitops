# 민감정보와 실습 환경

이 사본은 코드 열람용 예시입니다. 예시 도메인과 문서용 IP로는 실제 서비스에 연결되지 않습니다.

- 실제 CI token·Harbor password·Sonar token·Grafana password·kubeconfig·인증서 개인키를 commit하지 마세요.
- GitLab CI 변수는 GitLab 설정에서 주입합니다. 사용 브랜치와 runner 접근 범위를 제한하고 masked/protected 적용을 검토하세요. masking만으로 악성 CI 코드로부터 변수를 보호할 수는 없습니다.
- 원본에는 GitOps push URL에 token 변수를 포함하는 HTTP 방식과 scanner의 고정 hosts 설정이 있습니다. 공개 사본은 이 실험 구조를 남겼습니다. 실제 재현 전 HTTPS·신뢰 가능한 CA·자격증명 처리와 최소 권한을 검토하세요. 실제 token을 URL 예시에 써 넣지 마세요.
- Shell Executor와 Docker 접근 권한은 runner host에 큰 영향을 줄 수 있습니다. 신뢰할 수 없는 기여의 pipeline을 해당 runner에서 실행하지 마세요.
- Loki의 `auth_enabled: false`와 앱/Grafana의 HTTP listener는 격리된 실습망을 전제로 한 선언입니다. 인터넷에 직접 노출하지 마세요.
- 유출된 credential은 파일 삭제만으로 해결되지 않습니다. 먼저 해당 credential을 폐기·교체하고 이미 공개된 이력·복제본을 조사해야 합니다.

참고: [GitLab CI 변수 보안](https://docs.gitlab.com/ci/variables/), [GitHub 민감정보 제거](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
