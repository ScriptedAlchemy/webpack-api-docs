### `JavascriptParser` Class Documentation

The `JavascriptParser` class is a specialized parser for JavaScript code, extending the base `Parser` class. It utilizes the Acorn parser and integrates with the Tapable library to provide a hook-based system for evaluating and transforming JavaScript code.

#### Constructor

```javascript
class JavascriptParser extends Parser {
    /**
     * @param {"module" | "script" | "auto"} sourceType - The default source type for the parser.
     */
    constructor(sourceType = "auto") {
        super();
        this.hooks = Object.freeze({
            evaluateTypeof: new HookMap(() => new SyncBailHook(["expression"])),
            evaluate: new HookMap(() => new SyncBailHook(["expression"])),
            evaluateIdentifier: new HookMap(() => new SyncBailHook(["expression"])),
            evaluateDefinedIdentifier: new HookMap(() => new SyncBailHook(["expression"])),
            evaluateNewExpression: new HookMap(() => new SyncBailHook(["expression"])),
            evaluateCallExpression: new HookMap(() => new SyncBailHook(["expression"])),
            evaluateCallExpressionMember: new HookMap(() => new SyncBailHook(["expression", "param"])),
            isPure: new HookMap(() => new SyncBailHook(["expression", "commentsStartPosition"])),
            preStatement: new SyncBailHook(["statement"]),
            blockPreStatement: new SyncBailHook(["declaration"]),
            statement: new SyncBailHook(["statement"]),
            statementIf: new SyncBailHook(["statement"]),
            classExtendsExpression: new SyncBailHook(["expression", "classDefinition"]),
            classBodyElement: new SyncBailHook(["element", "classDefinition"]),
            classBodyValue: new SyncBailHook(["expression", "element", "classDefinition"]),
            label: new HookMap(() => new SyncBailHook(["statement"])),
            import: new SyncBailHook(["statement", "source"]),
            importSpecifier: new SyncBailHook(["statement", "source", "exportName", "identifierName"]),
            export: new SyncBailHook(["statement"]),
            exportImport: new SyncBailHook(["statement", "source"]),
            exportDeclaration: new SyncBailHook(["statement", "declaration"]),
            exportExpression: new SyncBailHook(["statement", "declaration"]),
            exportSpecifier: new SyncBailHook(["statement", "identifierName", "exportName", "index"]),
            exportImportSpecifier: new SyncBailHook(["statement", "source", "identifierName", "exportName", "index"]),
            preDeclarator: new SyncBailHook(["declarator", "statement"]),
            declarator: new SyncBailHook(["declarator", "statement"]),
            varDeclaration: new HookMap(() => new SyncBailHook(["declaration"])),
            varDeclarationLet: new HookMap(() => new SyncBailHook(["declaration"])),
            varDeclarationConst: new HookMap(() => new SyncBailHook(["declaration"])),
            varDeclarationVar: new HookMap(() => new SyncBailHook(["declaration"])),
            pattern: new HookMap(() => new SyncBailHook(["pattern"])),
            canRename: new HookMap(() => new SyncBailHook(["initExpression"])),
            rename: new HookMap(() => new SyncBailHook(["initExpression"])),
            assign: new HookMap(() => new SyncBailHook(["expression"])),
            assignMemberChain: new HookMap(() => new SyncBailHook(["expression", "members"])),
            typeof: new HookMap(() => new SyncBailHook(["expression"])),
            importCall: new SyncBailHook(["expression"]),
            topLevelAwait: new SyncBailHook(["expression"]),
            call: new HookMap(() => new SyncBailHook(["expression"])),
            callMemberChain: new HookMap(() => new SyncBailHook(["expression", "members", "membersOptionals", "memberRanges"])),
            memberChainOfCallMemberChain: new HookMap(() => new SyncBailHook(["expression", "calleeMembers", "callExpression", "members", "memberRanges"])),
            callMemberChainOfCallMemberChain: new HookMap(() => new SyncBailHook(["expression", "calleeMembers", "innerCallExpression", "members", "memberRanges"])),
            optionalChaining: new SyncBailHook(["optionalChaining"]),
            new: new HookMap(() => new SyncBailHook(["expression"])),
            binaryExpression: new SyncBailHook(["binaryExpression"]),
            expression: new HookMap(() => new SyncBailHook(["expression"])),
            expressionMemberChain: new HookMap(() => new SyncBailHook(["expression", "members", "membersOptionals", "memberRanges"])),
            unhandledExpressionMemberChain: new HookMap(() => new SyncBailHook(["expression", "members"])),
            expressionConditionalOperator: new SyncBailHook(["expression"]),
            expressionLogicalOperator: new SyncBailHook(["expression"]),
            program: new SyncBailHook(["ast", "comments"]),
            finish: new SyncBailHook(["ast", "comments"])
        });
        this.sourceType = sourceType;
        this.scope = undefined;
        this.state = undefined;
        this.comments = undefined;
        this.semicolons = undefined;
        this.statementPath = undefined;
        this.prevStatement = undefined;
        this.destructuringAssignmentProperties = undefined;
        this.currentTagData = undefined;
        this.magicCommentContext = vm.createContext(undefined, {
            name: "Webpack Magic Comment Parser",
            codeGeneration: { strings: false, wasm: false }
        });
        this._initializeEvaluating();
    }
}
```

