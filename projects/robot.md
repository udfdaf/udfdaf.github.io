---
layout: page
title: Robot Monitoring System
permalink: /projects/robot/
image: /assets/img/projects/robot/robot-thumb.png
---

## 프로젝트 소개
로봇의 실시간 상태(Telemetry)를 수집하고, 대시보드에서 로봇 목록/현재 위치/상태/이력 로그를 한 화면에서 확인할 수 있도록 만든 **로봇 관제 MVP** 입니다.  
단순 CRUD가 아니라, **로봇 인증(API Key)** 과 **실시간 캐시(Redis)**, **이벤트 발행(RabbitMQ)**, **Docker Compose 인프라 구성**까지 “운영 관점”을 포함해 설계했습니다.

---

## Tech Stack

### Backend
`TypeScript` · `NestJS`

### Database / Cache / MQ
`PostgreSQL` · `Redis` · `RabbitMQ`

### DevOps
`Docker` · `Docker Compose`

### API Testing
`Postman` · `Swagger UI`

---

## 주요 화면

### Dashboard (로봇 목록 + 맵 + Telemetry 전송)
- 로봇 생성/목록 조회
- 로봇 선택 시 맵에서 위치 확인
- Telemetry 전송 및 현재 상태 확인

![Dashboard](/assets/img/projects/robot/dashboard.png)

---

### Telemetry History (최근 이력)
- 최근 N개 이력을 빠르게 확인하도록 구성

![TelemetryHistory](/assets/img/projects/robot/telemetryhistory.png)

---

### Logs (필터/검색 기반 운영 로그)
- 레벨 필터(ALL/INFO/WARN/ERROR/DEBUG)
- 이벤트/메시지 검색으로 트러블슈팅 보조

![Logs](/assets/img/projects/robot/logs.png)

---

### Admin (키 기반 접근 + DB 테이블 조회)
- `x-admin-key` 기반으로 어드민 기능 접근
- robots / telemetry_history 테이블을 웹에서 확인

![AdminNoKey](/assets/img/projects/robot/no_key_admin.png)

![AdminRobotsHistory](/assets/img/projects/robot/admin_robots_history.png)

---

### 현재 상태 API 응답 확인 (robots/me/telemetry)
- 단일 로봇 관점에서 “현재 상태”를 JSON으로 확인

![MeTelemetry](/assets/img/projects/robot/me_telemetry.png)

---

## 기술 설계 및 문제 해결

### 1) 사람 중심 인증 구조의 한계를 Robot API Key 기반 기기 인증으로 전환하여 로봇 단위 접근 제어를 확보했습니다

**문제**  
관제 시스템에서는 사람이 로그인하는 인증보다, **로봇(기기) 단위 인증**이 핵심이었습니다.  
단순 토큰 인증으로는 “어떤 로봇이 보내는 데이터인지”를 안정적으로 보장하기 어려웠습니다.

**해결**  
로봇 생성 시 서버가 **Robot API Key**를 발급하고, Telemetry 수집 요청은 해당 Key를 통해 인증되도록 설계했습니다.  
또한 DB에는 원문 키를 저장하지 않고 **SHA-256 해시(api_key_hash)** 만 저장하여 유출 리스크를 줄였습니다.

**결과**  
- 로봇 단위로 접근 제어가 가능해졌고  
- 운영 관점에서 “인증된 로봇만 데이터 업로드”가 보장되었습니다.

---

### 2) 고빈도 Telemetry 조회로 인한 DB 부담을 Redis 캐시 + DB 이중 저장 구조로 분리하여 성능과 분석 기반을 동시에 확보했습니다

**문제**  
Telemetry는 빈도가 높고, 대시보드에서는 “현재 상태” 조회가 많아 DB만으로는 비용이 커질 수 있었습니다.

**해결**  
- “현재 상태”는 Redis에 저장하여 빠르게 조회  
- “이력”은 PostgreSQL에 저장하여 분석/조회 기반 확보  
- Redis는 TTL 기반으로 최신 상태만 유지하여 “online/offline” 판정까지 단순화했습니다.

**결과**  
- 대시보드 조회는 빠르게 유지되고  
- 데이터는 분석 가능한 형태로 축적되었습니다.

