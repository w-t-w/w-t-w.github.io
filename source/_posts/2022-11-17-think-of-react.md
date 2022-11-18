---
title: think of react
date: 2022-11-17 11:48:59
tags: [react,source]
categories: react
---

# react 源码解析

## createElement

```javascript
/**
 * Create and return a new ReactElement of the given type.
 * See https://reactjs.org/docs/react-api.html#createelement
 */
export function createElement(type, config, children) {
    let propName;

    const props = {};

    let key = null;
    let ref = null;
    let self = null;
    let source = null;

    // 判断 config 是否存在
    if (config != null) {
        // 判断是否存在 props => ref 
        if (hasValidRef(config)) {
            ref = config.ref;

            if (__DEV__) {
                warnIfStringRefCannotBeAutoConverted(config);
            }
        }
        // 判断是否存在 props => key
        if (hasValidKey(config)) {
            key = '' + config.key;
        }

        self = config.__self === undefined ? null : config.__self;
        source = config.__source === undefined ? null : config.__source;
        // 将除了 ref、key、__self 以及 __source 的 props 赋给内部对象 props
        for (propName in config) {
            if (
                hasOwnProperty.call(config, propName) &&
                !RESERVED_PROPS.hasOwnProperty(propName)
            ) {
                props[propName] = config[propName];
            }
        }
    }

    // 获取 children 部分
    const childrenLength = arguments.length - 2;
    if (childrenLength === 1) {
        props.children = children;
    } else if (childrenLength > 1) {
        const childArray = Array(childrenLength);
        for (let i = 0; i < childrenLength; i++) {
            childArray[i] = arguments[i + 2];
        }
        if (__DEV__) {
            if (Object.freeze) {
                Object.freeze(childArray);
            }
        }
        props.children = childArray;
    }

    // 如果存在原生标签组件,将原生标签组件上的原生属性赋给 props
    if (type && type.defaultProps) {
        const defaultProps = type.defaultProps;
        for (propName in defaultProps) {
            if (props[propName] === undefined) {
                props[propName] = defaultProps[propName];
            }
        }
    }
    if (__DEV__) {
        if (key || ref) {
            const displayName =
                typeof type === 'function'
                    ? type.displayName || type.name || 'Unknown'
                    : type;
            if (key) {
                defineKeyPropWarningGetter(props, displayName);
            }
            if (ref) {
                defineRefPropWarningGetter(props, displayName);
            }
        }
    }
    // 交给 ReactElement 处理
    return ReactElement(
        type,
        key,
        ref,
        self,
        source,
        ReactCurrentOwner.current,
        props,
    );
}
```

### 不同种类的 fiber Tag

> tag 与 element 的对应关系

```typescript
/**
 * Copyright (c) Meta Platforms, Inc. and affiliates.
 *
 * This source code is licensed under the MIT license found in the
 * LICENSE file in the root directory of this source tree.
 *
 * @flow
 */

export type WorkTag =
    | 0
    | 1
    | 2
    | 3
    | 4
    | 5
    | 6
    | 7
    | 8
    | 9
    | 10
    | 11
    | 12
    | 13
    | 14
    | 15
    | 16
    | 17
    | 18
    | 19
    | 20
    | 21
    | 22
    | 23
    | 24
    | 25
    | 26
    | 27;

export const FunctionComponent = 0;       // 函数组件
export const ClassComponent = 1;          // 类组件
export const IndeterminateComponent = 2;  // 初始化的时候区分是函数组件还是类组件 
export const HostRoot = 3;                // Root Fiber 可以理解为根元素 ， 通过reactDom.render()产生的根元素
export const HostPortal = 4;              // 对应  ReactDOM.createPortal 产生的 Portal 
export const HostComponent = 5;           // dom 元素 比如 <div>
export const HostText = 6;                // 文本节点
export const Fragment = 7;                // 对应 <React.Fragment> 
export const Mode = 8;                    // 对应 <React.StrictMode>   
export const ContextConsumer = 9;         // 对应 <Context.Consumer>
export const ContextProvider = 10;        // 对应 <Context.Provider>
export const ForwardRef = 11;             // 对应 React.ForwardRef
export const Profiler = 12;                // 对应 <Profiler/ >
export const SuspenseComponent = 13;      // 对应 <Suspense>
export const MemoComponent = 14;          // 对应 React.memo 返回的组件
export const SimpleMemoComponent = 15;
export const LazyComponent = 16;
export const IncompleteClassComponent = 17;
export const DehydratedFragment = 18;
export const SuspenseListComponent = 19;
export const ScopeComponent = 21;
export const OffscreenComponent = 22;
export const LegacyHiddenComponent = 23;
export const CacheComponent = 24;
export const TracingMarkerComponent = 25;
export const HostResource = 26;
export const HostSingleton = 27;
```

