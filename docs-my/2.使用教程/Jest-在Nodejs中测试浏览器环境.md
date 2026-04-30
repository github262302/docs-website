---
title: Jest-在Nodejs中测试浏览器环境
category: tutorial
date: 2025-04-01
tags: []
---

# Jest 在 Node.js 中测试浏览器环境完全指南

> 💡 本文介绍如何使用 Jest 在 Node.js 环境中模拟浏览器环境进行测试

---

## 目录

1. [概述](#概述)
2. [环境搭建](#环境搭建)
3. [JSDOM 环境配置](#jsdom-环境配置)
4. [常用测试场景](#常用测试场景)
5. [高级技巧](#高级技巧)
6. [常见问题](#常见问题)

---

## 概述

### 为什么需要在 Node.js 中测试浏览器环境？

- **速度快**: Node.js 测试比真实浏览器测试快得多
- **CI/CD 友好**: 无需配置复杂的浏览器环境
- **开发体验好**: 即时反馈，快速迭代
- **成本低**: 不需要启动完整的浏览器实例

### Jest 的优势

- 内置 JSDOM 环境支持
- 零配置开箱即用
- 强大的 Mock 能力
- 优秀的断言库

---

## 环境搭建

### 1. 安装依赖

```bash
# 基础安装
npm install --save-dev jest

# 或 yarn
yarn add -D jest

# 或 pnpm
pnpm add -D jest
```

### 2. 基本配置文件 (jest.config.js)

```javascript
/** @type {import('jest').Config} */
module.exports = {
  // 测试环境设置为 jsdom
  testEnvironment: 'jsdom',
  
  // 测试文件匹配模式
  testMatch: [
    '**/__tests__/**/*.[jt]s?(x)',
    '**/?(*.)+(spec|test).[jt]s?(x)'
  ],
  
  // 模块别名映射
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
    '\\.(jpg|jpeg|png|gif|webp|svg)$': '<rootDir>/__mocks__/fileMock.js'
  },
  
  // 测试前置文件
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  
  // 覆盖率配置
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
  ],
};
```

### 3. 安装 JSDOM 环境

```bash
npm install --save-dev jest-environment-jsdom
```

---

## JSDOM 环境配置

### 什么是 JSDOM？

JSDOM 是一个纯 JavaScript 实现的许多 Web 标准的库，特别是 WHATWG 组织制定的 DOM 和 HTML 标准。它模拟了浏览器环境的核心 API。

### 支持的浏览器 API

| API 类别 | 支持情况 | 示例 |
|---------|---------|------|
| DOM 操作 | ✅ 完整 | document.querySelector, createElement |
| 事件系统 | ✅ 完整 | addEventListener, CustomEvent |
| 定时器 | ✅ 完整 | setTimeout, setInterval |
| 本地存储 | ⚠️ 模拟 | localStorage, sessionStorage |
| Fetch API | ⚠️ 需 polyfill | fetch |
| WebSocket | ❌ 不支持 | - |
| Canvas | ⚠️ 部分支持 | 2D context |

### 配置 JSDOM 选项

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  testEnvironmentOptions: {
    url: 'http://localhost:3000',
    userAgent: 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
    resources: 'usable', // 允许加载外部资源
    pretendToBeVisual: true, // 启用视觉相关 API
  },
};
```

---

## 常用测试场景

### 场景 1: DOM 操作测试

```javascript
// src/dom-utils.js
export function createButton(text, onClick) {
  const button = document.createElement('button');
  button.textContent = text;
  button.className = 'btn btn-primary';
  button.addEventListener('click', onClick);
  return button;
}

export function toggleClass(element, className) {
  element.classList.toggle(className);
}
```

```javascript
// __tests__/dom-utils.test.js
import { createButton, toggleClass } from '../src/dom-utils';

describe('DOM 操作测试', () => {
  beforeEach(() => {
    // 每个测试前清理 DOM
    document.body.innerHTML = '';
  });

  test('createButton 创建按钮元素', () => {
    const mockClick = jest.fn();
    const button = createButton('点击我', mockClick);
    
    expect(button.tagName).toBe('BUTTON');
    expect(button.textContent).toBe('点击我');
    expect(button.className).toBe('btn btn-primary');
  });

  test('按钮点击触发回调', () => {
    const mockClick = jest.fn();
    const button = createButton('点击我', mockClick);
    
    // 模拟点击
    button.click();
    
    expect(mockClick).toHaveBeenCalledTimes(1);
  });

  test('toggleClass 切换类名', () => {
    const div = document.createElement('div');
    div.className = 'active';
    
    toggleClass(div, 'hidden');
    expect(div.classList.contains('hidden')).toBe(true);
    
    toggleClass(div, 'hidden');
    expect(div.classList.contains('hidden')).toBe(false);
  });
});
```

### 场景 2: 表单处理测试

```javascript
// src/form-handler.js
export function validateEmail(email) {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}

export function getFormData(form) {
  const formData = new FormData(form);
  const data = {};
  formData.forEach((value, key) => {
    data[key] = value;
  });
  return data;
}
```

```javascript
// __tests__/form-handler.test.js
import { validateEmail, getFormData } from '../src/form-handler';

describe('表单处理测试', () => {
  test('validateEmail 验证邮箱格式', () => {
    expect(validateEmail('test@example.com')).toBe(true);
    expect(validateEmail('invalid-email')).toBe(false);
    expect(validateEmail('test@domain')).toBe(false);
    expect(validateEmail('')).toBe(false);
  });

  test('getFormData 提取表单数据', () => {
    // 创建表单 HTML
    document.body.innerHTML = `
      <form id="testForm">
        <input name="username" value="john_doe" />
        <input name="email" value="john@example.com" />
        <select name="country">
          <option value="us" selected>USA</option>
        </select>
      </form>
    `;
    
    const form = document.getElementById('testForm');
    const data = getFormData(form);
    
    expect(data).toEqual({
      username: 'john_doe',
      email: 'john@example.com',
      country: 'us',
    });
  });
});
```

### 场景 3: 本地存储测试

```javascript
// src/storage.js
export const storage = {
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },
  
  get(key) {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : null;
  },
  
  remove(key) {
    localStorage.removeItem(key);
  },
  
  clear() {
    localStorage.clear();
  },
};
```

```javascript
// __tests__/storage.test.js
import { storage } from '../src/storage';

describe('本地存储测试', () => {
  // 每个测试前清理存储
  beforeEach(() => {
    localStorage.clear();
  });

  afterEach(() => {
    localStorage.clear();
  });

  test('set 和 get 存储对象', () => {
    const user = { id: 1, name: 'John' };
    storage.set('user', user);
    
    expect(storage.get('user')).toEqual(user);
  });

  test('remove 删除存储项', () => {
    storage.set('key', 'value');
    storage.remove('key');
    
    expect(storage.get('key')).toBeNull();
  });

  test('clear 清空所有存储', () => {
    storage.set('key1', 'value1');
    storage.set('key2', 'value2');
    storage.clear();
    
    expect(storage.get('key1')).toBeNull();
    expect(storage.get('key2')).toBeNull();
  });
});
```

### 场景 4: 事件监听测试

```javascript
// src/event-bus.js
export class EventBus {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
}
```

```javascript
// __tests__/event-bus.test.js
import { EventBus } from '../src/event-bus';

describe('事件总线测试', () => {
  let eventBus;
  
  beforeEach(() => {
    eventBus = new EventBus();
  });

  test('on 和 emit 正常工作', () => {
    const callback = jest.fn();
    eventBus.on('test', callback);
    eventBus.emit('test', { message: 'hello' });
    
    expect(callback).toHaveBeenCalledWith({ message: 'hello' });
  });

  test('多个监听器都能收到事件', () => {
    const callback1 = jest.fn();
    const callback2 = jest.fn();
    
    eventBus.on('test', callback1);
    eventBus.on('test', callback2);
    eventBus.emit('test', 'data');
    
    expect(callback1).toHaveBeenCalledWith('data');
    expect(callback2).toHaveBeenCalledWith('data');
  });

  test('off 移除监听器', () => {
    const callback = jest.fn();
    eventBus.on('test', callback);
    eventBus.off('test', callback);
    eventBus.emit('test', 'data');
    
    expect(callback).not.toHaveBeenCalled();
  });
});
```

---

## 高级技巧

### 1. 模拟 window 对象属性

```javascript
// jest.setup.js
// 在测试前设置全局变量
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});

// 模拟 IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
  constructor() {}
  disconnect() {}
  observe() {}
  unobserve() {}
};
```

### 2. 使用 jest.spyOn 监视 DOM 方法

```javascript
test('监视 console.error', () => {
  const spy = jest.spyOn(console, 'error').mockImplementation(() => {});
  
  // 执行可能报错的操作
  someFunction();
  
  expect(spy).toHaveBeenCalled();
  spy.mockRestore();
});

test('监视 fetch', async () => {
  const mockFetch = jest.spyOn(global, 'fetch').mockResolvedValue({
    json: jest.fn().mockResolvedValue({ data: 'test' }),
  });
  
  await fetchData();
  
  expect(mockFetch).toHaveBeenCalledWith('/api/data');
  mockFetch.mockRestore();
});
```

### 3. 模拟定时器

```javascript
jest.useFakeTimers();

test('测试 setTimeout', () => {
  const callback = jest.fn();
  
  setTimeout(callback, 1000);
  expect(callback).not.toHaveBeenCalled();
  
  // 快进时间
  jest.advanceTimersByTime(1000);
  expect(callback).toHaveBeenCalledTimes(1);
});

test('测试 setInterval', () => {
  const callback = jest.fn();
  
  setInterval(callback, 1000);
  jest.advanceTimersByTime(5000);
  
  expect(callback).toHaveBeenCalledTimes(5);
});

afterEach(() => {
  jest.useRealTimers();
});
```

### 4. 测试 DOM 快照

```javascript
test('DOM 结构快照', () => {
  document.body.innerHTML = `
    <div class="container">
      <h1>Hello World</h1>
      <p>Test content</p>
    </div>
  `;
  
  expect(document.body.innerHTML).toMatchSnapshot();
});
```

### 5. 自定义匹配器

```javascript
// jest.setup.js
expect.extend({
  toHaveClass(received, className) {
    const hasClass = received.classList.contains(className);
    return {
      message: () => `expected element to have class "${className}"`,
      pass: hasClass,
    };
  },
});

// 使用
test('元素有指定类名', () => {
  const div = document.createElement('div');
  div.className = 'active disabled';
  
  expect(div).toHaveClass('active');
});
```

---

## 常见问题

### Q1: localStorage/sessionStorage 报错

**问题**: `localStorage is not available for opaque origins`

**解决**: 在 jest.config.js 中设置 URL

```javascript
module.exports = {
  testEnvironmentOptions: {
    url: 'http://localhost:3000',
  },
};
```

### Q2: 无法找到模块

**问题**: `Cannot find module '@/*'`

**解决**: 配置 moduleNameMapper

```javascript
module.exports = {
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
};
```

### Q3: CSS 导入报错

**问题**: `Cannot parse CSS`

**解决**: 使用 identity-obj-proxy

```bash
npm install --save-dev identity-obj-proxy
```

```javascript
module.exports = {
  moduleNameMapper: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
};
```

### Q4: fetch API 未定义

**问题**: `fetch is not defined`

**解决**: 使用 node-fetch 或 jsdom 的 resources 选项

```bash
npm install --save-dev node-fetch
```

```javascript
// jest.setup.js
global.fetch = require('node-fetch');
```

或者升级到 JSDOM 16+:

```javascript
module.exports = {
  testEnvironmentOptions: {
    resources: 'usable',
  },
};
```

### Q5: ResizeObserver/IntersectionObserver 未定义

**解决**: 手动模拟

```javascript
// jest.setup.js
global.ResizeObserver = jest.fn().mockImplementation(() => ({
  observe: jest.fn(),
  unobserve: jest.fn(),
  disconnect: jest.fn(),
}));
```

---

## 完整的 package.json 示例

```json
{
  "name": "jest-browser-test",
  "version": "1.0.0",
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:debug": "node --inspect-brk node_modules/.bin/jest --runInBand"
  },
  "devDependencies": {
    "@babel/core": "^7.23.0",
    "@babel/preset-env": "^7.23.0",
    "babel-jest": "^29.7.0",
    "identity-obj-proxy": "^3.0.0",
    "jest": "^29.7.0",
    "jest-environment-jsdom": "^29.7.0"
  }
}
```

---

## 总结

使用 Jest + JSDOM 在 Node.js 中测试浏览器环境的优势：

| 特性 | 说明 |
|-----|------|
| 🚀 快速 | 无需启动真实浏览器 |
| 🎯 精确 | 可以精确控制 DOM 状态 |
| 🔧 灵活 | 强大的 Mock 和 Spy 能力 |
| 📊 可测性 | 容易达到高代码覆盖率 |
| 🔄 集成 | 与 CI/CD 流程无缝集成 |

**注意事项**:
- JSDOM 不是真实浏览器，某些浏览器特性可能不完全支持
- 复杂视觉效果测试仍需使用 Puppeteer/Playwright
- 网络请求通常需要 Mock

---

*文档生成时间: 2024年*
*适用于: Jest 29.x, jest-environment-jsdom 29.x*
