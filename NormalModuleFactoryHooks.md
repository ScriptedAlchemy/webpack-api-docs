# NormalModuleFactory

The `NormalModuleFactory` is a class in Webpack responsible for creating and managing modules. It provides various hooks that allow plugins to tap into the module creation process and modify it as needed.

## Hooks

The `NormalModuleFactory` provides several hooks that plugins can use to customize the module creation process. These hooks are:

### `normalModuleFactory.hooks.resolve`

The `normalModuleFactory.hooks.resolve` is a hook provided by Webpack's `NormalModuleFactory`. This hook allows you to intercept and modify the resolution of modules.

#### How to Use It

You can tap into this hook to customize how modules are resolved. This is useful for tasks like aliasing modules, adding custom resolution logic, or modifying the request before it is resolved.

#### How It Works

The `resolve` hook is called with the request object and a callback. You can modify the request object or perform asynchronous operations before calling the callback to continue the resolution process.

#### What It's For

- **Aliasing modules**: Redirecting module requests to different paths.
- **Custom resolution logic**: Implementing custom logic to determine how modules should be resolved.
- **Modifying requests**: Changing the request object before it is resolved.

#### Parameters

- **request**: The request object containing details about the module to be resolved.
- **callback**: A function to call once your custom resolution logic is complete.

#### Example

Here is an example of how to use the `normalModuleFactory.hooks.resolve` hook:

```javascript
const NormalModuleFactory = require('webpack/lib/NormalModuleFactory');

module.exports = {
  // Other webpack configuration...
  plugins: [
    {
      apply: (compiler) => {
        compiler.hooks.normalModuleFactory.tap('MyPlugin', (normalModuleFactory) => {
          normalModuleFactory.hooks.resolve.tapAsync('MyPlugin', (request, resolveContext, callback) => {
            // Custom resolution logic
            if (request.request === 'my-alias') {
              request.request = 'actual-module';
            }
            // Continue with the resolution process
            callback();
          });
        });
      }
    }
  ]
};
```

In this example, any request for `my-alias` is redirected to `actual-module`. The `callback` is called to continue the resolution process after the custom logic is applied.

### `normalModuleFactory.hooks.resolveForScheme`

The `resolveForScheme` hook in the `NormalModuleFactory` class is a part of the Webpack module resolution process. It is used to handle resources with specific schemes (like `http`, `https`, etc.) during the module resolution phase.

#### How to Use It

The `resolveForScheme` hook is a `HookMap` of `AsyncSeriesBailHook` instances. Each hook in the map corresponds to a specific scheme and is called during the resolution process if the resource has that scheme.

#### How It Works

- **Initialization**: The `resolveForScheme` hook is initialized as a `HookMap` of `AsyncSeriesBailHook` instances.
- **Usage**: When a resource with a specific scheme is encountered, the corresponding hook is called to handle the resolution.
- **Callback Parameters**: The hook takes two parameters: `resourceData` and `resolveData`.

#### Parameters

- **resourceData**: An object containing details about the resource being resolved.
- **resolveData**: An object containing context and options for the resolution process.

#### Example

Here is an example of how you might tap into the `resolveForScheme` hook to handle a custom scheme:

```javascript
const factory = new NormalModuleFactory({ /* params */ });

factory.hooks.resolveForScheme.for('custom-scheme').tapAsync(
  'CustomSchemePlugin',
  (resourceData, resolveData, callback) => {
    // Custom resolution logic for 'custom-scheme'
    if (resourceData.resource.startsWith('custom-scheme://')) {
      // Handle the custom scheme
      resourceData.resource = resourceData.resource.replace('custom-scheme://', '');
      callback(null, true);
    } else {
      callback();
    }
  }
);
```

#### Code Reference

The `resolveForScheme` hook is defined and used in the following lines:

```javascript
resolveForScheme: new HookMap(
  () => new AsyncSeriesBailHook(["resourceData", "resolveData"])
),
```

### `normalModuleFactory.hooks.resolveInScheme`

The `resolveInScheme` hook in the `NormalModuleFactory` class is a part of the Webpack module resolution process. It is used to handle resources that have a specific scheme in their request path.

#### How to Use It

The `resolveInScheme` hook is a `HookMap` of `AsyncSeriesBailHook` instances. Each hook in the map is keyed by a scheme (e.g., `http`, `https`). You can tap into this hook to provide custom resolution logic for resources with specific schemes.

