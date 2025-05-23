# Plugin Manifest (`plugin-manifest.json`)

The `plugin-manifest.json` file is essential for every plugin. It provides metadata about the plugin to the CAD/CAM FUN application, such as its name, version, author, and the capabilities it requires.

## Structure

The manifest is a JSON file with the following key properties:

```json
{
  "id": "com.yourcompany.your-plugin-name", // Unique identifier
  "name": "Your Plugin Name",                // Display name
  "version": "1.0.0",                         // Semantic versioning
  "description": "A brief description of what your plugin does.",
  "author": "Your Name or Company",
  "repository": "(Optional) URL of the source code repository",
  "license": "(Optional) SPDX license identifier (e.g., MIT)",
  "icon": "(Optional) Path to an icon file (e.g., icon.png)",
  "main": "dist/main.js",                    // Entry point for plugin code
  "engines": {
    "cadcam": "^1.0.0"                      // Required CAD/CAM FUN version
  },
  "permissions": [
    // List of required API permissions (e.g., "model:read", "ui:sidebar")
  ],
  "contributes": {
    // Definition of UI contributions (sidebars, commands, menus)
  },
  "dependencies": {
    // (Optional) Dependencies on other plugins
  },
  "configuration": {
    // (Optional) Plugin settings configurable by the user
  }
}
```

## Key Fields Explained

-   `id`: A unique identifier, typically in reverse domain name notation.
-   `main`: Path to the main JavaScript file generated by the build process.
-   `engines.cadcam`: Specifies the compatible version range of the CAD/CAM FUN application.
-   `permissions`: Crucial for security. Declares the specific APIs your plugin needs access to. See [API Reference](./api-reference.md#permissions) for available permissions.
-   `contributes`: Defines how your plugin integrates with the UI (e.g., adding sidebar panels, toolbar buttons, context menu items). See [UI Contributions](./api-reference.md#contributions).

## Validation

The SDK provides tools to validate your manifest file. See [Validation & Testing](./validation-testing.md). 