## render

```typescript
export function render(
    element: React$Element<any>,
    container: Container,
    callback: ?Function,
): React$Component<any, any> | PublicInstance | null {
    if (__DEV__) {
        console.error(
            'ReactDOM.render is no longer supported in React 18. Use createRoot ' +
            'instead. Until you switch to the new API, your app will behave as ' +
            "if it's running React 17. Learn " +
            'more: https://reactjs.org/link/switch-to-createroot',
        );
    }

    if (!isValidContainerLegacy(container)) {
        throw new Error('Target container is not a DOM element.');
    }

    if (__DEV__) {
        const isModernRoot =
            isContainerMarkedAsRoot(container) &&
            container._reactRootContainer === undefined;
        if (isModernRoot) {
            console.error(
                'You are calling ReactDOM.render() on a container that was previously ' +
                'passed to ReactDOMClient.createRoot(). This is not supported. ' +
                'Did you mean to call root.render(element)?',
            );
        }
    }
    return legacyRenderSubtreeIntoContainer(
        null,
        element,
        container,
        false,
        callback,
    );
}

function legacyRenderSubtreeIntoContainer(
    parentComponent: ?React$Component<any, any>,
    children: ReactNodeList,
    container: Container,
    forceHydrate: boolean,
    callback: ?Function,
): React$Component<any, any> | PublicInstance | null {
    if (__DEV__) {
        topLevelUpdateWarnings(container);
        warnOnInvalidCallback(callback === undefined ? null : callback, 'render');
    }

    const maybeRoot = container._reactRootContainer;
    let root: FiberRoot;
    if (!maybeRoot) {
        // Initial mount
        root = legacyCreateRootFromDOMContainer(
            container,
            children,
            parentComponent,
            callback,
            forceHydrate,
        );
    } else {
        root = maybeRoot;
        if (typeof callback === 'function') {
            const originalCallback = callback;
            callback = function () {
                const instance = getPublicRootInstance(root);
                originalCallback.call(instance);
            };
        }
        // Update
        updateContainer(children, root, parentComponent, callback);
    }
    return getPublicRootInstance(root);
}

function legacyCreateRootFromDOMContainer(
    container: Container,
    initialChildren: ReactNodeList,
    parentComponent: ?React$Component<any, any>,
    callback: ?Function,
    isHydrationContainer: boolean,
): FiberRoot {
    if (isHydrationContainer) {
        if (typeof callback === 'function') {
            const originalCallback = callback;
            callback = function () {
                const instance = getPublicRootInstance(root);
                originalCallback.call(instance);
            };
        }

        const root = createHydrationContainer(
            initialChildren,
            callback,
            container,
            LegacyRoot,
            null, // hydrationCallbacks
            false, // isStrictMode
            false, // concurrentUpdatesByDefaultOverride,
            '', // identifierPrefix
            noopOnRecoverableError,
            // TODO(luna) Support hydration later
            null,
        );
        container._reactRootContainer = root;
        markContainerAsRoot(root.current, container);

        const rootContainerElement =
            container.nodeType === COMMENT_NODE ? container.parentNode : container;
        // $FlowFixMe[incompatible-call]
        listenToAllSupportedEvents(rootContainerElement);

        flushSync();
        return root;
    } else {
        // First clear any existing content.
        let rootSibling;
        while ((rootSibling = container.lastChild)) {
            container.removeChild(rootSibling);
        }

        if (typeof callback === 'function') {
            const originalCallback = callback;
            callback = function () {
                const instance = getPublicRootInstance(root);
                originalCallback.call(instance);
            };
        }

        const root = createContainer(
            container,
            LegacyRoot,
            null, // hydrationCallbacks
            false, // isStrictMode
            false, // concurrentUpdatesByDefaultOverride,
            '', // identifierPrefix
            noopOnRecoverableError, // onRecoverableError
            null, // transitionCallbacks
        );
        container._reactRootContainer = root;
        markContainerAsRoot(root.current, container);

        const rootContainerElement =
            container.nodeType === COMMENT_NODE ? container.parentNode : container;
        // $FlowFixMe[incompatible-call]
        listenToAllSupportedEvents(rootContainerElement);

        // Initial mount should not be batched.
        flushSync(() => {
            updateContainer(initialChildren, root, parentComponent, callback);
        });

        return root;
    }
}
```

