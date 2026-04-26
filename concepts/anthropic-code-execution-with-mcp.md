---
title: "Code execution with MCP: Building more efficient AI agents"
source: Anthropic Engineering
date: 2025-11-04
url: https://www.anthropic.com/engineering/code-execution-with-mcp
tags:
  - llm
  - mcp
  - code-execution
  - engineering
  - anthropic
---

# Code execution with MCP: Building more efficient AI agents

## 핵심 요약

MCP 서버를 직접 도구 호출이 아닌 **코드 API로 제공**하여 에이전트의 토큰 사용량을 98.7% 절감.

## 문제: 도구 증가로 인한 비효율

### 문제 1. 툴 정의가 컨텍스트 윈도우 과적재

수천 개의 도구가 연결된 에이전트는 수십만 토큰을 처리해야 첫 요청을 읽을 수 있음.

### 문제 2. 중간 결과가 추가 토큰 소모

예: Google Drive에서 2시간 회의록을 가져와 Salesforce에 첨부
- 전체 내용이 두 번 컨텍스트 통과
- 2시간 회의의 경우 ~50,000 토큰 추가 소모

## 해결책: 코드 실행 환경 + MCP

MCP 서버를 직접 도구 호출 대신 **코드 API**로 제공:

```
servers/
├── google-drive/
│ ├── getDocument.ts
│ └── index.ts
├── salesforce/
│ ├── updateRecord.ts
│ └── index.ts
```

```typescript
// agent가 도구를 파일시스템처럼 탐색
import * as gdrive from './servers/google-drive';
import * as salesforce from './servers/salesforce';

const transcript = (await gdrive.getDocument({ documentId: 'abc123' })).content;
await salesforce.updateRecord({
  objectType: 'SalesMeeting',
  recordId: '00Q5f000001abcXYZ',
  data: { Notes: transcript }
});
```

**결과**: 150,000 토큰 → 2,000 토큰 (98.7% 절감)

## 코드 실행의 장점

### 1. Progressive Disclosure
도구를 파일시스템처럼呈现하여 필요할 때만 로드.

### 2. 컨텍스트 효율적 결과 처리
```typescript
// 10,000행 스프레드시트 처리
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(row => row["Status"] === 'pending');
console.log(pendingOrders.slice(0, 5)); // 모델은 5행만 봄
```

### 3. 강력한 제어 흐름
```typescript
// 대기 루프를 에이전트 루프 대신 코드에서 처리
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({ channel: 'C123456' });
  found = messages.some(m => m.text.includes('deployment complete'));
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
```

### 4. 프라이버시 보호
중간 결과를 실행 환경에 유지. 민감 정보는 토큰화되어 모델 컨텍스트에 전달되지 않음.

### 5. 상태 지속 및 스킬
```typescript
// 에이전트가 재사용 가능한 스킬로 저장
// ./skills/save-sheet-as-csv.ts
export async function saveSheetAsCsv(sheetId: string) {
  const data = await gdrive.getSheet({ sheetId });
  const csv = data.map(row => row.join(',')).join('\n');
  await fs.writeFile(`./workspace/sheet-${sheetId}.csv`, csv);
  return `./workspace/sheet-${sheetId}.csv`;
}
```

## 트레이드오프

| 이점 | 비용 |
|------|------|
| 토큰 비용 절감 | 보안 실행 환경 필요 |
| 지연 시간 감소 | 샌드박싱, 리소스 제한 |
| 강력한 도구 구성 | 인프라 운영 부담 |

## 출처

[Code execution with MCP: Building more efficient agents](https://www.anthropic.com/engineering/code-execution-with-mcp) - Anthropic Engineering, 2025.11.04