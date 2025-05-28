---
title: EAS Update가 적용되지 않는 이유는? 
description: runtimeVersion 때문!
# author: d_o0o_b
categories: [Minime]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

Expo EAS Update를 활용해 앱 기능을 업데이트하려 했지만 앱을 껐다 켜도 **변경 사항이 반영되지 않는 문제**가 발생했습니다.  
처음에는 원인을 몰라 한참을 헤맸는데 알고 보니 **runtimeVersion 설정 문제** 때문이었습니다.

이번 글에서는 제가 겪은 시행착오와 문제 해결 과정을 공유하고자 합니다.

> Expo EAS Update는 Over-the-Air(OTA) 방식으로 앱을 업데이트할 수 있게 도와주는 기능입니다.



<br/>

## 문제 상황
> `eas update`는 성공했지만 앱에는 적용되지 않는다.

`eas update` 명령어를 통해 업데이트를 성공적으로 배포하였습니다.

```bash
✔ Computed project fingerprints
✔ Published!
Branch             production
Runtime version    1.1.0
...
Message            달력 추가
```

위처럼 `eas update`는 문제없이 완료됐지만 앱을 껐다 켜도, 삭제 후 재설치해도 변경 사항이 반영되지 않았습니다.


<br/>

## 원인 파악
> 문제는 `runtimeVersion` 불일치!

처음에는 `version`과 `runtimeVersion`을 동일한 개념으로 생각하고 `1.0.0 → 1.1.0`으로 같이 올렸습니다.   
하지만 EAS Update에서는 **빌드에 포함된 `runtimeVersion`**과 **업데이트 시 설정한 `runtimeVersion`**이 정확히 **일치**해야만 앱이 해당 업데이트를 적용합니다.

즉, 기존 빌드가 `runtimeVersion: 1.0.0`으로 생성되었는데 `1.1.0`으로 설정한 업데이트를 배포하면 그 업데이트는 기존 빌드에는 적용되지 않습니다.

```text
기존 빌드의 runtimeVersion: 1.0.0
eas update에서 설정한 runtimeVersion: 1.1.0
```

이렇게 불일치할 경우 앱은 해당 업데이트를 무시합니다.

> OTA는 런타임 버전이 다른 업데이트를 적용하지 않습니다.
{: .prompt-info }

<br/>


## 해결 과정

### 1. runtimeVersion을 다시 1.0.0으로 낮춰서 update 시도

```json
"runtimeVersion": "1.0.0"
```

이후 다시 `eas update`를 실행했습니다.   
업데이트는 성공했지만 여전히 앱에는 반영되지 않았습니다.

#### 반영되지 않은 이유

현재 사용 중인 개발 클라이언트와 빌드가 `runtimeVersion: 1.1.0`으로 빌드되어 있었기 때문입니다.

> `v1.0.0` OTA는 `v1.1.0` 빌드에 매칭되지 않기 때문입니다.

---

### 2. 현재 앱 클라이언트의 runtimeVersion 확인

```ts
import Constants from 'expo-constants';

console.log(Constants.manifest2?.runtimeVersion); // '1.1.0'
```

→ 클라이언트가 `v1.1.0`을 사용 중이므로 `v1.0.0`으로 배포한 업데이트는 무시됩니다.

---

### 3. 결국 새 빌드를 만들기로 결정
`eas update`가 적용되지 않아 `eas build`도 해보고 `version`도 바꿔보는 등 다양한 시도를 하다 보니 점점 상황이 꼬였습니다.

결국, `runtimeVersion: 1.1.0`에 맞춘 새로운 빌드를 생성하고 Google Play Console에 업로드했습니다.

빌드를 새로 만들고 나니 상황이 깔끔해졌고 이후 업데이트 디버깅도 수월했습니다.

이후에 `version`만 변경하고 `runtimeVersion`은 그대로 (1.1.0) 유지하면서 OTA 업데이트를 진행하였습니다.

그 결과, OTA 업데이트가 정상적으로 반영되었습니다. 🎉🎉



<br/>

## 정리
<!-- > `version`은 일반적으로 app.json 또는 app.config.js에 설정하며, runtimeVersion은 OTA 업데이트의 적용 범위를 결정합니다. -->

| 항목              | 의미                                           | 변경 시점                     |
|------------------|------------------------------------------------|------------------------------|
| `version`        | 사용자에게 보여지는 앱 버전                     | 기능 변경, UI 수정 등        |
| `runtimeVersion` | EAS 업데이트 호환성을 결정하는 **런타임 그룹 식별자**     | **네이티브 코드 변경 시**만 변경 |

> `runtimeVersion`이 바뀌면 반드시 새 빌드가 필요합니다. <br/> 
> 이전 빌드와의 OTA 호환은 끊기게 됩니다. <br/>
{: .prompt-warning }


<br/>

## 마무리

처음엔 "왜 업데이트가 반영되지 않을까?"라는 단순한 의문에서 출발했지만 그 뒤엔 `runtimeVersion` 개념에 대한 이해 부족이 있었습니다.

OTA 업데이트가 적용되지 않는 원인을 파악하기까지 꽤 많은 시간을 허비했지만 그만큼 EAS Update의 동작 방식과 런타임 버전 관리의 중요성을 체감할 수 있었습니다.

이번 경험을 통해 `version`과 `runtimeVersion`을 단순히 숫자가 아닌 각각의 역할과 책임이 분명한 도구로 인식하게 되었습니다.   
앞으로는 OTA와 네이티브 빌드 간의 관계를 고려해 불필요한 빌드 없이 효율적으로 배포하는 구조를 만들어갈 수 있을 것 같습니다. 😀