```typescript
export function createContainer(
  containerInfo: Container,
  tag: RootTag,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
  isStrictMode: boolean,
  concurrentUpdatesByDefaultOverride: null | boolean,
  identifierPrefix: string,
  onRecoverableError: (error: mixed) => void,
  transitionCallbacks: null | TransitionTracingCallbacks,
): OpaqueRoot {
  const hydrate = false;
  const initialChildren = null;
  return createFiberRoot(
    containerInfo,
    tag,
    hydrate,
    initialChildren,
    hydrationCallbacks,
    isStrictMode,
    concurrentUpdatesByDefaultOverride,
    identifierPrefix,
    onRecoverableError,
    transitionCallbacks,
  );
}

export function createFiberRoot(
    containerInfo: Container,
    tag: RootTag,
    hydrate: boolean,
    initialChildren: ReactNodeList,
    hydrationCallbacks: null | SuspenseHydrationCallbacks,
    isStrictMode: boolean,
    concurrentUpdatesByDefaultOverride: null | boolean,
    // TODO: We have several of these arguments that are conceptually part of the
    // host config, but because they are passed in at runtime, we have to thread
    // them through the root constructor. Perhaps we should put them all into a
    // single type, like a DynamicHostConfig that is defined by the renderer.
    identifierPrefix: string,
    onRecoverableError: null | ((error: mixed) => void),
    transitionCallbacks: null | TransitionTracingCallbacks,
): FiberRoot {
    // $FlowFixMe[invalid-constructor] Flow no longer supports calling new on functions
    const root: FiberRoot = (new FiberRootNode(
        containerInfo,
        tag,
        hydrate,
        identifierPrefix,
        onRecoverableError,
    ): any);
    if (enableSuspenseCallback) {
        root.hydrationCallbacks = hydrationCallbacks;
    }

    if (enableTransitionTracing) {
        root.transitionCallbacks = transitionCallbacks;
    }

    // Cyclic construction. This cheats the type system right now because
    // stateNode is any.
    const uninitializedFiber = createHostRootFiber(
        tag,
        isStrictMode,
        concurrentUpdatesByDefaultOverride,
    );
    root.current = uninitializedFiber;
    uninitializedFiber.stateNode = root;

    if (enableCache) {
        const initialCache = createCache();
        retainCache(initialCache);

        // The pooledCache is a fresh cache instance that is used temporarily
        // for newly mounted boundaries during a render. In general, the
        // pooledCache is always cleared from the root at the end of a render:
        // it is either released when render commits, or moved to an Offscreen
        // component if rendering suspends. Because the lifetime of the pooled
        // cache is distinct from the main memoizedState.cache, it must be
        // retained separately.
        root.pooledCache = initialCache;
        retainCache(initialCache);
        const initialState: RootState = {
            element: initialChildren,
            isDehydrated: hydrate,
            cache: initialCache,
        };
        uninitializedFiber.memoizedState = initialState;
    } else {
        const initialState: RootState = {
            element: initialChildren,
            isDehydrated: hydrate,
            cache: (null: any), // not enabled yet
    };
        uninitializedFiber.memoizedState = initialState;
    }

    initializeUpdateQueue(uninitializedFiber);

    return root;
}

function FiberRootNode(
    containerInfo,
    tag,
    hydrate,
    identifierPrefix,
    onRecoverableError,
) {
    this.tag = tag;
    this.containerInfo = containerInfo;
    this.pendingChildren = null;
    this.current = null;
    this.pingCache = null;
    this.finishedWork = null;
    this.timeoutHandle = noTimeout;
    this.context = null;
    this.pendingContext = null;
    this.callbackNode = null;
    this.callbackPriority = NoLane;
    this.eventTimes = createLaneMap(NoLanes);
    this.expirationTimes = createLaneMap(NoTimestamp);

    this.pendingLanes = NoLanes;
    this.suspendedLanes = NoLanes;
    this.pingedLanes = NoLanes;
    this.expiredLanes = NoLanes;
    this.mutableReadLanes = NoLanes;
    this.finishedLanes = NoLanes;
    this.errorRecoveryDisabledLanes = NoLanes;

    this.entangledLanes = NoLanes;
    this.entanglements = createLaneMap(NoLanes);

    this.hiddenUpdates = createLaneMap(null);

    this.identifierPrefix = identifierPrefix;
    this.onRecoverableError = onRecoverableError;

    if (enableCache) {
        this.pooledCache = null;
        this.pooledCacheLanes = NoLanes;
    }

    if (supportsHydration) {
        this.mutableSourceEagerHydrationData = null;
    }

    if (enableSuspenseCallback) {
        this.hydrationCallbacks = null;
    }

    this.incompleteTransitions = new Map();
    if (enableTransitionTracing) {
        this.transitionCallbacks = null;
        const transitionLanesMap = (this.transitionLanes = []);
        for (let i = 0; i < TotalLanes; i++) {
            transitionLanesMap.push(null);
        }
    }

    if (enableProfilerTimer && enableProfilerCommitHooks) {
        this.effectDuration = 0;
        this.passiveEffectDuration = 0;
    }

    if (enableUpdaterTracking) {
        this.memoizedUpdaters = new Set();
        const pendingUpdatersLaneMap = (this.pendingUpdatersLaneMap = []);
        for (let i = 0; i < TotalLanes; i++) {
            pendingUpdatersLaneMap.push(new Set());
        }
    }

    if (__DEV__) {
        switch (tag) {
            case ConcurrentRoot:
                this._debugRootType = hydrate ? 'hydrateRoot()' : 'createRoot()';
                break;
            case LegacyRoot:
                this._debugRootType = hydrate ? 'hydrate()' : 'render()';
                break;
        }
    }
}

export function createHostRootFiber(
    tag: RootTag,
    isStrictMode: boolean,
    concurrentUpdatesByDefaultOverride: null | boolean,
): Fiber {
    let mode;
    if (tag === ConcurrentRoot) {
        mode = ConcurrentMode;
        if (isStrictMode === true || createRootStrictEffectsByDefault) {
            mode |= StrictLegacyMode | StrictEffectsMode;
        }
        if (
            // We only use this flag for our repo tests to check both behaviors.
            // TODO: Flip this flag and rename it something like "forceConcurrentByDefaultForTesting"
            !enableSyncDefaultUpdates ||
            // Only for internal experiments.
            (allowConcurrentByDefault && concurrentUpdatesByDefaultOverride)
        ) {
            mode |= ConcurrentUpdatesByDefaultMode;
        }
    } else {
        mode = NoMode;
    }

    if (enableProfilerTimer && isDevToolsPresent) {
        // Always collect profile timings when DevTools are present.
        // This enables DevTools to start capturing timing at any point–
        // Without some nodes in the tree having empty base times.
        mode |= ProfileMode;
    }

    return createFiber(HostRoot, null, null, mode);
}

/ This is a constructor function, rather than a POJO constructor, still
// please ensure we do the following:
// 1) Nobody should add any instance methods on this. Instance methods can be
//    more difficult to predict when they get optimized and they are almost
//    never inlined properly in static compilers.
// 2) Nobody should rely on `instanceof Fiber` for type testing. We should
//    always know when it is a fiber.
// 3) We might want to experiment with using numeric keys since they are easier
//    to optimize in a non-JIT environment.
// 4) We can easily go from a constructor to a createFiber object literal if that
//    is faster.
// 5) It should be easy to port this to a C struct and keep a C implementation
//    compatible.
const createFiber = function(
    tag: WorkTag,
    pendingProps: mixed,
    key: null | string,
    mode: TypeOfMode,
): Fiber {
    // $FlowFixMe: the shapes are exact here but Flow doesn't like constructors
    return new FiberNode(tag, pendingProps, key, mode);
};

function FiberNode(
    tag: WorkTag,
    pendingProps: mixed,
    key: null | string,
    mode: TypeOfMode,
) {
    // Instance
    this.tag = tag;
    this.key = key;
    this.elementType = null;
    this.type = null;
    this.stateNode = null;

    // Fiber
    this.return = null;
    this.child = null;
    this.sibling = null;
    this.index = 0;

    this.ref = null;

    this.pendingProps = pendingProps;
    this.memoizedProps = null;
    this.updateQueue = null;
    this.memoizedState = null;
    this.dependencies = null;

    this.mode = mode;

    // Effects
    this.flags = NoFlags;
    this.subtreeFlags = NoFlags;
    this.deletions = null;

    this.lanes = NoLanes;
    this.childLanes = NoLanes;

    this.alternate = null;

    if (enableProfilerTimer) {
        // Note: The following is done to avoid a v8 performance cliff.
        //
        // Initializing the fields below to smis and later updating them with
        // double values will cause Fibers to end up having separate shapes.
        // This behavior/bug has something to do with Object.preventExtension().
        // Fortunately this only impacts DEV builds.
        // Unfortunately it makes React unusably slow for some applications.
        // To work around this, initialize the fields below with doubles.
        //
        // Learn more about this here:
        // https://github.com/facebook/react/issues/14365
        // https://bugs.chromium.org/p/v8/issues/detail?id=8538
        this.actualDuration = Number.NaN;
        this.actualStartTime = Number.NaN;
        this.selfBaseDuration = Number.NaN;
        this.treeBaseDuration = Number.NaN;

        // It's okay to replace the initial doubles with smis after initialization.
        // This won't trigger the performance cliff mentioned above,
        // and it simplifies other profiler code (including DevTools).
        this.actualDuration = 0;
        this.actualStartTime = -1;
        this.selfBaseDuration = 0;
        this.treeBaseDuration = 0;
    }

    if (__DEV__) {
        // This isn't directly used but is handy for debugging internals:

        this._debugSource = null;
        this._debugOwner = null;
        this._debugNeedsRemount = false;
        this._debugHookTypes = null;
        if (!hasBadMapPolyfill && typeof Object.preventExtensions === 'function') {
            Object.preventExtensions(this);
        }
    }
}
```