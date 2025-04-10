# CAD/CAM FUN Plugin SDK

A comprehensive toolkit for creating, building, testing, and packaging plugins for the CAD/CAM FUN application. This SDK provides a command-line interface (CLI) and a full-featured API for plugin development.

## ⚠️ Important Prerequisites

Before installation, ensure you have:
- Node.js (version 18.0.0 or higher recommended)
- NPM, Yarn, or pnpm

**Required dependency**: This package requires `adm-zip`. If you encounter any errors related to missing modules, install it manually:

```bash
npm install -g adm-zip
```

## Installation

### Global Installation (recommended for CLI usage)

```bash
npm install -g @cadcamfun/cadcamfun-plugins-sdk
npm install -g adm-zip  # Important! Install required dependency
```

### Local Installation (for a specific project)

```bash
npm install --save-dev @cadcamfun/cadcamfun-plugins-sdk
npm install --save-dev adm-zip  # Important! Install required dependency
```

## Quick Start

```bash
# Create a new plugin
cadcamfun-plugin-cli create my-awesome-plugin

# Navigate to the plugin directory
cd my-awesome-plugin

# Run development mode with auto-reload
npm run dev

# Build for production
npm run build

# Create a distributable package
npm run package
```

## CLI Commands

| Command | Description | Example |
|---------|-------------|---------|
| `create` | Creates a new plugin project | `cadcamfun-plugin-cli create my-plugin` |
| `build` | Builds the plugin | `cadcamfun-plugin-cli build --production` |
| `test` | Runs tests | `cadcamfun-plugin-cli test` |
| `package` | Creates a distributable package | `cadcamfun-plugin-cli package` |
| `validate` | Validates a plugin | `cadcamfun-plugin-cli validate` |
| `docs` | Generates documentation | `cadcamfun-plugin-cli docs` |
| `run` | Runs a plugin in development mode | `cadcamfun-plugin-cli run` |

## Detailed Documentation

### Creating a Plugin

The SDK provides templates for different types of plugins:

```bash
cadcamfun-plugin-cli create my-plugin
```

This interactive command will prompt you for:
- Plugin ID (format: domain.organization.plugin-name)
- Plugin name and description
- Author information
- Template type:
  - **basic**: Minimal plugin with core functionality
  - **sidebar**: Plugin with a sidebar panel
  - **toolbar**: Plugin with toolbar buttons and commands
  - **full**: Complete plugin with sidebar, toolbar, and more
- Language preference (TypeScript or JavaScript)
- Git and dependency options

### Plugin Project Structure

```
my-plugin/
├── assets/               # Plugin assets (icons, images)
├── dist/                 # Compiled output (generated during build)
├── src/                  # Source code
│   ├── main.ts           # Main entry point
│   ├── commands.ts       # Command handlers (if using toolbar/full template)
│   └── ui/               # UI components
│       └── sidebar.ts    # Sidebar implementation (if using sidebar/full template)
├── tests/                # Test files
├── plugin.json           # Plugin manifest
├── package.json          # NPM package config
├── tsconfig.json         # TypeScript config (if using TypeScript)
├── webpack.config.js     # Build configuration
└── README.md             # Plugin documentation
```

### Plugin Manifest (plugin.json)

The manifest defines your plugin's metadata, permissions, and UI contributions:

```json
{
  "id": "com.example.my-plugin",
  "name": "My Plugin",
  "version": "0.1.0",
  "description": "A plugin for CAD/CAM FUN",
  "author": "Your Name",
  "main": "dist/main.js",
  "engines": {
    "cadcam": "^1.0.0"
  },
  "permissions": [
    "model:read",
    "ui:sidebar"
  ],
  "contributes": {
    "sidebar": {
      "title": "My Plugin",
      "icon": "assets/icon.svg",
      "entry": "dist/ui/sidebar.js"
    }
  }
}
```

### Plugin Lifecycle

Your plugin should implement these functions:

```typescript
// Initialize the plugin
export function initialize(context: CADCAM.PluginInitializationContext, api: CADCAM.PluginAPI): void {
  // Register for cleanup
  context.onDeactivation(() => {
    // Clean up resources
  });
  
  // Setup initial resources
  api.host.log('Plugin initialized');
}

// Activate the plugin when it starts
export async function activate(api: CADCAM.PluginAPI): Promise<void> {
  // Start your plugin functionality
  
  // Register event handlers
  api.model.onSelectionChanged((selection) => {
    // Handle selection changes
  });
  
  // Notify the host that we're ready
  await api.host.notifyReady();
}

// Optional: Clean up when plugin is deactivated
export function deactivate(): void {
  // Release resources
}
```

