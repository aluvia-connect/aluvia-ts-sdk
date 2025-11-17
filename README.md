# Aluvia SDK for TypeScript

[![npm version](https://badge.fury.io/js/aluvia-ts-sdk.svg)](https://www.npmjs.com/package/aluvia-ts-sdk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-Ready-blue.svg)](https://www.typescriptlang.org)
[![Node.js](https://img.shields.io/node/v/aluvia-ts-sdk.svg)](https://nodejs.org)

The **official Aluvia SDK for TypeScript**, designed for managing your Aluvia connectivity credentials, retrieving usage data, and integrating mobile network access into automation tools, backend services, and browser environments.

This SDK works in **Node.js**, **browsers**, **serverless runtimes**, and modern build tools.

---

## Features

- **Universal** — Works in Node.js, Browser, Deno, Bun, and edge runtimes
- **TypeScript Native** — Complete typings and IntelliSense
- **Lightweight** — Zero heavy dependencies, powered by native `fetch`
- **Secure** — Built-in auth + structured error handling
- **Tested** — High test coverage
- **Documented** — Clean API with helpful examples
- **Production Ready** — Ideal for automation platforms, dashboards, and backend services

---

## Installation

```bash
npm install aluvia-ts-sdk
```

```bash
yarn add aluvia-ts-sdk
```

```bash
pnpm add aluvia-ts-sdk
```

---

## Quick Start

### Basic Setup

```typescript
import Aluvia from "aluvia-ts-sdk";

// Initialize with your API token
const aluvia = new Aluvia("your-api-token");
```

### Get Your First Credential

```typescript
try {
  const credential = await aluvia.first();

  if (credential) {
    console.log("Connection URL:", credential.toUrl());
    console.log("HTTPS URL:", credential.toUrl("https"));
    console.log("Username:", credential.username);
    console.log("Password:", credential.password);
    console.log("Host:", credential.host);
  } else {
    console.log("No credentials available. Create one first!");
  }
} catch (error) {
  console.error("Error fetching credential:", error.message);
}
```

---

## Usage Examples

### Creating Connectivity Credentials

```typescript
import Aluvia from "aluvia-ts-sdk";

const aluvia = new Aluvia("your-api-token");

try {
  // Create a single credential
  const [credential] = await aluvia.create(1);
  console.log("Created credential:", credential.toUrl());

  // Create multiple credentials
  const credentials = await aluvia.create(5);
  console.log(`Created ${credentials.length} credentials`);

  credentials.forEach((c, index) => {
    console.log(`Credential ${index + 1}: ${c.toUrl()}`);
  });
} catch (error) {
  console.error("Error:", error.message);
}
```

---

### Managing Connectivity Credentials

```typescript
// Find a specific credential by username
const credential = await aluvia.find("username123");

if (credential) {
  console.log("Found credential:", credential.toJSON());

  // Update credential settings
  await aluvia.update("username123", {
    useSticky: true,
  });

  // Retrieve usage information
  const usage = await aluvia.getUsage("username123");
  console.log(`Data used: ${usage.dataUsed} GB`);
} else {
  console.log("Credential not found");
}
```

---

### Advanced Credential Features

```typescript
const credential = await aluvia.first();

if (credential) {
  credential.useSticky = true;
  await credential.save(); // Apply changes

  // Connection URL with current settings
  const enhancedUrl = credential.toUrl();
  console.log("Connection URL:", enhancedUrl);

  console.log("Username:", credential.username);
  console.log("Password:", credential.password);
  console.log("Host:", credential.host);
  console.log("HTTP Port:", credential.httpPort);
  console.log("HTTPS Port:", credential.httpsPort);
  console.log("Sticky enabled:", credential.useSticky);

  const usage = await credential.getUsage();
  console.log(`Data used: ${usage.dataUsed} GB`);
}
```

---

### Usage Analytics

```typescript
const currentUsage = await aluvia.getUsage("username123");
console.log("Current usage:", currentUsage);

const weekAgo = Math.floor(Date.now() / 1000) - 7 * 24 * 60 * 60;
const now = Math.floor(Date.now() / 1000);

const weeklyUsage = await aluvia.getUsage("username123", {
  usageStart: weekAgo,
  usageEnd: now,
});

console.log(`Weekly data usage: ${weeklyUsage.dataUsed} GB`);
```

---

### Error Handling

```typescript
import Aluvia, {
  ApiError,
  ValidationError,
  NetworkError,
  NotFoundError,
} from "aluvia-ts-sdk";

const aluvia = new Aluvia("your-api-token");

try {
  const credential = await aluvia.first();
} catch (error) {
  if (error instanceof ValidationError) {
    console.error("Validation failed:", error.message);
  } else if (error instanceof ApiError) {
    console.error("API error:", error.message);
  } else if (error instanceof NetworkError) {
    console.error("Network error:", error.message);
  } else if (error instanceof NotFoundError) {
    console.error("Not found:", error.message);
  } else {
    console.error("Unexpected error:", error);
  }
}
```

---

## API Reference

### `Aluvia` Class

#### Constructor

```typescript
new Aluvia(token: string)
```

Parameters:

- `token`: Your Aluvia API authentication token

---

### Methods

#### `first(): Promise<Proxy | null>`

Returns the most recent connectivity credential.

#### `find(username: string): Promise<Proxy | null>`

Find a specific credential by username.

#### `create(count?: number): Promise<Proxy[]>`

Create one or more credentials.

#### `update(username: string, options: UpdateOptions): Promise<Proxy | null>`

Update a credential’s configuration (only `useSticky`).

#### `delete(username: string): Promise<void>`

Delete a credential.

#### `getUsage(username: string, options?: UsageOptions): Promise<UsageInfo>`

Retrieve usage analytics for a credential.

#### `all(): Promise<Proxy[]>`

Returns all credentials.

---

## `Proxy` Class

### Properties

- `username: string`
- `password: string`
- `host: string`
- `httpPort: number`
- `httpsPort: number`
- `useSticky: boolean`

### Methods

- `toUrl(protocol?: 'http' | 'https'): string`
- `save(): Promise<this>`
- `delete(): Promise<void>`
- `getUsage(options?: UsageOptions): Promise<UsageInfo>`
- `toJSON(): Record<string, any>`

---

## Type Definitions

```typescript
interface ProxyCredential {
  username: string;
  password: string;
  useSticky?: boolean;
  sessionSalt?: string;
}

interface ProxyConfig {
  host: string;
  httpPort: number;
  httpsPort: number;
}

interface UsageOptions {
  usageStart?: number;
  usageEnd?: number;
}

interface UsageInfo {
  usageStart: number;
  usageEnd: number;
  dataUsed: number;
}
```

---

## Testing

```bash
npm test
npm run test:watch
npm run test:coverage
```

---

## License

MIT License – see the LICENSE file for details.
