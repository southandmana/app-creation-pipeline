# Micro SaaS Pipeline Architecture

## Containment Strategy
This pipeline is **self-contained** - all required files are within the `micro-saas-web-app/` directory.

## Directory Structure
```
micro-saas-web-app/
├── START_MICRO_SAAS_PIPELINE.md          # Main entry point
├── instructions/                          # Step-by-step execution guides
│   ├── prompts/                          # Detailed step instructions
│   │   ├── 01_automated_research_selection.md
│   │   ├── 02_system_tech_optimization.md
│   │   ├── 03_automated_business_intelligence.md
│   │   ├── 04_competitive_differentiation.md
│   │   ├── 05_defensibility_moat_strategy.md
│   │   └── 06_comprehensive_summary_app_generation.md
│   └── patterns/                         # Implementation patterns
│       └── micro_saas_tech_stack.md
├── research_links/                       # Curated research resources
│   ├── market_research.md
│   ├── business_intelligence.md
│   ├── competitive_differentiation.md
│   └── defensibility_moat.md
├── shared_resources/                     # Dependencies copied from global
│   └── research_methodology.md
└── README.md                            # Usage guide
```

## Path Resolution Rules

### For Pipeline START file:
- All paths are relative to `micro-saas-web-app/` directory
- Example: `instructions/prompts/01_automated_research_selection.md`

### For Instruction files:
- Paths can be relative to their own directory or the pipeline root
- Use `../` to go up to pipeline root
- Example: `../shared_resources/research_methodology.md`

## Benefits of Containment
1. **Zero External Dependencies**: No risk of missing files
2. **Portable**: Can be copied/moved as a unit
3. **Predictable Path Resolution**: All paths resolve within known structure
4. **Version Control**: Pipeline evolution is self-contained
5. **Testing**: Can test pipeline in isolation

## Template Authority Rules

When template inconsistencies exist between files:

1. **START file** = User-facing messages (authoritative)
2. **Instruction files** = Implementation details  
3. **Conflict resolution**: START file takes precedence for user messaging
4. **Validation**: All templates must be consistent before production

## Usage
Always execute pipeline from within the `micro-saas-web-app/` directory context for proper relative path resolution.