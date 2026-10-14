## VCF AIops 2.1.0 — 엔터프라이즈

2.0.0 의 모든 기능을 포함하고, 접근 통제·보안 연동·경영 가시성을 엔터프라이즈 수준으로 올렸습니다. 전체 기능은 [README](README.md)·[docs/FEATURES.md](docs/FEATURES.md), 변경 상세는 [CHANGELOG.md](CHANGELOG.md).

### 2.1 에서 새로 들어간 것
- **MFA(TOTP, RFC 6238)** — 로컬·AD/LDAP·SSO 계정 공통 2단계 인증. QR(순수 파이썬 segno) 또는 키 직접 입력으로 등록, 복구 코드 8개(1회용·해시 저장), 코드 재사용 거부, `auth.mfa.required_roles` 로 역할별 필수화(등록 전 화면 사용 제한), 관리자 해제. 로그인은 비밀번호 → `mfa_token`(5분) → 코드 검증 → 세션.
- **OIDC SSO** — Authorization Code + PKCE(S256), state·nonce 서명 쿠키, id_token JWKS 서명 검증(RS/ES, alg=none 거부, 키 회전 대응), iss·aud·exp·nonce 검사, 그룹 클레임 → 역할 매핑(중첩 클레임·userinfo 보완), Keycloak·Entra ID·Okta·ADFS. 로그인 화면 SSO 버튼, 관리자 점검 API.
- **SIEM 전달** — 감사 로그·이상징후·인시던트를 ArcSight CEF 로 syslog(RFC 5424, UDP·TCP·TLS)에 실시간 전달(Splunk·QRadar·ArcSight·Sentinel·Elastic). 큐·재연결·유실 집계·테스트 전송, 운영 설정 'SIEM 전달' 탭.
- **PagerDuty·Opsgenie** 온콜 채널 — Events API v2(dedup_key)·Alert API(alias, P1~P5). 기존 Teams·Slack·웹훅·SMTP 와 같은 발송함(재시도·브레이커·실패함).
- **API 토큰 IP 허용 목록** — 토큰별 IP/CIDR, 불일치는 거부 + 감사.
- **경영진 요약 `/noc/exec`** — 30일 인시던트·SLA·MTTR/MTTA(전 30일 대비), 호스트 가용·vSAN·용량 90% 임박, 추세, 열린 인시던트, 하드닝 준수율·CVE·구성 변경, 이번 달 정산(테넌트별), 적정화 절감, 플랫폼 상태. 관제 월 자동 순환 포함.

### 설치·업그레이드
- 새로 설치: `vcf-aiops-2.1.0.ova` (SHA256SUMS 로 확인)
- 1.3 이상·2.0 → 2.1.0: 웹 콘솔 › 시스템·패치 › `vcf-aiops-patch-2.1.0.tgz` 올리기 → 적용(서명 검증·자동 백업·실패 시 롤백), 또는 `sudo vcf-aiopsctl patch vcf-aiops-patch-2.1.0.tgz`. 번들에 새 의존 패키지(segno) wheel 이 들어 있어 폐쇄망에서도 적용됩니다. 2.0.0 → 2.1.0 웹 패치와 2.1.0 OVA 신규 부팅을 검증했습니다.
- 기본값: MFA 는 '선택 등록'(`auth.mfa.required_roles: []`), SSO·SIEM 은 꺼짐. 설정 변경 없이 동작합니다.

### 참고
- 새 의존성 segno 1.6.6(BSD-3-Clause) — THIRD_PARTY_NOTICES.md·THIRD_PARTY_LICENSES.txt 갱신.
- This product uses the NVD API but is not endorsed or certified by the NVD.