---

### 3) Telemetry 저장 구조의 높은 결합도를 MQ 이벤트 발행 구조로 개선하여 비동기 확장 기반을 마련했습니다

**문제**  
Telemetry 저장이 단순 API 처리로 끝나면, 이후 기능(알람/분석/리포트)을 붙일 때 결합도가 높아질 수 있었습니다.

**해결**  
Telemetry 수집 시 RabbitMQ에 이벤트를 발행하고, 별도 Consumer가 PostgreSQL에 이력을 적재하도록 분리했습니다.  
이로써 수집 API는 “빠른 응답(ingest)”에 집중하고, 저장/분석 계열 작업은 비동기 파이프라인으로 확장 가능해졌습니다.

**결과**  
- 수집(ingest)과 적재(persist)를 분리해 안정성을 확보했고  
- 이후 기능을 **비동기 컨슈머**로 붙일 수 있는 기반을 마련했습니다.

---

### 4) 로컬 실행 환경의 재현성 문제를 Docker Compose 기반 통합 환경 구성으로 해결하여 개발 안정성을 확보했습니다

**문제**  
로컬에서 각각 실행하면 네트워크/환경변수/실행 순서 문제로 재현성이 떨어졌습니다.

**해결**  
Docker Compose로 Backend + PostgreSQL + Redis + RabbitMQ를 하나의 네트워크에서 구성했고, 컨테이너 내부에서는 `localhost` 대신 서비스 이름(`postgres`, `redis`, `rabbitmq`)으로 접근하도록 정리했습니다.

**결과**  
- “실행 한 번에 개발 환경 구성”이 가능해졌고  
- 재현성이 좋아졌습니다.

---

## Code Samples

> 아래 샘플은 실제 프로젝트에서 핵심 설계(기기 인증 / Realtime+MQ 파이프라인 / 비동기 적재)를 가장 잘 보여주는 부분만 20~40줄 단위로 발췌했습니다.

---

### 1) Robot API Key 인증 Guard (x-api-key → SHA-256 → Robot 식별)

**의도**  
로봇 요청을 사용자 인증과 분리하기 위해 `x-api-key` 기반 기기 인증을 적용했습니다.  
DB에는 원문 Key를 저장하지 않고 해시만 저장하여 유출 위험을 줄였습니다.

```ts
import {
  CanActivate,
  ExecutionContext,
  Injectable,
  UnauthorizedException,
} from '@nestjs/common';
import { RobotsService } from '../robots/robots.service';
import { Request } from 'express';
import * as crypto from 'crypto';

@Injectable()
export class RobotAuthGuard implements CanActivate {
  constructor(private readonly robotsService: RobotsService) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest<Request>();
    const apiKey = request.headers['x-api-key'];

    if (!apiKey || typeof apiKey !== 'string') {
      throw new UnauthorizedException('API key is missing');
    }

    const hash = crypto.createHash('sha256').update(apiKey).digest('hex');
    const robot = await this.robotsService.findByApiKeyHash(hash);

    if (!robot) {
      throw new UnauthorizedException('Invalid API key');
    }

    request.robot = robot; // express.d.ts로 타입 확장
    return true;
  }
}
```

---

### 2) Telemetry 수집: Redis 최신 상태 저장 + MQ 이벤트 발행 (Realtime/History 분리)

**의도**  
“현재 상태 조회”는 Redis에 저장해 빠르게 제공하고, “이력 적재”는 MQ로 분리해 비동기 확장 기반을 만들었습니다.  
MQ 발행 실패는 로깅 후 예외로 처리해 운영 시 원인 추적이 가능하도록 했습니다.

