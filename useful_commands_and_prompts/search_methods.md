# Search Methods in Claude Code

## Available Search Methods

### **1. Grep Tool (Most Powerful)**
Claude can search your entire project for any pattern/text:
```
Example: "Search for all instances of 'useState' in the project"
Example: "Find all TODO comments" 
Example: "Search for any mentions of 'authentication'"
```

### **2. Glob Tool**
Claude can find files by name patterns:
```
Example: "Find all TypeScript files"
Example: "Find all test files"
Example: "Find all markdown documentation"
```

### **3. Task Tool with Agent**
For complex, multi-step searches:
```
Example: "Search for all API endpoints and their authentication methods"
Example: "Find and analyze all database queries"
Example: "Search for security vulnerabilities across the codebase"
```

## How to Request a Deep Search

Just ask naturally, like:
- "Search the entire project for any references to Stripe"
- "Find all files that import React"
- "Search for all API routes in the project"
- "Find every place where we're making database calls"

## What Claude Can Search For

- **Text patterns** (keywords, function names, variables)
- **Code patterns** (imports, exports, specific syntax)
- **File types** (all .ts files, all .md files)
- **Comments** (TODOs, FIXMEs, documentation)
- **Dependencies** (package usage, imports)
- **Security issues** (exposed keys, vulnerabilities)
- **Business logic** (specific features, workflows)

---

*Claude Code's search capabilities help you navigate and understand your entire codebase quickly.*