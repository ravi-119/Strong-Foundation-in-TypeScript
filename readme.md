# Installation of TypeScript Compiler

To install the TypeScript compiler globally using npm, run:

```sh
npm install -g typescript
```

You can verify the installation by checking the version:

```sh
tsc --version
```

# Creating a tsconfig.json File

To initialize a TypeScript project and create a `tsconfig.json` file, run:

```sh
tsc --init
```

This command generates a `tsconfig.json` file in your project directory, which allows you to specify the root files and compiler options required to compile the project.

# Continuous Watching Compilation

To automatically recompile your TypeScript files whenever you save changes, use the `--watch` (or `-w`) flag:

```sh
tsc --watch
```

This will keep the TypeScript compiler running in watch mode, recompiling files as needed whenever changes are detected.