```ts
async ingestTelemetry(robotId: string, telemetry: TelemetryDto) {
  const ttl = Number(this.config.get<string>('REDIS_TTL_SECONDS', '60'));
  const key = `robot:${robotId}:telemetry`;

  const payload = {
    robotId,
    telemetry,
    receivedAt: new Date().toISOString(),
  };

  // 1) Redis: 실시간 최신 상태 저장
  await this.redis.set(key, JSON.stringify(payload), 'EX', ttl);
  this.logger.log(
    `[EVENT] telemetry.cached robotId=${robotId} battery=${telemetry.battery} status=${telemetry.status} ttl=${ttl}`,
  );

  // 2) RabbitMQ: 비동기 적재용 이벤트 발행
  try {
    await this.mq.publish('telemetry.ingested', {
      eventType: 'telemetry.ingested',
      ...payload,
    });
    this.logger.log(
      `[EVENT] telemetry.published rk=telemetry.ingested robotId=${robotId}`,
    );
  } catch (e) {
    const msg = e instanceof Error ? e.message : String(e);
    this.logger.error(
      `[EVENT] telemetry.publish_failed robotId=${robotId} err=${msg}`,
    );
    throw e;
  }

  return { ok: true, robotId, ttl };
}
```

---

### 3) Telemetry Consumer: Payload 검증 + DB 적재 + 재시도(nack requeue)

**의도**  
MQ payload는 타입이 깨지기 쉬워 type guard로 스키마를 검증했습니다.  
잘못된 payload는 drop 처리하고, DB 적재 실패는 throw로 전파해 nack(requeue)로 재시도하도록 설계했습니다.

```ts
await this.mq.consume(
  'telemetry.history.save',
  'telemetry.ingested',
  async (payload) => {
    // 1) 스키마 검증 실패는 DROP (재시도 X)
    if (!isTelemetryIngestedEvent(payload)) {
      this.logger.warn(
        `[DROP] telemetry.invalid_payload raw=${JSON.stringify(payload)}`,
      );
      return;
    }

    // 2) 정상 payload는 DB 적재
    try {
      await this.telemetryRepo.save({
        robotId: payload.robotId,
        battery: payload.telemetry.battery,
        status: payload.telemetry.status,
        lat: payload.telemetry.lat ?? null,
        lng: payload.telemetry.lng ?? null,
      });

      this.logger.log(
        `[EVENT] telemetry.persisted robotId=${payload.robotId} battery=${payload.telemetry.battery} status=${payload.telemetry.status}`,
      );
      return;
    } catch (e) {
      const msg = e instanceof Error ? e.message : String(e);
      this.logger.error(
        `[RETRY] telemetry.persist_failed robotId=${payload.robotId} err=${msg}`,
      );
      throw e; // mq.service.ts에서 nack(requeue=true)
    }
  },
);
```
---

## API Specification (요약)

> 아래는 “프로젝트 이해용 요약”이며, 실제 테스트/문서화는 Swagger UI를 통해 확인할 수 있습니다.

---

### Robots

| Method | URL | Description |
|---|---|---|
| POST | /robots | 로봇 생성 (API Key 발급) |
| GET | /robots | 로봇 목록 조회 (online 포함) |
| GET | /robots/:robotId | 로봇 단건 조회 (online 포함) |
| DELETE | /robots/:robotId | 로봇 삭제 (캐시/이력 포함) |

---

### Telemetry (Robot Self)
| Method | URL | Description |
|---|---|---|
| POST | /robots/telemetry | Telemetry 업로드 (x-api-key) |
| GET | /robots/me | 인증된 로봇 정보 |
| GET | /robots/me/telemetry | 최신 상태 조회 (Redis) |
| GET | /robots/me/telemetry/history | 이력 조회 (PostgreSQL) |

---

### Admin
| Method | URL | Description |
|---|---|---|
| GET | /admin/db/robots | robots 테이블 조회 (x-admin-key) |
| GET | /admin/db/telemetry-history | telemetry_history 테이블 조회 (x-admin-key) |
| GET | /admin/db/logs | app.log 조회 (x-admin-key) |

---

## 회고
관제 시스템은 “기능 구현”보다 “운영 관점에서 안전하게 굴러가게 만들기”가 더 중요하다는 걸 체감했습니다.  
특히 인증/캐시/이벤트/인프라를 MVP 단계에서부터 최소 단위로라도 녹여낸 것이 가장 큰 수확이었습니다.

또한 단순히 “저장했다”에서 끝나는 게 아니라,  
**실시간/이력 분리**, **비동기 파이프라인**, **관리자 레이어**, **로그 기반 트러블슈팅**까지 연결하면서  
백엔드가 실무에서 어떤 고민을 해야 하는지 경험할 수 있었습니다.
