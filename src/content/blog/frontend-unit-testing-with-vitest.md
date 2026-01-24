---
title: '使用 Vitest 进行前端单元测试：从入门到实践'
description: '一份关于如何使用 Vitest 进行前端单元测试的全面指南，包括基本设置、测试编写和高级特性。'
pubDate: 'Jan 24 2026'
heroImage: '/blog-placeholder-3.jpg'
---

前端测试是确保代码质量和应用稳定性的关键环节。在众多测试工具中，Vitest 以其快速、与 Vite 完美集成以及出色的开发者体验而迅速崛起。本文将带领你从入门到实践，全面了解如何使用 Vitest 进行前端单元测试。

## 为什么选择 Vitest？

*   **快如闪电：** Vitest 基于 Vite 的 HMR (Hot Module Replacement) 机制，提供了极快的测试运行速度和即时反馈。
*   **与 Vite 完美集成：** 如果你的项目使用 Vite 构建，Vitest 可以无缝集成，无需额外的配置。
*   **丰富的特性：** 支持 Jest 兼容的 API、组件快照测试、模块模拟 (mocking)、代码覆盖率等。
*   **TypeScript 支持：** 开箱即用地支持 TypeScript。
*   **开发者体验：** 友好的命令行界面和清晰的错误报告。

## 快速入门：设置 Vitest

### 1. 安装 Vitest

首先，在你的 Vite 项目中安装 Vitest：

```bash
npm install -D vitest jsdom @testing-library/react # 如果是 React 项目
# 或者
npm install -D vitest jsdom @testing-library/vue # 如果是 Vue 项目
```

`jsdom` 是一个 DOM 环境的模拟器，用于在 Node.js 环境中运行需要浏览器 DOM 的测试。`@testing-library/react` 或 `@testing-library/vue` 是用于组件测试的推荐库。

### 2. 配置 `package.json`

在 `package.json` 中添加测试脚本：

```json
{
  "scripts": {
    "test": "vitest",
    "coverage": "vitest run --coverage"
  }
}
```

### 3. 配置 `vite.config.ts` (可选)

如果你需要自定义 Vitest 的行为，可以在 `vite.config.ts` 中添加 `test` 配置。例如，为了支持 JSX 或 TSX，你可能需要配置 `globals` 和 `environment`。

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/setupTests.ts', // 全局设置文件
  },
});
```

### 4. 创建测试设置文件 (例如 `src/setupTests.ts`)

这个文件可以用来导入一些全局的测试工具，例如 `testing-library` 的扩展匹配器。

```typescript
// src/setupTests.ts
import '@testing-library/jest-dom';
```

## 编写你的第一个单元测试

假设我们有一个简单的函数 `sum.js`：

```javascript
// src/utils/sum.js
export function sum(a, b) {
  return a + b;
}
```

为其编写测试文件 `sum.test.js`：

```javascript
// src/utils/sum.test.js
import { expect, test } from 'vitest';
import { sum } from './sum';

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});

test('adds 0 + 0 to equal 0', () => {
  expect(sum(0, 0)).toBe(0);
});
```

运行 `npm test`，你将会看到测试通过的输出。

## 组件测试 (以 React 为例)

假设我们有一个 React 组件 `Button.jsx`：

```jsx
// src/components/Button.jsx
import React from 'react';

export default function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

为其编写测试文件 `Button.test.jsx`：

```jsx
// src/components/Button.test.jsx
import React from 'react';
import { expect, test, vi } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

test('renders button with text', () => {
  render(<Button>Click Me</Button>);
  expect(screen.getByText('Click Me')).toBeInTheDocument();
});

test('calls onClick prop when clicked', () => {
  const handleClick = vi.fn(); // 创建一个 mock 函数
  render(<Button onClick={handleClick}>Click Me</Button>);
  fireEvent.click(screen.getByText('Click Me'));
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

## 模块模拟 (Mocking)

在单元测试中，我们经常需要模拟 (mock) 某些模块或函数，以隔离测试范围，确保测试的独立性。Vitest 提供了强大的模拟能力。

假设我们有一个 `api.js` 模块用于发送网络请求：

```javascript
// src/utils/api.js
export async function fetchData(url) {
  const response = await fetch(url);
  return response.json();
}
```

现在，我们有一个组件 `UserProfile.jsx` 依赖于 `fetchData`：

```jsx
// src/components/UserProfile.jsx
import React, { useEffect, useState } from 'react';
import { fetchData } from '../utils/api';

export default function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchData(`/api/users/${userId}`).then(data => setUser(data));
  }, [userId]);

  if (!user) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

为了测试 `UserProfile` 组件，我们可以模拟 `fetchData` 函数：

```jsx
// src/components/UserProfile.test.jsx
import React from 'react';
import { expect, test, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import UserProfile from './UserProfile';
import * as api from '../utils/api'; // 导入整个模块以便模拟

// 模拟 fetchData 函数
vi.mock('../utils/api', () => ({
  fetchData: vi.fn(() => Promise.resolve({ name: 'Test User', email: 'test@example.com' }))
}));

test('renders user profile after data is fetched', async () => {
  render(<UserProfile userId="123" />);

  expect(screen.getByText('Loading...')).toBeInTheDocument();

  await waitFor(() => {
    expect(screen.getByText('Test User')).toBeInTheDocument();
    expect(screen.getByText('Email: test@example.com')).toBeInTheDocument();
  });

  // 验证 fetchData 是否被调用
  expect(api.fetchData).toHaveBeenCalledWith('/api/users/123');
});
```

## 代码覆盖率

代码覆盖率可以帮助你了解测试覆盖了多少代码。Vitest 内置了对代码覆盖率的支持，通常使用 `c8` (或 `istanbul`)。

要生成覆盖率报告，只需运行 `npm run coverage`。你可以在 `vite.config.ts` 中配置覆盖率报告的输出格式和目录。

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/setupTests.ts',
    coverage: {
      reporter: ['text', 'json', 'html'], // 输出报告格式
      reportsDirectory: './coverage', // 报告输出目录
    },
  },
});
```

运行 `npm run coverage` 后，你会在项目根目录下找到一个 `coverage` 文件夹，其中包含详细的覆盖率报告。

## 总结

Vitest 是一个功能强大、速度快且易于使用的前端测试框架。凭借与 Vite 的深度集成和对 Jest API 的兼容，它为现代前端项目提供了卓越的测试体验。通过本文的介绍，你应该已经掌握了 Vitest 的基本设置、单元测试和组件测试的编写、模块模拟以及代码覆盖率的生成。现在，你可以将 Vitest 应用到你的项目中，构建更健壮、更可靠的前端应用。