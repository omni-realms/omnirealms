# Client Module Architecture

## Package: com.novaforge.omnirealms.client

### Overview
The client module handles all client-side functionality including:
- Network communication with game servers
- Input processing and management
- Multi-style graphics rendering
- User interface management
- Asset management
- Client-side prediction

### Interfaces

```java
/**
 * Manages client-side game engine functionality and server communication.
 * Handles connection management, network message processing, and client-side game state.
 * Implements security protocols defined in the security framework.
 */
interface ClientEngine {
    /**
     * Establishes connection to a game server with automatic reconnection handling.
     * 
     * @param serverAddress address of the server to connect to
     * @throws ConnectionException if connection cannot be established
     * @return Registration handle that will disconnect when unregistered
     */
    Registration connect(String serverAddress);

    /**
     * Registers a handler for network messages of a specific type.
     * Messages are processed in the game update thread for thread safety.
     * 
     * @param messageType class of message to handle
     * @param handler callback to process messages
     * @return Registration handle to unregister the handler
     */
    <T extends NetworkMessage> Registration registerMessageHandler(Class<T> messageType, MessageHandler<T> handler);

    /**
     * Sends a network message to the server with automatic retries.
     * Messages are encrypted and queued for reliable delivery.
     * 
     * @param message network message to send
     * @throws NetworkException if message cannot be sent
     */
    void sendMessage(NetworkMessage message);
}

/**
 * Manages user input processing and mapping.
 * Handles keyboard, mouse, controller, and touch input with configurable bindings.
 * Supports input combinations and device hot-plugging.
 */
interface InputManager {
    /**
     * Registers a new input binding with support for complex combinations.
     * 
     * @param inputName logical name for the input action
     * @param keyCode physical key or button code
     * @return Registration handle to unregister the binding
     */
    Registration registerInput(String inputName, int keyCode);

    /**
     * Registers a listener for input events.
     * 
     * @param inputName logical name of the input to listen for
     * @param listener callback to handle input events
     * @return Registration handle to unregister the listener
     */
    Registration registerInputListener(String inputName, InputListener listener);

    /**
     * Registers a raw input device listener.
     * 
     * @param deviceType type of input device to monitor
     * @param listener callback for raw input events
     * @return Registration handle to unregister the listener
     */
    Registration registerDeviceListener(InputDeviceType deviceType, DeviceListener listener);
    
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
 * Manages rendering of game graphics.
 * Handles multi-style rendering, post-processing, and graphics resource management.
 * Supports PBR, cartoon, 2D, and storybook rendering styles.
 */
interface Renderer {
    /**
     * Initializes the renderer and graphics resources.
     * 
     * @throws RendererInitializationException if graphics initialization fails
     */
    void initialize();

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

    /**
     * Registers a viewport for rendering.
     * 
     * @param viewport viewport configuration
     * @return Registration handle to unregister the viewport
     */
    Registration registerViewport(ViewPort viewport);
    
    /**
     * Updates render state and processes deferred rendering operations.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);
    
    /**
     * Cleans up renderer resources.
     */
    void cleanup();
}

/**
 * Manages user interface elements and screens.
 * Handles UI layout, interaction, animation, and theme management.
 * Supports accessibility features and transition effects.
 */
interface UserInterface {
    /**
     * Registers a UI screen for display.
     * 
     * @param screenId identifier for the screen
     * @param screen screen implementation
     * @return Registration handle to unregister the screen
     */
    Registration registerScreen(String screenId, Screen screen);

    /**
     * Shows a registered UI screen with transitions.
     * 
     * @param screenId identifier for the screen to show
     * @throws ScreenNotFoundException if screen is not registered
     * @return Registration handle that will hide screen when unregistered
     */
    Registration showScreen(String screenId);

    /**
     * Registers a UI event listener.
     * 
     * @param eventType type of UI event to listen for
     * @param listener callback for UI events
     * @return Registration handle to unregister the listener
     */
    <T extends UIEvent> Registration registerUIListener(Class<T> eventType, UIEventListener<T> listener);

    /**
     * Updates UI elements and animations.
     * 
     * @param tpf time per frame in seconds
     */
    void update(float tpf);
}
```

