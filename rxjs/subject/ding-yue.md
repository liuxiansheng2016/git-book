---
description: 什么是订阅？订阅是表示可支配资源的对象，通常是 Observable 的执行
---

# 取消订阅



<figure><img src="../../.gitbook/assets/image (1).png" alt="" width="501"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt="" width="450"><figcaption></figcaption></figure>

### 取消订阅

1. &#x20;subscription.unsubscribe();
2. &#x20;private subscriptions: Subscription\[] = \[];
3. &#x20;通过 "adding" 一个订阅到另一个订阅来做到这一点：

```javascript
const observable1 = interval(400);
const observable2 = interval(300);

const subscription = observable1.subscribe(x => console.log('first: ' + x));
const childSubscription = observable2.subscribe(x => console.log('second: ' + x));

subscription.add(childSubscription);

setTimeout(() => {
    // Unsubscribes BOTH subscription and childSubscription
    subscription.unsubscribe();
}, 1000);

```
