# Core Architecture

## Utility Interfaces

```java
/**
 * Represents a registration that can be unregistered.
 * Used for cleanup of listeners, bindings, and other registrations.
 */
@FunctionalInterface
interface Registration {
    /**
     * Unregisters/removes the registration and performs any necessary cleanup.
     */
    void unregister();
}
```

## Updated Core Interfaces Example

```java
/**
 * Manages user input processing and mapping.
 * Handles keyboard, mouse, and controller input with configurable bindings.
 */
interface InputManager {
    /**
     * Registers a new input binding.
     * 
     * @param inputName logical name for the input action
     * @param keyCode physical key or button code
     * @return Registration handle to unregister the input binding
     */
    Registration registerInput(String inputName, int keyCode);
    
    /**
     * Registers an input action listener.
     * 
     * @param inputName logical name of the input to listen for
     * @param listener callback to invoke when input is activated
     * @return Registration handle to unregister the listener
     */
    Registration registerInputListener(String inputName, InputListener listener);
    
    /**
     * Checks if an input is currently active.
     * 
     * @param inputName logical name of the input to check
     * @return true if input is active, false otherwise
     */
    boolean isInputActive(String inputName);
    
    /**
     * Updates input state for the current frame.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);
}

/**
 * Manages event handling and messaging between components.
 */
interface EventBus {
    /**
     * Registers an event listener for a specific event type.
     * 
     * @param eventType type of event to listen for
     * @param listener callback to invoke when event occurs
     * @return Registration handle to unregister the listener
     */
    <T> Registration subscribe(Class<T> eventType, EventListener<T> listener);
    
    /**
     * Posts an event to all registered listeners.
     * 
     * @param event event to publish
     */
    void publish(Object event);
}

/**
 * Manages physics collision callbacks and triggers.
 */
interface PhysicsSystem {
    /**
     * Registers a collision listener between two collision groups.
     * 
     * @param groupA first collision group
     * @param groupB second collision group
     * @param listener callback for collision events
     * @return Registration handle to unregister the collision listener
     */
    Registration registerCollisionListener(
        short groupA, 
        short groupB, 
        CollisionListener listener
    );
    
    /**
     * Registers a trigger volume in the physics world.
     * 
     * @param volume trigger volume definition
     * @param listener callback for trigger events
     * @return Registration handle to unregister the trigger
     */
    Registration registerTriggerVolume(
        TriggerVolume volume,
        TriggerListener listener
    );
}

/**
 * Manages asset loading and lifecycle.
 */
interface AssetManager {
    /**
     * Registers an asset loader for a specific asset type.
     * 
     * @param assetType type of asset this loader handles
     * @param loader loader implementation
     * @return Registration handle to unregister the loader
     */
    Registration registerLoader(Class<?> assetType, AssetLoader loader);
    
    /**
     * Registers a listener for asset loading events.
     * 
     * @param listener callback for asset events
     * @return Registration handle to unregister the listener
     */
    Registration registerLoadingListener(AssetLoadingListener listener);
}

/**
 * Manages rendering pipeline and graphics resources.
 */
interface Renderer {
    /**
     * Registers a render pass in the rendering pipeline.
     * 
     * @param pass render pass implementation
     * @return Registration handle to unregister the pass
     */
    Registration registerRenderPass(RenderPass pass);
    
    /**
     * Registers a post-processing effect.
     * 
     * @param effect post-process effect implementation
     * @return Registration handle to unregister the effect
     */
    Registration registerPostProcess(PostProcessEffect effect);
}
```

## Usage Example

```java
class GameplaySystem {
    private final List<Registration> registrations = new ArrayList<>();
    
    public void initialize(InputManager input, EventBus events) {
        // Register input binding
        registrations.add(
            input.registerInput("JUMP", KeyCode.SPACE)
        );
        
        // Register input listener
        registrations.add(
            input.registerInputListener("JUMP", this::onJump)
        );
        
        // Register event listener
        registrations.add(
            events.subscribe(PlayerSpawnEvent.class, this::onPlayerSpawn)
        );
    }
    
    public void cleanup() {
        // Unregister everything at once
        registrations.forEach(Registration::unregister);
        registrations.clear();
    }
    
    private void onJump(InputEvent event) {
        // Handle jump input
    }
    
    private void onPlayerSpawn(PlayerSpawnEvent event) {
        // Handle player spawn
    }
}
```

This pattern provides several benefits:
1. Clean resource management - registrations can be stored and cleaned up together
2. Automatic cleanup - no need to remember specific unregister methods
3. Encapsulated state - the Registration object contains everything needed for cleanup
4. Type safety - each registration is strongly typed to its purpose
5. Simplified interfaces - fewer public methods needed

Would you like me to continue updating the other modules to use this registration pattern? We can apply it to:
- Network message handlers
- Scene loading listeners
- UI element registrations
- World chunk loading listeners
- And more...