### Abstract Classes

```java
/**
 * Base implementation for UI screens.
 * Provides common functionality for UI screen management and rendering.
 * Handles screen lifecycle and registration management.
 */
abstract class AbstractScreen implements Screen {
    protected String screenId;
    protected boolean visible;
    protected final List<Registration> registrations = new ArrayList<>();
    
    /**
     * Initializes the screen and its UI elements.
     * Registration handles should be added to registrations list.
     */
    abstract void initialize();
    
    /**
     * Updates screen elements and animations.
     * 
     * @param tpf time per frame in seconds
     */
    abstract void update(float tpf);
    
    /**
     * Cleans up the screen by unregistering all registrations.
     */
    public void cleanup() {
        registrations.forEach(Registration::unregister);
        registrations.clear();
    }
}

/**
 * Base implementation for renderers.
 * Provides common functionality for scene rendering and camera management.
 * Handles render pass organization and resource management.
 */
abstract class AbstractRenderer implements Renderer {
    protected Camera camera;
    protected final List<Registration> registrations = new ArrayList<>();
    
    /**
     * Sets up render passes for the frame.
     * Registration handles should be added to registrations list.
     */
    abstract void setupRenderPasses();
    
    /**
     * Updates camera and viewport parameters.
     * 
     * @param tpf time per frame in seconds
     */
    abstract void updateCamera(float tpf);
    
    @Override
    public void cleanup() {
        registrations.forEach(Registration::unregister);
        registrations.clear();
    }
}
```

### Enums

```java
/**
 * Represents possible states of the client connection.
 */
enum ClientState {
    /** Not connected to server */
    DISCONNECTED,
    /** Attempting to establish connection */
    CONNECTING,
    /** Successfully connected to server */
    CONNECTED,
    /** Connection error state */
    ERROR
}

/**
 * Defines different types of UI screens.
 */
enum ScreenType {
    /** Main menu screen */
    MENU,
    /** In-game heads-up display */
    HUD,
    /** Inventory management screen */
    INVENTORY,
    /** Dialog and conversation screen */
    DIALOG,
    /** Loading screen */
    LOADING
}

/**
 * Defines different input device types.
 */
enum InputDeviceType {
    /** Keyboard input */
    KEYBOARD,
    /** Mouse input */
    MOUSE,
    /** Gamepad/controller input */
    GAMEPAD,
    /** Touch screen input */
    TOUCH
}
```

### Implementation Notes
1. All registration handles must be stored and cleaned up appropriately
2. Input system supports device hot-plugging and complex combinations
3. Rendering system supports multiple styles and viewports
4. UI system supports layered screens with transitions
5. Security protocols must be implemented for network communication
6. Client-side prediction should be used for smooth gameplay
7. Asset streaming and resource management must be efficient

### Example Usage

```java
class GameScreen extends AbstractScreen {
    private final InputManager inputManager;
    private final UserInterface ui;
    private final Renderer renderer;
    
    @Override
    void initialize() {
        // Register multiple input bindings
        registrations.add(
            inputManager.registerInput("PAUSE", KeyCode.ESCAPE)
        );
        registrations.add(
            inputManager.registerInput("INVENTORY", KeyCode.I)
        );
        
        // Register input listeners
        registrations.add(
            inputManager.registerInputListener("PAUSE", event -> togglePause())
        );
        
        // Register UI elements with animation
        registrations.add(
            ui.registerUIListener(ButtonClickEvent.class, this::handleButtonClick)
        );
        
        // Setup rendering passes
        registrations.add(
            renderer.registerRenderPass(new MainScenePass())
        );
        registrations.add(
            renderer.registerPostProcess(new BloomEffect())
        );
    }
    
    // Screen is automatically cleaned up through AbstractScreen.cleanup()
}
```