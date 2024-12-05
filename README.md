# How Does Cross-Platform Mobile App Work?

- Explain Cross-Platform compilation processs on Android/Ios device.
- How Cross-Platform render UI and connect to Native API.
- Compare between Cross-Platform vs Native App and Cross-Platform together.

## Table of Contents

- [Native](#Native)
- [Flutter](#Flutter)
- [Kotlin Multiplatform](#Kotlin-Multiplatform)
- [React Native](#React-Native)

## Native

- First we need overview android and ios platform architecture and APIs. 

### 1. Compilation Process


![image](https://github.com/user-attachments/assets/b1c4a273-e0a2-4370-9a0b-708277584116)

#### Android

    Kotlin/Java → Bytecode Java(kolinc/javac) → Dalvik bytecode (.dex)(Dx/D8/R8) -> APK → Machine Code (ART/Dalvik VM) -> CPU/GPU

#### IOS

    Swift/Object C → LLVM IR(CLang) → Assembly(LLVM) -> Ipa ->  Machine Code(Assembler) -> CPU/GPU

---

### 2. UI
- Android:

       XML: XML Layout -> LayoutInflater -> Measure & Layout -> Drawing(Canvas) -> DisplayList -> OpenGL + GPU -> Display.
   
       Compose UI: Composition -> Composition Tree -> Layout Phase -> Draw(Canvas + Skia) -> Skia + GPU -> Display.

- IOS:
    
---


### 3. Native API

#### Android

![image](https://github.com/user-attachments/assets/fedd1aad-b6a7-4b96-b40d-3d4fcf837910)

    1. Linux Kernel
    - Provides core functionalities like:
      - Security.
      - Memory management.
      - Process management.
      - Networking stack.
    - Customized for Android with **Binder IPC** and **Power Management**.
    
    2. Hardware Abstraction Layer (HAL)
    - Acts as a bridge between hardware and software.
    - Includes modules for audio, camera, sensors, Bluetooth, etc.
    
    3. Android Runtime (ART)
    - Executes apps using:
      - Ahead-of-Time (AOT) and Just-In-Time (JIT) compilation.
      - Optimized Garbage Collection (GC) for memory management.
    
    4. Native Libraries
    - Written in C/C++ to provide core features:
      - libc: Standard C library.
      - OpenGL ES: 2D/3D graphics rendering.
      - Media Framework: Audio and video processing.
      - SQLite: Database management.
    
    5. Application Framework
    - Supports app development with:
      - Activity Manager: Manages app lifecycle.
      - Content Providers: Shares data across apps.
      - Resource Manager: Handles non-code resources.
      - Notification Manager: Manages notifications.
      - View System: For UI elements.
    
    6. Applications
    - The top layer where user-facing apps run.
    - Written in **Java/Kotlin** and executed on ART.


#### IOS

![image](https://github.com/user-attachments/assets/dda75dc3-0445-4050-9cd0-2f2e814a4b27)

      1. Core OS (Kernel Layer)
      - Built on Darwin (Unix-based).
      - Manages memory, processes, system security, and hardware communication.
      
      2. Core Services
      - Provides essential APIs, including:
        - Foundation: Data and system task management.
        - Core Data: Persistent data storage.
        - CloudKit: Cloud synchronization.
        - Core Location: GPS and location services.
        - Core Bluetooth: Bluetooth management.
      
      3. Media Layer
      - Handles graphics, audio, and video using:
        - Quartz Core: Graphics and effects.
        - Metal: High-performance 2D/3D graphics.
        - AVFoundation: Audio and video processing.
        - Core Animation: Smooth UI effects.
      
      4. Cocoa Touch (Application Layer)
      - Provides frameworks for app development:
        - UIKit: User interface design.
        - Event Kit: Calendar and reminder management.
        - Push Notification Service: Notifications.
        - ARKit: Augmented reality features.
      
      5. Security
      - Ensures robust protection:
        - Secure Boot: Verifies system integrity.
        - Data Protection: Encrypts user data.
        - App Sandbox: Restricts app access to system resources.
        - Face ID/Touch ID: Biometric authentication.



---


## Flutter

Flutter is an open-source UI toolkit by Google for building cross-platform apps from a single codebase. Using the Dart language, Flutter lets developers create apps for Android, iOS, web, and desktop with high performance and beautiful UIs. Key features include customizable widgets and "hot reload," which allows real-time code changes, making development faster and more efficient.
### Dart and Dart compiler.
Dart compiles directly into machine code by Dart VM
- JIT Compilation: During execution the dart program may be compiled into native code. Flutter using it during development(hot restart, hot reload).
- AOT Compilation: The Dart code is fully compiled to machine code before execution. Production builds for optimized performance.
=> Flutter APK and ipa contain machine code.

### Flutter engine

**Here is Flutter Architectural layers**

![image](https://github.com/user-attachments/assets/07dbd1c1-81b2-4870-806f-b742a6bb1478)

- Flutter engine is The core of Flutter. Written in C++ and supports the primitives necessary to support all Flutter applications.
- The engine is responsible for rasterizing composited scenes whenever a new frame needs to be painted. It provides the low-level implementation of Flutter's core API, including graphics (through Impeller on iOS and coming to Android and macOS, and Skia on other platforms) text layout, file and network I/O, accessibility support, plugin architecture, and a Dart runtime and compile toolchain.**

**Main components**

  - Graphics Rendering
  - Text Rendering Subsystem
  - Compositor
  - Dart Runtime and Compiler
  - Input System
  - Accessibility
  - Event Loop
  - Plugin System
  - Rendering Pipeline
  - Assets and Font Management
  - Animation System
  - Internationalization

**Load time**

- The Flutter render engine is initialized as soon as the app starts.
  - On Android, this happens in the FlutterActivity or FlutterFragment.
  - On iOS, this is triggered via the FlutterViewController.
- The render engine initializes:
  - The Skia graphics library for drawing.
  - The Dart runtime to execute application logic.
  - Platform channels for native communication.

=>  That why app flutter is heavy than native app.

### Render UI
- Flutter doesn’t use native UI components; instead, it renders all components using Graphics Rendering (Skia/Impeller). 
- Until now Flutter is using Skia - an open source rendering engine developed by Google. Skia is designed to support high-speed 2D graphics rendering on multiple platforms, including Android, iOS, macOS, Windows, Linux, and even web browsers, but some feature isn't alway optimized for Flutter => Flutter need a new rendering engine that is Impeller.
- Impeller is a rendering optimization introduced by the Flutter team. Impeller is aimed at improving rendering performance, particularly on mobile devices.
- Graphics Rendering (Skia/Impeller) is part of Flutter Engine.
- How it work?

      Widget -> Widget Tree -> RenderObject -> Paint(Canvas + Impeller) -> Impeller + GPU -> Display.

- Beside that flutter support platform views(AndroidView, UiKitView) come with performance trade-offs.
---

### Native UI
 
Flutter uses platform channels to enable communication between Dart (Flutter) and native code (Android/iOS).

![image](https://github.com/user-attachments/assets/0beb340c-5d29-4733-8a1d-9ea96a89cbba)

Flutter platform channels: 
    - MethodChannel
    - EventChannel
    - BasicMessageChannel


## Kotlin Multiplatform


Kotlin Multiplatform is a feature from JetBrains that enables developers to use Kotlin for building cross-platform applications. It allows you to share code across multiple platforms, including Android, iOS, web, and desktop, while still using platform-specific code where needed. By sharing common business logic and using Kotlin’s interoperability with Java and Swift, Kotlin Multiplatform helps streamline development and reduce duplicated code. It's especially popular for mobile development, where a single codebase can handle both Android and iOS, increasing efficiency and reducing maintenance.

## React Native

React Native is an open-source framework developed by Facebook that allows developers to build cross-platform mobile applications using JavaScript and React. It enables developers to write a single codebase that runs on both Android and iOS, with access to native components to create a seamless, native-like experience.
