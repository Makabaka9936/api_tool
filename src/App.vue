<template>
  <div class="api-tester">
    <h2>接口测试工具</h2>
    <form @submit.prevent="sendRequest">
      <div>
        <label for="url">URL:</label>
        <input type="text" id="url" v-model="url" required />
      </div>
      <div>
        <label for="method">请求方法:</label>
        <select id="method" v-model="method" required>
          <option value="GET">GET</option>
          <option value="POST">POST</option>
          <option value="PUT">PUT</option>
          <option value="DELETE">DELETE</option>
        </select>
      </div>
      <div>
        <label for="headers">请求头 (JSON):</label>
        <textarea id="headers" v-model="headersJson" rows="4"></textarea>
      </div>
      <div>
        <label for="body">请求体 (JSON):</label>
        <textarea id="body" v-model="bodyJson" rows="4"></textarea>
      </div>
      <button type="submit">发送请求</button>
    </form>
    <div v-if="response">
      <h2>响应</h2>
      <div id="response">{{ response }}</div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const url = ref('');
    const method = ref('GET');
    const headersJson = ref('');
    const bodyJson = ref('');
    const response = ref(null);

    const sendRequest = async () => {
      try {
        const headers = JSON.parse(headersJson.value) || {};
        const body = method.value === 'GET' ? null : JSON.parse(bodyJson.value);

        const responseData = await fetch(url.value, {
          method: method.value,
          headers: {
            'Content-Type': 'application/json',
            ...headers,
          },
          body: body ? JSON.stringify(body) : undefined,
        }).then(res => res.json());

        response.value = JSON.stringify(responseData, null, 2);
      } catch (error) {
        response.value = `Error: ${error.message}`;
      }
    };

    return {
      url,
      method,
      headersJson,
      bodyJson,
      response,
      sendRequest,
    };
  },
};
</script>

<style scoped>
.api-tester {
  min-width: 800px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.api-tester h1 {
  text-align: center;
}

.api-tester form div {
  margin-bottom: 15px;
}

.api-tester form label {
  display: block;
  margin-bottom: 5px;
}

.api-tester form input,
.api-tester form textarea,
.api-tester form select {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
}

.api-tester form button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.api-tester form button:hover {
  background-color: #0056b3;
}

#response {
  white-space: pre-wrap;
  word-wrap: break-word;
  background-color: #f5f5f5;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  text-align: left;
}
</style>