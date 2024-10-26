# react-performance-detector

### Introduction

**`react-pd`** is a React library designed to help developers automatically detect when a user's browser is experiencing performance issues such as throttling or lag. When performance issues are detected, the application can adapt to provide a smoother, more lightweight experience for the user.

Modern websites often feature rich animations, high-resolution images, and interactive elements that can be resource-intensive, especially on older or low-powered devices. By using `react-pd`, you can detect when the user's browsing environment is struggling and adjust your UI dynamically to keep the user experience optimal, even under less-than-ideal conditions.

### Key Features

- **Frame Rate Monitoring**: Track frame rate (FPS) to identify if the user's device is struggling to keep up with animations or other tasks.
- **Long Task Detection**: Use the `PerformanceObserver` API to monitor long-running tasks that could affect responsiveness.
- **Customizable Parameters**: Easily adjust detection thresholds to suit your specific needs or let the library use its defaults.
- **React Hooks**: Provides easy integration through a `usePerformance` hook to access lagging status wherever you need in your application.
- **Fallback Handling**: You can optionally define custom behavior when the environment does not support performance detection features.

Whether you're building a highly interactive web application or an e-commerce site, `react-pd` ensures your users enjoy the best experience, regardless of their hardware capabilities or the conditions under which they browse.


## Quickstart: Basic Usage

1. Install library with `npm i react-pd` or `yarn add react-pd`
2. Use the `PerformanceProvider` and `usePerformance` Hook in your app

Example:
```tsx
import React from 'react';
import { PerformanceProvider, usePerformance } from 'react-performance-detector';

const MyComponent: React.FC = () => {
  const isLagging = usePerformance(); // Use the usePerformance hook within any child component to get a boolean (isLagging) indicating whether the user is experiencing performance issues

  return (
    <div>
      {isLagging ? ( // If isLagging is true, the application can switch to a lightweight version, reducing the load on the browser
        <div>Rendering lightweight version...</div>
      ) : (
        <div>Rendering full, animated version...</div>
      )}
    </div>
  );
};

const App: React.FC = () => {
  return (
    <PerformanceProvider>
      {/* Wrap your components with the PerformanceProvider to enable performance monitoring throughout the app or a specific section of it */}
      <MyComponent />
    </PerformanceProvider>
  );
};

export default App;
```

### Default Behavior
With the default configuration, the `react-pd` library will:
- Detect low frame rates (`fpsThreshold` of 20).
- Monitor for long tasks exceeding 50ms.
- Check performance every second (`checkInterval` of 1000ms).

## Browser Requirements

This library uses `PerformanceObserver` to detect performance issues in the browser. Please note the following:

- The library **requires** a modern browser with support for `PerformanceObserver`. (Basically [all browsers since 2015](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver) support it.) If this API is unavailable, throttling detection will be disabled, and a warning will be logged to the console.
- If you are running in a non-browser environment (e.g., SSR), the hook will log an error and disable itself.

## Handling Unsupported Environments

If your target audience includes older browsers, you can:

- Gracefully degrade features that depend on throttling detection.
- Use a polyfill where applicable (note that polyfill support for `PerformanceObserver` may be limited).

## Example with config and fallback behavior

```tsx
import React from "react";
import { usePerformanceStatus, ThrottleDetectionConfig } from "react-pd";

const MyComponent: React.FC = () => {
  const config: ThrottleDetectionConfig = {
    fpsThreshold: 20,
    longTaskThreshold: 50,
    checkInterval: 1000,
    onFeatureNotAvailable: () => {
      console.warn("Performance features are not available, running fallback behavior...");
      // Here you could disable some animations, show a fallback UI, etc.
    },
  };

  const isLagging = usePerformanceStatus(config);

  return (
    <div>
      {isLagging ? (
        <div>Rendering lightweight version...</div>
      ) : (
        <div>Rendering heavy, animated version...</div>
      )}
    </div>
  );
};

export default MyComponent;
```