### Building & Development

During development:
```bash
npm run dev
# or
cadcamfun-plugin-cli build --watch
```

For production:
```bash
npm run build
# or
cadcamfun-plugin-cli build --production
```

### Testing

Run tests with:
```bash
npm test
# or 
cadcamfun-plugin-cli test
```

For UI component testing:
```bash
cadcamfun-plugin-cli test --ui
```

### Packaging

Create a distributable package:
```bash
npm run package
# or
cadcamfun-plugin-cli package
```

### Validation

Ensure your plugin meets all requirements:
```bash
npm run validate
# or
cadcamfun-plugin-cli validate
```

## API Examples

### Working with the Model

```typescript
// Get selected entities
const selection = await api.model.getSelection();

// Get entity details
for (const id of selection) {
  const entity = await api.model.getEntityById(id);
  api.host.log(`Selected: ${entity.name} (${entity.type})`);
}

// Create a new entity
const newEntity = await api.model.createEntity({
  type: 'cube',
  name: 'My Cube',
  data: {
    width: 10,
    height: 10,
    depth: 10
  }
});

// Transform entities
await api.model.transformEntities([newEntity.id], {
  translate: { x: 10, y: 0, z: 0 }
});
```

### UI Interactions

```typescript
// Show a notification
await api.ui.showNotification('Operation completed!', {
  type: 'success',
  duration: 3000
});

// Show a dialog
const result = await api.ui.showDialog({
  title: 'Confirm Action',
  message: 'Do you want to proceed?',
  type: 'question',
  buttons: [
    { text: 'Yes', id: 'yes', primary: true },
    { text: 'No', id: 'no' }
  ]
});

if (result.buttonId === 'yes') {
  // User confirmed
}
```

### Creating Sidebar UI

```typescript
export function initializeSidebar(hostElement: HTMLElement, api: CADCAM.PluginAPI): void {
  // Create UI
  const container = document.createElement('div');
  
  // Add button
  const button = document.createElement('button');
  button.textContent = 'Get Selection';
  button.addEventListener('click', async () => {
    const selection = await api.model.getSelection();
    // Display selection info
  });
  
  container.appendChild(button);
  hostElement.appendChild(container);
}
```

### Command Handlers

```typescript
export function registerCommands(api: CADCAM.PluginAPI): void {
  // Register command handler
  api.events.on(`command:com.example.my-plugin.command1`, () => {
    handleCommand(api);
  });
}

async function handleCommand(api: CADCAM.PluginAPI): Promise<void> {
  // Command implementation
  const selection = await api.model.getSelection();
  
  if (selection.length === 0) {
    await api.ui.showNotification('Please select an entity first', {
      type: 'warning'
    });
    return;
  }
  
  // Process selection
  // ...
  
  await api.ui.showNotification('Command completed successfully');
}
```

## Troubleshooting

### Common Issues

1. **"Cannot find module 'adm-zip'"**
   - Install adm-zip globally: `npm install -g adm-zip`
   - Or locally: `npm install adm-zip`

2. **Build fails with TypeScript errors**
   - Check for type errors in your code
   - Make sure you're using compatible TypeScript versions
   - Verify tsconfig.json settings

3. **Plugin doesn't appear in the application**
   - Ensure plugin.json is correctly formatted
   - Check that all required files are in the correct locations
   - Verify the main entry point exists and is correctly referenced

4. **Permission errors during execution**
   - Check that you have the necessary permissions in plugin.json
   - Ensure your plugin only uses APIs that match its permissions

### Support

If you encounter issues:
1. Check the validation results (`cadcamfun-plugin-cli validate`)
2. Review the API documentation
3. Check the console for error messages
4. Ensure dependencies are correctly installed

## Best Practices

1. **Permissions**: Request only what your plugin needs
2. **Error Handling**: Always use try/catch with async operations
3. **Cleanup**: Dispose of event listeners and resources
4. **UI Performance**: Minimize DOM manipulations
5. **Version Control**: Use semantic versioning for your plugin
6. **Documentation**: Provide clear usage instructions in your README

## License

MIT

## Contributing

Contributions welcome! Please submit issues and pull requests to help improve the SDK.

---

Happy plugin development! If you have questions or feedback, please open an issue on our GitHub repository.