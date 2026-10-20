---
title: "@claude-flow/browser"
tags: ["browser", "puppeteer", "playwright", "automation", "screenshot", "pdf", "scraping", "web-testing"]
category: "@claude-flow/browser"
created: "2024-01-01"
updated: "2026-10-20"
source: 'internal'
type: 'index'
status: 'stable'
lifecycle: 'active'
---

# @claude-flow/browser

[![npm version](https://img.shields.io/npm/v/@claude-flow/browser.svg)](https://www.npmjs.com/package/@claude-flow/browser)
[![npm downloads](https://img.shields.io/npm/dm/@claude-flow/browser.svg)](https://www.npmjs.com/package/@claude-flow/browser)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![Puppeteer](https://img.shields.io/badge/Puppeteer-Enabled-green.svg)](https://pptr.dev/)
[![Playwright](https://img.shields.io/badge/Playwright-Enabled-blue.svg)](https://playwright.dev/)

---

## 💡 概述

Browser automation module for Claude Flow V3 - Puppeteer and Playwright integration for web scraping, screenshot capture, PDF generation, accessibility testing, and automated browser interactions.

---

## ⚡ 功能

- **Dual Engine Support** - Puppeteer and Playwright with unified API
- **Web Scraping** - Intelligent content extraction with selector support
- **Screenshot Capture** - Full page, element, and viewport screenshots
- **PDF Generation** - Print-ready PDFs with customization
- **Form Automation** - Form filling, submission, and validation
- **Accessibility Testing** - Automated a11y audits with axe-core
- **Performance Metrics** - Web Vitals, Core Web Vitals, and custom metrics
- **Network Interception** - Request/response monitoring and modification
- **Cookie Management** - Session persistence and cookie manipulation
- **Stealth Mode** - Evade bot detection for scraping
- **Parallel Browsing** - Multi-tab and multi-browser orchestration
- **Headless & Headful** - Both modes supported
- **Mobile Emulation** - Device emulation for responsive testing

### Browser Engines

| Engine | Best For | Notes |
|--------|----------|-------|
| **Puppeteer** | Chrome/Chromium automation, PDF, performance | Default engine |
| **Playwright** | Cross-browser (Chrome, Firefox, WebKit), a11y | Use for cross-browser testing |

---

## 📊 指标

### Performance Modes

| Mode | Memory | Speed | Features |
|------|--------|-------|----------|
| `lite` | ~50MB | Fast | Basic navigation, screenshots |
| `standard` | ~150MB | Normal | Full API, scraping, forms |
| `full` | ~300MB | Slower | All features + accessibility |

### Screenshot Options

| Format | Use Case | Quality |
|--------|----------|---------|
| PNG | General purpose, transparency | Lossless |
| JPEG | Smaller size, photos | 1-100 |
| WebP | Modern web, best compression | 1-100 |
| PDF | Documents, multi-page | Vector |

---

## 🏛️ 设计

### Quick Start

```typescript
import { Browser, createBrowser } from '@claude-flow/browser';

const browser = await createBrowser({ headless: true });

// Navigate and get content
const page = await browser.newPage();
await page.goto('https://example.com');
const title = await page.title();
const text = await page.textContent();

// Screenshot
await page.screenshot({ path: 'screenshot.png' });

await browser.close();
```

### Browser Configuration

```typescript
import { BrowserConfig, createBrowser } from '@claude-flow/browser';

const config: BrowserConfig = {
  headless: true,
  engine: 'puppeteer', // 'puppeteer' | 'playwright'
  stealth: true,
  viewport: { width: 1920, height: 1080 },
  userAgent: 'Mozilla/5.0 ...',
  timeout: 30000,
  args: ['--no-sandbox', '--disable-setuid-sandbox']
};

const browser = await createBrowser(config);
```

### Web Scraping

```typescript
import { Scraper, scrape } from '@claude-flow/browser';

// Simple one-shot scrape
const content = await scrape('https://example.com', {
  selectors: {
    title: 'h1',
    description: 'meta[name="description"]',
    links: 'a[href]'
  },
  extractText: true
});

// Advanced scraping
const scraper = new Scraper({ browser });
const articles = await scraper.extract('https://blog.example.com', {
  itemSelector: 'article',
  fields: {
    title: 'h2',
    author: '.author',
    date: 'time',
    content: '.content'
  },
  pagination: { nextSelector: '.pagination .next' },
  maxPages: 5
});

// JavaScript-rendered content
const dynamic = await scraper.waitAndExtract('https://spa.example.com', {
  waitFor: '.app-loaded', // Wait for selector
  waitTimeout: 5000
});
```

### Screenshot Capture

```typescript
import { Screenshotter, screenshot } from '@claude-flow/browser';

// Full page screenshot
await screenshot('https://example.com', 'fullpage.png', {
  fullPage: true
});

// Element screenshot
const shotter = new Screenshotter({ browser });
await shotter.capture({
  url: 'https://example.com',
  selector: '.hero-section',
  path: 'hero.png',
  format: 'png',
  clip: { x: 0, y: 0, width: 800, height: 600 }
});
```

### PDF Generation

```typescript
import { PDFGenerator, generatePDF } from '@claude-flow/browser';

// Simple PDF
await generatePDF('https://example.com', 'output.pdf');

// Customized PDF
const pdf = new PDFGenerator({ browser });
await pdf.generate({
  url: 'https://example.com',
  path: 'document.pdf',
  format: 'A4',
  landscape: false,
  margin: { top: '20mm', right: '20mm', bottom: '20mm', left: '20mm' },
  printBackground: true,
  displayHeaderFooter: true,
  headerTemplate: '<div>Header</div>',
  footerTemplate: '<div>Page <span class="pageNumber"></span></div>'
});
```

### Form Automation

```typescript
import { FormFiller, fillForm } from '@claude-flow/browser';

const filler = new FormFiller({ browser });

await filler.fill('https://example.com/form', {
  '#name': 'John Doe',
  '#email': 'john@example.com',
  '#message': 'Hello!',
  'select#country': 'US',
  'input[type="checkbox"]#agree': true
});

// Submit
await filler.submit('#submit-button');

// Check validation
const errors = await filler.getValidationErrors();
```

### Accessibility Testing

```typescript
import { A11yTester, testA11y } from '@claude-flow/browser';

// Quick test
const issues = await testA11y('https://example.com', {
  standard: 'WCAG2AA',   // 'WCAG2A' | 'WCAG2AA' | 'WCAG2AAA'
  includePasses: false,
  excludeRules: ['color-contrast']
});

// Detailed testing
const tester = new A11yTester({ browser, axeCore });
const results = await tester.analyze('https://example.com');

for (const violation of results.violations) {
  console.log(`${violation.impact}: ${violation.help} (${violation.nodes.length} occurrences)`);
}
```

### Network Interception

```typescript
import { NetworkMonitor } from '@claude-flow/browser';

const monitor = new NetworkMonitor({ page });

// Log all requests
monitor.onRequest((request) => {
  console.log(`${request.method} ${request.url}`);
});

// Log all responses
monitor.onResponse((response) => {
  console.log(`${response.status} ${response.url}`);
});

// Block specific domains
monitor.blockDomains(['google-analytics.com', 'doubleclick.net']);

// Modify headers
monitor.modifyHeaders({ 'X-Custom': 'value' });
```

### Cookie & Session Management

```typescript
import { SessionManager } from '@claude-flow/browser';

const session = new SessionManager({ browser });

// Save session
await session.save('session-1.json');

// Restore session
await session.restore('session-1.json');

// Get all cookies
const cookies = await session.getCookies();

// Set cookies
await session.setCookies([
  { name: 'session', value: 'abc123', domain: 'example.com' }
]);

// Persist across browser restarts
const browser2 = await session.newBrowserWithSession('session-1.json');
```

### Stealth Mode

```typescript
import { StealthBrowser } from '@claude-flow/browser';

const browser = await StealthBrowser.create({
  headless: true,
  evadeDetection: true,
  randomizeFingerprint: true,
  rotateUserAgent: true
});

// Navigate without detection
const page = await browser.newPage();
await page.goto('https://bot-protected-site.com');
```

### Parallel Browsing

```typescript
import { BrowserPool } from '@claude-flow/browser';

const pool = new BrowserPool({
  maxBrowsers: 5,
  maxPagesPerBrowser: 10,
  idleTimeout: 60000
});

// Parallel scraping
const sites = ['site1.com', 'site2.com', 'site3.com'];
const results = await Promise.all(sites.map(async (site) => {
  const browser = await pool.acquire();
  try {
    const page = await browser.newPage();
    await page.goto(`https://${site}`);
    return await page.textContent();
  } finally {
    await pool.release(browser);
  }
}));

await pool.close();
```

### Mobile Emulation

```typescript
import { MobileEmulator } from '@claude-flow/browser';

const emulator = new MobileEmulator({ browser });

// Emulate iPhone
const iPhone = await emulator.emulate('iPhone 15 Pro');

// Emulate specific device
await emulator.emulate({
  viewport: { width: 375, height: 812 },
  userAgent: 'Mozilla/5.0 (iPhone...)',
  deviceScaleFactor: 3,
  isMobile: true,
  hasTouch: true
});

// Emulate multiple devices
await emulator.emulateMultiple(['iPhone 15 Pro', 'iPad Pro', 'Pixel 7']);
```

### Web Vitals / Performance Metrics

```typescript
import { PerformanceMetrics } from '@claude-flow/browser';

const metrics = new PerformanceMetrics({ page });

await page.goto('https://example.com');

const vitals = await metrics.collect();
// {
//   FCP: 1500,        // First Contentful Paint (ms)
//   LCP: 2500,        // Largest Contentful Paint (ms)
//   FID: 50,          // First Input Delay (ms)
//   CLS: 0.05,        // Cumulative Layout Shift
//   TTFB: 200,        // Time to First Byte (ms)
//   TTI: 3000         // Time to Interactive (ms)
// }

const report = await metrics.generateReport();
```

---

## 📁 目录

「待补充」

---

## 🔗 资源

### Installation

```bash
# With Puppeteer (default)
npm install @claude-flow/browser puppeteer

# With Playwright (for cross-browser)
npm install @claude-flow/browser playwright

# Install browser binaries
npx playwright install
```

### Environment Variables

```bash
BROWSER_HEADLESS=true
BROWSER_ENGINE=puppeteer
BROWSER_TIMEOUT=30000
BROWSER_STEALTH=true
PUPPETEER_EXECUTABLE_PATH=/path/to/chrome
```

### Related Packages

- [@claude-flow/security](../security) - URL and path validation
- [@claude-flow/aidefence](../aidefence) - Anti-detection evasion
- [@claude-flow/testing](../testing) - Browser test utilities

### License

MIT

---

## 🚀 快速开始

```typescript
import { createBrowser, scrape, screenshot, generatePDF } from '@claude-flow/browser';

(async () => {
  // Create browser
  const browser = await createBrowser({ headless: true });

  // Quick scrape
  const content = await scrape('https://example.com', {
    selectors: { title: 'h1' }
  });
  console.log(content.title);

  // Quick screenshot
  await screenshot('https://example.com', 'screenshot.png');

  // Quick PDF
  await generatePDF('https://example.com', 'output.pdf');

  await browser.close();
})();
```

---

## ⚙️ 技术栈

- TypeScript
- Node.js
- Puppeteer (Chromium)
- Playwright (Chrome, Firefox, WebKit)
- axe-core (accessibility)
- Web Vitals (performance)