#### Methods

- **_initializeEvaluating**: Initializes the evaluation hooks for various JavaScript expressions.
- **undefineVariable**: Undefines a variable in the current scope.
- **isVariableDefined**: Checks if a variable is defined in the current scope.
- **getVariableInfo**: Retrieves information about a variable.
- **setVariable**: Sets information for a variable.
- **extractMemberExpressionChain**: Extracts the member expression chain from a given expression.
- **getFreeInfoFromVariable**: Retrieves free variable information.

#### Example Usage

```javascript
const parser = new JavascriptParser("module");
const ast = parser.parse("const x = 42;", { sourceType: "module" });
console.log(ast);
```

#### Hooks

The `JavascriptParser` class provides a comprehensive set of hooks for various stages of parsing and evaluating JavaScript code. These hooks are instances of `HookMap` or `SyncBailHook` from the Tapable library.

- **evaluateTypeof**: Hook for evaluating `typeof` expressions.
- **evaluate**: Hook for evaluating general expressions.
- **evaluateIdentifier**: Hook for evaluating identifiers.
- **evaluateDefinedIdentifier**: Hook for evaluating defined identifiers.
- **evaluateNewExpression**: Hook for evaluating `new` expressions.
- **evaluateCallExpression**: Hook for evaluating call expressions.
- **evaluateCallExpressionMember**: Hook for evaluating call expression members.
- **isPure**: Hook for checking if an expression is pure.
- **preStatement**: Hook for pre-processing statements.
- **blockPreStatement**: Hook for pre-processing block statements.
- **statement**: Hook for processing statements.
- **statementIf**: Hook for processing `if` statements.
- **classExtendsExpression**: Hook for processing class extends expressions.
- **classBodyElement**: Hook for processing class body elements.
- **classBodyValue**: Hook for processing class body values.
- **label**: Hook for processing labeled statements.
- **import**: Hook for processing import statements.
- **importSpecifier**: Hook for processing import specifiers.
- **export**: Hook for processing export statements.
- **exportImport**: Hook for processing export-import statements.
- **exportDeclaration**: Hook for processing export declarations.
- **exportExpression**: Hook for processing export expressions.
- **exportSpecifier**: Hook for processing export specifiers.
- **exportImportSpecifier**: Hook for processing export-import specifiers.
- **preDeclarator**: Hook for pre-processing declarators.
- **declarator**: Hook for processing declarators.
- **varDeclaration**: Hook for processing variable declarations.
- **varDeclarationLet**: Hook for processing `let` declarations.
- **varDeclarationConst**: Hook for processing `const` declarations.
- **varDeclarationVar**: Hook for processing `var` declarations.
- **pattern**: Hook for processing patterns.
- **canRename**: Hook for checking if an expression can be renamed.
- **rename**: Hook for renaming expressions.
- **assign**: Hook for processing assignment expressions.
- **assignMemberChain**: Hook for processing assignment member chains.
- **typeof**: Hook for processing `typeof` expressions.
- **importCall**: Hook for processing import calls.
- **topLevelAwait**: Hook for processing top-level await expressions.
- **call**: Hook for processing call expressions.
- **callMemberChain**: Hook for processing call member chains.
- **memberChainOfCallMemberChain**: Hook for processing member chains of call member chains.
- **callMemberChainOfCallMemberChain**: Hook for processing call member chains of call member chains.
- **optionalChaining**: Hook for processing optional chaining expressions.
- **new**: Hook for processing `new` expressions.
- **binaryExpression**: Hook for processing binary expressions.
- **expression**: Hook for processing general expressions.
- **expressionMemberChain**: Hook for processing expression member chains.
- **unhandledExpressionMemberChain**: Hook for

