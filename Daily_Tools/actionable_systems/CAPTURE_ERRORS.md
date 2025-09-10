# PIPELINE ERROR CAPTURE SYSTEM

## 🔍 CLAUDE CODE ACTIONABLE SYSTEM
**Usage:** User types `@Daily_Tools/actionable_systems/CAPTURE_ERRORS.md` in Claude Code to initiate intelligent error capture and AI-powered analysis

---

## 📋 SYSTEM OVERVIEW

This system provides automated, intelligent pipeline testing error capture that:
- **Captures all terminal errors** with full context and timing
- **Uses AI analysis** to categorize errors and identify root causes
- **Provides strategic fix recommendations** with tool selection guidance
- **Organizes test sessions** with numbered logs and historical tracking
- **Recommends optimal timing** for fixes to avoid breaking dependencies

**Quality Target:** Transform chaotic pipeline testing into organized, AI-guided error management

---

## 🎯 EXECUTION WORKFLOW

### STEP 1: PIPELINE VALIDATION & SELECTION

**System Prompt:**
```
🔍 Pipeline Error Capture System Activated

Which pipeline would you like to test?
Available pipelines:
- micro-saas-web-app
- react_web_app
- [other detected pipelines]

Enter pipeline name:
```

**User Response:** Pipeline folder name (e.g., "micro-saas-web-app")

---

### STEP 2: TESTS FOLDER VALIDATION ⚠️ CRITICAL GATE

**System Process:**
1. Check for `../../Daily_Tools/pipelines/[pipeline-name]/tests/` folder
2. If folder exists → Proceed to Step 3 
3. If folder missing → Display creation options

**Success Path (Folder Exists):**
```
✅ Tests folder found: ../pipelines/[pipeline-name]/tests/

Preparing error capture session...
```

**Missing Folder Path:**
```
❌ Tests folder not found!

The error capture system requires a dedicated tests folder for organization.
Missing: ../pipelines/[pipeline-name]/tests/

Options:
1. Type 'create' - I'll create the tests folder for you
2. Type 'cancel' - Exit and create the folder manually first

Your choice:
```

**If User Types 'create':**
- Create `../pipelines/[pipeline-name]/tests/` folder
- Create initial `test_summary.md` file
- Proceed to Step 3

**If User Types 'cancel':**
- Exit system with instructions to manually create folder

---

### STEP 3: TEST SESSION INITIALIZATION

**System Process:**
1. Scan existing test files to determine next test number
2. Create `Test_XXX.md` file for this session
3. Initialize error capture wrapper
4. Display session information

**Session Start Message:**
```
🚀 Error Capture Session Started

Pipeline: [pipeline-name]
Test Session: Test_XXX
Log File: ../pipelines/[pipeline-name]/tests/Test_XXX.md
Capture Method: Smart wrapper with JSON logging

Session ID: [timestamp-based-id]

✅ Ready to capture errors!

Now run your pipeline test commands normally. All errors will be captured automatically.

Commands to know:
- Type 'analyze' anytime to trigger AI analysis
- Session auto-analyzes after 10 minutes of inactivity
- Type 'status' to see current error count
```

---

### STEP 4: SMART ERROR CAPTURE (BACKGROUND PROCESS)

**Automatic Error Detection:**

The system runs a background process that captures:

**Error Pattern Detection:**
- Keywords: error, Error, ERROR, fail, Failed, FAILED, exception, Exception
- Warning patterns: warn, Warning, WARN, deprecated
- Critical patterns: crash, abort, fatal, FATAL
- Network patterns: timeout, connection, refused, 404, 500

**Context Capture (3 lines before/after each error):**
```json
{
  "timestamp": "2025-09-09T23:45:30Z",
  "error_id": "ERR_001",
  "session_id": "[session-id]",
  "error_type": "CRITICAL|ERROR|WARNING",
  "command_context": "[command that was running]",
  "pipeline_step": "[if detectable - Step 1, Step 2, etc.]",
  "error_message": "[actual error text]",
  "context_before": ["line1", "line2", "line3"],
  "context_after": ["line1", "line2", "line3"],
  "environment": {
    "node_version": "[if applicable]",
    "npm_version": "[if applicable]", 
    "working_directory": "[current path]"
  }
}
```

