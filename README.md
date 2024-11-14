# How Does Cross-Platform Mobile App Work?


## Table of Contents

- [Flutter](#Flutter)
- [Kotlin Multiplatform](#Kotlin-Multiplatform)
- [React Native](#React-Native)

## Flutter

Flutter is an open-source UI toolkit by Google for building cross-platform apps from a single codebase. Using the Dart language, Flutter lets developers create apps for Android, iOS, web, and desktop with high performance and beautiful UIs. Key features include customizable widgets and "hot reload," which allows real-time code changes, making development faster and more efficient.

### Flutter engine

**Here is Flutter Architectural layers**

![image](https://github.com/user-attachments/assets/07dbd1c1-81b2-4870-806f-b742a6bb1478)

At the core of Flutter is the Flutter engine, which is mostly written in C++ and supports the primitives necessary to support all Flutter applications. The engine is responsible for rasterizing composited scenes whenever a new frame needs to be painted. It provides the low-level implementation of Flutter's core API, including graphics (through Impeller on iOS and coming to Android and macOS, and Skia on other platforms) text layout, file and network I/O, accessibility support, plugin architecture, and a Dart runtime and compile toolchain.

## Kotlin Multiplatform

Kotlin Multiplatform is a feature from JetBrains that enables developers to use Kotlin for building cross-platform applications. It allows you to share code across multiple platforms, including Android, iOS, web, and desktop, while still using platform-specific code where needed. By sharing common business logic and using Kotlin’s interoperability with Java and Swift, Kotlin Multiplatform helps streamline development and reduce duplicated code. It's especially popular for mobile development, where a single codebase can handle both Android and iOS, increasing efficiency and reducing maintenance.

## React Native

React Native is an open-source framework developed by Facebook that allows developers to build cross-platform mobile applications using JavaScript and React. It enables developers to write a single codebase that runs on both Android and iOS, with access to native components to create a seamless, native-like experience.
