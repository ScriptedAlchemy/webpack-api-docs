### [NormalModuleFactory](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#250%2C7-250%2C7) Class Documentation

The [NormalModuleFactory](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#250%2C7-250%2C7) class is a core component in Webpack responsible for creating instances of [NormalModule](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#22%2C7-22%2C7). It extends the [ModuleFactory](file:///Volumes/sandisk/lulu_dev/webpack/lib/NormalModuleFactory.js#250%2C35-250%2C35) class and provides various hooks and methods to resolve and create modules.

#### Constructor

```javascript
constructor({
  context,
  fs,
  resolverFactory,
  options,
  associatedObjectForCache,
  layers = false
})
```

- **context**: *(string, optional)* - The base directory for resolving requests.
- **fs**: *(InputFileSystem)* - The file system to use for resolving files.
- **resolverFactory**: *(ResolverFactory)* - Factory to create resolvers.
- **options**: *(ModuleOptions)* - Configuration options for the module factory.
- **associatedObjectForCache**: *(object, optional)* - An object to which the cache will be attached.
- **layers**: *(boolean, optional)* - Enable layers.

#### Properties

- **hooks**: A collection of hooks used during the module creation process.
- **resolverFactory**: The resolver factory instance.
- **ruleSet**: Compiled rules from the provided options.
- **context**: The base directory for resolving requests.
- **fs**: The file system instance.
- **parserCache**: Cache for parsers.
- **generatorCache**: Cache for generators.
- **_restoredUnsafeCacheEntries**: Set of modules restored from the unsafe cache.

#### Hooks

- **resolve**: *(AsyncSeriesBailHook<[ResolveData], Module | false | void>)* - Hook for resolving modules.
- **resolveForScheme**: *(HookMap<AsyncSeriesBailHook<[ResourceDataWithData, ResolveData], true | void>>)* - Hook for resolving resources with a specific scheme.
- **resolveInScheme**: *(HookMap<AsyncSeriesBailHook<[ResourceDataWithData, ResolveData], true | void>>)* - Hook for resolving resources within a specific scheme.
- **factorize**: *(AsyncSeriesBailHook<[ResolveData], Module | undefined>)* - Hook for factorizing modules.
- **beforeResolve**: *(AsyncSeriesBailHook<[ResolveData], false | void>)* - Hook before resolving modules.
- **afterResolve**: *(AsyncSeriesBailHook<[ResolveData], false | void>)* - Hook after resolving modules.
- **createModule**: *(AsyncSeriesBailHook<[ResolveData["createData"], ResolveData], Module | void>)* - Hook for creating modules.
- **module**: *(SyncWaterfallHook<[Module, ResolveData["createData"], ResolveData], Module>)* - Hook for modifying created modules.
- **createParser**: *(HookMap<SyncBailHook<[ParserOptions], Parser>>)* - Hook for creating parsers.
- **parser**: *(HookMap<SyncHook<[Parser, ParserOptions]>>)* - Hook for modifying parsers.
- **createGenerator**: *(HookMap<SyncBailHook<[GeneratorOptions], Generator>>)* - Hook for creating generators.
- **generator**: *(HookMap<SyncHook<[Generator, GeneratorOptions]>>)* - Hook for modifying generators.
- **createModuleClass**: *(HookMap<SyncBailHook<[TODO, ResolveData], Module>>)* - Hook for creating module classes.

#### Methods

- **cleanupForCache()**
    - Cleans up modules for caching.

- **create(data, callback)**
    - Creates a module based on the provided data.
    - **data**: *(ModuleFactoryCreateData)* - Data for creating the module.
    - **callback**: *(function)* - Callback function.

- **resolveResource(contextInfo, context, unresolvedResource, resolver, resolveContext, callback)**
    - Resolves a resource.
    - **contextInfo**: *(ModuleFactoryCreateDataContextInfo)* - Context information.
    - **context**: *(string)* - The context path.
    - **unresolvedResource**: *(string)* - The unresolved resource.
    - **resolver**: *(ResolverWithOptions)* - The resolver instance.
    - **resolveContext**: *(ResolveContext)* - The resolve context.
    - **callback**: *(function)* - Callback function.

- **getParser(type, parserOptions = EMPTY_PARSER_OPTIONS)**
    - Retrieves a parser for the specified type and options.
    - **type**: *(string)* - The type of parser.
    - **parserOptions**: *(ParserOptions, optional)* - Options for the parser.

- **createParser(type, parserOptions = {})**
    - Creates a parser for the specified type and options.
    - **type**: *(string)* - The type of parser.
    - **parserOptions**: *(ParserOptions, optional)* - Options for the parser.

- **getGenerator(type, generatorOptions = EMPTY_GENERATOR_OPTIONS)**
    - Retrieves a generator for the specified type and options.
    - **type**: *(string)* - The type of generator.
    - **generatorOptions**: *(GeneratorOptions, optional)* - Options for the generator.

- **createGenerator(type, generatorOptions = {})**
    - Creates a generator for the specified type and options.
    - **type**: *(string)* - The type of generator.
    - **generatorOptions**: *(GeneratorOptions, optional)* - Options for the generator.

- **getResolver(type, resolveOptions)**
    - Retrieves a resolver for the specified type and options.
    - **type**: *(string)* - The type of resolver.
    - **resolveOptions**: *(object, optional)* - Options for the resolver.

#### Example Usage

```javascript
const factory = new NormalModuleFactory({
  context: __dirname,
  fs: require('fs'),
  resolverFactory: new ResolverFactory(),
  options: {
    defaultRules: [],
    rules: []
  }
});

factory.create({
  contextInfo: {},
  resolveOptions: {},
  context: __dirname,
  request: './module.js',
  dependencies: []
}, (err, result) => {
  if (err) {
    console.error(err);
  } else {
    console.log(result);
  }
});
```

#### References

- Constructor: `lib/NormalModuleFactory.js` (lines 251-266)
- Properties: `lib/NormalModuleFactory.js` (lines 268-324)
- Hooks: `lib/NormalModuleFactory.js` (lines 269-307)
- Methods: `lib/NormalModuleFactory.js` (lines 832-1302)