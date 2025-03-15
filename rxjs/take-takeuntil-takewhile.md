# take(), takeUntil(), takeWhile()

### **`take()`, `takeUntil()`, `takeWhile()` 的区别**

#### **1️⃣ `take(n)`**

* **只取前 `n` 个值，之后自动完成。**
* **适用于限制数据流的数量（如分页、倒计时）。**

**📌 示例**

```typescript
import { interval } from 'rxjs';
import { take } from 'rxjs/operators';

interval(1000).pipe(take(3)).subscribe(console.log);

// 输出：0, 1, 2，然后完成
```

***

#### **2️⃣ `takeUntil(notifier$)`**

* **订阅直到 `notifier$` 触发，才会终止数据流。**
* **适用于** **取消订阅（如组件销毁时终止流）**。

**📌 示例**

```typescript
import { interval, timer } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

interval(1000).pipe(
  takeUntil(timer(5000)) // 5 秒后终止
).subscribe(console.log);
```

✅ **适用于**

* **Angular 组件销毁时自动取消订阅**
* **手动触发的取消逻辑**

***

#### **3️⃣ `takeWhile(predicate)`**

* **当条件 `predicate` 为 `true` 时持续订阅，`false` 时终止。**
* **适用于** **根据条件动态控制流的结束**。

**📌 示例**

```typescript
import { interval } from 'rxjs';
import { takeWhile } from 'rxjs/operators';

interval(1000).pipe(
  takeWhile(x => x < 3)
).subscribe(console.log);

// 输出：0, 1, 2，然后完成
```

✅ **适用于**

* **倒计时、加载进度条**
* **监听数据变化，直到满足某个条件**
