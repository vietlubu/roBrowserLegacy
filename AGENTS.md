# AI Agent Development Guide for roBrowserLegacy

This document provides comprehensive guidance for AI agents (GitHub Copilot, Cursor, etc.) working on the roBrowserLegacy project.

## Project Overview

**roBrowserLegacy** is a web-based Ragnarok Online client that runs entirely in modern browsers using WebGL for 3D rendering and WebSocket for network communication. This is a continuation of the original roBrowser project with expanded features.

### Technology Stack
- **JavaScript** with AMD module pattern (RequireJS)
- **WebGL** for 3D graphics rendering
- **WebSocket** for game server communication
- **Node.js** for build tools and development
- **jQuery** for DOM manipulation
- **No TypeScript** - Pure JavaScript project

### Key Architectural Principles
1. **AMD Modules**: All modules use `define()` with RequireJS
2. **Event-Driven**: Heavy use of event system for component communication
3. **Packet-Based**: Network communication via structured packets
4. **Component-Based UI**: Modular UI components with version management
5. **Asset Loading**: Dynamic loading from GRF files or remote client

## Project Structure

```
roBrowserLegacy/
├── src/                          # Main source code
│   ├── App/                      # Application entry points
│   │   ├── Online.js            # Main game client
│   │   ├── MapViewer.js         # Map viewer tool
│   │   ├── ModelViewer.js       # Model viewer tool
│   │   └── ...                  # Other viewers
│   ├── Audio/                    # Audio management
│   │   ├── BGM.js               # Background music
│   │   └── SoundManager.js      # Sound effects
│   ├── Controls/                 # Input handling
│   │   ├── KeyEventHandler.js   # Keyboard input
│   │   ├── MouseEventHandler.js # Mouse input
│   │   └── MapControl.js        # Map interaction
│   ├── Core/                     # Core systems
│   │   ├── Client.js            # Client initialization & GRF loading
│   │   ├── Configs.js           # Configuration management
│   │   ├── Thread.js            # Web Worker management
│   │   ├── MemoryManager.js     # Memory management
│   │   └── Events.js            # Event system
│   ├── DB/                       # Game database
│   │   ├── DBManager.js         # Database manager
│   │   ├── Items/               # Item definitions
│   │   ├── Skills/              # Skill definitions
│   │   ├── Jobs/                # Job/Class definitions
│   │   ├── Monsters/            # Monster data
│   │   └── Status/              # Status effects
│   ├── Engine/                   # Game engines
│   │   ├── LoginEngine.js       # Login screen logic
│   │   ├── CharEngine.js        # Character selection logic
│   │   ├── GameEngine.js        # Main game loop
│   │   ├── MapEngine.js         # Map/gameplay logic
│   │   └── SessionStorage.js    # Session data
│   ├── Loaders/                  # Asset loaders
│   │   ├── Model.js             # 3D model loader
│   │   ├── Sprite.js            # Sprite loader
│   │   ├── Action.js            # Animation loader
│   │   └── Ground.js            # Ground/terrain loader
│   ├── Network/                  # Network layer
│   │   ├── NetworkManager.js    # Connection management
│   │   ├── PacketStructure.js   # Packet definitions
│   │   ├── PacketRegister.js    # Packet handler registry
│   │   ├── PacketLength.js      # Packet length lookup
│   │   ├── PacketCrypt.js       # Packet encryption
│   │   ├── PacketVerManager.js  # Packet version management
│   │   └── Packets/             # Packet handlers by type
│   ├── Renderer/                 # Rendering system
│   │   ├── Renderer.js          # Main WebGL renderer
│   │   ├── Camera.js            # Camera controller
│   │   ├── MapRenderer.js       # Map rendering
│   │   ├── EntityManager.js     # Entity management
│   │   ├── EffectManager.js     # Effect rendering
│   │   ├── Entity/              # Entity rendering components
│   │   ├── Effects/             # Effect components
│   │   └── Map/                 # Map rendering components
│   ├── UI/                       # User Interface
│   │   ├── UIManager.js         # UI lifecycle management
│   │   ├── UIVersionManager.js  # Version-specific UI handling
│   │   ├── CursorManager.js     # Custom cursor system
│   │   └── Components/          # UI components
│   │       ├── Inventory/       # Inventory window
│   │       ├── ChatBox/         # Chat interface
│   │       ├── Equipment/       # Equipment window
│   │       ├── SkillList/       # Skill window
│   │       └── ...              # Many more components
│   ├── Utils/                    # Utility modules
│   │   ├── BinaryReader.js      # Binary data reading
│   │   ├── BinaryWriter.js      # Binary data writing
│   │   ├── Texture.js           # Texture management
│   │   ├── WebGL.js             # WebGL utilities
│   │   └── ...
│   └── Vendors/                  # Third-party libraries
│       ├── require.js           # RequireJS AMD loader
│       ├── jquery.js            # jQuery
│       ├── gl-matrix.js         # WebGL math library
│       └── ...
├── applications/                 # Application configs
│   ├── tools/                   # Build tools
│   │   └── builder-web.js       # Main build script
│   ├── browser-examples/        # Demo examples
│   ├── pwa/                     # PWA configuration
│   └── nwjs/                    # Desktop app config
├── dist/                         # Build output
├── doc/                          # Documentation
└── AI/                           # AI bot scripts

```

