![](https://github.com/unchartedsky/discourse/workflows/discourse/badge.svg)

# Discourse on Kubernetes

> MAYBE [bitnami/discourse - Docker Hub](https://hub.docker.com/r/bitnami/discourse/)가 더 나은 대안일 수도 있습니다.

Kubernetes용 Discourse 이미지를 만듭니다. Discourse의 `launcher.sh` 스크립트가 Mac에서는 오류를 내뱉기 때문에 어쩔 수 없이 Github Actions의 리눅스 머신을 이용하기로 합니다.

## 플러그인 운영 정책 (2026-11-01 정리)

이 저장소는 Discourse upstream(`discourse/discourse` main 브랜치)을 추적해 매 빌드마다 최신 코드를 가져옵니다. 이 정책을 유지하기로 했으므로, upstream 변경이 플러그인 호환성을 깨뜨리면 빌드가 실패하고 그때마다 아래와 같은 정리가 필요할 수 있습니다.

### 코어 번들로 대체됨 (무손실)

Upstream이 플러그인을 코어로 흡수하면서 독립 저장소는 archived가 됐고, 남은 클론은 최신 코어와 호환되지 않아 제거했습니다. 기능은 코어 번들본이 그대로 제공합니다.

- checklist — `cpradio/discourse-plugin-checklist` 클론 제거, 코어 `plugins/checklist`가 대체
- spoiler-alert — `discourse/discourse-spoiler-alert` 클론 제거, 코어 `plugins/spoiler-alert`가 대체
- voting — `discourse/discourse-voting` 클론 제거, 코어 `plugins/discourse-topic-voting`이 대체

### 코어 대체 없음 (기능 제외)

- discourse-locations — `paviliondev/discourse-locations` 클론 제거. 이 플러그인은 부팅 때마다 공개 Nominatim에 테스트 지오코딩 쿼리를 날리고, GitHub 러너의 공유 IP가 레이트리밋에 걸리면 부트가 죽기 때문에 CI와 양립할 수 없습니다. 플러그인 자체는 활성 상태지만 코어 대체 기능이 없어, 이 이미지에는 위치·지오코딩 기능이 없습니다. 플러그인 쪽이 부트 쿼리를 건너뛰는 옵션을 제공하면 다시 넣을 수 있습니다.

### 데이터베이스 요구사항: pgvector

Discourse main의 `db/structure.sql`가 `CREATE EXTENSION vector`를 요구합니다.

- CI DB — `pgvector/pgvector:pg15` 이미지를 사용합니다 (`.github/workflows/containerise-discourse.yaml`)
- 이미지 내장 PostgreSQL 15 — base 이미지에 내장된 PGDG 저장소에서 `postgresql-15-pgvector`를 설치합니다 (`web_only.yml`, `web_only_empty_discourse.yml`)

## Kubernetes 자료

- [How to set up an internal team forum in half a day using Discourse](https://medium.freecodecamp.org/how-to-set-up-an-internal-team-forum-in-half-a-day-using-discourse-b13588d907fe)
- [oursky/discourse-k8s](https://github.com/oursky/discourse-k8s)

## 설정 자료

- [Create Admin Account from Console](https://meta.discourse.org/t/create-admin-account-from-console/17274)
- [Setting up file and image uploads to S3](https://meta.discourse.org/t/setting-up-file-and-image-uploads-to-s3/7229)
- [How to Setup Google OAuth2 Login to Discourse Forum? Google Cloud Project Verified Steps](https://crunchify.com/how-to-setup-google-oauth2-login-to-discourse-forum-google-cloud-verified-steps/)
