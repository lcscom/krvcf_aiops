# VCF AIops 2.1.0 — GitHub 업로드 정리

## 1. 저장소 정보(About)

| 항목 | 값 |
|---|---|
| 이름 | `vcf-aiops` |
| Description | VMware Cloud Foundation 통합 관제·로그 이상탐지·KB 근거 Private AI 분석 + 정산·보안 판독·용량 계획 포털 (vCenter·vSAN·NSX·Avi·VKS·UCS·FortiGate) |
| Topics | `vmware` `vcf` `vsphere` `vsan` `nsx` `aiops` `observability` `log-analytics` `anomaly-detection` `llm` `rag` `mcp` `fastapi` `noc` `chargeback` `capacity-planning` `security-hardening` |
| 공개 범위 | Public 가능(아래 3절의 사내 승인·저작권 표기 확인 후) |
| 기능 | Issues 켜기 · Security › Private vulnerability reporting 켜기 · Wiki 끄기 |

## 2. 올리는 것 / 올리지 않는 것

소스 압축(`vcf-aiops-2.1.0-source.tar.gz`)은 `.gitignore` 를 적용한 추적 파일 255개만 담았습니다. 그대로 풀어서 올리면 됩니다.

| 올림 | 올리지 않음(.gitignore 또는 저장소 밖) |
|---|---|
| `app/` `appliance/` `scripts/` `deploy/` `tests/` `docs/` | 패치 서명 개인 키 `~/.vcf-aiops/patch-signing.key` (공개 키 `appliance/patch/patch-signing.pub` 만 올림) |
| `README.md` `CHANGELOG.md` `SECURITY.md` `VERSION` | `data/`(DB·설정 암호화 키·초기 비밀번호) · `.env` · `config.yaml` · `certs/` |
| `config.example.yaml` `.env.example` | OVA·VMDK·패치 번들(`*.ova` `*.tgz`) → **Releases 첨부로** |
| `.github/`(CI · 이슈·PR 템플릿) | KB·TechDocs 아카이브 원문(Broadcom 저작물) |
| `LICENSE`(Apache-2.0) · `NOTICE` · `THIRD_PARTY_NOTICES.md` · `THIRD_PARTY_LICENSES.txt` · `docs/FEATURES.md` · `docs/OSS-APPLIANCE-PACKAGES.md` | |

점검 결과: 추적 파일에서 비밀번호·API 토큰·개인 키 문자열 검색 0건, README·GUIDE 상대 링크·이미지 깨짐 0건(2.1 이미지 4개 포함), CI 명령(`node --check` + `pytest`)을 Python 3.12 깨끗한 가상환경에서 실행해 통과.

## 3. 라이선스·고지(1.11.3 에서 정리, 2.1.0 갱신 — segno 추가)

| 파일 | 내용 |
|---|---|
| `LICENSE` | Apache License 2.0 전문(ASF 원문 그대로) |
| `NOTICE` | 저작권(`Copyright 2026 The VCF AIops Authors`)·제3자 요약·NVD·상표 고지 |
| `THIRD_PARTY_NOTICES.md` | 배포물 포함 Python 런타임 35개(버전·라이선스·용도), govc·WinSW·Pretendard, OVA OS, Docker 기반 이미지, 설치 시 내려받는 Fluent Bit·rsyslog·Miniforge, 개발 전용 7개, 외부 서비스·데이터, 상표 |
| `THIRD_PARTY_LICENSES.txt` | 위 구성요소 라이선스 원문(패키지 동봉 원문을 `scripts/third_party.py` 로 모음) |
| `docs/OSS-APPLIANCE-PACKAGES.md` | OVA Ubuntu 패키지 262개·버전·라이선스와 GPL·LGPL 대응 소스 제공 안내(OVA 이미지와 대조해 일치 확인) |
| `docs/FEATURES.md` | 전체 기능 명세(구성요소 16장, 부록 API 180개 경로·권한, 탐지 규칙 98종, 조치·모니터 카탈로그, 설정 항목) |

결정·확인이 필요한 것
- **저작권 표기**: `NOTICE` 는 `The VCF AIops Authors` 로 썼습니다. 개인 이름이나 회사 이름으로 바꾸려면 `NOTICE` 첫 두 줄만 고치면 됩니다.
- **소스 제공 약속**: `docs/OSS-APPLIANCE-PACKAGES.md` 에 'OVA 배포일로부터 3년 동안 저장소 관리자가 GPL·LGPL 패키지의 같은 버전 소스를 제공'한다는 문구가 있습니다(GPLv2 3(b) 방식). OVA 를 외부에 배포한다면 이 약속을 지킬 수 있는지 확인하세요. Ubuntu 아카이브에 같은 버전 소스가 있어 실제 부담은 작습니다.
- **사내 승인**: 재직 중 만든 코드라면 회사의 오픈소스 공개 승인 절차를 먼저 거치세요. README 지식 화면 스크린샷은 가상 KB 로 교체해 Broadcom KB 원문은 들어 있지 않습니다.

