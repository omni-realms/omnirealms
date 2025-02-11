# OmniRealms Technical Specification - Part 3: Entity Systems

## 1. Universal Attributes Framework

### Core Entity System
```java
public abstract class BaseEntity {
    private EntityScale scale;         // Current existence scale
    private Vector3D position;         // Position relative to scale
    private Consciousness consciousness; // Entity's awareness level
    private EnergyState energyState;   // Current energy configuration
    private Form currentForm;          // Physical manifestation
    private Set<Capability> capabilities; // Available abilities
    
    // Basic attributes that scale with form
    private double health;             // Life force/integrity
    private double energy;             // Power resource
    private double mass;               // Affects physics interactions
    private double consciousness_level; // Awareness/intelligence level
}
```

### Purpose & Implementation
The BaseEntity class serves as the foundation for all entities in OmniRealms, from quantum particles to cosmic beings. This system enables seamless transitions between different scales of existence while maintaining consistent underlying mechanics.

#### Required Attributes
- **Scale**: Always required, determines the entity's current plane of existence
- **Position**: Required, but interpretation varies by scale
- **Consciousness**: Required for players, optional for NPCs
- **EnergyState**: Required, represents the entity's fundamental energy configuration

#### Optional Attributes
- **Form**: Can be null for basic entities
- **Capabilities**: Empty set by default, gained through progression

#### Evolution Through Gameplay
- Entities begin with minimal attributes
- Consciousness level increases through exploration and interaction
- New capabilities unlock based on consciousness evolution
- Form changes available after reaching consciousness thresholds

## 2. Scale-Specific Systems

### 2.1 Quantum Scale Mechanics
```java
public class QuantumAttributes {
    private double wavelength;
    private double spin;
    private double superposition;
    private Set<QuantumState> possibleStates;
    private double entanglementStrength;
    private Map<QuantumEntity, Double> entangledParticles;
}
```

#### Purpose
Enables gameplay at the quantum level, allowing players to experience and manipulate fundamental particles and quantum phenomena.

#### Gameplay Effects
- Superposition allows existing in multiple states simultaneously
- Entanglement enables instant communication and coordination
- Quantum tunneling provides unique movement abilities
- Wave-particle duality affects interaction with the environment

#### Progression
- Initially limited to basic quantum states
- Unlock more complex quantum behaviors through observation
- Develop stronger entanglement capabilities
- Learn to manipulate quantum fields
- Available in games: Chronoverse Legends, Multiverse Forge

### 2.2 Cellular Scale Systems
```java
public class CellularAttributes {
    private double metabolicRate;
    private double reproductionRate;
    private Set<Organelle> organelles;
    private double membraneIntegrity;
    private BiochemicalProfile biochemistry;
    private Set<CellSignal> signallingPathways;
}
```

#### Purpose
Enables microscopic gameplay, allowing players to experience life at the cellular level and interact with biological systems.

#### Required Components
- Membrane integrity (health equivalent)
- Basic metabolic rate
- At least one organelle

#### Optional Components
- Additional organelles (gained through evolution)
- Advanced signaling pathways
- Specialized biochemical processes

#### Progression Path
1. Start as a basic cell
2. Develop new organelles
3. Establish signaling networks
4. Evolve specialized functions
5. Achieve multicellular cooperation

#### Available In
- Celestial Safari (microscopic mode)
- Multiverse Forge (cellular engineering)

### 2.3 Organism Scale Systems
```java
public class OrganismAttributes {
    private Genetics genetics;
    private PhysiologicalSystems systems;
    private BehavioralPatterns behavior;
    private Set<Skill> learnedSkills;
    private SocialConnections relationships;
    private EmotionalState emotionalState;
}
```

#### Purpose
Represents traditional character-scale entities with biological and social components.

#### Core Features
- **Genetics**: Determines base capabilities and potential
- **Physiology**: Handles health, stamina, and physical abilities
- **Behavior**: Controls AI and response patterns
- **Skills**: Learned abilities and knowledge
- **Social**: Relationships and interactions
- **Emotions**: Affects decision-making and interactions

#### Progression Systems
1. **Genetic Evolution**
   - Mutation chances during reproduction
   - Selective trait enhancement
   - Genetic engineering (advanced)

2. **Skill Development**
   - Experience-based learning
   - Training and practice
   - Knowledge sharing
   - Skill synergies

3. **Social Growth**
   - Relationship building
   - Reputation systems
   - Alliance formation
   - Cultural influence

