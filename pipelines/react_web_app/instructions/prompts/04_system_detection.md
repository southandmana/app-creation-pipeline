# Step 4: System Detection with Fallbacks

## Purpose
Automatically detect user's system capabilities to optimize dependency selection and build configuration.

---

## Detection Commands

### Primary Detection Sequence
Run these commands automatically (no user interaction needed):

```bash
# Node.js and npm versions
node --version
npm --version

# System memory (varies by OS)
# macOS
system_profiler SPHardwareDataType | grep "Memory:"
# Linux  
free -h | grep "Mem:"
# Windows (if applicable)
wmic OS get TotalVisibleMemorySize /value

# Operating system
uname -a

# CPU info
# macOS
sysctl -n machdep.cpu.brand_string
# Linux
lscpu | grep "Model name"

# Available disk space
df -h

# Git availability
git --version

# Check internet connectivity
ping -c 1 google.com
```

---

## Response Templates

### Successful Detection
```
✅ System Check Complete!

Your Setup:
• Node.js: v18.17.0 (Excellent - supports latest libraries!)
• RAM: 16GB (High-end - can use premium dependencies)
• OS: macOS 14.1 (Fully compatible)
• CPU: M2 Pro (Lightning fast builds!)
• Storage: 120GB free (Plenty of space)
• Git: v2.39.0 (Ready for deployment)

Perfect! I can build you something amazing with these capabilities.

Now let me ask a few questions to make sure I create exactly what you want...
```

### Partial Detection (Some Commands Failed)
```
🔍 System Check Complete!

Your Setup:
• Node.js: v16.8.0 (Great - fully compatible)
• RAM: 8GB (Perfect for React apps)
• OS: Windows 10 (Fully supported)
• Git: Available ✅

I'll optimize everything for your system to ensure smooth performance.

Now let me ask a few questions to make sure I create exactly what you want...
```

### Detection Failed (Need Manual Input)
```
🔍 System Check (Need Your Help!)

I couldn't detect some system info automatically. Quick questions:

What's your Node.js version? 
(Type: node --version in your terminal)

If that doesn't work, no problem - I'll set everything up to work on most systems!

Once I know your setup, I'll ask about your app to build exactly what you want...
```

---

## Processing Logic

### Step 4a: Run Detection Commands
```python
def detect_system():
    results = {}
    
    # Node.js version
    try:
        node_version = run_command("node --version")
        results['node'] = parse_node_version(node_version)
        results['node_tier'] = get_compatibility_tier(results['node'])
    except:
        results['node'] = None
    
    # Memory
    try:
        if system == "Darwin":  # macOS
            memory = run_command("system_profiler SPHardwareDataType | grep 'Memory:'")
        elif system == "Linux":
            memory = run_command("free -h | grep 'Mem:'")
        results['memory'] = parse_memory(memory)
        results['memory_tier'] = get_memory_tier(results['memory'])
    except:
        results['memory'] = None
    
    # Continue for all other detection...
    
    return results
```

### Step 4b: Determine System Tier
```python
def get_system_tier(results):
    if (results.get('node_tier') == 'modern' and 
        results.get('memory_tier') == 'high' and
        results.get('cpu_tier') == 'fast'):
        return 'high_end'
    elif (results.get('node_tier') == 'compatible' and
          results.get('memory_tier') == 'medium'):
        return 'mid_range'
    else:
        return 'budget_friendly'
```

---

## System Tiers and Implications

### High-End System
- **Node:** 18+ 
- **RAM:** 16GB+
- **CPU:** Modern (M1/M2, i7/i9, Ryzen 7+)
- **Can Use:** Premium animations, advanced state management, heavy dependencies
- **Build Time:** Fast (15-30 seconds)

### Mid-Range System  
- **Node:** 16+
- **RAM:** 8-16GB  
- **CPU:** Standard
- **Can Use:** Balanced libraries, standard features
- **Build Time:** Medium (30-60 seconds)

### Budget-Friendly System
- **Node:** 14+
- **RAM:** 4-8GB
- **CPU:** Older/slower
- **Can Use:** Lightweight libraries, essential features only
- **Build Time:** Slower (1-2 minutes)

---

## Fallback Handling

### If Node Detection Fails:
```
⚠️ Couldn't find Node.js automatically.

No worries! Let's check:
Type this in your terminal: node --version

What do you see?
- A version number (like v18.17.0) → Great!
- "command not found" → I'll help you install Node.js first
- Something else → Just tell me what you see

Don't worry - I'll get everything working!
```

### If Most Detection Fails:
```
🔍 Having trouble detecting your system automatically.

No problem! I'll build your app to work on most computers. This might mean:
- Slightly larger file sizes (but still fast)
- Compatibility with older systems too
- Rock-solid reliability

Ready to talk about your app? Let's make something awesome!
```

---

## Data Storage & Documentation

### Store Detection Results:
```python
system_config = {
    "node_version": "18.17.0",
    "node_tier": "modern",
    "memory_gb": 16,
    "memory_tier": "high",
    "os": "macOS 14.1",
    "cpu": "M2 Pro", 
    "system_tier": "high_end",
    "git_available": True,
    "detection_success": True,
    "fallback_needed": False
}
```

### Documentation Creation (Step 4)
**MANDATORY**: Create system analysis documentation:

```bash
# Copy and initialize system analysis template
cp instructions/templates/documentation/02_system_analysis_template.md \
   generated_apps/[app-name]/_agent_session/02_system_analysis.md
```

**Fill in the template with:**
- Complete system detection results
- Tier classification reasoning (high_end/mid_range/budget_friendly)
- Technology selection rationale based on system capabilities
- Performance optimization decisions
- Fallback strategies used (if any)
- Timestamp of detection process

This creates a complete record of how the system was optimized for the user's specific environment.

### Use for Dependency Selection:
- High-end → framer-motion, zustand, premium libraries
- Mid-range → react-transition-group, context API, balanced libraries  
- Budget → CSS animations, built-in React state, minimal dependencies

---

## Error Prevention

### Common Issues:
1. **Permission errors** → Use non-privileged commands only
2. **Different OS commands** → Have fallbacks for each OS
3. **Command not found** → Graceful failure with manual fallback
4. **Network timeouts** → Don't require internet for core detection

### Robustness:
- Never fail the entire flow due to detection issues
- Always have manual fallback options
- Default to compatible settings when uncertain

---

## Success Criteria
✅ System capabilities accurately detected (or gracefully handled)
✅ User sees their system recognized and optimized for
✅ Dependency selection tier determined
✅ Smooth transition to questioning phase
✅ No technical errors that break the flow
✅ Fallbacks work when detection fails

---

## Next Step
Always advances to Step 5: Structured Questions (with system tier context)