## 4. 올리기

이미 1.x 를 올린 저장소라면 4-2 를, 처음이면 4-1 을 따릅니다.

### 4-1. 처음 올리기(새 저장소)

```bash
tar xzf vcf-aiops-2.1.0-source.tar.gz && cd vcf-aiops
git init -b main
git add -A
git commit -F- <<'MSG'
VCF AIops 2.1.0

- 2.1.0: MFA(TOTP)·OIDC SSO·SIEM(CEF/syslog) 전달·PagerDuty/Opsgenie·API 토큰 IP 허용 목록·경영진 요약(/noc/exec)
- 2.0.0: vSAN Health(관리 API) · 용량 예측·VM 적정화 · 구성 변경 추적 · 월간 경영 리포트
- 1.12.0: VM·Pod 논리 토폴로지·서비스 흐름도, Avi 처리량·End user, vSAN 관제, 정산(Billing), 보안 판독(하드닝·KISA·VMSA/CVE), 메뉴 정리
- 1.11.4: KB 자동 연결·재분석
- README 전면 보강(한국어) + README.en.md
MSG
gh auth login                                   # 처음 한 번(브라우저 인증)
gh repo create vcf-aiops --public --source=. --remote=origin --push \
  --description "VMware Cloud Foundation 통합 관제·실시간 로그 이상탐지·KB 근거 기반 Private AI 분석 포털"
gh repo edit --add-topic vmware,vcf,vsphere,vsan,nsx,aiops,observability,log-analytics,anomaly-detection,llm,rag,mcp,fastapi,noc,chargeback,capacity-planning,security-hardening
```

Public 공개 승인 전이면 `--public` 을 `--private` 로 바꿔 먼저 올리고, 승인 후 `gh repo edit --visibility public --accept-visibility-change-consequences` 로 전환합니다. 이미 저장소가 있으면 `gh repo create` 대신, 받은 소스로 작업 폴더의 파일을 덮어쓴 뒤 `git add -A && git commit -F- … && git push` 를 실행합니다.


### 4-2. 기존 저장소 갱신(1.x·2.0 → 2.1.0)

```bash
git clone https://github.com/<계정>/vcf-aiops.git && cd vcf-aiops
git rm -rq . && tar xzf ../vcf-aiops-2.1.0-source.tar.gz --strip-components=1   # 삭제된 파일까지 정확히 반영
git add -A && git status --short | head                                            # 신규: app/security/mfa.py·oidc.py, app/siem.py, static/nocexec.js, tests/test_v210.py, docs/images 4개(noc-exec·console-mfa-enroll·login-mfa·console-siem)
git commit -m "VCF AIops 2.1.0" && git push
```

## 5. 릴리스(태그 + 첨부)

```bash
git tag -a v2.1.0 -m "VCF AIops 2.1.0"
git push origin v2.1.0
gh release create v2.1.0 --title "VCF AIops 2.1.0" --notes-file release-notes-v2.1.0.md \
  vcf-aiops-2.1.0.ova vcf-aiops-patch-2.1.0.tgz SHA256SUMS
```

- OVA(300MB)는 GitHub Release 첨부 한도(파일당 2GiB) 안이라 나누지 않고 그대로 올립니다.
- OVA 는 전달용으로 28MB 조각(`part00`~`part10`)으로 나눠 드렸습니다. 릴리스 전에 합치고 확인하세요.

```bash
cat vcf-aiops-2.1.0.ova.part* > vcf-aiops-2.1.0.ova
cat vcf-aiops-patch-2.1.0.tgz.part* > vcf-aiops-patch-2.1.0.tgz   # 패치(36MB, segno wheel 포함)도 2조각으로 전달
sha256sum -c SHA256SUMS          # ova · patch · source 세 줄 모두 OK
```

Windows(명령 프롬프트): `copy /b vcf-aiops-2.1.0.ova.part00+vcf-aiops-2.1.0.ova.part01+vcf-aiops-2.1.0.ova.part02+vcf-aiops-2.1.0.ova.part03+vcf-aiops-2.1.0.ova.part04+vcf-aiops-2.1.0.ova.part05+vcf-aiops-2.1.0.ova.part06+vcf-aiops-2.1.0.ova.part07+vcf-aiops-2.1.0.ova.part08+vcf-aiops-2.1.0.ova.part09+vcf-aiops-2.1.0.ova.part10 vcf-aiops-2.1.0.ova` 후 `certutil -hashfile vcf-aiops-2.1.0.ova SHA256` 값을 SHA256SUMS 첫 줄과 비교.

## 6. 올린 뒤 확인

1. Actions 탭에서 `ci` 워크플로가 초록색인지(vcsim 이 필요한 테스트 2개는 CI 에서 건너뜀).
2. README 첫 화면의 스크린샷·mermaid 아키텍처 그림이 보이는지.
3. Releases 의 `SHA256SUMS` 로 내려받은 OVA 를 `sha256sum -c SHA256SUMS --ignore-missing` 로 확인.