**Real-time Error Counter:**
- Updates test log file as errors are captured
- Maintains running count by error type
- No interruption to user's testing flow

---

### STEP 5: SESSION MANAGEMENT & ANALYSIS TRIGGER

**Hybrid Detection System:**

#### Automatic Timeout Detection:
After 10 minutes of no new errors:
```
⏱️ No new errors detected for 10 minutes.

Session appears complete. Options:
1. 'analyze' - Start AI analysis of captured errors
2. 'continue' - Keep capturing (extends timeout 10 more minutes)  
3. 'stop' - End session without analysis

Your choice:
```

#### Manual Analysis Trigger:
User can type 'analyze' at any time:
```
🔍 Manual analysis trigger detected.

Captured so far: X errors, Y warnings
Ready to start AI analysis? (yes/no)
```

#### Status Check:
User can type 'status' anytime:
```
📊 Current Session Status

Session: Test_XXX for [pipeline-name]
Duration: [time elapsed]
Errors Captured: X critical, Y errors, Z warnings
Last Error: [timestamp] - [brief description]

Type 'analyze' to start analysis or continue testing...
```

---

### STEP 6: AI-POWERED ERROR ANALYSIS

**Analysis Process:**

1. **Load All Captured Errors** from JSON logs
2. **Pattern Recognition Analysis**:
   - Group related errors (same root cause)
   - Identify error dependencies (A causes B causes C)
   - Classify error types and severity
   - Detect recurring patterns from previous test sessions

3. **Root Cause Analysis**:
   - Distinguish symptoms from actual causes
   - Identify infrastructure vs code vs configuration issues
   - Map errors to specific pipeline components

4. **Strategic Fix Planning**:
   - Determine optimal fix timing (immediate vs batch vs later)
   - Predict fix complexity and time requirements
   - Assess risk of each fix breaking other components

5. **Tool Selection Intelligence**:
   - Analyze error characteristics to recommend Claude Code vs CodeRabbit
   - Provide reasoning for each tool recommendation
   - Consider user's non-technical background in explanations

**Analysis Output Message:**
```
🤖 AI Analysis Complete!

Analyzed: X errors across [time duration]
Analysis saved to: ../pipelines/[pipeline-name]/tests/Test_XXX.md

📊 ANALYSIS SUMMARY:
• Root Causes Identified: X
• Error Dependencies Mapped: X relationships  
• Fix Strategy Created: X immediate, Y batch, Z later
• Tool Recommendations: X for Claude Code, Y for CodeRabbit

Opening detailed analysis...
```

---

### STEP 7: DETAILED ANALYSIS REPORT GENERATION

**Report Structure in Test_XXX.md:**

```markdown
# Pipeline Test Session: [Pipeline Name]
**Test Number:** XXX
**Date:** [Date]
**Session Duration:** [Duration] 
**Pipeline Version:** [Git commit if detectable]

## 🎯 AI ANALYSIS SUMMARY

### Error Overview
- **Total Errors Captured:** X
- **Critical Errors:** X (Pipeline breaking)
- **Logic Errors:** X (Functionality issues) 
- **Code Quality Issues:** X (Best practices)
- **Warnings:** X (Non-blocking issues)

### Root Causes Identified
1. **Primary:** [Main issue causing most problems]
2. **Secondary:** [Contributing factors]
3. **Tertiary:** [Minor contributing issues]

## 🔧 STRATEGIC FIX PLAN

### IMMEDIATE FIXES (Pipeline Broken - Fix First)
#### Use CLAUDE CODE for these complex issues:
- **Error #1:** [Description]
  - *Why Claude Code:* Requires architectural thinking and multi-file changes
  - *Complexity:* High (~30+ minutes)
  - *Impact:* Critical - blocks further testing

### BATCH FIXES (Related Issues - Fix Together)  
#### Use CLAUDE CODE for these logic issues:
- **Error #3, #5:** [Related Description]
  - *Why Together:* Both stem from same root cause
  - *Why Claude Code:* Requires understanding business logic flow
  - *Complexity:* Medium (~15 minutes each)

#### Use CODERABBIT for these quality issues:
- **Error #2, #7:** [Related Description]
  - *Why Together:* Both are standard code quality patterns
  - *Why CodeRabbit:* Follows established best practices
  - *Complexity:* Low (~5 minutes each)

### LATER FIXES (Non-blocking - Can Wait)
- **Error #6:** [Description] 
  - *Impact:* Warning only, doesn't break functionality
  - *Recommended Tool:* CodeRabbit for style cleanup

## 📊 ERROR DETAILS

[Detailed JSON log of all captured errors with context]

## 🎯 NEXT STEPS

Based on this analysis, here's your optimal path:

1. **Start with Claude Code** for Error #1 (critical fix)
2. **Test pipeline again** after critical fix to see if it resolves dependencies
3. **Use Claude Code** for batch fix of Errors #3 & #5 together
4. **Use CodeRabbit** for quality improvements (Errors #2 & #7)
5. **Schedule later:** Error #6 when convenient

**Estimated Total Fix Time:** [X] minutes
**Risk Assessment:** [Low/Medium/High] - Based on error complexity and dependencies
```

