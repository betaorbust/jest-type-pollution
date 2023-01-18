# Repro case for @jest/types global type pollution

This Typescript repo is set up in to target ES5, but because `@jest/types` is
installed (and it has a dependency of `@types/node@latest`), it pollutes the
global lib type space with type definitions of the highest language support
level. The following should throw a type error because `fromEntries` is not part
of ES5. The fact that it doesn't is shared across the entire production codebase
which is unsafe in a library targeting a non-esnext environment.

```ts
// This should throw a type error because fromEntries is not a member of
// the ObjectConstructor interface.
Object.fromEntries([['test': 'value']])
```

To exercise the incorrect behavior:

```sh
# Produces no error
npm install && npm run build

# Remove @jest/types with global @types/node subdependency
npm uninstall @jest/types
rm -rf node_modules

# Throws expected type error
npm install && npm run build

# Reset to try again
git reset HEAD --hard
```
