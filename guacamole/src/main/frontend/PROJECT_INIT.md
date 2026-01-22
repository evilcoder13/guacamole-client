# Guacamole Frontend Project

## Technology Stack

### Core
- **Framework**: AngularJS v1.8.3
- **Router**: Angular Route v1.8.3
- **i18n**: Angular Translate v2.19.1 (18 languages)
- **DOM**: jQuery v3.7.1
- **Utilities**: Lodash v4.17.21

### Build Tools
- **Bundler**: Webpack v4.47.0
- **Transpiler**: Babel v7.24.7 (ES2020 → ES5)
- **Minifier**: Google Closure Compiler 2024.03.17
- **CSS**: CSS-Minimizer v1.3.0

### Specialized Libraries
- Color picker: @simonwep/pickr v1.8.2
- Visualization: d3-path v3.1.0, d3-shape v3.2.0
- Search: fuzzysort v3.0.2
- File handling: file-saver v2.0.5, csv v6.3.9, yaml v2.5.0

## Project Structure

```
src/
├── app/                    # Feature modules (25 modules)
│   ├── index/             # Root module, routing, app state
│   ├── auth/              # Authentication token management
│   ├── client/            # Connection client management (largest)
│   ├── rest/              # REST API integration layer
│   ├── manage/            # Administration UI (connections, users, groups)
│   ├── settings/          # User preferences, CSV import/export
│   ├── form/              # Dynamic form generation
│   ├── home/              # Home page with connection lists
│   ├── player/            # Session recording playback
│   ├── login/             # Login functionality
│   └── navigation/        # Menu, tabs, breadcrumbs
├── fonts/                 # Font files
├── images/                # Application images
├── layouts/               # Keyboard layouts
├── translations/          # i18n JSON files (18 languages)
└── index.html             # Main HTML template
```

## Build Commands

```bash
# Install dependencies
npm install

# Build for production
npm run build
# Equivalent: webpack --progress

# Output: dist/guacamole.[contenthash].js
#         dist/guacamole.[contenthash].css
```

## Key Entry Points

**Application Entry**: `src/app/index/indexModule.js`
- Loads all 25 feature modules recursively
- Main controller: `indexController`

**Routing**: `src/app/index/config/indexRouteConfig.js`
- Hash-based routing (e.g., `#/client/123`)
- Routes: `/`, `/import`, `/settings`, `/manage`, `/client/:id`

## Important Patterns

### Dependency Injection
```javascript
angular.module('rest').factory('connectionService', ['$injector',
    function($injector) {
        const authenticationService = $injector.get('authenticationService');
        // ...
    }
]);
```

### Type Definitions
Services as factory functions that return object constructors:
```javascript
angular.module('rest').factory('Connection', [function() {
    return function Connection(template) {
        this.identifier = template.identifier;
        // ...
    };
}]);
```

### REST API Calls
```javascript
authenticationService.request({
    method: 'GET',
    url: 'api/session/data/' + dataSource + '/connections'
});
```

### State Management
- **Auth token**: localStorage key `'GUAC_AUTH_TOKEN'`
- **Preferences**: localStorage key `'GUAC_PREFERENCES'`
- **App state**: AngularJS `$scope` in controllers
- **Events**: `$rootScope` broadcasts (e.g., `guacLogin`, `guacLogout`)

## Module Sizes (largest)

1. `client` (496K) - Active connections, display rendering, file transfers
2. `rest` (312K) - REST API types (17) and services (18)
3. `manage` (300K) - Connection/user/group management UI
4. `form` (300K) - Dynamic form generation
5. `settings` (260K) - User preferences and history

## API Endpoints Pattern

```
/api/session/data/{dataSource}/connections
/api/session/data/{dataSource}/users
/api/session/data/{dataSource}/userGroups
/api/session/data/{dataSource}/permissions
/api/session/tunnels
/api/tokens
```

## Extension Points

### Adding a New Module
1. Create directory: `src/app/{moduleName}/`
2. Create `{moduleName}Module.js` with Angular module definition
3. Add module dependency to `src/app/index/indexModule.js`
4. Follow structure: `types/`, `services/`, `controllers/`, `directives/`, `templates/`, `styles/`

### Adding Translations
1. Add keys to `src/translations/*.json`
2. Format: `{"SECTION": {"KEY": "Translation"}}`
3. Use in templates: `{{'SECTION.KEY' | translate}}`

## Build Output

- **JavaScript**: `dist/guacamole.[contenthash].js`
- **CSS**: `dist/guacamole.[contenthash].css`
- **Assets**: Copied to `dist/` (fonts, images, layouts, translations, manifest)

## Testing

**No automated tests configured** - manual testing approach only.

## Known Limitations

1. Legacy AngularJS 1.x framework
2. No automated tests (unit/integration/E2E)
3. No CSS preprocessor
4. No TypeScript type checking

## Development Notes

- HTML templates are bundled via `AngularTemplateCacheWebpackPlugin`
- CSS is extracted to separate file, loaded before extension CSS for override support
- All JS/CSS in module directories is recursively loaded
- Source maps enabled in webpack config
