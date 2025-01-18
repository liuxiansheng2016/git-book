# Node API retry

## **使用 Axios 的拦截器**

Axios 提供了请求和响应拦截器，可以用来捕获失败的请求并在其中添加重试逻辑。

```javascript
const axios = require('axios');

const client = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 5000,
});

let retryCount = 0;
const maxRetries = 3;

client.interceptors.response.use(
    response => response,
    async error => {
        const originalRequest = error.config;
        if (error.response && error.response.status >= 500 && retryCount < maxRetries) {
            retryCount++;
            console.log(`Retrying request (${retryCount} of ${maxRetries})...`);
            return await client(originalRequest);
        }
        retryCount = 0;
        return Promise.reject(error);
    }
);

async function fetchData() {
    try {
        const response = await client.get('/data');
        console.log(response.data);
    } catch (error) {
        console.error('API call failed:', error.message);
    }
}

fetchData();

```

## **使用第三方库 retry-axios**

```javascript
const axios = require('axios');
const ra = require('retry-axios');

// 配置 Axios 实例
const client = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 5000,
});

// 使用 retry-axios 插件
client.defaults.raxConfig = {
  retry: 3,
  retryDelay: 1000,
  httpMethodsToRetry: ['GET', 'POST'],
  statusCodesToRetry: [[100, 199], [429, 429], [500, 599]],
  backoffType: 'exponential',
};

client.interceptors.request.use(ra.requestInterceptor);
client.interceptors.response.use(null, ra.responseInterceptor);

async function fetchData() {
  try {
    const response = await client.get('/data');
    console.log(response.data);
  } catch (error) {
    console.error('API call failed:', error.message);
  }
}

fetchData();

```

## **手动实现重试逻辑**

```javascript
const axios = require('axios');

async function fetchWithRetry(url, options, retries, delay) {
    let attempt = 0;

    while (attempt < retries) {
        try {
            const response = await axios(url, options);
            return response.data; // 成功则返回数据
        } catch (error) {
            attempt++;
            if (attempt === retries) throw error; // 最后一次失败则抛出错误
            console.log(`Attempt ${attempt} failed, retrying in ${delay}ms...`);
            await new Promise(resolve => setTimeout(resolve, delay)); // 等待 delay 毫秒
        }
    }
}

// 使用示例
const url = 'https://api.example.com/data';
const options = {
    method: 'GET',
    headers: { 'Content-Type': 'application/json' }
};
const retries = 3; // 最大重试次数
const delay = 1000; // 每次重试之间的等待时间（毫秒）

fetchWithRetry(url, options, retries, delay)
    .then(data => console.log('Data:', data))
    .catch(error => console.error('Error:', error));

```
