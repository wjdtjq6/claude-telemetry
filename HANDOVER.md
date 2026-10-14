# 인수인계

기존 Neon DB와 Cloudflare 대시보드(`claude-ricegang.pages.dev`)는 삭제됨. 새 주인이 처음부터 다시 세운다.

## 순서

1. **README의 Quick Start Step 1~2 그대로** — 새 Neon 프로젝트 생성 → `neon/schema.sql` 실행 → Cloudflare Pages 배포 + `DATABASE_URL` secret 설정.
   - 배포 스크립트는 `dashboard/package.json`의 `deploy`에 `--project-name=claude-ricegang`이 박혀 있으니 원하는 이름으로 바꿀 것.
2. **README에 없는 수정 2곳** — 바꾼 뒤 main에 push.
   - `agent/claude_telemetry/config.py`의 `INGEST_BASE_URL` → 새 대시보드 주소
   - `bootstrap.sh`의 `FORK_URL` → 이 저장소의 새 주소
3. **모든 PC 재설치** — DB가 새것이라 기존 PC의 api_key는 전부 무효. 각 PC는 401이 연속 3번 나면 스스로 수집을 멈추고 LaunchAgent를 지운다(`~/.claude-telemetry/.decommissioned`).
   - PC마다: `rm -f ~/.claude-telemetry/.decommissioned` → `bootstrap.sh` 실행 → `cc-telemetry setup`

## 알아둘 것

- 사용률 게이지(5h·1w·모델별)는 **모든 PC가 같은 Claude 계정을 쓴다는 전제**로 가장 최근 값을 보여준다. 멤버마다 계정이 다르면 값이 섞이므로 `dashboard/src/lib/rateLimits.ts` 헤더 주석대로 `weekly_reset_at` 기준 그룹핑이 필요.
- Neon 무료 플랜은 월 100 CU-h. 훅 동기화 간격(10분)을 줄이면 DB가 잠들지 못해 한도에 걸린다.
