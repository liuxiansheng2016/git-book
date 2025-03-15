# Cold vs. Hot Observable

### **Cold Observable（冷 Observable）**

* **每个订阅者都会收到一个独立的数据流**（数据源是**懒加载**的）。
* **每次订阅时都会重新启动数据生产**，不同的订阅者不会共享数据流。
* **数据是在订阅时创建的**，如果没有订阅者，数据不会被生成。
* 适用于 **API 请求、定时器、文件读取** 等场景。

### **Hot Observable（热 Observable）**

* **所有订阅者共享同一个数据流**（数据源是**主动的**）。
* **Observable 在没有订阅者时也可能会产生数据**，新订阅者只能收到后续的数据，而不会收到过去的数据。
* 适用于 **WebSocket、按钮点击事件、Subject** 等场景。

| 特性         | Cold Observable | Hot Observable      |
| ---------- | --------------- | ------------------- |
| **数据生产方式** | 订阅时创建，独立运行      | 订阅前就可能开始，数据共享       |
| **数据是否共享** | 否，每个订阅者都有独立的数据流 | 是，所有订阅者共享同一个数据流     |
| **适用场景**   | API 请求、定时器、文件读取 | WebSocket、按钮点击、用户输入 |
| **数据是否缓存** | 订阅前不会生产数据       | 订阅前就可能生产数据          |

### &#x20;将Cold Observable 变成 Hot Observable

使用share() 和 shareReplay()

{% content-ref url="../sharereplay.md" %}
[sharereplay.md](../sharereplay.md)
{% endcontent-ref %}