#### How It Works

When a resource with a scheme is encountered, the `resolveInScheme` hook for that scheme is called. If the hook resolves the resource, the resolution process continues with the resolved resource. If not, the default resolution process is used.

#### What It's For

The `resolveInScheme` hook is designed to allow custom resolution logic for resources with specific schemes. This can be useful for handling resources that are not local files, such as those fetched over HTTP.

#### Parameters

- **resourceData**: An object containing information about the resource to be resolved.
- **resolveData**: An object containing additional data needed for the resolution process.

#### Example

Here is an example of how to tap into the `resolveInScheme` hook:

```javascript
const NormalModuleFactory = require('webpack/lib/NormalModuleFactory');

const factory = new NormalModuleFactory({
  // initialization parameters
});

factory.hooks.resolveInScheme.for('http').tapAsync('MyPlugin', (resourceData, resolveData, callback) => {
  // Custom resolution logic for HTTP resources
  if (resourceData.resource.startsWith('http://')) {
    // Resolve the resource
    resourceData.resource = resolveHttpResource(resourceData.resource);
    callback(null, true);
  } else {
    callback();
  }
});
```

#### Code Reference

The `resolveInScheme` hook is defined in the `NormalModuleFactory` class:

```javascript
resolveInScheme: new HookMap(
  () => new AsyncSeriesBailHook(["resourceData", "resolveData"])
),
```

This hook is used in the `NormalModuleFactory` class to handle resources with schemes:

```javascript
.for(contextScheme)
.callAsync(resourceData, data, (err, handled) => {
  if (err) return continueCallback(err);
  if (!handled) return defaultResolve(this.context);
  continueCallback();
});
```

### `normalModuleFactory.hooks.factorize`

The `factorize` hook in `NormalModuleFactory` is an `AsyncSeriesBailHook` that is used to create a module based on the provided `ResolveData`. This hook allows asynchronous operations and can bail out, meaning it can stop the series of operations if a non-undefined value is returned.

#### How to Use It

To use the `factorize` hook, you need to tap into it using `tapAsync` or `tapPromise`. This allows you to add custom logic that will be executed during the module creation process.

#### How It Works

The `factorize` hook is called with `ResolveData` and a callback function. The hook can perform asynchronous operations and either return a module or bail out by returning a non-undefined value.

#### What It's For

The `factorize` hook is used to create a module based on the provided `ResolveData`. It is a critical part of the module resolution and creation process in Webpack.

#### Parameters

- **resolveData**: An object containing information needed to resolve and create the module.
- **callback**: A function to call when the hook's operations are complete.

#### Example

Here is an example of how to tap into the `factorize` hook:

```javascript
normalModuleFactory.hooks.factorize.tapAsync(
  "MyPlugin",
  (resolveData, callback) => {
    // Custom logic to create a module
    const myModule = createMyModule(resolveData);
    if (myModule) {
      return callback(null, myModule);
    }
    callback();
  }
);
```

#### Relevant Code

The `factorize` hook is defined and used in the following lines:

```javascript
factorize: new AsyncSeriesBailHook(["resolveData"]),
```

### `normalModuleFactory.hooks.beforeResolve`

#### What it is

The `beforeResolve` hook in `NormalModuleFactory` is an `AsyncSeriesBailHook` provided by Webpack. It is used to perform asynchronous operations before the module resolution process begins.

#### How to Use It

You can tap into the `beforeResolve` hook to execute custom logic before the module resolution. This can be useful for modifying the resolve data or aborting the resolution process based on certain conditions.

#### How It Works

The `beforeResolve` hook is called with `ResolveData` as its argument. If any of the hooked functions return `false`, the resolution process is aborted. If an error is passed to the callback, the resolution process is also aborted.

#### Parameters

- **resolveData**: An object containing information about the module to be resolved.
- **callback**: A function to call when the hook's logic is complete. It accepts an optional error argument.

#### Example

Here is an example of how to tap into the `beforeResolve` hook:

```javascript
normalModuleFactory.hooks.beforeResolve.tapAsync(
  "MyPlugin",
  (resolveData, callback) => {
    // Custom logic before resolving the module
   

 if (shouldAbort(resolveData)) {
      return callback(null, false); // Abort the resolution
    }
    // Continue with the resolution
    callback();
  }
);
```

