# Copilot Instructions - Textual (M5 Mac Optimized)

## Context: Optimizing for MacBook Pro M5

You're working on the Textual Python TUI framework, optimized for:
- **CPU**: Apple Silicon M5 (ARM64 architecture)
- **RAM**: 24GB unified memory
- **GPU**: Integrated M5 GPU with Neural Engine
- **OS**: macOS 26.6

## Key Optimization Goals

### 1. Metal GPU Acceleration
When modifying rendering code:
- Always check Metal device availability on macOS
- Use `canvas.context.macos_metal_device` to verify GPU support
- Enable GPU offloading for heavy computations

```python
# Example: Metal-aware widget rendering
def render_widget(self, canvas) -> str:
    if sys.platform == "darwin":  # macOS
        device = getattr(canvas.context, "macos_metal_device", None)
        if device:
            print(f"Metal GPU detected: {device}")
```

### 2. Unified Memory Optimization
- Leverage unified memory for cross-component data sharing
- Implement lazy loading for large datasets
- Use caching to reduce repeated rendering

### 3. Neural Engine Integration
When adding ML features:
- Use CoreML models that run on the Neural Engine
- Enable MPS (Metal Performance Shaders) for GPU acceleration
- Cache ML model inference results

### 4. ARM64-Specific Builds
When compiling extensions:
- Target platform: `macosx-arm64`
- Use `maturin` with ARM64 flags
- Verify Python binary is ARM64 native

```bash
# Build for M5 Mac
python -m pip wheel . --config-settings=setup-args="--plat-name=macosx-arm64"
```

## Code Style Guidelines for M5 Optimization

### Type Hints
Always include type hints, especially for:
- Widget rendering methods
- Event handlers
- Performance-critical functions

```python
from textual.widgets import Button

def on_button_pressed(self, event: Button.Pressed) -> None:
    """Handle button press with M5 optimization."""
    # ... implementation
```

### Performance Annotations
Use `@lru_cache` and similar optimizations where applicable:

```python
from functools import lru_cache

@lru_cache(maxsize=1024)
def render_complex_widget(widget_id: str, *args) -> str:
    """Cache widget rendering for M5 unified memory."""
    # ... implementation
```

## Common Patterns for M5 Mac

### Metal Device Detection
```python
import sys
from textual.canvas import Canvas

device = None
if sys.platform == "darwin":
    device = getattr(canvas.context, "macos_metal_device", None)
    if device:
        print(f"Metal GPU: {device}")
```

### Memory Usage Monitoring
```python
import psutil
import os

def check_memory_pressure() -> bool:
    """Check unified memory pressure on M5 Mac."""
    total = os.sysconf("SC_PAGE_SIZE") * os.sysconf("SC_PHYS_PAGES")
    available = psutil.virtual_memory().available
    return available > total * 0.4  # At least 40% free
```

### GPU Offloading Strategy
```python
from textual.app import App, ComposeResult

class GPUOffloadApp(App):
    """App that offloads heavy work to M5 GPU."""
    
    def on_mount(self) -> None:
        # Heavy computation offloaded to Metal GPU
        if sys.platform == "darwin":
            self._offload_to_gpu()
```

## Testing Checklist for M5 Mac

Before committing changes:

- [ ] Does it work on M5 with Metal GPU?
- [ ] Are ARM64 builds successful?
- [ ] Is memory usage optimized for 24GB unified memory?
- [ ] Does it leverage Neural Engine for ML tasks?
- [ ] Is type checking passing (mypy/pyright)?
- [ ] Performance benchmarks improved or unchanged

## Performance Benchmarks (Target)

| Metric | Current Target | Notes |
|--------|---------------|-------|
| Widget render | < 2ms/frame | Metal GPU enabled |
| Full redraw | < 33ms/frame | 60fps target |
| Memory peak | < 15GB | Leave room for OS |

## Resources

- [Metal Documentation](https://developer.apple.com/metal/)
- [CoreML Guide](https://developer.apple.com/machine-learning/coreml/)
- [M5 Chip Specs](https://www.apple.com/mac/m5/specs/)

---

**Remember**: Optimize for speed, leverage Metal GPU, and always test on M5 Mac! 🚀