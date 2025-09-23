# Installation of TypeScript Compiler

To install the TypeScript compiler globally using npm, run:

```sh
npm install -g typescript
```

You can verify the installation by checking the version:

```sh
tsc --version
```

# Troubleshooting: "tsc: command not found"

If you see an error like `tsc: command not found` after installing TypeScript globally, it usually means your system's PATH does not include the global npm binaries.

## Solution

1. **Find the global npm bin directory:**

   On Windows, run:
   ```sh
   npm config get prefix
   ```
   The global binaries are usually in the `bin` folder inside this directory (e.g., `C:\Users\<YourUser>\AppData\Roaming\npm`).

2. **Add the npm global bin directory to your PATH:**

   - Open the Start menu and search for "Environment Variables".
   - Click "Edit the system environment variables".
   - In the System Properties window, click "Environment Variables".
   - Under "User variables" or "System variables", find and select the `Path` variable, then click "Edit".
   - Click "New" and add the path to your global npm `bin` directory (e.g., `C:\Users\<YourUser>\AppData\Roaming\npm`).
   - Click OK to save and close all dialogs.

3. **Restart your terminal** and try running:
   ```sh
   tsc -v
   ```

This should resolve the issue. If you still have problems, ensure that TypeScript is installed globally and that you are editing the correct PATH variable.

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