#### Relevant Code

The `beforeResolve` hook is defined and used in the following lines:

```javascript
beforeResolve: new AsyncSeriesBailHook(["resolveData"]),
afterResolve: new AsyncSeriesBailHook(["resolveData"]),
```

### `normalModuleFactory.hooks.afterResolve`

#### What it is for

The `afterResolve` hook in the `NormalModuleFactory` is used to perform actions after the module resolution process is complete but before the module is created. This hook allows you to modify the `ResolveData` or perform additional checks and operations based on the resolved data.

#### How it Works

- The `afterResolve` hook is an `AsyncSeriesBailHook` that takes `ResolveData` as its argument.
- It is called after the `resolve` hook and before the `createModule` hook.
- If any of the registered functions in this hook return `false`, the module creation process is aborted.

#### Parameters

- **resolveData**: An object containing information about the resolved module, including context, request, dependencies, and more.

#### Example Usage

Here is an example of how you might tap into the `afterResolve` hook to log the resolved data:

```javascript
normalModuleFactory.hooks.afterResolve.tapAsync(
  "MyPlugin",
  (resolveData, callback) => {
    console.log("Resolved Data:", resolveData);
    // Perform additional operations or modifications
    callback();
  }
);
```

#### Relevant Code

The `afterResolve` hook is defined and used in the `NormalModuleFactory` class. Here are the relevant lines from the provided code:

```javascript
afterResolve: new AsyncSeriesBailHook(["resolveData"]),
```

The hook is called within the `factorize` method:

```javascript
this.hooks.afterResolve.callAsync(resolveData, (err, result) => {
  if (err) return callback(err);

  if (typeof result === "object")
    throw new Error(
      deprecationChangedHookMessage(
        "afterResolve",
        this.hooks.afterResolve
      )
    );

  // Ignored
  if (result === false) return callback();
```

### `normalModuleFactory.hooks.createModule`

#### What it is

The `createModule` hook in the `NormalModuleFactory` class is an `AsyncSeriesBailHook` from the tapable library. This hook is used to create a module during the module resolution process.

#### Details

- **Type**: `AsyncSeriesBailHook`
- **Parameters**:
    - **createData**: Partial data required to create a module.
    - **resolveData**: Data related to the resolution process.

#### How to Use

To use the `createModule` hook, you need to tap into it using the `tapAsync` method. This allows you to provide a callback that will be executed during the module creation process.

#### How it Works

The `createModule` hook is called during the module resolution process. It allows plugins to create a module based on the provided `createData` and `resolveData`. If a module is created, it is returned; otherwise, the process continues.

#### Example

Here is an example of how to tap into the `createModule` hook:

```javascript
normalModuleFactory.hooks.createModule.tapAsync(
  "MyPlugin",
  (createData, resolveData, callback) => {
    // Custom logic to create a module
    const myModule = new MyCustomModule(createData);
    callback(null, myModule);
  }
);
```

#### Parameters

- **createData**: Partial data required to create a module. This includes settings and other relevant information.
- **resolveData**: Data related to the resolution process, including context information and dependencies.

#### Code Reference

The `createModule` hook is defined in the `NormalModuleFactory` class:

```javascript
createModule: new AsyncSeriesBailHook(["createData", "resolveData"]),
```

It is used in the `NormalModuleFactory` constructor:

