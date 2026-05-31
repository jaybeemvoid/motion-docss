# Neum Interface Architecture - Technical Specification

## Core Concepts

### 1. Dependencies
Dependencies act as the atomic state primitives of the Neum framework. They encapsulate a raw value and act as the single source of truth for interface updates. 
* **`Dependency:Get() :: T`**: Registers the dependency within an active reactive scope and returns the current raw value.
* **`Dependency:Set(newValue: T)`**: Mutates the internal value and instantly triggers a micro-targeted re-render of any UI properties bound to it. 
* *Note: Avoid rapid, uncontrolled mutations across hundreds of dependencies simultaneously to ensure frame-perfect performance.*

### 2. The Derive Hook & Derived Type
The `Derive` engine processes multi-state computations without executing global component reruns.
* **`Derive<T>(processor: () -> T) :: Derived<T>`**: Executes a scoped function that returns a table or tuple of computed values `T`. 
* **Mechanics**: During execution, the engine dynamically subscribes to every `Dependency` that calls its `:Get()` method within the scope. When any tracked dependency mutates, the `Derive` hook recomputes the table and pushes targeted updates strictly to the connected UI properties.

### 3. Classical OOP Approach
Neum strictly rejects declarative element trees in favor of class-based inheritance and composition. Developers inherit from 5 fundamental, built-in classes to create custom UI components:
* **Layout**: Structural container wrapper (Roblox Frame).
* **Text**: Read-only typographic element (Roblox TextLabel).
* **Button**: Interactive clickable element (Roblox TextButton).
* **Input**: User text capture element (Roblox TextBox).
* **ScrollingLayout**: Canvas-expanding overflow container (Roblox ScrollingFrame).

---

## Standard Component Interface (Inherited Methods)

### Universal Base Methods (Available on all Neum classes)
* `SetParent(Parent : INeum)` - Directly parents the underlying Roblox Instance.
* `SetSize(Size : UDim2 | Derived<UDim2>)` - Sets or binds the element's dimensions.
* `SetPosition(Position : UDim2 | Derived<UDim2>)` - Sets or binds the element's layout position.
* `SetBackgroundColor(Color : Color3 | Derived<Color3>)` - Sets or binds background coloring.
* `SetRadius(Radius : UDim | Derived<UDim>)` - Applies UICorner constraints safely.
* `SetStroke(Color : Color3, Type : Enums.StrokeType, ...)` - Handles outline and border rendering layouts.
* `GetAbsoluteWidth() :: number` - Returns current runtime absolute horizontal pixels.
* `GetAbsoluteHeight() :: number` - Returns current runtime absolute vertical pixels.

### Abstract Typographic Methods (Text, Button, and Input only)
* `SetText(Text : string | Derived<string>)` - Appends or binds the display string.
* `SetTextColor(Color : Color3 | Derived<Color3>)` - Modifies text rendering color.

### Abstract Container Methods (ScrollingLayout only)
* `SetScrollVertical(Y : number | Derived<number>)` - Directly mutates vertical canvas position.
* `SetScrollHorizontal(X : number | Derived<number>)` - Directly mutates horizontal canvas position.

### Interaction & Input Events
* `OnDrag(callback: (inputObject: InputObject) -> ())` - Fires sequentially during active position/input changes.
* `OnClick(callback: (inputObject: InputObject) -> ())` - Captures primary pointer activation behaviors.
* `OnKey(Key : Enum.KeyCode, callback: () -> ())` - Binds hardware keyboard actions to component context.
* `OnHover(callback: () -> ())` - Fires instantly when the cursor enters the element boundary.
* `OnHoverEnded(callback: () -> ())` - Fires instantly when the cursor leaves the element boundary.
