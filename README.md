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
- Unlike Flutter, React Native does not draw its own widgets — it maps React components to **real native UI components** (UIView on iOS, android.view on Android).

### JavaScript Engine and how JS code runs on mobile

Unlike Flutter (Dart → machine code) or Kotlin Multiplatform (Kotlin → machine code), React Native does **not** compile JS to machine code ahead of time. Instead, it ships a JavaScript bundle inside the app and executes it on device using an embedded JavaScript engine.

**The bundling pipeline (Metro)**

- Metro is React Native's JavaScript bundler. It takes the entry file (typically `index.js`), resolves every `import`/`require`, transforms JSX/TypeScript via Babel, and produces a single JavaScript bundle.
- In development, Metro serves the bundle over HTTP and supports Fast Refresh.
- In release builds, the bundle is packaged inside the APK (`assets/index.android.bundle`) or IPA (`main.jsbundle`).

**JavaScript engines used by React Native**

- **JavaScriptCore (JSC):** the historical default. JSC is Apple's open-source JavaScript engine that ships with iOS (used by Safari). On Android, React Native bundles its own copy of JSC. JSC interprets and JIT-compiles JS at runtime — but note that on iOS, third-party apps are not allowed to JIT, so JSC runs in interpreter mode there, which hurts startup and runtime performance.
- **Hermes:** an open-source JS engine built by Meta specifically for React Native. Default since React Native 0.70 (Sep 2022). Key differences from JSC:
  - **Ahead-of-Time (AOT) compilation:** JS source is compiled to **Hermes Bytecode (`.hbc`)** at *build time*, not at runtime. The app ships bytecode, not JS text.
  - **No JIT:** Hermes is a bytecode interpreter optimized for fast startup and low memory, accepting slower steady-state throughput as a trade-off — a sensible trade on mobile where TTI (time-to-interactive) matters more than long-running benchmarks.
  - **Smaller heap, smaller app size, faster TTI**, and identical behavior on iOS and Android (no JIT-mode mismatch).

**Compilation / packaging pipeline**

      JS/JSX → Babel → Metro bundler → JS bundle
                                        ├─ (JSC path)   .jsbundle  → APK/IPA → interpreted/JIT by JSC at runtime
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
- Every call across the boundary (e.g. `fetch`, `setState` that produces a UI update, calling `CameraModule.takePicture()`) is:
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
      Shadow Tree (JS-side representation)
              │  serialize → JSON → Bridge
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
| `<TextInput>`          | `UITextField`          | `EditText`               |
| `<TouchableOpacity>`   | composed `UIView`      | composed `ViewGroup`     |

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

### Comparison summary

A quick comparison of the four approaches covered in this document:

| Aspect                | Native (Android / iOS)         | Flutter                              | Kotlin Multiplatform                       | React Native                                  |
|-----------------------|--------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------------|
| Language              | Kotlin/Java, Swift/ObjC        | Dart                                 | Kotlin (+ Swift/ObjC for iOS UI)           | JavaScript / TypeScript                       |
| What ships in the app | Native machine code            | Native machine code (Dart AOT)       | Native machine code (Kotlin/Native + JVM)  | JS bundle or Hermes bytecode + JS engine      |
| UI rendering          | Native views (drawn by OS)     | Custom canvas (Skia / Impeller)      | Native UI **or** Compose Multiplatform (Skia/Skiko) | Real native views, laid out with Yoga (Flexbox) |
| Cross-platform UI?    | No                             | Yes — same widgets everywhere        | Optional (share logic, native UI by default) | Yes — same components map to native views     |
| Bridge to native APIs | Direct (it's the platform)     | Platform Channels, Dart FFI          | `expect`/`actual`, cinterop                | TurboModules (JSI) / Native Modules (Bridge)  |
| Startup / TTI         | Best                           | Good (heavier binary)                | Native-like                                | Improved a lot by Hermes, still JS-bound      |
| Runtime performance   | Best                           | Near-native, GPU-accelerated         | Near-native                                | Good; bound by JS thread and JS↔Native crossings |
| App size              | Smallest                       | Larger (engine + framework)          | Close to native                            | Engine + JS bundle (Hermes shrinks both)      |
| Look & feel           | 100% platform-native           | Custom; can mimic platform           | 100% platform-native (if using native UI)  | Platform-native (uses real native widgets)    |
| Ecosystem / libraries | Largest per-platform           | Large, growing                       | Smaller, growing                           | Very large (npm + RN community)               |
| Best fit              | Max performance, deep platform | Pixel-perfect identical UI everywhere | Share logic with separate native UIs       | Web/React teams shipping mobile fast          |

**Key intuition:**

- If two apps look *identical* on iOS and Android, it is almost certainly **Flutter**.
- If an app looks *native on each platform* but is clearly cross-platform (same release cadence, same features), it is very often **React Native**.
- If the UI is platform-native and only the business logic is shared, it is likely **Kotlin Multiplatform**.