```javascript
class NormalModuleFactory extends ModuleFactory {
  /**
   * @param {object} param params
   * @param {string=} param.context context
   * @param {InputFileSystem} param.fs file system
   * @param {ResolverFactory} param.resolverFactory resolverFactory
   * @param {ModuleOptions} param.options options
   * @param {object=} param.associatedObjectForCache an object to which the cache will be attached
   * @param {boolean=} param.layers enable layers
   */
  constructor({
    context,
    fs,
    resolverFactory,
    options,
    associatedObjectForCache,
    layers = false
  }) {
    super();
    this.hooks = Object.freeze({
      /** @type {AsyncSeriesBailHook<[ResolveData], Module | false | void>} */
      resolve: new AsyncSeriesBailHook(["resolveData"]),
      /** @type {HookMap<AsyncSeriesBailHook<[ResourceDataWithData, ResolveData], true | void>>} */
      resolveForScheme: new HookMap(
        () => new AsyncSeriesBailHook(["resourceData", "resolveData"])
      ),
      /** @type {HookMap<AsyncSeriesBailHook<[ResourceDataWithData, ResolveData], true | void>>} */
      resolveInScheme: new HookMap(
        () => new AsyncSeriesBailHook(["resourceData", "resolveData"])
      ),
      /** @type {AsyncSeriesBailHook<[ResolveData], Module | undefined>} */
      factorize: new AsyncSeriesBailHook(["resolveData"]),
      /** @type {AsyncSeriesBailHook<[ResolveData], false | void>} */
      beforeResolve: new AsyncSeriesBailHook(["resolveData"]),
      /** @type {AsyncSeriesBailHook<[ResolveData], false | void>} */
      afterResolve: new AsyncSeriesBailHook(["resolveData"]),
      /** @type {AsyncSeriesBailHook<[ResolveData["createData"], ResolveData], Module | void>} */
      createModule: new AsyncSeriesBailHook(["createData", "resolveData"]),
      /** @type {SyncWaterfallHook<[Module, ResolveData["createData"], ResolveData], Module>} */
      module: new SyncWaterfallHook(["module", "createData", "resolveData"]),
      /** @type {HookMap<SyncBailHook<[ParserOptions], Parser>>} */
      createParser: new HookMap(() => new SyncBailHook(["parserOptions"])),
      /** @type {HookMap<SyncBailHook<[TODO, ParserOptions], void>>} */
      parser: new HookMap(() => new SyncHook(["parser", "parserOptions"])),
      /** @type {HookMap<SyncBailHook<[GeneratorOptions], Generator>>} */
      createGenerator: new HookMap(
        () => new SyncBailHook(["generatorOptions"])
      ),
      /** @type {HookMap<SyncBailHook<[TODO, GeneratorOptions], void>>} */
      generator: new HookMap(
        () => new SyncHook(["generator", "generatorOptions"])
      ),
      /** @type {HookMap<SyncBailHook<[TODO, ResolveData], Module>>} */
      createModuleClass: new HookMap(
        () => new SyncBailHook(["createData", "resolveData"])
      )
    });
    this.resolverFactory = resolverFactory;
```

And it is called during the module resolution process:

```javascript
this.hooks.createModule.callAsync(
  createData,
  resolveData,
  (err, createdModule) => {
    if (!createdModule) {
      if (!resolveData.request) {
        return callback(new Error("Empty dependency (no request)"));
      }

      createdModule = this.hooks.createModuleClass
        .for(
          createData.settings.type
        )
        .call(createData, resolveData);

      if (!createdModule) {
        createdModule = new NormalModule(createData);
      }
    }

    createdModule = this.hooks.module.call(
      createdModule,
      createData,
      resolveData
    );

    return callback(null, createdModule);
  }
);
```
### `normalModuleFactory.hooks.createParser`

#### What it is
`normalModuleFactory.hooks.createParser` is a [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C20-45%2C20) that holds [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C28-45%2C28) instances. Each [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C28-45%2C28) is keyed by a string representing the type of parser and is used to create a parser for that type.

#### How it works
- **HookMap**: A [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C20-45%2C20) is a collection of hooks, each associated with a specific key.
- **SyncBailHook**: A [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C28-45%2C28) is a type of hook that executes its taps (functions) in sequence until one of them returns a non-undefined value, which then becomes the result of the hook.

When a parser is needed, the [createParser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1226%2C18-1226%2C18) hook for the specific type is called. If a parser is created, it is returned; otherwise, an error is thrown.

#### Parameters
- **parserOptions**: Options specific to the parser being created.

#### Example Usage
To add a custom parser creation logic for a specific type, you can tap into the [createParser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1226%2C18-1226%2C18) hook:

```javascript
normalModuleFactory.hooks.createParser.for('customType').tap('MyPlugin', (parserOptions) => {
  // Custom parser creation logic
  return new CustomParser(parserOptions);
});
```

#### Code Reference

```javascript
290:292:lib/NormalModuleFactory.js
createParser: new HookMap(() => new SyncBailHook(["parserOptions"])),
parser: new HookMap(() => new SyncHook(["parser", "parserOptions"])),
```

```typescript
36:37:types/NormalModuleFactory.d.ts
createParser: HookMap<SyncBailHook<[ParserOptions], Parser>>;
```

