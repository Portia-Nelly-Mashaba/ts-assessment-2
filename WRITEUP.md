# Write-up

## 1) What does a generic constraint (`K extends keyof T`) buy you over `any`?

A generic constraint tells TypeScript that the type parameter must be a valid key of a specific object type. That means the compiler can check the relationship between the object and the key before the code runs. For example, if `T` is a `Product`, then `K extends keyof T` means `K` can only be values like `"id"`, `"name"`, `"price"`, or `"inStock"`.

Without that constraint, using `any` would let us pass anything, and TypeScript would no longer help us catch invalid keys or mismatched value types. The constraint makes the API safer and more self-documenting: it preserves type information, prevents wrong property access, and reduces runtime bugs.

## 2) When would you use a mapped type vs a utility type like `Pick`?

A mapped type is useful when you want to transform an object shape in a reusable, custom way. For example, if you want every property to become readonly, nullable, or to rename keys into getter methods, a mapped type is the right tool.

A utility type like `Pick` is better when you want a common built-in transformation with no custom logic. `Pick<T, "id" | "name">` is perfect when you only need a subset of properties and don’t need to change their meanings or names.

In short: use a mapped type when you are defining a new object shape from `T`, and use built-in utility types like `Pick`, `Omit`, `Partial`, and `Record` when the transformation is standard and already provided by TypeScript.

## 3) What is the difference between `unknown` and `any`, and why is a type guard safer than a cast?

`any` disables type checking completely. TypeScript allows you to read properties, assign values freely, and ignore invalid types. That makes code easier to write quickly, but it also removes the compiler’s safety net.

`unknown` is different: it represents values whose type is not known yet. You cannot use an `unknown` value as though it were a specific type until you narrow it. This forces you to validate the value before using it.

A type guard is safer than a cast because it checks runtime conditions before telling TypeScript that a value has a more specific type. For example, `isUser(value)` checks that the value is an object with a numeric `id` and a string `email` before returning `value is User`. A cast like `value as User` simply tells TypeScript to trust you, even if the runtime data is invalid. Type guards keep the program honest and help prevent type-related runtime crashes.

## 4) How does the `never` exhaustiveness check in the reducer protect you?

The reducer uses a `switch` over action types. Each case handles a valid action. The final `default` branch checks that the action is `never`.

If someone later adds a new action type to the union and forgets to handle it in the reducer, TypeScript will complain because the value in the default branch is no longer assignable to `never`. That means the compiler catches unhandled cases during development instead of letting the code compile with a missing branch and fail later at runtime.

This protects the code by enforcing complete handling of the union and making the state machine harder to break unintentionally.
