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

Expo EAS Update를 활용하여 앱 기능을 업데이트하려 했지만 앱을 껐다 켜도 변경 사항이 반영되지 않는 문제가 발생했습니다.  
처음에는 원인을 몰라 한참을 헤맸는데 알고 보니 **runtimeVersion 설정 문제** 때문이었습니다.

이번 글에서는 제가 겪은 시행착오와 문제 해결 과정을 공유하고자 합니다.

> Expo EAS Update는 Over-the-Air(OTA) 방식으로 앱을 업데이트할 수 있게 도와주는 기능입니다.



<br/>

## 문제 상황
> EAS Update는 성공했지만 앱에는 적용되지 않는다.

`eas update` 명령어를 통해 업데이트를 성공적으로 배포하였습니다.

```bash
✔ Computed project fingerprints
✔ Published!
Branch             production
Runtime version    1.1.0
...
Message            달력 추가
```

하지만 앱을 껐다 켜도, 삭제했다가 다시 설치해도 변경 사항이 앱에 반영되지 않았습니다.

<br/>

## 원인 파악
> `runtimeVersion` 불일치

처음에는 `version`과 `runtimeVersion`을 동일한 개념으로 생각하고 `1.0.0 → 1.1.0`으로 같이 올렸습니다.
그러나 EAS Update는 빌드에 포함된 `runtimeVersion`과 업데이트 시 설정한 `runtimeVersion`이 정확히 일치해야만 업데이트를 적용합니다.

즉, 기존 빌드가 `runtimeVersion: 1.0.0`으로 생성되었는데 `1.1.0`으로 설정한 업데이트를 배포하면 그 업데이트는 기존 빌드에서는 적용되지 않습니다.

> `runtimeVersion`이 일치하지 않으면 앱은 해당 업데이트를 무시합니다.

<br/>


## 해결 과정

### 1. runtimeVersion을 다시 1.0.0으로 낮춰서 update 시도

```
"runtimeVersion": "1.0.0"
```

이후 다시 eas update를 실행했습니다.
업데이트는 성공했지만 앱에는 여전히 반영되지 않았습니다.

→ 이유: 현재 사용하는 개발 클라이언트와 빌드가 이미 `1.1.0`을 보고 있었기 때문입니다.
> 이미 `v1.1.0`으로 빌드된 앱에서는 `1.0.0` 업데이트를 적용할 수 없습니다.

### 2. 현재 빌드가 사용하는 runtimeVersion 확인

```
import Constants from 'expo-constants';

console.log(Constants.manifest2?.runtimeVersion); // '1.1.0'
```

→ 클라이언트가 1.1.0을 사용 중이므로 1.0.0으로 배포한 업데이트는 무시됩니다.

### 3. 해결 방법
eas update가 적용되지 않아 eas build도 해보고 `version`도 바꿔보는 등 다양한 시도를 하다 보니 점점 상황이 꼬였습니다.

결국, `runtimeVersion: 1.1.0`으로 맞춘 새로운 빌드를 생성하여 Google Play Console에 업로드하고 승인을 받았습니다.
깔끔하게 v1.1.0 빌드를 올리는 편이 디버깅에도 유리하다고 판단했습니다.

그 이후부터는 `version`만 변경하고 `runtimeVersion`은 그대로 (1.1.0) 유지하면서 OTA 업데이트를 진행하였습니다.

그 결과, OTA 업데이트가 정상적으로 반영되었습니다. 🎉



<br/>

## 정리
> version은 일반적으로 app.json 또는 app.config.js에 설정하며, runtimeVersion은 OTA 업데이트의 적용 범위를 결정합니다.

| 항목              | 의미                                           | 변경 시점                     |
|------------------|------------------------------------------------|------------------------------|
| `version`        | 사용자에게 보여지는 앱 버전                     | 기능 변경, UI 수정 등        |
| `runtimeVersion` | EAS 업데이트 호환성을 결정하는 런타임 식별자     | **네이티브 코드 변경 시**만 변경 |

> `runtimeVersion`이 바뀌면 반드시 새 빌드를 생성해야 하며 이전 빌드와의 OTA 호환은 끊기게 됩니다. <br/>
{: .prompt-warning }




## 마무리

처음에는 왜 업데이트가 반영되지 않는지 알 수 없어 정말 답답했습니다.  
그러나 `runtimeVersion`의 개념을 명확히 이해하고 나서야 문제를 해결할 수 있었습니다.  
지금은 의도한 대로 OTA 업데이트가 정상적으로 반영되고 있습니다. 😀