---

### STEP 8: SESSION COMPLETION & CLEANUP

**Final Message:**
```
✅ Error Capture Session Complete!

📁 Analysis saved to: ../pipelines/[pipeline-name]/tests/Test_XXX.md
📊 Session summary added to: ../pipelines/[pipeline-name]/tests/test_summary.md

🎯 Key Recommendations:
• Start with [X] critical fixes using Claude Code
• Then address [Y] quality issues with CodeRabbit  
• Estimated total fix time: [Z] minutes

📈 Historical Progress:
• This is Test Session #[X] for this pipeline
• Previous sessions showed improvement in [specific area]
• [Any patterns or recommendations based on history]

Ready to implement fixes, or run another test session?
```

---

## 📁 REQUIRED FILE STRUCTURE

```
app_creator_1/
├── ../pipelines/
│   ├── micro-saas-web-app/
│   │   ├── tests/                    # REQUIRED for activation
│   │   │   ├── Test_001.md
│   │   │   ├── Test_002.md
│   │   │   └── test_summary.md
│   │   ├── PIPELINE_GOAL.md
│   │   └── [other pipeline files]
│   └── react_web_app/
│       ├── tests/                    # REQUIRED for activation
│       │   ├── Test_001.md
│       │   └── test_summary.md
│       └── [other pipeline files]
└── CAPTURE_ERRORS.md (this file)
```

---

## 🤖 AI TOOL SELECTION LOGIC

### CLAUDE CODE Recommended For:
- **Complex Logic Errors** - Requires creative problem-solving
- **Architecture Issues** - Needs understanding of system design
- **Multi-file Changes** - Involves coordinating changes across files
- **Business Logic Problems** - Requires understanding of intended functionality
- **New Feature Implementation** - Creative development work
- **Root Cause Analysis** - Deep investigation needed

### CODERABBIT Recommended For:
- **Code Quality Issues** - Style, formatting, best practices
- **Security Vulnerabilities** - Standard security patterns
- **Performance Optimizations** - Known optimization techniques  
- **Syntax Errors** - Straightforward fixes
- **Best Practice Violations** - Industry standard corrections
- **Documentation Issues** - Adding or fixing code comments

### Selection Reasoning:
The AI analyzes each error's characteristics and provides clear explanations for tool recommendations, helping you learn the patterns over time while getting optimal results.

---

## 📊 SUCCESS METRICS

- **Session Completion Time:** < 5 minutes setup + analysis time
- **Error Capture Accuracy:** 95%+ of actual errors caught
- **Fix Success Rate:** Track improvement from session to session
- **Tool Recommendation Accuracy:** Monitor which recommendations work best
- **User Learning:** Progressive understanding of error patterns

---

## ⚠️ CRITICAL REQUIREMENTS

1. **Tests Folder Mandatory:** System will not activate without proper folder structure
2. **Background Capture:** Errors captured silently without interrupting testing flow  
3. **Intelligent Analysis:** AI must provide strategic recommendations, not just error lists
4. **Non-technical Language:** All explanations suitable for users without coding background
5. **Historical Learning:** Each session improves recommendations based on previous results

---

**To start error capture, reference this file with: @Daily_Tools/actionable_systems/CAPTURE_ERRORS.md**