processing unhandled expression member chains.
- **expressionConditionalOperator**: Hook for processing conditional operator expressions.
- **expressionLogicalOperator**: Hook for processing logical operator expressions.
- **program**: Hook for processing the program AST.
- **finish**: Hook for finishing the processing of the AST.

This documentation provides an overview of the `JavascriptParser` class, its constructor, methods, and hooks. For more detailed usage and examples, refer to the specific methods and hooks in the class implementation.

### `parser.hooks.evaluateTypeof`

#### What It's For
The `evaluateTypeof` hook is used to evaluate `typeof` expressions in JavaScript code, enabling custom logic for evaluating these expressions.

#### How It Works
To use the `evaluateTypeof` hook, you need to tap into it and provide a callback function that will be executed whenever a `typeof` expression is encountered. The callback receives the `UnaryExpression` node and should return a `BasicEvaluatedExpression` or `undefined`.

#### Example Usage
```javascript
parser.hooks.evaluateTypeof.for("Identifier").tap("MyPlugin", expression => {
  if (expression.argument.name === "myVar") {
    return new BasicEvaluatedExpression().setString("string").setRange(expression.range);
  }
});
```

#### Parameters
- **expression**: The `UnaryExpression` node representing the `typeof` expression.
- **returns**: A `BasicEvaluatedExpression` or `undefined`.

#### Relevant Code Snippets
The `evaluateTypeof` hook is defined in the `JavascriptParser` class:

```javascript
class JavascriptParser extends Parser {
  constructor(sourceType = "auto") {
    super();
    this.hooks = Object.freeze({
      evaluateTypeof: new HookMap(() => new SyncBailHook(["expression"])),
    });
  }
}
```

The `BasicEvaluatedExpression` class:

```javascript
const TypeUnknown = 0;
const TypeUndefined = 1;
const TypeNull = 2;
const TypeString = 3;
const TypeNumber = 4;
const TypeBoolean = 5;
const TypeRegExp = 6;
const TypeConditional = 7;
const TypeArray = 8;
const TypeConstArray = 9;
const TypeIdentifier = 10;
const TypeWrapped = 11;
const TypeTemplateString = 12;
const TypeBigInt = 13;
```

### `parser.hooks.evaluate`

#### What It's For
The `parser.hooks.evaluate` hook is used to evaluate different types of expressions in the `JavascriptParser` class, allowing custom evaluation logic for various expression types.

#### How to Use It
Tap into the specific hook for the expression type you are interested in and provide a callback function that will be called with the expression to be evaluated.

#### Example Usage
##### Evaluating Literals
```javascript
parser.hooks.evaluate.for("Literal").tap("MyPlugin", expr => {
  if (typeof expr.value === "number") {
    return new BasicEvaluatedExpression().setNumber(expr.value).setRange(expr.range);
  } else if (typeof expr.value === "string") {
    return new BasicEvaluatedExpression().setString(expr.value).setRange(expr.range);
  }
});
```

##### Evaluating New Expressions
```javascript
parser.hooks.evaluate.for("NewExpression").tap("MyPlugin", expr => {
  const callee = expr.callee;
  if (callee.type !== "Identifier" || callee.name !== "RegExp") return;
  if (expr.arguments.length > 2) return;

  const patternArg = expr.arguments[0];
  const flagsArg = expr.arguments[1];

  let pattern = patternArg ? patternArg.value : '';
  let flags = flagsArg ? flagsArg.value : '';

  return new BasicEvaluatedExpression().setRegExp(new RegExp(pattern, flags)).setRange(expr.range);
});
```

##### Evaluating Logical Expressions
```javascript
parser.hooks.evaluate.for("LogicalExpression").tap("MyPlugin", expr => {
  const left = this.evaluateExpression(expr.left);
  const right = this.evaluateExpression(expr.right);

  if (expr.operator === "&&") {
    if (left.asBool() === false) return left.setRange(expr.range);
    return right.setRange(expr.range);
  } else if (expr.operator === "||") {
    if (left.asBool() === true) return left.setRange(expr.range);
    return right.setRange(expr.range);
  }
});
```