## Coding Conventions

### Module Definition Pattern

**ALWAYS** use this AMD module pattern:

```javascript
/**
 * Module/Path/FileName.js
 *
 * Brief description of what this module does
 *
 * This file is part of ROBrowser, (http://www.robrowser.com/).
 *
 * @author Author Name (optional)
 */
define(function(require)
{
	'use strict';

	// Load dependencies
	var DependencyA = require('Path/To/DependencyA');
	var DependencyB = require('Path/To/DependencyB');

	// Module implementation
	var ModuleName = {};

	ModuleName.someMethod = function() {
		// Implementation
	};

	// Return the module
	return ModuleName;
});
```

### Indentation & Formatting
- **Tabs** for indentation (size: 4 spaces equivalent)
- **UTF-8** encoding
- **LF** line endings (except Windows batch files use CRLF)
- Opening braces on same line for functions/conditionals
- Space after keywords: `if (`, `for (`, `while (`
- No trailing whitespace

### Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| **Modules/Classes** | PascalCase | `EntityManager`, `MapRenderer` |
| **Functions/Methods** | camelCase | `initialize()`, `getEntity()` |
| **Private functions** | camelCase with underscore | `_privateMethod()` |
| **Variables** | camelCase | `playerEntity`, `currentMap` |
| **Constants** | UPPER_SNAKE_CASE | `MAP_NAME_LENGTH`, `PACKET_VER` |
| **Packet names** | UPPER_SNAKE_CASE with prefix | `PACKET.CZ.REQUEST_MOVE` |
| **Files** | PascalCase.js | `EntityManager.js`, `PacketStructure.js` |

### Documentation

Use JSDoc-style comments for all public functions:

```javascript
/**
 * Brief description of function
 *
 * @param {type} paramName - Description
 * @param {optional|type} optionalParam - Description
 * @return {type} Description
 */
function myFunction(paramName, optionalParam) {
	// Implementation
}
```

### Variable Declaration
- Use `var` for all variable declarations (ES5 style)
- Declare variables at the top of their scope
- One variable per line for clarity

```javascript
var entity;
var position;
var animation;
```

## Network Layer (Critical)

### Packet Structure

Packets follow a strict naming and structure convention:

```javascript
// Packet definition in PacketStructure.js
PACKET.CZ.REQUEST_MOVE = function PACKET_CZ_REQUEST_MOVE() {
	this.dest = [0, 0, 0]; // x, y, dir
};

PACKET.CZ.REQUEST_MOVE.prototype.build = function() {
	var pkt_len = 2 + 3; // header + data
	var pkt_buf = new BinaryWriter(pkt_len);
	
	pkt_buf.writeShort(0x0085); // Packet ID
	pkt_buf.writeUByte(this.dest[0]);
	pkt_buf.writeUByte(this.dest[1]);
	pkt_buf.writeUByte(this.dest[2]);
	
	return pkt_buf.buffer;
};
```

### Packet Naming Convention

| Direction | Prefix | Meaning | Example |
|-----------|--------|---------|---------|
| Client → Login | CA | Client to Account | `PACKET.CA.LOGIN` |
| Login → Client | AC | Account to Client | `PACKET.AC.ACCEPT_LOGIN` |
| Client → Char | CH | Client to Character | `PACKET.CH.SELECT_CHAR` |
| Char → Client | HC | Character to Client | `PACKET.HC.ACCEPT_ENTER` |
| Client → Map | CZ | Client to Zone | `PACKET.CZ.REQUEST_MOVE` |
| Map → Client | ZC | Zone to Client | `PACKET.ZC.NOTIFY_MOVE` |
| Client → Any | CS | Client to Server | `PACKET.CS.REQUEST_HASH` |
| Any → Client | SC | Server to Client | `PACKET.SC.NOTIFY_BAN` |

