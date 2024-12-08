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

       XML: XML Layout -> AndroidView -> Measure & Layout -> Drawing(Canvas) -> DisplayList -> OpenGL + GPU -> Display.


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

- Flutter is an open source framework for building beautiful, natively compiled, multi-platform applications from a single codebase.
- Supported by Google
- Using the Dart language, Flutter lets developers create apps for Android, iOS, web, and desktop with high performance and beautiful UIs. 
- Key features include customizable widgets and "hot reload," which allows real-time code changes, making development faster and more efficient.

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
- The Flutter portion of the app sends messages to its host, the non-Dart portion of the app, over a platform channel.
- The host listens on the platform channel, and receives the message. It then calls into any number of platform-specific APIs—using the native programming language—and sends a response back to the client, the Flutter portion of the app
- The standard platform channels use a standard message codec that supports efficient binary serialization of simple JSON-like values, such as booleans, numbers, Strings, byte buffers, and Lists and Maps of these
- Kotlin or Java on Android
- Swift or Objective-C on iOS
What about lower level of native API like Native Libraries or HLA?
 => Dart FFI: Dart mobile, command-line, and server apps running on the Dart Native platform can use the dart:ffi library to call native C APIs, and to read, write, allocate, and deallocate native memory. 
- Write C code to call native C APIs.
- Compile
    - .so for Android
    - .dylib for IOS
- load and use on code dart
  ![image](https://github.com/user-attachments/assets/2ba86864-791b-4c3b-b424-327eba6f6d8e)

Move to the next frame work

## Kotlin Multiplatform


- Kotlin Multiplatform is a technology that allows you to create applications for various platforms and efficiently reuse code across them while retaining the benefits of native programming
- Open-source technology by JetBrains
- Allows you to share code across different platforms.

### Kotlin compiles 

KMM Idea is allows you to share code across different platforms.
=> It mean in KMM project native code and and Kotlin code work together on same thread.
So how it work?

**Android**
Android mobile development has been Kotlin-first since Google I/O in 2019

    Kotlin → Bytecode Java(kolinc - Jvm ) → Dalvik bytecode (.dex)(Dx/D8/R8) -> APK → Machine Code (ART/Dalvik VM) -> CPU/GPU

Kotlinc can compile Kotlin into Bytecode Java so that other task base on Android native

 
**IOS**

- Kotlin/Native is a technology for compiling Kotlin code to native binaries which can run without a virtual machine. Kotlin/Native includes an LLVM-based backend for the Kotlin compiler and a native implementation of the Kotlin standard library.
- It is easy to include compiled Kotlin code in existing projects written in C, C++, Swift, Objective-C, and other languages. It is also easy to use existing native code, static or dynamic C libraries, Swift/Objective-C frameworks, graphical engines, and anything else directly from Kotlin/Native.
- Kotlin/Native supports the following platforms:
    - macOS
    - iOS, tvOS, watchOS
    - Linux
    - Windows (MinGW)
    - Android NDK

            Kotlin → LLVM IR(kolinc - Kotlin\Native) → Assembly(LLVM) -> Ipa ->  Machine Code(Assembler) -> CPU/GPU

** How to let Kotlin-c know and compile to correct target? **
- You should first declare a target to instruct Kotlin to compile code for that specific target. In Gradle, you declare targets using predefined DSL calls inside the kotlin {} block:

![image](https://github.com/user-attachments/assets/28ae94b5-e258-45c0-b88e-19b3112a8cbd)

![image](https://github.com/user-attachments/assets/d8d1bf71-c270-48a4-bfd1-6893f06d6c83)

### Compose multiplatform
- Declarative framework for sharing UIs across multiple platforms. Based on Kotlin Multiplatform and Jetpack Compose
- Compose Multiplatform shares most of its API with Jetpack Compose, the Android UI framework developed by Google. You can use the same APIs to build user interfaces for both Android and iOS.
- Compose Multiplatform utilizes Skia under the hood to render the UI, a powerful 2D graphics library developed by Google. 
- Skia’s versatility enables it to work seamlessly across various platforms. 
- Interestingly, Skia is also the underlying rendering engine for Flutter, Google’s cross-platform UI toolkit. 
- Jetpack Compose, Google’s UI toolkit for Android, also relies on Skia for rendering, although indirectly. 
- However, Compose Multiplatform, developed by JetBrains, employs a Kotlin multiplatform library called Skiko. 
- Skiko serves as a wrapper for the Skia library, tailored specifically for Kotlin. 
- In essence, Skiko provides the bridge between Kotlin Multiplatform and Skia, enabling developers to leverage the power of Skia for rendering UI components in their cross-platform projects seamlessly.

       Compose UI: Composition -> (?) -> Layout Phase -> Draw(Canvas + Skiko) -> Skiko + GPU -> Display.

### KMM library
- KMM support some libs which support iOS and Android targets: https://github.com/terrakok/kmp-awesome

- How to use Android only/IOS only libs on KMM project

Why Compose multiplatform is Beta?

_**If apple won't fuck it up and prevent JetBrains from developing compose support for iOS then I think it has higher chances of success than flutter and react.**_

## React Native

React Native is an open-source framework developed by Facebook that allows developers to build cross-platform mobile applications using JavaScript and React. It enables developers to write a single codebase that runs on both Android and iOS, with access to native components to create a seamless, native-like experience.
