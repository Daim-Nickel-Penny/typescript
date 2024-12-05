# TypeScript

These guidelines are adapted from the TypeScript core's contributor coding guidelines.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

**Table of Contents**

* [Names](#names)
* [Exports](#exports)
* [Components](#components)
* [Types](#types)
* [`null` and `undefined`](#null-and-undefined)
* [General Assumptions](#general-assumptions)
* [Flags](#flags)
* [Comments](#comments)
* [Strings](#strings)
* [When to use `any`](#when-to-use-any)
* [Diagnostic Messages](#diagnostic-messages)
* [General Constructs](#general-constructs)
* [Style](#style)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Names

1.  Use PascalCase for type names.
    ```ts
    type UserDetails = {
    name: string;
    age: number;
    };
    ```
2.  Do not use "I" as a prefix for interface names.
    ```ts
    interface UserDetails {
    name: string;
    age: number;
    }
    ```
3.  Use PascalCase for enum values.
    ```ts
    enum Status {
      Active,
      Inactive,
      Pending
    }
    ```

4.  Use camelCase for function names.
    ```ts
    function getUserDetails(userId: number): UserDetails {
    // function implementation
    return { name: 'John', age: 30 };
    }
    ```
5.  Use camelCase for property names and local variables.
    ```ts
    class User {
    userName: string;
    userAge: number;

    constructor(userName: string, userAge: number) {
        this.userName = userName;
        this.userAge = userAge;
    }
    }

    const userName = 'John';
    ```
6.  Do not use "\_" as a prefix for private properties.
    ```ts
    // Instead of using "_userName"
    class User {
    private userName: string;

    constructor(userName: string) {
        this.userName = userName;
    }
    }
    ```


7.  Use whole words in names when possible.
    ```ts
    // Instead of "usr" use "user"
    const user = { name: 'John', age: 30 };
    ```


8.  Use `isXXXing` or `hasXXXXed` for variables representing states of things (e.g. `isLoading`, `hasCompletedOnboarding`).
    ```ts
    let isDataLoading: boolean = true;
    let hasCompletedOnboarding: boolean = false;
    ```

9.  Give folders/files/components/functions unique names.
    ```ts
    // Avoid naming conflicts by giving unique, descriptive names
    import { UserDetailsComponent } from './UserDetailsComponent';
    ```



## Exports

1.  Only use named exports. The only exceptions are a tool requires default exports (e.g `React.lazy()`, Gatsby and Next.js `pages`, `typography.js`)

    ```ts
    // Named export
    export const getUserDetails = (userId: number): UserDetails => {
         return { name: 'John', age: 30 };
    };

    // Default export (e.g., for React.lazy)
    const Typography = () => {
         return <div>Typography Component</div>;
    };

    export default Typography;
    ```

## Components

1.  1 file per logical component (e.g. parser, scanner, emitter, checker).
    ```ts
    // File: UserDetailsComponent.tsx
    const UserDetailsComponent: React.FC<{ user: UserDetails }> = ({ user }) => {
         return <div>{user.name}</div>;
    };

    export default UserDetailsComponent;
    ``` 

2.  If not kept in a separate folder, files with ".generated.\*" suffix are auto-generated, do not hand-edit them.
    ```ts
    // Auto-generated file (e.g., UserDetails.generated.ts)
    export type GeneratedUserDetails = {
        id: number;
        username: string;
    };
    ``` 

3.  Tests should be kept in the same directory with ".test.\*" suffix
    ```ts
    // File: UserDetailsComponent.test.tsx
    import { render } from '@testing-library/react';
    import UserDetailsComponent from './UserDetailsComponent';

    test('displays user name', () => {
        render(<UserDetailsComponent user={{ name: 'John', age: 30 }} />);
        expect(screen.getByText('John')).toBeInTheDocument();
    });
    ```
4.  Filename should match the component name. Interfaces for React components should be called `<ComponentName>Props` and `<ComponentName>State`. The only exception is when writing a render prop. In this situation, you, the author, should call the interface for your component's props `<ComponentName>Config` and then the render prop interface `<ComponentName>Props` so it is easy for everyone else to use.
    ```ts
    // File: UserDetailsComponent.tsx
    interface UserDetailsComponentProps {
    user: UserDetails;
    }

    const UserDetailsComponent: React.FC<UserDetailsComponentProps> = ({ user }) => {
    return <div>{user.name}</div>;
    };
    ```
## Types

1.  Do not export types/functions unless you need to share it across multiple components.
    ```ts
    // Don't export unless necessary
    type UserDetails = {
        name: string;
        age: number;
    };
    ```
2.  Do not introduce new types/values to the global namespace.
    ```ts
    // Avoid polluting the global namespace
    type UserDetails = {
    name: string;
    age: number;
    };
    ```

3.  Shared types should be defined in 'types.ts'.
    ```ts
    // types.ts
    export type UserDetails = {
    name: string;
    age: number;
    };
    ```     
4.  Within a file, type definitions should come first (after the imports).
    ```ts
    // File: UserDetailsComponent.tsx
    import React from 'react';

    type UserDetails = {
    name: string;
    age: number;
    };

    const UserDetailsComponent: React.FC<{ user: UserDetails }> = ({ user }) => {
    return <div>{user.name}</div>;
    };

    ```

## `null` and `undefined`

1.  Use **undefined**. Do not use `null`. EVER. If null is used (like in legacy Redux code), it should be kept isolated from other code with selectors.

    ```ts
    let userDetails: UserDetails | undefined = undefined;

    function setUserDetails(details: UserDetails | undefined) {
    userDetails = details;
    }
    ```


## General Assumptions

1.  Consider objects like Nodes, Symbols, etc. as immutable outside the component that created them. Do not change them.
    ```ts
    const userDetails: UserDetails = { name: 'John', age: 30 };

    // Do not mutate userDetails
    userDetails.name = 'Jane'; // This is wrong, userDetails should remain immutable
    ```
2.  Consider arrays as immutable by default after creation.
    ```ts
    const users: UserDetails[] = [{ name: 'John', age: 30 }];
    users.push({ name: 'Jane', age: 25 }); // Avoid mutation like this
    ```

## Flags

1.  More than 2 related Boolean properties on a type should be turned into a flag.

    ```ts
    type UserFlags = {
    isActive: boolean;
    isVerified: boolean;
    isPremium: boolean;
    };

    type User = {
    name: string;
    flags: UserFlags;
    };
    ```

## Comments

1.  Use JSDoc style comments for functions, interfaces, enums, and classes.
    ```ts
    /**
     * Represents user details
     */
    interface UserDetails {
    name: string;
    age: number;
    }

    /**
     * Gets user details
     * @param userId - ID of the user
     * @returns The user details
     */
    function getUserDetails(userId: number): UserDetails {
    return { name: 'John', age: 30 };
    }
    ```

2.  **Document crazy stuff.** Always add `@see <url>` and the current date when referencing external resources, blog posts, tweets, snippets, gists, issues etc.
    ```ts
    /**
     * Fetches user details
     * @see https://example.com/user-details-api
     * @date 2024-12-05
     */
    function getUserDetails(userId: number): UserDetails {
    return { name: 'John', age: 30 };
    }
    ```

3.  Make note conditions upon which hacks and smelly code can be removed.
    ```ts
    /**
     * This is a temporary hack for the legacy issue with user ID format.
     * @see https://example.com/legacy-user-id
     * @date 2024-12-05
     */
    function getUserDetails(userId: string): UserDetails {
    if (userId.includes('legacy')) {
        return { name: 'John', age: 30 };
    }
    // Hack to handle old IDs
    return { name: 'Jane', age: 25 };
    }
    ```

## Strings

1.  Use single quotes for strings. Double quotes around JSX string props.
    ```ts
    const message = 'Hello, world!';
    const userName = 'John';

    const element = <div className="user-name">{userName}</div>;
    ```

2.  All strings visible to the user need to be localized (make an entry in diagnosticMessages.json).

    ```ts
    const message = 'Hello, world!'; // This should be in diagnosticMessages.json
    ```


## When to use `any`


1.  If something takes you longer than 10 minutes to type or you feel the need to read through TS Advanced Types docs, you should take a step back and ask for help, or use `unknown`.
    ```ts
    let data: unknown = fetchData();
    ```
2.  Custom typings of 3rd-party modules should be added to a `.d.ts` file in a `typings` directory. Document the date and version of the module you are typing at the top of the file.
    ```ts
    // File: typings/custom-third-party.d.ts
    // @date 2024-12-05
    // @version 1.0.0

    declare module 'third-party-library' {
    export function thirdPartyFunction(): void;
    }
    ```
3.  Consider rewriting tiny modules in typescript if types are too hard to think through.
    ```ts
    // If types are hard to manage in JS, rewrite the module in TS
    function calculateSum(a: number, b: number): number {

    ```
4.  Use `unknown`
    ```ts
    let data: unknown = fetchData();
    if (typeof data === 'string') {
    console.log(data.toUpperCase());
    }
    ```

## Diagnostic Messages

1.  Use a period at the end of a sentence.
    ```ts
    const message = 'User not found.';
    ```
2.  Use indefinite articles for indefinite entities.
    ```ts
    const message = 'A user was not found.';
    ```
3.  Definite entities should be named (this is for a variable name, type name, etc..).
    ```ts
    const user: UserDetails = { name: 'John', age: 30 };
    ```
4.  When stating a rule, the subject should be in the singular (e.g. "An external module cannot..." instead of "External modules cannot...").
    ```
    // Rule: An external module cannot mutate the user data directly.
    ```
5.  Use present tense.
    ```
    // Correct: "The function returns the user details."
    ```
6.  Use active voice.
    ```
    // Correct: "The function updates the user data."
    ```

## General Constructs

For a variety of reasons, we avoid certain constructs, and use some of our own. Among them:

1.  Prefer `for..in` loops over `.reduce` if it makes the code clearer or if you think you may need to do async work since you can `await` inside of it.

    ```ts
    for (const key in userDetails) {
    console.log(`${key}: ${userDetails[key]}`);
    }
    ```


## Style

0.  Use prettier and tslint/eslint. [Biome as well]
    ```ts
    // Ensure prettier,biome and eslint are configured and running in the project.
    ```
1.  Use arrow functions over anonymous function expressions.
    ```ts
      const add = (x: number, y: number) => x + y;
    ```


1.  Only surround arrow function parameters when necessary. <br />For example, `(x) => x + x` is wrong but the following are correct:
    1.  `x => x + x`
    2.  `(x,y) => x + y`
    3.  `<T>(x: T, y: T) => x === y`
    
1.  Always surround loop and conditional bodies with curly braces. Statements on the same line are allowed to omit braces.
    ```ts
    for (let i = 0; i < 10; i++) {
        console.log(i);
    }
    ```

1.  Open curly braces always go on the same line as whatever necessitates them.
    ```ts
    if (x < 10) { 
    console.log(x);
    }
    ```

1.  Parenthesized constructs should have no surrounding whitespace. <br />A single space follows commas, colons, and semicolons in those constructs. For example:
    1.  `for (var i = 0, n = str.length; i < 10; i++) { }`
    2.  `if (x < 10) { }`
    3.  `function f(x: number, y: string): void { }`
1.  Use a single declaration per variable statement <br />(i.e. use `var x = 1; var y = 2;` over `var x = 1, y = 2;`).
1.  Use 2 spaces per indentation.



---

MIT LICENSE