### Packet Handler Registration

```javascript
// In Network/Packets/PACKET_NAME.js
define(function(require) {
	'use strict';

	var Network = require('Network/NetworkManager');
	var PACKET = require('Network/PacketStructure');

	function pktSomePacketHandler(pkt) {
		// Handle packet data
		var data = pkt.someField;
		// Process...
	}

	// Register handler
	Network.hookPacket(PACKET.ZC.SOME_PACKET, pktSomePacketHandler);
});
```

## UI Component Pattern

### Component Structure

UI components follow a versioned pattern to support different client versions:

```javascript
/**
 * UI/Components/ComponentName/ComponentName.js
 */
define(function(require) {
	'use strict';

	var publicName = 'ComponentName';

	var ComponentV0 = require('./ComponentV0/ComponentV0'); // Base version
	var ComponentV1 = require('./ComponentV1/ComponentV1'); // Enhanced version

	var UIVersionManager = require('UI/UIVersionManager');
	var KEYS = require('Controls/KeyEventHandler');

	var versionInfo = {
		default: ComponentV0,
		common: {
			20171207: ComponentV1, // Date when this version was introduced
		},
		re: {
			// Renewal-specific versions
		},
		prere: {
			// Pre-renewal-specific versions
		}
	};

	var ComponentController = UIVersionManager.getUIController(publicName, versionInfo);

	// Extend with additional logic
	var _selectUIVersion = ComponentController.selectUIVersion;
	ComponentController.selectUIVersion = function() {
		_selectUIVersion();
		var component = ComponentController.getUI();
		
		// Add key handlers
		component.onKeyDown = function onKeyDown(e) {
			if (e.which === KEYS.ESCAPE) {
				component.toggle();
			}
		};
	};

	return ComponentController;
});
```

### Component Implementation (Base Version)

```javascript
/**
 * UI/Components/ComponentName/ComponentV0/ComponentV0.js
 */
define(function(require) {
	'use strict';

	var jQuery = require('Utils/jquery');
	var UIManager = require('UI/UIManager');
	var UIComponent = require('UI/UIComponent');
	var htmlText = require('text!./ComponentV0.html');
	var cssText = require('text!./ComponentV0.css');

	function ComponentV0() {
		this.ui = null;
		// Component state
	}

	ComponentV0.prototype.init = function init() {
		this.ui = jQuery(htmlText);
		this.ui.css('zIndex', 50);
		
		// Draggable
		this.draggable(this.ui.find('.titlebar'));
		
		// Event handlers
		this.ui.find('.close').click(this.toggle.bind(this));
		
		// Append to body
		this.ui.appendTo('body');
	};

	ComponentV0.prototype.toggle = function toggle() {
		this.ui.toggle();
	};

	ComponentV0.prototype.append = function append() {
		if (!this.ui) {
			this.init();
		}
		return this;
	};

	// Mix UIComponent methods
	Object.assign(ComponentV0.prototype, UIComponent);

	return ComponentV0;
});
```

### HTML Template Pattern

```html
<!-- UI/Components/ComponentName/ComponentV0/ComponentV0.html -->
<div class="componentname" id="componentname">
	<div class="titlebar">
		<div class="title">Component Title</div>
		<div class="close"></div>
	</div>
	<div class="content">
		<!-- Component content -->
	</div>
</div>
```

## Renderer/Engine Patterns

### Entity Management

```javascript
// Creating/managing entities
var entity = new Entity();
entity.GID = uniqueId;
entity.objecttype = EntityType.PC; // PC, NPC, MOB, etc.
entity.sex = 0; // 0=female, 1=male
entity.job = 4001; // Job ID from DB
entity.position = [x, y, z];

EntityManager.add(entity);
```

### Camera Control

```javascript
// Camera manipulation
Camera.target = entity; // Follow entity
Camera.distance = 250; // Zoom level
Camera.angle = 45; // Rotation angle
Camera.updateView(); // Apply changes
```

### Map Rendering

```javascript
// Load and render map
MapRenderer.setMap(mapName, x, y, width, height);
MapRenderer.free(); // Clean up previous map
```

## Event System

The project uses a centralized event system:

```javascript
var Events = require('Core/Events');

// Emit event
Events.emit('EventName', data1, data2);

// Listen to event
Events.on('EventName', function(data1, data2) {
	// Handle event
});

// Remove listener
Events.off('EventName', handlerFunction);
```

