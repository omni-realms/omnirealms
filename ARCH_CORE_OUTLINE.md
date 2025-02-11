# Core System Architecture

## com.novaforge.omnirealms.core

### Interfaces
```java
/**
 * Core game engine interface responsible for managing the game lifecycle.
 * Handles initialization, main game loop updates, and cleanup operations.
 */
interface GameEngine {
    /**
     * Initializes the game engine and all required subsystems.
     * This includes graphics, physics, audio, and input systems.
     * 
     * @throws EngineInitializationException if critical systems fail to initialize
     */
    void initialize();

    /**
     * Updates all engine subsystems and game logic.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);

    /**
     * Performs cleanup of all engine resources and subsystems.
     * Should be called before engine shutdown.
     */
    void cleanup();
}

/**
 * Manages the current state of the game including menus, gameplay, and transitions.
 * Each game state represents a distinct phase of the game with its own update and render logic.
 */
interface GameState {
    /**
     * Called when this state becomes active.
     * Initialize resources and setup required for this state.
     */
    void enter();

    /**
     * Called when this state is no longer active.
     * Cleanup any resources specific to this state.
     */
    void exit();

    /**
     * Temporarily suspends this state while keeping it in memory.
     * Used for overlays and nested states.
     */
    void pause();

    /**
     * Resumes this state from a paused state.
     */
    void resume();

    /**
     * Updates the state logic.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);
}

/**
 * Manages loading, unloading, and updating of game scenes.
 * A scene represents a discrete gameplay area with its own entities and resources.
 */
interface SceneManager {
    /**
     * Loads a scene and its required resources.
     * 
     * @param sceneId unique identifier for the scene
     * @throws SceneLoadException if the scene cannot be loaded
     */
    void loadScene(String sceneId);

    /**
     * Unloads a scene and frees its resources.
     * 
     * @param sceneId unique identifier for the scene
     */
    void unloadScene(String sceneId);

    /**
     * Updates the currently active scene.
     * 
     * @param tpf time per frame in seconds
     */
    void updateScene(float tpf);
}

/**
 * Manages loading and unloading of game assets including models, textures, sounds, etc.
 * Implements resource pooling and cache management for optimal performance.
 */
interface AssetManager {
    /**
     * Loads an asset into memory and prepares it for use.
     * 
     * @param assetId unique identifier for the asset
     * @throws AssetLoadException if the asset cannot be loaded
     */
    void loadAsset(String assetId);

    /**
     * Unloads an asset from memory.
     * 
     * @param assetId unique identifier for the asset
     */
    void unloadAsset(String assetId);

    /**
     * Checks if an asset is currently loaded in memory.
     * 
     * @param assetId unique identifier for the asset
     * @return true if the asset is loaded, false otherwise
     */
    boolean isAssetLoaded(String assetId);
}

/**
 * Base interface for all game entities.
 * An entity represents any object in the game world that can be updated and interacted with.
 */
interface Entity {
    /**
     * Gets the unique identifier for this entity.
     * 
     * @return unique entity identifier
     */
    String getId();

    /**
     * Updates the entity's state and components.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);

    /**
     * Cleans up entity resources when the entity is destroyed.
     */
    void cleanup();
}

/**
 * Base interface for entity components.
 * Components provide specific functionality to entities through composition.
 */
interface Component {
    /**
     * Initializes the component when it's first added to an entity.
     */
    void initialize();

    /**
     * Updates the component's state.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);

    /**
     * Cleans up component resources when removed from an entity.
     */
    void cleanup();
}

/**
 * Manages physics simulation for the game world.
 * Handles collision detection, resolution, and physics updates.
 */
interface PhysicsSystem {
    /**
     * Initializes the physics system and its resources.
     */
    void initialize();

    /**
     * Updates physics simulation.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);

    /**
     * Cleans up physics system resources.
     */
    void cleanup();

    /**
     * Adds a physics body to the simulation.
     * 
     * @param body physics body to add
     */
    void addBody(PhysicsBody body);

    /**
     * Removes a physics body from the simulation.
     * 
     * @param body physics body to remove
     */
    void removeBody(PhysicsBody body);
}

/**
 * Represents a physical body in the physics simulation.
 * Handles physical properties and forces for an entity.
 */
interface PhysicsBody {
    /**
     * Applies a force to the body.
     * 
     * @param force force vector to apply
     */
    void applyForce(Vector3f force);

    /**
     * Sets the absolute position of the body.
     * 
     * @param position new position vector
     */
    void setPosition(Vector3f position);

    /**
     * Gets the current position of the body.
     * 
     * @return current position vector
     */
    Vector3f getPosition();
}

/**
 * Handles procedural generation of the game world.
 * Manages chunk loading, generation, and unloading.
 */
interface WorldGenerator {
    /**
     * Generates a new chunk at the specified coordinates.
     * 
     * @param coords chunk coordinates
     */
    void generateChunk(ChunkCoordinates coords);

    /**
     * Unloads a chunk and frees its resources.
     * 
     * @param coords chunk coordinates
     */
    void unloadChunk(ChunkCoordinates coords);

    /**
     * Checks if a chunk is currently loaded.
     * 
     * @param coords chunk coordinates
     * @return true if the chunk is loaded, false otherwise
     */
    boolean isChunkLoaded(ChunkCoordinates coords);
}
```

### Abstract Classes
```java
/**
 * Base implementation of GameState providing common functionality.
 * Implements basic state management and engine references.
 */
abstract class AbstractGameState implements GameState {
    protected GameEngine engine;
    protected SceneManager sceneManager;
}

/**
 * Base implementation of Entity providing common functionality.
 * Implements component management and basic entity operations.
 */
abstract class AbstractEntity implements Entity {
    protected String id;
    protected List<Component> components;
}

/**
 * Base implementation of Component providing common functionality.
 * Implements parent entity reference and basic component operations.
 */
abstract class AbstractComponent implements Component {
    protected Entity parent;
}

/**
 * Base implementation of PhysicsBody providing common functionality.
 * Implements basic physics properties and calculations.
 */
abstract class AbstractPhysicsBody implements PhysicsBody {
    protected Vector3f position;
    protected Vector3f velocity;
    protected float mass;
}
```

### Enums
```java
/**
 * Represents the possible states of the game engine.
 */
enum GameState {
    /** Engine is initializing systems */
    INITIALIZING,
    /** Normal game operation */
    RUNNING,
    /** Game is paused */
    PAUSED,
    /** Loading resources */
    LOADING,
    /** Cleaning up resources */
    CLEANUP
}

/**
 * Defines the different types of entities in the game.
 */
enum EntityType {
    /** Player controlled entity */
    PLAYER,
    /** Non-player character */
    NPC,
    /** Non-moving world object */
    STATIC_OBJECT,
    /** Moving world object */
    DYNAMIC_OBJECT,
    /** Particle effect system */
    PARTICLE_SYSTEM
}

/**
 * Defines the different types of physics bodies.
 */
enum PhysicsBodyType {
    /** Non-moving body */
    STATIC,
    /** Fully simulated body */
    DYNAMIC,
    /** Programmatically controlled body */
    KINEMATIC
}

/**
 * Defines the different types of game assets.
 */
enum AssetType {
    /** 3D model asset */
    MODEL,
    /** Image texture asset */
    TEXTURE,
    /** Audio asset */
    SOUND,
    /** Shader program asset */
    SHADER,
    /** Material definition asset */
    MATERIAL
}
```