### 2.4 Cosmic Scale Systems
```java
public class CosmicAttributes {
    private GravitationalField gravityField;
    private Set<CelestialBody> controlledBodies;
    private DarkMatterInfluence darkMatterControl;
    private Set<SpacetimeAnomaly> generatedAnomalies;
    private CosmicEnergyOutput energyOutput;
    private UniversalForceManipulation forceControl;
}
```

#### Purpose
Enables gameplay at universal scales, allowing manipulation of cosmic forces and celestial bodies.

#### Core Mechanics
- Gravitational field manipulation
- Dark matter/energy control
- Spacetime distortion
- Cosmic energy channeling

#### Progression Path
1. **Gravitational Mastery**
   - Basic attraction/repulsion
   - Orbital manipulation
   - Gravitational lensing
   - Space-time warping

2. **Dark Matter Control**
   - Detection
   - Basic manipulation
   - Structure formation
   - Galaxy shaping

3. **Energy Manipulation**
   - Stellar energy tapping
   - Cosmic ray channeling
   - Vacuum energy extraction
   - Universal force control

## 3. Evolution & Transformation Systems

```java
public interface Evolvable {
    void evolve(EvolutionTrigger trigger);
    boolean canEvolve(Form targetForm);
    Set<Form> getPossibleEvolutions();
    void regressForm(Form previousForm);
}
```

### Purpose
Manages entity transformation across scales and forms, enabling progression and adaptation.

### Evolution Types

#### 1. Natural Evolution
- Occurs through normal gameplay
- Influenced by environment
- Gradual progression
- Reversible changes

#### 2. Induced Evolution
- Triggered by specific events
- Requires resources/energy
- Rapid transformation
- May have side effects

#### 3. Technological Enhancement
- Equipment-based
- Resource intensive
- Highly customizable
- Immediately reversible

#### 4. Cosmic Ascension
- End-game progression
- Permanent changes
- Scale transcendence
- New ability unlocks

### Progression Requirements
- Consciousness level thresholds
- Resource accumulation
- Knowledge acquisition
- Environmental adaptation
- Social/technological advancement

## 4. Inventory & Equipment Systems

```java
public class MultiversalInventory {
    private final Map<EntityScale, Set<Item>> scaleSpecificItems;
    private final Map<ItemCategory, Set<Item>> categorizedItems;
    private final QuantumStorage quantumStorage;
    private final DimensionalPocket dimensionalSpace;
}
```

### Purpose
Provides scale-appropriate storage and equipment management across all entity types.

### Storage Types

#### 1. Quantum Storage
- Unlimited theoretical capacity
- Scale-independent storage
- Energy cost for access
- Probability-based retrieval

#### 2. Dimensional Pocket
- Limited but expandable space
- Instant access
- Scale-specific organization
- Custom categorization

#### 3. Physical Storage
- Scale-appropriate capacity
- Traditional inventory management
- Weight/volume limitations
- Physical access requirements

### Equipment Mechanics

#### 1. Scale Compatibility
- Items have valid scale ranges
- Automatic size adaptation
- Cross-scale interactions
- Power scaling

#### 2. Evolution Integration
- Equipment affects evolution
- Form-specific restrictions
- Technological synergy
- Biological compatibility

## 5. Capabilities & Powers

```java
public class Capability {
    private String name;
    private Set<EntityScale> viableScales;
    private ResourceCost energyCost;
    private CooldownTimer cooldown;
    private Set<Effect> effects;
    private UpgradeTree upgrades;
}
```

### Purpose
Defines the abilities and powers available to entities across different scales.

### Capability Categories

#### 1. Scale-Specific Powers
- Quantum entanglement
- Cellular manipulation
- Organic abilities
- Cosmic forces

#### 2. Universal Abilities
- Energy manipulation
- Matter transformation
- Consciousness projection
- Reality perception

#### 3. Hybrid Powers
- Cross-scale effects
- Form-specific variations
- Environmental interaction
- Multi-entity coordination

### Progression Systems

#### 1. Power Development
- Use-based improvement
- Resource investment
- Knowledge acquisition
- Synergy discovery

#### 2. Upgrade Paths
- Linear progression
- Branching specialization
- Cross-power synthesis
- Scale adaptation

#### 3. Mastery System
- Technique refinement
- Energy efficiency
- Effect enhancement
- Cooldown reduction

## Implementation Notes

1. All systems should support seamless scale transitions
2. Performance optimization crucial for multi-scale interactions
3. Balance requirements vary by game type
4. Modular design enables game-specific customization
5. Consider cross-game progression and balance
6. Maintain consistent underlying mechanics across scales
7. Enable easy extension for new games and scales