### Common Events

| Event | Description | Data |
|-------|-------------|------|
| `ENGINE_READY` | Engine initialization complete | - |
| `LOADING_PROGRESS` | Asset loading progress | `{current, total}` |
| `CONNECTION_SUCCESS` | Connected to server | - |
| `CONNECTION_CLOSED` | Disconnected from server | - |
| `ENTITY_ADDED` | Entity added to map | `entity` |
| `ENTITY_REMOVED` | Entity removed from map | `GID` |
| `INVENTORY_UPDATED` | Inventory changed | - |
| `SKILL_USE` | Skill used | `{skill, level, target}` |

## Database System

### DB Structure

```javascript
// Accessing DB
var DB = require('DB/DBManager');

// Item info
var itemInfo = DB.getItemInfo(itemId);
// Returns: {name, type, weight, description, ...}

// Skill info
var skillInfo = DB.getSkillInfo(skillId);
// Returns: {name, description, type, ...}

// Job info
var jobInfo = DB.getJobInfo(jobId);
// Returns: {name, weight, ...}

// Monster info
var monsterInfo = DB.getMonsterInfo(monsterId);
// Returns: {name, level, race, ...}
```

## Asset Loading

### Loading Files from GRF

```javascript
var Client = require('Core/Client');

// Load single file
Client.loadFile('data\\sprite\\player.spr', function(buffer) {
	// Process buffer
}, function(error) {
	// Handle error
});

// Load multiple files
Client.loadFiles([
	'data\\model\\map.rsm',
	'data\\texture\\map.bmp'
], function(buffers) {
	// Process buffers array
});
```

### Texture Loading

```javascript
var Texture = require('Utils/Texture');

Texture.load('texture.bmp', function(texture) {
	// Use WebGL texture
});
```

## Build System

### Build Commands

```bash
# Development server
npm run live          # Live server with hot reload

# Build specific modules
npm run build         # Interactive build
npm run build:online  # Build main client
npm run build:all     # Build all modules

# Build with minification
npm run build:all:minify

# Build PWA
npm run build:pwa

# Build desktop app
npm run build:nw
```

### Build Script Structure

The build process (`applications/tools/builder-web.js`) uses RequireJS optimizer:
- Concatenates AMD modules
- Optional minification with Terser
- Generates standalone JS files
- Copies HTML and asset files

## Configuration

### ROConfig Object

```javascript
var ROConfig = {
	target: document.getElementById('robrowser'),
	type: ROBrowser.TYPE.FRAME, // FRAME, POPUP, or APP
	application: ROBrowser.APP.ONLINE, // ONLINE, MAPVIEWER, etc.
	
	servers: [{
		display: 'Server Name',
		address: '127.0.0.1',
		port: 6900,
		version: 55,
		langtype: 5,
		packetver: 20211103,
		renewal: false,
		socketProxy: 'ws://127.0.0.1:5999',
		remoteClient: 'http://127.0.0.1:8088/',
		packetKeys: false, // or [key1, key2, key3]
		adminList: [2000000], // Admin account IDs
	}],
	
	development: true,
	packetDump: false,
	skipIntro: false,
	
	// Camera settings
	ThirdPersonCamera: true,
	FirstPersonCamera: true,
	CameraMaxZoomOut: 50,
	
	// Features
	enableConsole: true,
	enableCashShop: true,
	enableBank: true,
	enableMapName: true,
	
	// Plugins
	plugins: {
		PluginName: true
	}
};
```

## Common Patterns & Best Practices

### 1. Async Operations

```javascript
// Use callbacks for async operations
function loadData(callback) {
	Client.loadFile('file.dat', function(buffer) {
		var data = processBuffer(buffer);
		callback(data);
	});
}
```

### 2. Error Handling

```javascript
// Always provide error callbacks
Client.loadFile('file.dat', onSuccess, onError);

function onError(error) {
	console.error('Failed to load file:', error);
	// Graceful fallback
}
```

### 3. Memory Management

```javascript
// Clean up references when done
function cleanup() {
	// Remove event listeners
	Events.off('EventName', handler);
	
	// Clear DOM elements
	if (this.ui) {
		this.ui.remove();
		this.ui = null;
	}
	
	// Clear large arrays
	this.dataArray = [];
}
```

### 4. WebGL Resources

```javascript
// Always delete WebGL resources
function freeTexture(gl, texture) {
	if (texture) {
		gl.deleteTexture(texture);
		texture = null;
	}
}
```

### 5. Binary Data Reading