```typescript
9034:9035:types.d.ts
createParser: HookMap<SyncBailHook<[ParserOptions], Parser>>;
parser: HookMap<SyncBailHook<[any, ParserOptions], void>>;
```

### `normalModuleFactory.hooks.parser`

#### What it is
The `normalModuleFactory.hooks.parser` is a [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#37%2C20-37%2C20) that maps a [SyncHook](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#14%2C2-14%2C2) for parser-related operations. This hook allows you to tap into the parser creation process and modify or extend the behavior of parsers used in the module factory.

#### How to Use It
To use the [parser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1212%2C42-1212%2C42) hook, you need to tap into it using the [for](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1244%2C42-1244%2C42) method with the specific parser type you want to target. You can then add your custom logic to the hook.

#### How It Works
The [parser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1212%2C42-1212%2C42) hook is called with the parser instance and the parser options. You can use this hook to modify the parser instance or perform additional setup.

#### What It's For
The [parser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1212%2C42-1212%2C42) hook is used to customize the behavior of parsers in the module factory. This can be useful for adding custom parsing logic or modifying existing parsers to fit specific needs.

#### Example
Here is an example of how to tap into the [parser](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1212%2C42-1212%2C42) hook to add custom logic:

```javascript
normalModuleFactory.hooks.parser
  .for("javascript/auto")
  .tap("MyCustomParserPlugin", (parser, parserOptions) => {
    // Custom logic to modify the parser
    parser.hooks.someHook.tap("MyCustomParserPlugin", (args) => {
      // Custom behavior
    });
  });
```

#### Parameters
- **parser**: The parser instance that is being created.
- **parserOptions**: The options used to configure the parser.

#### Relevant Code

```javascript
292:294:lib/NormalModuleFactory.js
parser: new HookMap(() => new SyncHook(["parser", "parserOptions"])),
createGenerator: new HookMap(
```

```typescript
38:39:types/NormalModuleFactory.d.ts
parser: HookMap<SyncBailHook<[TODO, ParserOptions], void>>;
```

```typescript
5578:5832:types.d.ts
declare class JavascriptParser extends Parser {
    declarator: SyncBailHook<[VariableDeclarator, Statement], boolean | void>;
    varDeclaration: HookMap<SyncBailHook<[Declaration], boolean | void>>;
    varDeclarationLet: HookMap<SyncBailHook<[Declaration], boolean | void>>;
    varDeclarationConst: HookMap<SyncBailHook<[Declaration], boolean | void>>;
    varDeclarationVar: HookMap<SyncBailHook<[Declaration], boolean | void>>;
    pattern: HookMap<SyncBailHook<[Identifier], boolean | void>>;
```

### `normalModuleFactory.hooks.createGenerator`

#### What it is
`normalModuleFactory.hooks.createGenerator` is a [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#298%2C15-298%2C15) that holds [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#298%2C23-298%2C23) instances. Each [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#298%2C23-298%2C23) is keyed by a string representing the type of generator and is used to create a generator for that type.

#### How it works
- **HookMap**: A [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#298%2C15-298%2C15) is a collection of hooks, each associated with a specific key.
- **SyncBailHook**: A [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#298%2C23-298%2C23) is a type of hook that executes its taps (functions) in sequence until one of them returns a non-undefined value, which then becomes the result of the hook.

When a generator is needed, the [createGenerator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1280%2C2-1280%2C2) hook for the specific type is called. If a generator is created, it is returned; otherwise, an error is thrown.

#### Parameters
- **generatorOptions**: Options specific to the generator being created.

#### Example Usage
To add custom generator creation logic for a specific type, you can tap into the [createGenerator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1280%2C2-1280%2C2) hook:

```javascript
normalModuleFactory.hooks.createGenerator.for('customType').tap('MyPlugin', (generatorOptions) => {
  // Custom generator creation logic
  return new CustomGenerator(generatorOptions);
});
```

#### Code Reference

```javascript
1278:1292:lib/NormalModuleFactory.js
createGenerator(type, generatorOptions = {}) {
    generatorOptions = mergeGlobalOptions(
        this._globalGeneratorOptions,
        type,
        generatorOptions
    );
    const generator = this.hooks.createGenerator
        .for(type)
        .call(generatorOptions);
    if (!generator) {
        throw new Error(`No generator registered for ${type}`);
    }
    this.hooks.generator.for(type).call(generator, generatorOptions);
    return generator;
```

```typescript
40:41:types/NormalModuleFactory.d.ts
createGenerator: HookMap<SyncBailHook<[GeneratorOptions], Generator>>;
```

```typescript
9036:9037:types.d.ts
createGenerator: HookMap<SyncBailHook<[GeneratorOptions], Generator>>;
generator: HookMap<SyncBailHook<[any, GeneratorOptions], void>>;
```

### `normalModuleFactory.hooks.generator`

#### What it is:
`normalModuleFactory.hooks.generator` is a [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#37%2C20-37%2C20) that maps a [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#37%2C28-37%2C28) for generator-related operations. This hook allows you to tap into the generator creation process and modify or extend the behavior of generators used in the module factory.

#### How to Use It:
To use the [generator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1276%2C34-1276%2C34) hook, you need to tap into it using the [for](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1287%2C5-1287%2C5) method with the specific generator type you want to target. You can then add your custom logic to the hook.

#### How It Works:
The [generator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1276%2C34-1276%2C34) hook is called with the generator instance and the generator options. You can use this hook to modify the generator instance or perform additional setup.

#### What It's For:
The [generator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1276%2C34-1276%2C34) hook is used to customize the behavior of generators in the module factory. This can be useful for adding custom generation logic or modifying existing generators to fit specific needs.

#### Example:
Here is an example of how to tap into the [generator](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#1276%2C34-1276%2C34) hook to add custom logic:

```javascript
normalModuleFactory.hooks.generator
  .for("javascript/auto")
  .tap("MyCustomGeneratorPlugin", (generator, generatorOptions) => {
    // Custom logic to modify the generator
    generator.hooks.someHook.tap("MyCustomGeneratorPlugin", (args) => {
      // Custom behavior
    });
  });
```

#### Parameters:
- **generator**: The generator instance that is being created.
- **generatorOptions**: The options used to configure the generator.

#### Relevant Code:

```javascript
1278:1292:lib/NormalModuleFactory.js
createGenerator(type, generatorOptions = {}) {
    generatorOptions = mergeGlobalOptions(
        this._globalGeneratorOptions,
        type,
        generatorOptions
    );
    const generator = this.hooks.createGenerator
        .for(type)
        .call(generatorOptions);
    if (!generator) {
        throw new Error(`No generator registered for ${type}`);
    }
    this.hooks.generator.for(type).call(generator, generatorOptions);
    return generator;
```

```typescript
38:39:types/NormalModuleFactory.d.ts
parser: HookMap<SyncBailHook<[TODO, ParserOptions], void>>;
```

```typescript
9036:9037:types.d.ts
createGenerator: HookMap<SyncBailHook<[GeneratorOptions], Generator>>;
generator: HookMap<SyncBailHook<[any, GeneratorOptions], void>>;
```

### `normalModuleFactory.hooks.createModuleClass`

#### What it is
`createModuleClass` is a hook in the [NormalModuleFactory](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#250%2C7-250%2C7) class, which is part of the Webpack module bundler. This hook is used to create a custom module class during the module creation process.

#### How to use it
To use the `createModuleClass` hook, you need to tap into it using the `tap` method provided by the [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C20-45%2C20) class. This allows you to define custom behavior for creating module classes based on specific conditions.

#### How it works
The `createModuleClass` hook is a [HookMap](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C20-45%2C20) of [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C28-45%2C28) instances. Each [SyncBailHook](file:///Volumes/sandisk/lulu_dev/webpack/types/NormalModuleFactory.d.ts#45%2C28-45%2C28) can be tapped with a function that takes `createData` and `resolveData` as arguments. If the tapped function returns a module, it will be used as the created module class.

#### Parameters
- **createData**: Partial data used to create the module.
- **resolveData**: Data related to the module resolution process.

#### Example
Here is an example of how to tap into the `createModuleClass` hook to create a custom module class:

```javascript
normalModuleFactory.hooks.createModuleClass.for('custom-type').tap('MyPlugin', (createData, resolveData) => {
  if (createData.settings.type === 'custom-type') {
    return new CustomModule(createData);
  }
});
```

#### Relevant Code

```javascript
302:305:lib/NormalModuleFactory.js
createModuleClass: new HookMap(
  () => new SyncBailHook(["createData", "resolveData"])
)
```

This code snippet shows the definition of the `createModuleClass` hook within the `NormalModuleFactory` class.


