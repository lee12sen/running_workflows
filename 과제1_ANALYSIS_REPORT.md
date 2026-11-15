# Matrix Build 실행 분석 리포트

## 1. 분석 대상

- 워크플로: [`Matrix Build`](.github/workflows/matrix-build.yml)
- 실행 이벤트: `push`
- 실행 일시: 2026-11-14
- 실행 결과: 9개 Job 모두 성공
- 대상 조합: Ubuntu, Windows, macOS × Node.js 16, 18, 20

## 2. 워크플로 설정

### Fail-fast

```yaml
strategy:
  fail-fast: false
```

한 Job이 실패해도 다른 OS/Node.js 조합의 Job을 취소하지 않고 계속 실행하도록 설정했다. 따라서 모든 환경의 테스트 결과를 확인할 수 있다.

### 병렬 실행

```yaml
strategy:
  max-parallel: 3
```

Matrix로 총 9개 Job을 생성하지만, 동시에 실행되는 Job 수는 최대 3개로 제한했다. 즉, 9개 Job이 순차 실행되는 방식이 아니라 최대 3개씩 병렬로 실행된다.

## 3. OS별 실행 시간

실행 시간은 GitHub Actions의 각 Job `started_at`부터 `completed_at`까지의 시간이다. 따라서 checkout, Node.js 설정, `npm ci`, `npm test`를 포함한 Job 전체 시간이다.

| OS | Node.js 16 | Node.js 18 | Node.js 20 | 평균 |
|---|---:|---:|---:|---:|
| Ubuntu | 6초 | 8초 | 7초 | 7.0초 |
| Windows | 21초 | 19초 | 17초 | 19.0초 |
| macOS | 10초 | 11초 | 13초 | 11.3초 |

## 4. 비교 및 결론

- Ubuntu가 평균 **7.0초**로 가장 빨랐다.
- macOS는 평균 **11.3초**로 Ubuntu보다 약 4.3초 느렸다.
- Windows는 평균 **19.0초**로 가장 오래 걸렸으며, Ubuntu보다 약 12.0초 느렸다.
- Node.js 버전별 차이보다 OS별 실행 환경과 의존성 설치 시간의 차이가 더 크게 나타났다.
- `max-parallel: 3` 설정으로 병렬성을 제한하면서도 전체 Matrix 테스트를 수행했다.
- 이번 실행에서는 실패한 Job이 없었으므로 `fail-fast: false`의 취소 방지 효과는 실제로 발생하지 않았지만, 실패가 발생해도 나머지 환경을 계속 검증할 수 있는 구성이다.

> 측정값은 GitHub-hosted runner의 단일 실행 결과이므로, runner 상태와 네트워크 상황에 따라 달라질 수 있다.

## 5. 실행 정보

- Workflow run: [Matrix Build #1](https://github.com/lee12sen/running_workflows/actions/runs/43574080324)
- 분석 커밋: `25a8721`