```javascript
var BinaryReader = require('Utils/BinaryReader');

var reader = new BinaryReader(buffer);
var header = reader.readString(4); // 4-byte string
var version = reader.readUByte();  // Unsigned byte
var count = reader.readUShort();   // Unsigned short
var value = reader.readLong();     // Signed long
```

### 6. Binary Data Writing

```javascript
var BinaryWriter = require('Utils/BinaryWriter');

var writer = new BinaryWriter(10); // 10-byte buffer
writer.writeShort(0x0064);         // Packet ID
writer.writeString('username', 24); // Fixed-length string
writer.writeByte(1);                // Byte value
var buffer = writer.buffer;
```

## Testing Considerations

When modifying code:
1. **Test in browser** - This is a browser-based application
2. **Check WebGL errors** - Use browser dev tools
3. **Monitor network packets** - Use `packetDump: true` config
4. **Test with different packet versions** - Client compatibility
5. **Check memory leaks** - Long gaming sessions
6. **Test UI on different resolutions** - Responsive design

## Version Compatibility

The client supports multiple packet versions (packetver):
- Different RO client dates: 20130000, 20170000, 20210000, etc.
- Renewal vs Pre-Renewal mode
- Feature flags based on client version

Always check version compatibility when adding features:

```javascript
var PACKETVER = require('Network/PacketVerManager');

if (PACKETVER.value >= 20171207) {
	// Feature available in this version
}
```

## Important Notes for AI Agents

1. **Do NOT use ES6+ features** - This is an ES5 codebase
   - No `let`/`const` - use `var`
   - No arrow functions - use `function`
   - No template literals - use string concatenation
   - No classes - use prototype pattern
   - No async/await - use callbacks

2. **AMD modules only** - Always use `define()` and `require()`

3. **File paths use backslashes** - Game assets use Windows path format: `data\\sprite\\file.spr`

4. **Packet IDs are hexadecimal** - Always use hex: `0x0064` not `100`

5. **Coordinate system** - RO uses: X (width), Y (height), Z (altitude)

6. **Buffer is left-to-right** - Network packets are little-endian

7. **jQuery is used extensively** - UI manipulation uses jQuery syntax

8. **No build-time transpilation** - Code runs as-is in browser

9. **WebGL 1.0** - Do not use WebGL 2.0 features

10. **Tab indentation** - Always use tabs, not spaces

## Common Mistakes to Avoid

❌ **Don't:**
```javascript
// ES6 syntax
const player = getPlayer();
let items = [...inventory];
const move = (x, y) => player.moveTo(x, y);
```

✅ **Do:**
```javascript
// ES5 syntax
var player = getPlayer();
var items = inventory.slice();
var move = function(x, y) {
	return player.moveTo(x, y);
};
```

❌ **Don't:**
```javascript
// CommonJS/ES6 imports
import Entity from './Entity';
const MapRenderer = require('./MapRenderer');
module.exports = MyModule;
```

✅ **Do:**
```javascript
// AMD modules
define(function(require) {
	var Entity = require('Renderer/Entity/Entity');
	var MapRenderer = require('Renderer/MapRenderer');
	return MyModule;
});
```

## Useful Resources

- **Original roBrowser**: https://www.robrowser.com/
- **rAthena Packet Documentation**: http://rathena.org/wiki/Packets
- **Hercules Documentation**: https://github.com/HerculesWS/Hercules
- **GRF File Format**: Various community documentation
- **RO Client Dates**: https://rathena.org/board/topic/106413-client-hash-and-date/

## Contributing Guidelines

When generating new code:
1. Follow the exact module pattern shown above
2. Use proper JSDoc comments
3. Match the existing code style
4. Test with actual RO server if possible
5. Consider packet version compatibility
6. Add error handling
7. Clean up resources properly
8. Update documentation if adding new features

## Quick Reference

### File Header Template
```javascript
/**
 * Category/SubCategory/FileName.js
 *
 * Brief description
 *
 * This file is part of ROBrowser, (http://www.robrowser.com/).
 */
```

### Module Template
```javascript
define(function(require) {
	'use strict';
	var Dependency = require('Path/To/Dependency');
	var Module = {};
	return Module;
});
```

### Function Template
```javascript
/**
 * Function description
 * @param {type} param
 * @return {type}
 */
function functionName(param) {
	// Implementation
}
```

---

**Remember**: This is a mature, production-grade project with thousands of users. Code quality, compatibility, and stability are paramount. When in doubt, follow existing patterns in the codebase.
