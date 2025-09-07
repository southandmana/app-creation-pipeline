# Pipeline Structure Standard

## Overview
This document defines the standard folder structure that EVERY pipeline must follow. This ensures consistency and makes the system easy to understand and maintain.

---

## Standard Pipeline Structure

Every pipeline MUST follow this exact structure:

```
pipelines/
└── [pipeline_name]/
    ├── README.md                 # Pipeline overview and how it works
    ├── development_log.md        # Track development of THIS pipeline
    ├── instructions/             # AI INSTRUCTIONS (what tells AI how to build)
    │   ├── prompts/             # Tested prompts that work
    │   ├── templates/           # Code templates to use
    │   ├── patterns/            # Common patterns to follow
    │   └── specifications/      # How to generate app specs
    └── generated_apps/          # ACTUAL APPS CREATED (output from using instructions)
        ├── test_app_1/         # First test app
        │   ├── [app code files]
        │   └── testing_log.md  # Test results for THIS specific app
        ├── test_app_2/         
        │   ├── [app code files]
        │   └── testing_log.md  # Test results for THIS specific app
        └── [app_name]/         
            ├── [app code files]
            └── testing_log.md  # Test results for THIS specific app
```

---

## Folder Purposes

### 📚 `instructions/` - The AI's Playbook
Everything the AI needs to know to generate apps:
- **prompts/** - Exact prompts that generate good code
- **templates/** - Boilerplate code to start from (includes TESTING_LOG_TEMPLATE.md)
- **patterns/** - Reusable solutions (auth, API calls, etc.)
- **specifications/** - How to turn user ideas into technical specs

### 🚀 `generated_apps/` - The Output
Actual apps created using the instructions:
- Each app in its own folder
- Complete, runnable applications
- Each app has its own `testing_log.md`
- Used for testing the pipeline
- Shows what the pipeline can actually produce

### 📝 Documentation Files
- **README.md** - How this specific pipeline works
- **development_log.md** - Journal of building this pipeline
- **testing_log.md** (in each generated app) - Test results for that specific app

---

## Log Types and Their Purposes

### 🔨 `development_log.md` (Pipeline Level)
**Location:** `pipelines/[pipeline_name]/development_log.md`
**Purpose:** Track the creation and evolution of the pipeline itself
- Design decisions for the pipeline
- Problems encountered building the pipeline
- Time spent developing pipeline features
- Improvements and iterations

### 🧪 `testing_log.md` (App Level)
**Location:** `pipelines/[pipeline_name]/generated_apps/[app_name]/testing_log.md`
**Purpose:** Track the testing of each generated app
- What worked/failed in THIS specific app
- Manual fixes required
- Generation time and stats
- Quality assessment
- Lessons for improving the pipeline

### 📊 Difference Between Logs
| Log Type | Tracks | Location | Example Content |
|----------|--------|----------|-----------------|
| Development Log | Pipeline creation | Pipeline root | "Added new authentication template" |
| Testing Log | App generation results | Each generated app | "Todo app failed to compile, missing import" |

---

## Example: React Web App Pipeline

```
pipelines/
└── react_web_app/
    ├── README.md
    ├── development_log.md
    ├── instructions/
    │   ├── prompts/
    │   │   ├── component_generation.md
    │   │   ├── state_management.md
    │   │   └── routing_setup.md
    │   ├── templates/
    │   │   ├── TESTING_LOG_TEMPLATE.md
    │   │   ├── base_app/
    │   │   ├── components/
    │   │   └── hooks/
    │   ├── patterns/
    │   │   ├── authentication.js
    │   │   ├── api_service.js
    │   │   └── form_validation.js
    │   └── specifications/
    │       └── spec_generator.md
    └── generated_apps/
        ├── todo_app_test/
        │   ├── src/
        │   ├── package.json
        │   └── testing_log.md
        ├── weather_dashboard/
        │   ├── src/
        │   ├── package.json
        │   └── testing_log.md
        └── connect_four_game/
            ├── src/
            ├── package.json
            └── testing_log.md
```

---

## Rules for All Pipelines

### ✅ MUST Have:
1. Clear separation between instructions and output
2. `generated_apps/` folder for testing
3. Own `development_log.md` 
4. README explaining the pipeline

### ❌ MUST NOT:
1. Mix instruction files with generated code
2. Put generated apps outside the pipeline folder
3. Share development logs between pipelines
4. Deviate from this structure

---

## Creating a New Pipeline

When creating any new pipeline (CLI, REST API, etc.):

1. **Copy this structure exactly**
```bash
pipelines/
└── new_pipeline_name/
    ├── README.md
    ├── development_log.md
    ├── instructions/
    │   ├── prompts/
    │   ├── templates/
    │   ├── patterns/
    │   └── specifications/
    └── generated_apps/
```

2. **Start documenting in `development_log.md`**

3. **Build instructions before generating apps**

4. **Test by generating apps in `generated_apps/`**

---

## Why This Structure?

### 🎯 Clear Separation
- **Instructions** = Input (what AI uses)
- **Generated Apps** = Output (what AI creates)
- Never confuse the two

### 🔬 Better Testing
- Each generated app is isolated
- Easy to compare multiple test apps
- Can delete/regenerate without affecting instructions

### 📈 Scalability
- Same structure for ALL pipelines
- Easy to add new pipelines
- Consistent across the entire system

### 🧠 Mental Model
Think of it like a kitchen:
- `instructions/` = Recipes and techniques
- `generated_apps/` = The actual dishes you cook
- You don't mix recipes with finished meals!

---

## Current Pipelines Status

| Pipeline | Structure Ready | Instructions Built | Apps Generated |
|----------|----------------|-------------------|----------------|
| React Web App | ✅ | 🚧 In Progress | ⏳ Not Started |
| CLI Tools | ❌ | ❌ | ❌ |
| REST API | ❌ | ❌ | ❌ |
| Chrome Extension | ❌ | ❌ | ❌ |

---

## Important Notes

1. **Always test in `generated_apps/`** - Never create test apps elsewhere
2. **Document everything** - Future you will thank present you
3. **Follow the structure** - Consistency is key
4. **One pipeline at a time** - Complete React before starting CLI

This structure standard ensures every pipeline is organized, testable, and maintainable.