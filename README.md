# How Does Cross-Platform Mobile App Work?
This document is my research about mobile cross-platform frameworks, 
in this document I will:
- Explain how cross-platform like Flutter and Kotlin Multiplatform work on each mobile platform, such as Android and iOS.
- Compare Cross-Platform vs Native App and Cross-Platform.

_Please give me some feedback if you have any concerns. 
It will help me become a better developer. :))_

Thank you so much!
## Table of Contents

- [Native](#Native)
- [Flutter](#Flutter)
- [Kotlin Multiplatform](#Kotlin-Multiplatform)
- [React Native](#React-Native)
- [Unity](#Unity)
- [Comparison summary](#Comparison-summary)

## Native

First, we will overview Android and IOS platform architecture. 

### 1. Compilation Process

**What is the Compilation Process:**

<img src="https://github.com/user-attachments/assets/b1c4a273-e0a2-4370-9a0b-708277584116" width="500"/>

- Machine Language (Binary Code): is the lowest-level programming language, consisting entirely of binary numbers (0s and 1s) that a computer's CPU can execute directly. It is the most fundamental language of computers and is specific to the architecture of the machine's processor.
- Assembly Language:  a low-level programming language that uses human-readable mnemonics (e.g., MOV, ADD, JMP) to represent machine code instructions. It is processor-specific and provides direct access to hardware resources like CPU registers and memory.
- High-Level Programming Languages: programming languages that are designed to be easy for humans to read and write.
- Compilation Process is the series of steps that a compiler performs to translate source code written in a high-level programming language (like C++, Java, or Python) into machine code (binary instructions) that a computer's processor can execute

So how to compile code on Android and IOS?

#### Android

    Kotlin/Java → Bytecode Java(kolinc/javac) → Dalvik bytecode (.dex)(Dx/D8/R8) -> APK → Machine Code (ART/Dalvik VM) -> CPU/GPU
_APK contains DEX !_
#### IOS

    Swift/Object C → LLVM IR(CLang) → Assembly(LLVM) -> Ipa ->  Machine Code(Assembler) -> CPU/GPU
_IPA contains Assembly !_


---

### 2. UI

Render UI pineline on the native app?

<img src="https://github.com/user-attachments/assets/2de12413-5099-4a52-a264-626e906fbe48" width="500"/>


- Android:

       XML: XML Layout -> AndroidView -> Measure & Layout -> Drawing(Canvas) -> OpenGL ES API + GPU -> Screen.

OpenGL ES (Open Graphics Library for Embedded Systems) is a lightweight version of OpenGL designed for embedded devices like smartphones, tablets, and gaming consoles. It provides a cross-platform API for rendering 2D and 3D graphics, optimized for low-power and resource-constrained environments. Managed by the Khronos Group, it is widely used in mobile gaming, AR/VR applications, and interactive graphical user interface

- IOS:
    
       Swift UI: View -> View Tree -> Geometry, Constraints -> Draw (Core Animation, Core Graphics) -> GPU + Metal API -> Screen.

Metal is a high-performance graphics and computing API developed by Apple, optimized for iOS, macOS, and other Apple devices.

---


### 3. Native API

Where is Native API on platform architecture?

#### Android

<img src="https://github.com/user-attachments/assets/fedd1aad-b6a7-4b96-b40d-3d4fcf837910" width="500"/>

- Java API Framework
- Native C++ Libraries
- HAL
- Third-party libraries

#### IOS

<img src="https://github.com/user-attachments/assets/dda75dc3-0445-4050-9cd0-2f2e814a4b27" width="500"/>

- Cocoa/Cocoa Touch
- Media
- Core service
- Third-party libraries


---


## Flutter

- Flutter is an open source framework for building beautiful, natively compiled, multi-platform applications from a single codebase.
- Supported by Google
- Using the Dart language, Flutter lets developers create apps for Android, iOS, web, and desktop with high performance and beautiful UIs. 
- Key features include customizable widgets and "hot reload," which allows real-time code changes, making development faster and more efficient.

### Dart and Dart compiler.

Dart compiles directly into machine code by Dart VM
- JIT Compilation: During execution the dart program may be compiled into native code. Flutter using it during development(hot restart, hot reload).
- AOT Compilation: The Dart code is fully compiled into machine code before execution. Production builds for optimized performance.

=> Flutter APK and Ipa contain machine code.
=> Flutter app is usually heavier than a native app.

### Flutter engine

**Here are Flutter Architectural layers**

<img src="https://github.com/user-attachments/assets/07dbd1c1-81b2-4870-806f-b742a6bb1478" height="500"/>

<img src="https://github.com/user-attachments/assets/12515791-c0a8-4b81-a030-f4f833747011" height="500"/>

- Flutter engine is The core of Flutter. Written in C++ and supports the primitives necessary to support all Flutter applications.
- The engine is responsible for rasterizing composited scenes whenever a new frame needs to be painted. It provides the low-level implementation of Flutter's core API, including graphics (through Impeller on iOS and coming to Android and macOS, and Skia on other platforms) text layout, file and network I/O, accessibility support, plugin architecture, and Dart runtime and compile toolchain.**

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

### Render UI

- Flutter doesn’t use native UI components, instead, it renders all components using Graphics Rendering (Skia/Impeller). 
- Until now Flutter is using Skia - an open-source rendering engine developed by Google. 
- Skia is designed to support high-speed 2D graphics rendering on multiple platforms, including Android, iOS, macOS, Windows, Linux, and even web browsers, but some feature isn't alway optimized for Flutter => => Flutter need a new rendering engine that is Impeller.
- Impeller is a rendering optimization introduced by the Flutter team. Impeller is aimed at improving rendering performance, particularly on mobile devices.
- Graphics Rendering (Skia/Impeller) is part of Flutter Engine.
- How does it work?

      Widget -> Widget Tree -> RenderObject -> Paint(Canvas + Impeller) -> Impeller + GPU -> Display.

- Android: Impeller supports both Vulkan and Open GL ES rendering on Android. Impeller will prefer rendering with Vulkan and fallback to OpenGL ES 2.0 for compatibility
- Ios: Metal API

---

### Native UI
 
Flutter uses platform channels to enable communication between Dart (Flutter) and native code (Android/iOS).

![image](https://github.com/user-attachments/assets/0beb340c-5d29-4733-8a1d-9ea96a89cbba)

Flutter platform channels: 
    - MethodChannel
    - EventChannel
    - BasicMessageChannel

- The Flutter portion of the app sends messages to its host, the non-Dart portion of the app, over a platform channel.
- The host listens on the platform channel and receives the message. It then calls into any number of platform-specific APIs—using the native programming language—and sends a response back to the client, the Flutter portion of the app
- The standard platform channels use a standard message codec that supports efficient binary serialization of simple JSON-like values, such as booleans, numbers, Strings, byte buffers, and Lists and Maps of these
- Kotlin or Java on Android
- Swift or Objective-C on iOS
What about a lower level of native API like Native Libraries or HLA?
 => Dart FFI: Dart mobile, command-line, and server apps running on the Dart Native platform can use the dart:ffi library to call native C APIs, and to read, write, allocate, and deallocate native memory. 
- Write C/C++ module.
- Compile
    - .so for Android
    - .dylib for IOS
- load and use on code dart

  ![image](https://github.com/user-attachments/assets/2ba86864-791b-4c3b-b424-327eba6f6d8e)

Move to the next frameworks

## Kotlin Multiplatform


- Kotlin Multiplatform is a technology that allows you to create applications for various platforms and efficiently reuse code across them while retaining the benefits of native programming
- Open-source technology by JetBrains
- Allows you to share code across different platforms.

### Kotlin compiles 

KMM Idea allows you to share code across different platforms.
=> It means in KMM project native code and Kotlin code works together.
So how does it work?

**Android**
- Android mobile development has been Kotlin-first since Google I/O in 2019
- Kotlinc can compile Kotlin into Bytecode Java so that other tasks based on Android native

        Kotlin → Bytecode Java(kolinc - Jvm ) → Dalvik bytecode (.dex)(Dx/D8/R8) -> APK → Machine Code (ART/Dalvik VM) -> CPU/GPU

Java code can be naturally called from Kotlin, and Kotlin code can be used from Java rather smoothly as well

 
**IOS**

- Kotlin/Native is a technology for compiling Kotlin code to native binaries that can run without a virtual machine. Kotlin/Native includes an LLVM-based backend for the Kotlin compiler and a native implementation of the Kotlin standard library.
- It is easy to include compiled Kotlin code in existing projects written in C, C++, Swift, Objective-C, and other languages. It is also easy to use existing native code, static or dynamic C libraries, Swift/Objective-C frameworks, graphical engines, and anything else directly from Kotlin/Native.
- Kotlin/Native supports the following platforms:
    - macOS
    - iOS, tvOS, watchOS
    - Linux
    - Windows (MinGW)
    - Android NDK

            Kotlin → LLVM IR(kolinc - Kotlin\Native) → Assembly(LLVM) -> Ipa ->  Machine Code(Assembler) -> CPU/GPU

Kotlin/Native provides bidirectional interoperability with Swift/Objective-C. You can both use Objective-C frameworks and libraries in Kotlin code, and Kotlin modules in Swift/Objective-C code.


** How do we let Kotlin-c know and compile it to the correct target? **
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

       Compose UI: Composition -> Composition Tree -> Layout Phase -> Draw(Canvas + Skiko) -> Skiko + GPU -> Display.



## React Native

React Native is an open-source framework developed by Facebook that allows developers to build cross-platform mobile applications using JavaScript and React. It enables developers to write a single codebase that runs on both Android and iOS, with access to native components to create a seamless, native-like experience.

- Open-source, supported by Meta (Facebook).
- Uses JavaScript + React (JSX) as the development language.
- Unlike Flutter, React Native does not draw its own widgets — it maps React components to **real native UI components** (UIView on iOS, android.view.View on Android).

### JavaScript Engine and how JS code runs on mobile

Unlike Flutter (Dart → machine code) or Kotlin Multiplatform (Kotlin → machine code), React Native does **not** compile JS to machine code ahead of time. Instead, it ships a JavaScript bundle inside the app and executes it on device using an embedded JavaScript engine.

**The bundling pipeline (Metro)**

- [Metro](https://metrobundler.dev/) is React Native's JavaScript bundler. It takes the entry file (typically `index.js`), resolves every `import`/`require`, transforms JSX/TypeScript via Babel, and produces a single JavaScript bundle.
- In development, Metro serves the bundle over HTTP and supports Fast Refresh.
- In release builds, the bundle is packaged inside the APK (`assets/index.android.bundle`) or IPA (`main.jsbundle`).

**JavaScript engines used by React Native**

- **[JavaScriptCore (JSC)](https://developer.apple.com/documentation/javascriptcore):** the historical default. JSC is Apple's open-source JavaScript engine that ships with iOS (used by Safari). On Android, React Native bundles its own copy of JSC. JSC interprets and JIT-compiles JS at runtime — but note that on iOS, third-party apps are not allowed to JIT (the OS forbids allocating executable memory at runtime for security reasons), so JSC runs in interpreter mode there, which hurts startup and runtime performance. This security restriction is precisely why a bytecode-first engine like Hermes pays off so much on iOS.
- **[Hermes](https://hermesengine.dev/):** an open-source JS engine built by Meta specifically for React Native. Default since React Native 0.70 (Sep 2022). Key differences from JSC:
  - **Ahead-of-Time (AOT) compilation:** JS source is compiled to **Hermes Bytecode (`.hbc`)** at *build time*, not at runtime. The app ships bytecode, not JS text.
  - **No JIT:** Hermes is a bytecode interpreter optimized for fast startup and low memory, accepting slower steady-state throughput as a trade-off — a sensible trade on mobile where TTI (time-to-interactive) matters more than long-running benchmarks.
  - **Smaller heap, smaller app size, faster TTI**, and identical behavior on iOS and Android (no JIT-mode mismatch).

**Compilation / packaging pipeline**

      JS/JSX → Babel → Metro bundler → JS bundle
                                        ├─ (JSC path)   .jsbundle  → APK/IPA → interpreted (iOS) / JIT (Android) by JSC at runtime
                                        └─ (Hermes path) hermesc → .hbc  → APK/IPA → executed by Hermes interpreter at runtime

So:

- An RN APK/IPA contains either a **plain JS bundle** (JSC) or **Hermes bytecode** (Hermes), plus the engine binary itself.
- The engine is what actually runs your code on device — there is no Dart-VM-style native compilation of app logic.

### Old Architecture — The Bridge

Before React Native 0.68, all communication between JavaScript and the native side went through a single component called **the Bridge**.

**Three threads**

A classic RN app runs on three main threads:

- **JS Thread:** runs the JavaScript engine (JSC/Hermes). Executes your React code, business logic, and dispatches updates.
- **Shadow Thread (Layout Thread):** runs **Yoga**, a cross-platform Flexbox layout engine written in C/C++. It computes the size and position of every node off the main thread, then ships the result to the UI thread.
- **Main Thread (UI / Native Thread):** the platform's UI thread. Creates and updates real native views (UIView on iOS, android.view.View on Android), handles touch events, runs platform animations.

This separation is the reason a React Native app stays responsive even while heavy JS work is running — the UI thread is not blocked by the JS thread.

**How the Bridge works**

- The Bridge is an **asynchronous, batched, serialized** message channel between JS and Native.
- Every call across the boundary (e.g. `fetch`, UI mutations dispatched by the UIManager after reconciliation, calling `CameraModule.takePicture()`) is:
  1. Serialized to **JSON**.
  2. Placed on a message queue.
  3. Delivered asynchronously to the other side, where it is deserialized and dispatched.
- Messages flow in both directions:
  - JS → Native: invoking a Native Module method, sending UI mutations to the UI Manager.
  - Native → JS: events (touch, network, sensors), callbacks, results of native calls.

**Layout flow (Old Architecture)**

      React component tree (JS thread)
              │  setState / re-render
              ▼
      React Element Tree (Virtual DOM)
              │  serialize commands → JSON → Bridge
              ▼
      Yoga layout engine (Shadow thread, C++)
              │  computed frames
              ▼
      UI Manager (Main thread)
              │  create / update
              ▼
      Native views (UIView / android.view.View) → drawn by platform

**Limitations of the Bridge**

These limitations are what motivated the New Architecture:

- **Asynchronous only.** JS cannot synchronously read a value from native (e.g. measure a view, get a layout property). Every interaction is a round-trip on the queue.
- **JSON serialization overhead.** Large payloads (big lists, images, animated values) are expensive to encode/decode on every frame.
- **Single bottleneck.** All cross-boundary traffic shares one queue — a chatty module can starve the UI updates.
- **Eager module loading.** All Native Modules are initialized at app startup, even ones the user may never trigger, increasing TTI.
- **No type safety.** The Bridge contract is dynamic; mismatches between JS and native signatures only surface at runtime.

### New Architecture — JSI, Fabric, TurboModules

Starting with React Native 0.68 (opt-in) and becoming the default in 0.76 (Oct 2024), the Bridge is replaced by a stack built around **JSI**. The async JSON queue is gone — JS and native code now share C++ objects directly.

#### JSI — JavaScript Interface

- **JSI** is a lightweight, **engine-agnostic C++ API** that gives the JS runtime (Hermes, JSC, or any future engine) a way to expose native C++ objects as JavaScript values.
- These exposed objects are called **HostObjects** — from JS they look and behave like normal JS objects, but every property read/method call is implemented in C++.
- This unlocks the things the Bridge could not do:
  - **Synchronous** JS ↔ Native calls (no message queue, no JSON).
  - **Direct memory sharing** (e.g. an `ArrayBuffer` backed by native memory — useful for images, audio, ML tensors).
  - **Lazy** instantiation — native objects are created only when JS first touches them.

#### Fabric — the new renderer

Fabric is the new rendering system, sitting on top of JSI. The major differences from the old UI Manager:

- The **Shadow Tree is built in C++**, not on the JS side. It is shared between threads via immutable data structures, so renders can happen concurrently without locking.
- React (JS) calls into Fabric **synchronously** via JSI to mount/update views — no JSON round-trip.
- Layout (Yoga) still runs in C++, but now lives in the same process and same tree as the rendering pipeline, so there's no serialization between layout and render.
- Updates are **priority-based** and integrate with React 18's **concurrent rendering** (e.g. urgent touch updates can interrupt a lower-priority re-render).
- Better interop with native gesture/animation systems because everything is reachable synchronously from JS.

**Fabric render pipeline**

      React render (JS) ──JSI──► C++ Shadow Tree ──► Yoga layout ──► Mount instructions ──► Native views (UIView / android.view.View)

#### TurboModules — the new Native Modules

- TurboModules are Native Modules rewritten on top of JSI.
- Key wins over the old Native Modules:
  - **Lazy-loaded:** a module is only initialized the first time JS imports it — improves app startup.
  - **Synchronous calls** when needed (e.g. reading a settings value), still async when appropriate.
  - **Type-safe**: the JS ↔ Native contract is generated, not hand-written.

#### Codegen

- **Codegen** is a build-time tool that reads your **TypeScript or Flow** type definitions for a TurboModule / Fabric component and generates the matching **C++, Java, and Objective-C** interface code.
- Benefits:
  - The compiler catches signature mismatches between JS and native at build time, not at runtime.
  - Less boilerplate for module authors.
  - The generated C++ glue is the bridge between JSI HostObjects and your platform-specific implementation.

#### Putting it together

      ┌────────────────────────────┐
      │  JavaScript (React, JSX)   │
      └──────────────┬─────────────┘
                     │  JSI  (sync, no JSON)
      ┌──────────────▼─────────────┐
      │   C++ core (shared)        │
      │   - Fabric renderer        │
      │   - Yoga layout            │
      │   - TurboModule registry   │
      └──────┬──────────────┬──────┘
             │              │
      ┌──────▼─────┐ ┌──────▼─────┐
      │  iOS       │ │  Android   │
      │  UIView /  │ │  View /    │
      │  ObjC/Swift│ │  Java/Kotlin│
      └────────────┘ └────────────┘

### Render UI

This is the single most important architectural difference between React Native and Flutter:

- **Flutter** draws everything itself with Skia/Impeller — a `Container` is *not* an Android `View`, it is pixels on a canvas controlled by Flutter.
- **React Native** does **not** draw its own widgets. Each React component is mapped to a **real native UI component**. A `<View>` becomes a `UIView` on iOS and an `android.view.ViewGroup` on Android. A `<Text>` becomes a `UILabel` / `TextView`. A `<ScrollView>` becomes a `UIScrollView` / `ScrollView`.

The consequence is that React Native apps inherit native look, feel, accessibility, and platform behavior **for free** — but the cost is the JS ↔ Native boundary, which any cross-platform UI work has to cross.

**Component → native view mapping (examples)**

| React Native           | iOS                    | Android                  |
|------------------------|------------------------|--------------------------|
| `<View>`               | `UIView`               | `android.view.ViewGroup` |
| `<Text>`               | `UILabel` (RCTText)    | `TextView`               |
| `<Image>`              | `UIImageView`          | `ImageView`              |
| `<ScrollView>`         | `UIScrollView`         | `ScrollView`             |
| `<TextInput>`          | `UITextField` / `UITextView` | `EditText`         |
| `<ActivityIndicator>`  | `UIActivityIndicatorView` | `ProgressBar`         |

**Rendering pipeline (New Architecture / Fabric)**

      JSX
       │  React reconciler
       ▼
      React Element Tree (JS, virtual DOM-like)
       │  JSI (synchronous)
       ▼
      C++ Shadow Tree                ◄── shared, immutable, thread-safe
       │
       ▼
      Yoga (Flexbox layout in C++)   ◄── computes frames
       │
       ▼
      Mount layer                    ◄── diff against currently mounted tree
       │
       ▼
      Native view hierarchy
       │
       ▼
      Platform draw
       ├─ Android:  Canvas → OpenGL ES / Vulkan via Skia → GPU
       └─ iOS:      Core Animation / Core Graphics → Metal → GPU
       │
       ▼
      Screen

A few things worth noting:

- The **drawing** step is exactly the same as a fully native app — React Native is not in that path. Once the views are mounted, the OS owns rendering.
- Layout (**Yoga / Flexbox**) is what gives React Native its single cross-platform layout model, even though the views themselves are different on each OS.
- In the **Old Architecture**, the Shadow Tree was diffed in JS and mutations were sent through the Bridge as JSON; under **Fabric** the tree lives in C++ and mutations are applied synchronously, which is why animations and gesture-driven UIs are noticeably smoother on the New Architecture.

### Native API — Native Modules

React Native ships with a useful set of components and APIs, but it cannot cover every platform feature (camera, Bluetooth, biometrics, push notifications, deep sensors, etc.). For anything not built-in, JS reaches the platform through a **Native Module**.

A Native Module is the React Native equivalent of Flutter's platform channels and KMP's `expect`/`actual`: a typed boundary between JS and platform-specific code.

#### Native Modules (Old Architecture)

- Written in **Java/Kotlin** on Android and **Objective-C/Swift** on iOS.
- Registered with the React Native runtime; methods are then callable from JS as a normal JS object.
- Communication goes through the **Bridge**: every call is serialized to JSON and dispatched asynchronously.
- All Native Modules are initialized at app startup.

Conceptual flow:

      JS code: NativeModules.Camera.takePicture()
               │  serialize args → JSON
               ▼
            Bridge (queue, async)
               │  deserialize
               ▼
      iOS:    CameraModule.m  (ObjC/Swift)  →  AVFoundation
      Android: CameraModule.kt (Kotlin/Java) →  Camera2 / CameraX

#### TurboModules (New Architecture)

Same idea, but rebuilt on **JSI**:

- The native module is exposed to JS as a **JSI HostObject** — JS holds a direct reference to a C++ object, no Bridge, no JSON.
- **Synchronous** calls become possible (e.g. read a value from native and use it in the same JS tick).
- **Lazy loading:** a TurboModule is not constructed until the first JS access — fewer things happen at startup, faster TTI.
- **Codegen** reads your TS/Flow spec for the module and generates the C++/Java/ObjC glue, so signature mismatches between JS and native are caught at build time.

      JS: TurboModuleRegistry.get('Camera').takePicture()
                │  JSI (direct C++ call)
                ▼
            C++ glue (Codegen-generated)
                │
                ▼
      iOS / Android native implementation

#### Going lower: JSI HostObjects and Fabric Components

For libraries that need deeper integration (high-performance graphics, ML inference, custom UI primitives), two more escape hatches exist:

- **Custom JSI HostObjects:** any C++ object can be exposed straight to JS through JSI. Used by libraries like *react-native-mmkv* (storage), *react-native-reanimated* (animations driven entirely on the UI thread), and *VisionCamera* (frame processors).
- **Fabric Native Components:** custom native views (e.g. a map renderer, a video player) authored directly against the Fabric renderer, with Codegen-generated props and event handlers.

#### Summary of "how JS talks to native"

| Mechanism                 | Architecture | Sync? | Loading        | Typical use                            |
|---------------------------|--------------|-------|----------------|----------------------------------------|
| Native Modules (Bridge)   | Old          | No    | Eager          | Legacy access to platform APIs         |
| TurboModules (JSI)        | New          | Yes   | Lazy           | Modern access to platform APIs         |
| Custom JSI HostObjects    | New          | Yes   | Lazy / on-demand | High-perf libs (storage, animations) |
| Fabric Native Components  | New          | Yes   | On mount       | Custom native UI views                 |

## Unity

- Unity is a **real-time engine** developed by Unity Technologies, primarily known as a **game engine** but also used for AR/VR, automotive HMI, architecture visualization, simulation, and animation/film.
- Cross-platform: Android, iOS, Windows, macOS, Linux, WebGL, PlayStation, Xbox, Nintendo Switch.
- User code is written in **C#**; the engine core is written in **C/C++**.
- Important framing for this document: Unity is **a game engine first**, not a typical mobile app framework. While React Native and KMP (Native UI) integrate into the host platform's UI system (`Activity` / `UIViewController`), Unity (and to an extent Flutter) **takes over the entire surface** and renders its own pixels straight to the GPU. Crucially, Unity runs a **continuous frame loop** instead of an event-driven UI loop.

### C# and the Scripting Backend

Unity does not compile your C# straight to machine code. C# first becomes .NET **Intermediate Language (IL)**, and what happens next depends on which **Scripting Backend** you target. Unity ships two backends, and they exist for very different reasons.

**Shared front-end (both backends)**

Regardless of the backend, every Unity build starts with the same steps:

1. **[Roslyn](https://github.com/dotnet/roslyn)** (the official C# compiler) compiles every `.cs` file into IL inside `.dll` assemblies — exactly like a normal .NET application.
2. **Assembly stripping (managed code stripping)** scans the IL and removes classes/methods that the game never calls, including big chunks of the .NET base class library you didn't use. This is critical on mobile because the full .NET surface is large.
3. From here the two backends diverge.

**Mono — the JIT backend**

- Mono is an open-source implementation of the .NET runtime that Unity has shipped with for years.
- It executes IL **Just-In-Time (JIT)**: at runtime the Mono VM translates IL into machine code on-the-fly.
- Mono is what the **Editor** uses (so Play Mode can hot-reload scripts) and what most **development builds** use.
- Pros: fast iteration, smaller build, supports runtime code generation.
- Cons: slower steady-state performance than native, and not allowed on iOS (Apple forbids JIT in App Store apps) or on most consoles.

**IL2CPP — the AOT backend**

- IL2CPP (*Intermediate Language To C++*) is Unity's proprietary **Ahead-of-Time** toolchain.
- Instead of running IL at runtime, IL2CPP converts the IL into **C++** source code at build time. Unity's IL2CPP toolchain reads each `.dll` and emits equivalent C++ that calls into a small C++ runtime (`libil2cpp`) for things like GC and reflection.
- The generated C++ is then handed to the platform's native toolchain (NDK/Clang on Android, Clang/LLVM on iOS) and compiled to machine code.
- IL2CPP is **mandatory** for iOS (no JIT possible) and for **64-bit Android** binaries on the Play Store (a Google Play submission requirement since 2019).
- Pros: native-level performance, smaller startup overhead, better code stripping, harder to reverse-engineer than IL.
- Cons: longer build times, no runtime code generation (so dynamic features like `System.Reflection.Emit` don't work).

**Pipeline overview**

      Your C# scripts (.cs)
              │
              ▼
        Roslyn compiler
              │
              ▼
          IL (.dll)
              │
              ▼
      Assembly stripping
              │
        ┌───────────┴───────────────────┐
        ▼                               ▼
   Mono backend                    IL2CPP backend
   JIT compile IL at runtime       IL → C++ → native machine code (at build time)
   (Editor, dev builds, some       (iOS always, 64-bit Android,
    Android builds)                 consoles)

#### Android

**Build pipeline (IL2CPP path)**

1. C# scripts → Roslyn → IL `.dll`.
2. **IL2CPP** converts the IL into C++ source.
3. The generated C++ is compiled together with the Unity engine's own C++ sources by the **Android NDK / Clang** into native shared libraries (`.so`) for each target ABI (typically `arm64-v8a`, sometimes `armeabi-v7a`):
   - `libunity.so` — the engine core itself.
   - `libil2cpp.so` — the IL2CPP runtime (GC, reflection helpers, etc.).
   - `libmain.so` — a small launcher that ties them together.
4. Unity emits a **Gradle Android project** containing:
   - The `.so` libraries above (under `lib/<abi>/`).
   - Game **Assets** — textures, models, audio — packed into Unity's binary asset format under `assets/bin/Data/`.
   - `AndroidManifest.xml` — declares `UnityPlayerActivity` as the launcher activity.
   - Any third-party Android plugins (`.aar` / `.jar`).
5. Gradle assembles, signs, and produces the **APK** (install directly) or **AAB** (upload to Play Store).

**Runtime**

When the user taps the icon:

1. Android starts the launcher activity defined in the manifest — `UnityPlayerActivity` (a normal Java/Kotlin `Activity`).
2. The activity calls `System.loadLibrary("unity")`, which loads `libunity.so` and the IL2CPP libraries into the process.
3. Through **JNI (Java Native Interface)**, control is handed to the C++ engine. Unity attaches its own `SurfaceView` to the activity's window — from this point on, the engine owns the surface.
4. The engine starts the **game loop**: `Input → Update (C# user code via IL2CPP) → Physics → Render (Vulkan / OpenGL ES) → repeat`.
5. When user C# code needs to call an Android API (e.g. show a toast, read sensor data), it goes the other direction through JNI: C# → `AndroidJavaObject` → Java/Kotlin → Android SDK.

      C# → IL (.dll) → C++ (IL2CPP) → .so (NDK / Clang) → APK/AAB → UnityPlayerActivity → JNI → libunity.so → game loop

#### iOS

iOS has one important constraint: **Apple does not allow JIT compilation in App Store apps**. That means **Mono is not an option** — every iOS build must use **IL2CPP**.

**Build pipeline — Stage A: Unity generates an Xcode project**

Unity does not produce an `.ipa` directly. Instead it produces a **full Xcode project** that you (or your CI) then build on macOS:

1. C# scripts → Roslyn → IL `.dll`.
2. **IL2CPP** converts the IL into thousands of C++ source files (`.cpp` / `.h`).
3. Unity writes out an Xcode project containing:
   - The IL2CPP-generated C++.
   - The Unity engine's own C++ sources / pre-compiled libraries.
   - Game **Assets** (Unity binary asset format).
   - `main.mm` (an Objective-C++ entry point) and the `UnityAppController` boilerplate.
   - Any iOS native plugins you've added (`.framework`, `.a`, `.m`, `.mm`, `.swift`).

**Build pipeline — Stage B: Xcode produces the IPA**

This stage runs on macOS using the standard Apple toolchain:

1. **Clang / LLVM** compiles the entire pile of C++ to ARM64 machine code.
2. The linker groups the output into two artifacts:
   - **`UnityFramework.framework`** — a dynamic library containing the engine + your IL2CPP-compiled game code + the asset loader. This is where the bulk of the binary lives.
   - **`Unity-iPhone`** — a thin launcher executable whose only real job is to load `UnityFramework` and hand off control.
3. The app is **code-signed** using your Apple Developer certificate and a Provisioning Profile.
4. Xcode packages everything into an **`.ipa`** for distribution.

**Runtime**

1. iOS launches the `Unity-iPhone` executable. Execution enters `main.mm`.
2. `main.mm` calls `UnityFrameworkLoad()`, which loads `UnityFramework.framework` into memory.
3. The framework instantiates **`UnityAppController`** — a `UIApplicationDelegate` subclass — and the OS hands the standard `application:didFinishLaunchingWithOptions:` lifecycle to it.
4. `UnityAppController` creates the window, attaches Unity's **`CAMetalLayer`** (or a `MTKView`) as the rendering surface, and starts the engine.
5. The engine starts the **game loop**: `Input → Update (C# user code via IL2CPP) → Physics → Render (Metal) → repeat`.
6. When user C# code needs to call an iOS API (e.g. read GPS, open a URL), it goes through `[DllImport("__Internal")]` (P/Invoke) into Objective-C / Swift functions you've added to the Xcode project.

      C# → IL (.dll) → C++ (IL2CPP) → Xcode project → Clang/LLVM → ARM64 → UnityFramework.framework + Unity-iPhone → IPA → main.mm → UnityFrameworkLoad → UnityAppController → game loop

### Render UI

Unity does **not** map components to native UI like React Native, nor does it embed itself inside a Flutter-style `FlutterView` next to other native views. Unity **takes over a full drawing surface and renders every pixel itself**, all the way from the 3D scene down to the on-screen buttons.

**Where Unity draws**

- Android: a **`SurfaceView`** (or `TextureView`) attached to `UnityPlayerActivity`. The `Surface` is given to the engine's C++ side, which initializes a Vulkan or OpenGL ES context on it.
- iOS: a **`CAMetalLayer`** (typically wrapped in `MTKView`), owned by `UnityAppController`. The engine renders to this layer through Metal.

In both cases the entire visible content of the app is a single GPU surface drawn by Unity. The OS does not lay out any widgets inside it.

**Render Pipelines**

Unity ships three rendering pipelines and you pick one per project:

- **Built-in Render Pipeline (BRP)** — the original, simple, harder to customize.
- **URP — Universal Render Pipeline** — the modern default for mobile/AR/VR. Performance-tuned for limited GPUs.
- **HDRP — High Definition Render Pipeline** — desktop/console-class fidelity. Not for mobile.

URP is what almost every modern mobile Unity project uses.

**Graphics APIs by platform**

| Platform | Primary API | Fallback |
|----------|-------------|----------|
| Android  | **Vulkan**  | OpenGL ES 3.x |
| iOS      | **Metal**   | (no fallback — OpenGL ES is deprecated on iOS) |

**The rendering pipeline (per frame)**

      Scene (GameObjects with Mesh/Skinned/Sprite/Particle renderers + Cameras)
              │
              ▼
      Culling                ◄── frustum + occlusion: skip what each camera can't see
              │
              ▼
      Batching               ◄── static / dynamic / SRP batching: merge draw calls
              │
              ▼
      Sort draw calls        ◄── front-to-back for opaques, back-to-front for transparents
              │
              ▼
      Build CommandBuffer    ◄── shader bindings, uniforms, textures, draw commands
              │
              ▼
      Graphics API           ◄── Vulkan / OpenGL ES on Android, Metal on iOS
              │
              ▼
      GPU                    ◄── vertex shader → rasterizer → fragment shader → blend
              │
              ▼
      Screen

**What about UI (buttons, HUD, text)?**

Unity has its own UI system — these are *not* `UIButton` or `android.widget.Button`. They are GameObjects rendered by the same pipeline above:

- **UGUI (Canvas-based)** — the classic Unity UI. Every UI element lives under a `Canvas`, gets rendered as quads with `Image` / `Text` / `TextMeshPro` components.
- **UI Toolkit** — a newer, web-inspired system using UXML (markup) + USS (styles), retained-mode, better for editor tools and complex UI.
- **IMGUI** — immediate-mode, used mainly for Editor tooling.

**Consequence**

Because Unity draws everything itself:

- **Pro:** pixel-identical rendering across every platform; full GPU control; can use custom shaders for anything.
- **Con:** does not inherit native look & feel, accessibility, IME behavior, or platform UI conventions — you reimplement those if you need them.

### Native API

Unity ships a large standard API (rendering, physics, input, audio, networking, file I/O), but for anything platform-specific — push notifications, IAP, ARKit/ARCore, AdMob, BLE, the camera, deep system services — your C# code has to reach into the platform. The mechanisms are different on each side.

#### Android — JNI

C# in Unity talks to Android Java/Kotlin code through **JNI (Java Native Interface)**. Unity wraps JNI in two helper classes you use from C#:

- **`AndroidJavaClass`** — represents a Java class (static methods / fields).
- **`AndroidJavaObject`** — represents a Java instance (constructor + instance methods).

Typical Android plugin formats:

- **`.jar`** — compiled Java classes.
- **`.aar`** — Android Archive: Java/Kotlin code + resources + an `AndroidManifest.xml`. The standard format.

Calls flow like this:

      C# code in Unity
          │  AndroidJavaObject / AndroidJavaClass
          ▼
      libunity.so  ──JNI──►  Java/Kotlin code in your .aar / Android SDK
                                  │
                                  ▼
                          Android Framework APIs (Camera2, Bluetooth, etc.)

#### iOS — P/Invoke and Objective-C++

iOS plugins are not invoked through anything as ceremonial as JNI. Instead Unity uses the standard .NET **P/Invoke** mechanism: `[DllImport]`.

- C# declares an external function:
  - `[DllImport("__Internal")] static extern void MyNativeFunc();`
  - The special name `"__Internal"` tells the runtime: "this symbol is statically linked into the main binary" — which is how all iOS plugins are shipped (no dynamic loading of arbitrary code, by Apple's rules).
- The matching symbol is implemented in **Objective-C / Objective-C++ / Swift** files (`.m`, `.mm`, `.swift`) that Unity drops into the generated Xcode project.
- For richer interop (callbacks, structs, classes), authors typically write **Objective-C++ (`.mm`)** wrappers that bridge between the C ABI required by P/Invoke and the Objective-C / Swift APIs of iOS.

iOS plugin formats:

- Source files (`.m`, `.mm`, `.swift`) placed under `Assets/Plugins/iOS/`.
- Static libraries (`.a`).
- **`.framework`** / **`.xcframework`** — the modern packaging format.

Calls flow like this:

      C# code in Unity
          │  [DllImport("__Internal")]
          ▼
      UnityFramework  ──P/Invoke──►  Objective-C / Swift in the Xcode project
                                          │
                                          ▼
                                  iOS Frameworks (UIKit, AVFoundation, CoreLocation, …)

#### Going lower: plain C/C++ via P/Invoke

Both platforms also let you call plain C/C++ libraries:

- Compile your C/C++ into `.so` (Android) or `.a` / `.dylib` (iOS).
- Drop it into `Assets/Plugins/<platform>/`.
- Call from C# via `[DllImport("LibName")]`.

This is conceptually the same idea as **Dart FFI** in Flutter or **`expect`/`actual` + cinterop** in Kotlin Multiplatform: a C ABI is the universal bridge.

### Unity engine architecture

A Unity build is best understood as **four stacked layers**. From the developer-facing top to the hardware-facing bottom:

**1. Editor & User Scripting**

This is the layer the developer sees and touches.

- **Unity Editor** — the IDE itself (Scene view, Hierarchy, Inspector, Project window). Not shipped with the runtime; only used during development.
- **Scene Graph** — the hierarchy of `GameObject`s in the current scene. Each `GameObject` is a container of `Component`s, the most important of which is `Transform` (position/rotation/scale, parent/child links).
- **User Scripts (C#)** — your gameplay code. Most scripts inherit from **`MonoBehaviour`**, which gives them access to lifecycle hooks (`Awake`, `Start`, `Update`, …) called by the engine each frame.

**2. Scripting Runtime**

The layer that actually executes the C# in your scripts. This is the **Mono vs IL2CPP** choice covered earlier.

- In the **Editor and dev builds**, this is **Mono** — IL is JIT-compiled at runtime.
- In **mobile release builds**, this is **IL2CPP** — IL has already been transpiled to C++ and compiled to native code at build time; at runtime there is no IL execution, just a small `libil2cpp` runtime for GC and reflection helpers.

This layer also includes the **Garbage Collector** (the Boehm GC in Unity, incremental since Unity 2019). GC behavior is a major performance topic in Unity because GC spikes show up as dropped frames.

**3. Engine Core (Native C++)**

The heart of Unity. Written in C++ for performance and shipped as native code on every platform (`libunity.so` on Android, `UnityFramework.framework` on iOS). The Engine Core groups several subsystems:

| Subsystem      | Responsibility                                              | Underlying library/API (examples)    |
|----------------|-------------------------------------------------------------|--------------------------------------|
| **Graphics**   | Scene rendering, shaders, lighting, post-processing         | Vulkan, OpenGL ES, Metal, DirectX    |
| **Physics**    | Collisions, rigid bodies, joints, raycasts                  | **PhysX** (3D), **Box2D** (2D)       |
| **Audio**      | Mixing, 3D positional audio, effects, streaming             | **FMOD**                             |
| **Animation**  | Skeletal animation, blend trees, state machines             | **Mecanim**                          |
| **Input**      | Touch, keyboard, gamepad, sensors                           | Legacy Input Manager / New Input System |
| **Asset & Scene loading** | Load/unload scenes, asset bundles, Addressables  | Internal + Addressables package      |
| **Networking** | Low-level transport, high-level game networking             | Unity Transport, Netcode for GameObjects |

C# code on layer 2 talks to these subsystems through the **Unity scripting API** (e.g. `Rigidbody.AddForce`, `AudioSource.Play`). Under the hood, those calls cross from managed code into native C++ via P/Invoke-like internal calls.

**4. Platform Abstraction Layer (PAL)**

The thinnest, lowest layer. Its job is to hide platform differences from the Engine Core so the same C++ codebase can run on Android, iOS, Windows, macOS, consoles, etc.

The PAL wraps:

- **Graphics** — uniform interface over Vulkan / Metal / OpenGL ES / DirectX.
- **Windowing & surfaces** — `SurfaceView` on Android, `CAMetalLayer` on iOS, `HWND` on Windows.
- **Input devices** — touch on mobile, keyboard/mouse on desktop, gamepads everywhere.
- **Audio output** — OpenSL ES / AAudio on Android, AVAudioEngine on iOS.
- **File system, threading, networking** — anything the OS exposes differently.

The PAL is also where Unity hands off to the host activity / view controller (JNI on Android, Objective-C++ on iOS), which is what makes the **Game Loop vs Event Loop** discussion below possible.

**Putting it together**

      ┌──────────────────────────────────────────────────────────┐
      │ 1. Editor & User Scripting                               │
      │    Unity Editor · Scene graph · MonoBehaviour (C#)       │
      └──────────────┬───────────────────────────────────────────┘
                     │
      ┌──────────────▼───────────────────────────────────────────┐
      │ 2. Scripting Runtime                                     │
      │    Mono (JIT)    or    IL2CPP (AOT) + libil2cpp + GC     │
      └──────────────┬───────────────────────────────────────────┘
                     │
      ┌──────────────▼───────────────────────────────────────────┐
      │ 3. Engine Core (C++)                                     │
      │    Graphics · Physics · Audio · Animation · Input · …    │
      └──────────────┬───────────────────────────────────────────┘
                     │
      ┌──────────────▼───────────────────────────────────────────┐
      │ 4. Platform Abstraction Layer                            │
      │    Vulkan/Metal · JNI · CoreAudio · OS file/thread/net   │
      └──────────────┬───────────────────────────────────────────┘
                     │
                  Android / iOS / Windows / macOS / consoles

### Game Loop vs Event Loop — why Unity is different

This is the punchline of the whole Unity section, and the single biggest reason a Unity app feels different from a Flutter / React Native / native app on the same device.

**Two models for "how the program runs"**

- **Reactive (event-driven)** — the model used by **native mobile apps, Flutter, React Native, KMP UIs**, and basically every traditional mobile/desktop GUI. The app is **idle by default**. The OS delivers events (a touch, a network response, a timer firing) and the app reacts: handle the event, maybe re-layout / re-render the parts that changed, then go back to idle. **Dirty-region rendering** — only the parts of the screen that changed get redrawn.

- **Continuous (game loop)** — the model used by Unity, Unreal, and almost every game engine. The app is **never idle while visible**. It runs a tight loop that, every frame, does the full pipeline: read input, advance simulation, redraw everything, present to the display, repeat — typically at **60 FPS on mobile** (16.6 ms per frame), 90/120 FPS on high-refresh devices.

**What Unity does every frame**

The frame loop, simplified, looks like this on every visible frame:

      while (app is visible) {
          poll input             // touches, sensors, gamepads
          run FixedUpdate(s)     // physics steps, fixed time delta
          run Update             // user logic, animations, AI
          run LateUpdate         // camera follow, IK, post-Update fixes
          render                 // culling → batching → draw calls → GPU
          present                // hand finished frame to the display
      }

**MonoBehaviour lifecycle as hooks into the loop**

The `MonoBehaviour` callbacks every Unity developer learns aren't a coincidence — they are just **named hooks into the frame loop above**:

| Hook                  | Where it fires                                                          |
|-----------------------|-------------------------------------------------------------------------|
| `Awake()`             | Once, when the script instance is loaded (before any `Start`).          |
| `OnEnable()`          | When the GameObject/component becomes active.                           |
| `Start()`             | Once, just before the first `Update` of that script.                    |
| `FixedUpdate()`       | Each physics step (default 50 Hz, decoupled from frame rate).           |
| `Update()`            | Once per **frame** — variable interval depending on FPS.                |
| `LateUpdate()`        | Once per frame, **after all `Update`s** — used for camera follow, etc.  |
| `OnDisable()` / `OnDestroy()` | On deactivation / destruction.                                  |

**Consequences of the continuous model**

- **Higher CPU/GPU usage and faster battery drain.** Even a "still" Unity screen is doing 60 full redraws per second. A native app showing the same screen would draw it once and idle.
- **Lower input latency.** Because the loop is always running, the gap between a touch and the next rendered frame is at most one frame — critical for games.
- **No dirty-region rendering.** Unity throws away the entire previous frame and redraws every pixel. This is why mobile Unity games warm up the device noticeably faster than native apps.
- **Frame budget thinking.** Everything on layer 1 (your C# code) must fit inside ~16 ms together with the engine's own work. This is why GC spikes, `Resources.Load` on the main thread, and excessive logging or heavy main-thread logic all show up as stutters.
- **Backgrounding behavior is explicit.** When the OS sends the app to background, Unity pauses the loop (`OnApplicationPause`) — you don't get the implicit "do nothing" idle of a native app because there *is* no idle state in the first place.

**One-line summary of where Unity sits**

A native app, a Flutter app, and a React Native app are all *guests* inside the platform's UI thread, reacting to events.
A Unity app is the *host* of its own surface, running a self-driven render loop on top of the OS.

## Comparison summary

A quick comparison of the five approaches covered in this document:

| Aspect                | Native (Android / iOS)         | Flutter                              | Kotlin Multiplatform                       | React Native                                  | Unity                                            |
|-----------------------|--------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------------|--------------------------------------------------|
| Language              | Kotlin/Java, Swift/ObjC        | Dart                                 | Kotlin (+ Swift/ObjC for iOS UI)           | JavaScript / TypeScript                       | C# (user code), C/C++ (engine)                   |
| What ships in the app | Native machine code            | Native machine code (Dart AOT)       | Native machine code (Kotlin/Native + JVM)  | JS bundle or Hermes bytecode + JS engine      | Native machine code via IL2CPP (AOT) + engine    |
| UI rendering          | Native views (drawn by OS)     | Custom canvas (Skia / Impeller)      | Native UI **or** Compose Multiplatform (Skia/Skiko) | Real native views, laid out with Yoga (Flexbox) | Own GPU surface (Vulkan/Metal); no native widgets |
| Cross-platform UI?    | No                             | Yes — same widgets everywhere        | Optional (share logic, native UI by default) | Yes — same components map to native views     | Yes — Unity draws every pixel itself             |
| Bridge to native APIs | Direct (it's the platform)     | Platform Channels, Dart FFI          | `expect`/`actual`, cinterop                | TurboModules (JSI) / Native Modules (Bridge)  | JNI (Android), P/Invoke + Obj-C++ (iOS)          |
| Startup / TTI         | Best                           | Good (heavier binary)                | Native-like                                | Improved a lot by Hermes, still JS-bound      | Slower (engine init + asset load)                |
| Runtime performance   | Best                           | Near-native, GPU-accelerated         | Near-native                                | Good; bound by JS thread and JS↔Native crossings | Continuous frame loop, GPU-bound (60+ FPS)    |
| App size              | Smallest                       | Larger (engine + framework)          | Close to native                            | Engine + JS bundle (Hermes shrinks Android; on iOS it adds a few MB since JSC is system-provided) | Largest (engine + assets)                |
| Look & feel           | 100% platform-native           | Custom; can mimic platform           | 100% platform-native (if using native UI)  | Platform-native (uses real native widgets)    | Not native — Unity's own UI system (UGUI / UI Toolkit) |
| Ecosystem / libraries | Largest per-platform           | Large, growing                       | Smaller, growing                           | Very large (npm + RN community)               | Huge for games (Asset Store)                     |
| Best fit              | Max performance, deep platform | Pixel-perfect identical UI everywhere | Share logic with separate native UIs       | Web/React teams shipping mobile fast          | Games, AR/VR, real-time 3D/2D                    |

**Key intuition:**

- If two apps look *identical* on iOS and Android, it is almost certainly **Flutter**.
- If an app looks *native on each platform* but is clearly cross-platform (same release cadence, same features), it is very often **React Native**.
- If the UI is platform-native and only the business logic is shared, it is likely **Kotlin Multiplatform**.
- If the app is a game, AR/VR experience, or real-time 3D/2D simulation, it is almost certainly **Unity** (or Unreal).