#### Parameters
- **Literal**: The callback receives a `Literal` expression and returns a `BasicEvaluatedExpression` based on the type of the literal value.
- **NewExpression**: The callback receives a `NewExpression` and can evaluate it, particularly handling cases like `new RegExp()`.
- **LogicalExpression**: The callback receives a `LogicalExpression` and evaluates it based on the logical operator and the evaluated values of the left and right expressions.

### `parser.hooks.evaluateIdentifier`

#### What It's For
The `parser.hooks.evaluateIdentifier` hook is used to evaluate specific identifiers within the JavaScript code being parsed.

#### How to Use It
Tap into the specific hook for the identifier you are interested in and provide a callback function that will be called with the identifier to be evaluated.

#### Example Usage
```javascript
parser.hooks.evaluateIdentifier.for("myIdentifier").tap("MyPlugin", expr => {
  return new BasicEvaluatedExpression().setString("evaluatedValue").setRange(expr.range);
});
```

#### Relevant Code
```javascript
parser.hooks.evaluateIdentifier
  .for("import.meta.webpackContext")
  .tap("ImportMetaContextDependencyParserPlugin", expr => {
    return evaluateToIdentifier(
      "import.meta.webpackContext",
      "import.meta",
      () => ["webpackContext"],
      true
    )(expr);
  });
```

### `parser.hooks.evaluateDefinedIdentifier`

#### What It's For
The `evaluateDefinedIdentifier` hook is used to evaluate expressions specifically for identifiers that are defined within the current scope.

#### How to Use It
Tap into this hook to provide custom evaluation logic for defined identifiers.

#### Example Usage
```javascript
parser.hooks.evaluateDefinedIdentifier.for("myVar").tap("MyPlugin", expr => {
  return new BasicEvaluatedExpression().setString("evaluatedValue").setRange(expr.range);
});
```

#### Relevant Code
```javascript
evaluateIdentifier: new HookMap(() => new SyncBailHook(["expression"])),
evaluateDefinedIdentifier: new HookMap(() => new SyncBailHook(["expression"])),
```

```typescript
evaluateDefinedIdentifier: HookMap<
  SyncBailHook<
    [Identifier | MemberExpression | ThisExpression],
    undefined | null | BasicEvaluatedExpression
  >
>;
```

### `parser.hooks.evaluateNewExpression`

#### What It's For
The `evaluateNewExpression` hook is used to evaluate `NewExpression` nodes in the Abstract Syntax Tree (AST).

#### How to Use It
Tap into the `evaluateNewExpression` hook and provide a callback function that will be executed whenever a `NewExpression` is encountered during the parsing process.

#### Example Usage
```javascript
parser.hooks.evaluateNewExpression.for("RegExp").tap("MyPlugin", expr => {
  const callee = expr.callee;
  if (callee.type !== "Identifier") return;
  if (callee.name !== "RegExp") {
    return this.callHooksForName(
      this.hooks.evaluateNewExpression,
      callee.name,
      expr
    );
  } else if (
    expr.arguments.length > 2 ||
    this.getVariableInfo("RegExp") !== "RegExp"
  )
    return;

  let regExp, flags;
  const arg1 = expr.arguments[0];

  if (arg1) {
    if (arg1.type === "SpreadElement") return;

    const evaluatedRegExp = this.evaluateExpression(arg1);

    if (!evaluatedRegExp) return;

    regExp = evaluatedRegExp.asString();

    if (!regExp) return;
  } else {
    return new BasicEvaluatedExpression().setRegExp(new RegExp("")).setRange(expr.range);
  }

  const arg2 = expr.arguments[1];

  if (arg2) {
    if (arg2.type === "SpreadElement") return;

    const evaluatedFlags = this.evaluateExpression(arg2);

    if (!evaluatedFlags) return;

    if (!evaluatedFlags.isUndefined()) {
      flags = evaluatedFlags.asString();

      if (
        flags === undefined ||
        !BasicEvaluatedExpression.isValidRegExpFlags(flags)
      )
        return;
    }
  }

  return new BasicEvaluatedExpression().setRegExp(flags ? new RegExp(regExp, flags) : new RegExp(regExp)).setRange(expr.range);
});
```

#### Parameters
- **expression**: The `NewExpression` node that is being evaluated.

This enhanced documentation provides a more detailed and comprehensive overview of the `JavascriptParser` class, its methods, hooks, and usage examples.