# Code Quality Validation System

## Purpose
Ensure all generated React applications meet professional standards through automated quality checks, validation rules, and continuous monitoring during the generation process.

---

## Quality Validation Framework

### 1. Multi-Layer Quality Assurance

```javascript
const validateCodeQuality = (generatedCode, specification) => {
  const validationResults = {
    syntaxValidation: validateSyntax(generatedCode),
    bestPracticesCompliance: validateBestPractices(generatedCode),
    securityValidation: validateSecurity(generatedCode),
    performanceValidation: validatePerformance(generatedCode),
    accessibilityValidation: validateAccessibility(generatedCode),
    testCoverage: validateTestCoverage(generatedCode),
    codeComplexity: validateComplexity(generatedCode),
    architectureCompliance: validateArchitecture(generatedCode, specification)
  }

  const overallScore = calculateQualityScore(validationResults)
  
  return {
    passed: overallScore >= QUALITY_THRESHOLD,
    score: overallScore,
    validationResults,
    criticalIssues: identifyCriticalIssues(validationResults),
    recommendations: generateRecommendations(validationResults)
  }
}

const QUALITY_THRESHOLD = 85 // Minimum 85% quality score required
```

### 2. Syntax and Structure Validation

```javascript
const validateSyntax = (codeFiles) => {
  const issues = []
  let score = 100

  codeFiles.forEach(file => {
    // JavaScript/JSX syntax validation
    const syntaxErrors = parseSyntax(file.content, file.extension)
    if (syntaxErrors.length > 0) {
      issues.push(...syntaxErrors.map(error => ({
        file: file.path,
        type: 'SYNTAX_ERROR',
        severity: 'CRITICAL',
        message: error.message,
        line: error.line,
        fix: error.suggestedFix
      })))
      score -= 20 // Major penalty for syntax errors
    }

    // ESLint rule violations
    const lintIssues = runESLint(file.content, file.path)
    lintIssues.forEach(issue => {
      issues.push({
        file: file.path,
        type: 'LINT_VIOLATION',
        severity: issue.severity === 2 ? 'HIGH' : 'MEDIUM',
        message: issue.message,
        line: issue.line,
        rule: issue.ruleId,
        fix: issue.fix || generateFixSuggestion(issue)
      })
      score -= issue.severity === 2 ? 5 : 2
    })

    // Prettier formatting violations
    const formattingIssues = checkFormatting(file.content)
    if (formattingIssues.length > 0) {
      issues.push({
        file: file.path,
        type: 'FORMATTING_ISSUE',
        severity: 'LOW',
        message: 'Code formatting inconsistencies detected',
        fixes: formattingIssues
      })
      score -= 1
    }
  })

  return {
    score: Math.max(0, score),
    issues,
    passed: issues.filter(i => i.severity === 'CRITICAL').length === 0
  }
}

const validateBestPractices = (codeFiles) => {
  const violations = []
  let score = 100

  codeFiles.forEach(file => {
    if (file.extension === 'jsx' || file.extension === 'js') {
      // React-specific best practices
      const reactViolations = validateReactPractices(file.content, file.path)
      violations.push(...reactViolations)

      // General JavaScript best practices
      const jsViolations = validateJSPractices(file.content, file.path)
      violations.push(...jsViolations)

      // SOLID principles adherence
      const solidViolations = validateSOLIDPrinciples(file.content, file.path)
      violations.push(...solidViolations)
    }
  })

  // Calculate score based on violations
  violations.forEach(violation => {
    const penalty = {
      'CRITICAL': 15,
      'HIGH': 8,
      'MEDIUM': 4,
      'LOW': 2
    }[violation.severity] || 2
    
    score -= penalty
  })

  return {
    score: Math.max(0, score),
    violations,
    passed: violations.filter(v => v.severity === 'CRITICAL').length === 0
  }
}
```

---

## React-Specific Quality Checks

### 1. Component Quality Validation

```javascript
const validateReactPractices = (code, filePath) => {
  const violations = []
  const ast = parseToAST(code)

  // Check for proper component structure
  const componentViolations = [
    checkFunctionalVsClassComponents(ast),
    checkHooksRules(ast),
    checkPropTypes(ast),
    checkKeyProps(ast),
    checkStateManagement(ast),
    checkEffectDependencies(ast),
    checkMemoization(ast),
    checkAccessibility(ast)
  ].flat()

  violations.push(...componentViolations.map(v => ({
    ...v,
    file: filePath,
    category: 'REACT_BEST_PRACTICES'
  })))

  return violations
}

const checkFunctionalVsClassComponents = (ast) => {
  const issues = []
  
  // Ensure functional components are used over class components
  const classComponents = findClassComponents(ast)
  classComponents.forEach(component => {
    issues.push({
      type: 'OUTDATED_PATTERN',
      severity: 'MEDIUM',
      message: `Class component '${component.name}' should be converted to functional component with hooks`,
      line: component.line,
      fix: 'Convert to functional component using useState and useEffect hooks'
    })
  })

  return issues
}

const checkHooksRules = (ast) => {
  const issues = []
  
  // Rules of Hooks validation
  const hooksUsage = analyzeHooksUsage(ast)
  
  hooksUsage.violations.forEach(violation => {
    issues.push({
      type: 'HOOKS_RULE_VIOLATION',
      severity: 'HIGH',
      message: violation.message,
      line: violation.line,
      fix: violation.suggestedFix
    })
  })

  // Custom hooks naming convention
  const customHooks = findCustomHooks(ast)
  customHooks.forEach(hook => {
    if (!hook.name.startsWith('use')) {
      issues.push({
        type: 'NAMING_CONVENTION',
        severity: 'MEDIUM',
        message: `Custom hook '${hook.name}' should start with 'use'`,
        line: hook.line,
        fix: `Rename to 'use${capitalize(hook.name)}'`
      })
    }
  })

  return issues
}

const checkPropTypes = (ast) => {
  const issues = []
  const components = findComponents(ast)
  
  components.forEach(component => {
    if (component.hasProps && !component.hasPropTypes && !component.hasTypeScript) {
      issues.push({
        type: 'MISSING_PROP_VALIDATION',
        severity: 'MEDIUM',
        message: `Component '${component.name}' accepts props but lacks PropTypes or TypeScript definitions`,
        line: component.line,
        fix: 'Add PropTypes definition or convert to TypeScript'
      })
    }
  })

  return issues
}
```

### 2. Performance Quality Checks

```javascript
const validatePerformance = (codeFiles) => {
  const issues = []
  let score = 100

  codeFiles.forEach(file => {
    if (file.extension === 'jsx' || file.extension === 'js') {
      const performanceIssues = [
        checkUnnecessaryRerenders(file.content),
        checkMemoizationOpportunities(file.content),
        checkLargeComponents(file.content),
        checkExpensiveOperations(file.content),
        checkBundleSize(file.content)
      ].flat()

      issues.push(...performanceIssues.map(issue => ({
        ...issue,
        file: file.path
      })))

      score -= performanceIssues.reduce((total, issue) => {
        return total + (issue.impact || 5)
      }, 0)
    }
  })

  return {
    score: Math.max(0, score),
    issues,
    passed: issues.filter(i => i.severity === 'HIGH').length === 0
  }
}

const checkUnnecessaryRerenders = (code) => {
  const issues = []
  const ast = parseToAST(code)
  
  // Find components without memoization that could benefit from it
  const components = findComponents(ast)
  
  components.forEach(component => {
    if (shouldBeMemoized(component) && !isMemoized(component)) {
      issues.push({
        type: 'PERFORMANCE_OPPORTUNITY',
        severity: 'MEDIUM',
        message: `Component '${component.name}' may benefit from React.memo`,
        line: component.line,
        impact: 8,
        fix: 'Wrap component with React.memo or use useMemo for expensive calculations'
      })
    }
  })

  return issues
}

const checkExpensiveOperations = (code) => {
  const issues = []
  const ast = parseToAST(code)
  
  // Find expensive operations in render
  const expensiveOperations = findExpensiveOperationsInRender(ast)
  
  expensiveOperations.forEach(operation => {
    issues.push({
      type: 'EXPENSIVE_RENDER_OPERATION',
      severity: 'HIGH',
      message: `Expensive operation '${operation.type}' should be memoized`,
      line: operation.line,
      impact: 15,
      fix: `Move to useMemo hook: useMemo(() => ${operation.code}, [dependencies])`
    })
  })

  return issues
}
```

---

## Security Quality Validation

### 1. Security Vulnerability Detection

```javascript
const validateSecurity = (codeFiles) => {
  const vulnerabilities = []
  let score = 100

  codeFiles.forEach(file => {
    const securityIssues = [
      checkXSSVulnerabilities(file.content),
      checkDangerousHTML(file.content),
      checkInputValidation(file.content),
      checkSecretExposure(file.content),
      checkInsecureDependencies(file.path, file.content)
    ].flat()

    vulnerabilities.push(...securityIssues.map(issue => ({
      ...issue,
      file: file.path
    })))

    score -= securityIssues.reduce((total, issue) => {
      const penalty = {
        'CRITICAL': 30,
        'HIGH': 15,
        'MEDIUM': 8,
        'LOW': 3
      }[issue.severity] || 3
      
      return total + penalty
    }, 0)
  })

  return {
    score: Math.max(0, score),
    vulnerabilities,
    passed: vulnerabilities.filter(v => v.severity === 'CRITICAL').length === 0
  }
}

const checkDangerousHTML = (code) => {
  const issues = []
  const dangerousUsages = findDangerouslySetInnerHTML(code)
  
  dangerousUsages.forEach(usage => {
    if (!hasSanitization(usage)) {
      issues.push({
        type: 'XSS_VULNERABILITY',
        severity: 'CRITICAL',
        message: 'dangerouslySetInnerHTML used without proper sanitization',
        line: usage.line,
        fix: 'Use DOMPurify.sanitize() before setting HTML content',
        remediation: `
// Add DOMPurify sanitization:
import DOMPurify from 'dompurify'

const sanitizedHTML = DOMPurify.sanitize(htmlContent)
<div dangerouslySetInnerHTML={{ __html: sanitizedHTML }} />
`
      })
    }
  })

  return issues
}

const checkInputValidation = (code) => {
  const issues = []
  const inputHandlers = findInputHandlers(code)
  
  inputHandlers.forEach(handler => {
    if (!hasValidation(handler)) {
      issues.push({
        type: 'INPUT_VALIDATION_MISSING',
        severity: 'HIGH',
        message: `Input handler '${handler.name}' lacks validation`,
        line: handler.line,
        fix: 'Add input validation using validation library or custom validation',
        remediation: generateValidationExample(handler)
      })
    }
  })

  return issues
}
```

---

## Accessibility Quality Validation

### 1. A11y Compliance Checking

```javascript
const validateAccessibility = (codeFiles) => {
  const issues = []
  let score = 100

  codeFiles.forEach(file => {
    if (file.extension === 'jsx') {
      const a11yIssues = [
        checkAriaAttributes(file.content),
        checkKeyboardNavigation(file.content),
        checkSemanticHTML(file.content),
        checkColorContrast(file.content),
        checkImageAltText(file.content),
        checkFormAccessibility(file.content)
      ].flat()

      issues.push(...a11yIssues.map(issue => ({
        ...issue,
        file: file.path
      })))

      score -= a11yIssues.length * 3 // 3 points per accessibility issue
    }
  })

  return {
    score: Math.max(0, score),
    issues,
    passed: issues.filter(i => i.severity === 'HIGH').length === 0
  }
}

const checkAriaAttributes = (code) => {
  const issues = []
  const interactiveElements = findInteractiveElements(code)
  
  interactiveElements.forEach(element => {
    if (element.type === 'button' && !element.hasAriaLabel && !element.hasTextContent) {
      issues.push({
        type: 'ACCESSIBILITY_MISSING_LABEL',
        severity: 'MEDIUM',
        message: `Button element lacks accessible label`,
        line: element.line,
        fix: 'Add aria-label attribute or visible text content'
      })
    }

    if (element.type === 'input' && !element.hasLabel) {
      issues.push({
        type: 'ACCESSIBILITY_MISSING_LABEL',
        severity: 'HIGH',
        message: `Input element lacks associated label`,
        line: element.line,
        fix: 'Add <label> element or aria-labelledby attribute'
      })
    }
  })

  return issues
}

const checkKeyboardNavigation = (code) => {
  const issues = []
  const customInteractives = findCustomInteractiveElements(code)
  
  customInteractives.forEach(element => {
    if (!element.hasTabIndex && !element.hasKeyHandlers) {
      issues.push({
        type: 'ACCESSIBILITY_KEYBOARD_NAV',
        severity: 'MEDIUM',
        message: `Custom interactive element not keyboard accessible`,
        line: element.line,
        fix: 'Add tabIndex and onKeyDown handler for Enter/Space keys'
      })
    }
  })

  return issues
}
```

---

## Architecture Quality Validation

### 1. Code Architecture Compliance

```javascript
const validateArchitecture = (codeFiles, specification) => {
  const issues = []
  let score = 100

  // Check file organization
  const organizationIssues = validateFileOrganization(codeFiles)
  issues.push(...organizationIssues)

  // Check component hierarchy
  const hierarchyIssues = validateComponentHierarchy(codeFiles)
  issues.push(...hierarchyIssues)

  // Check state management pattern adherence
  const stateIssues = validateStateManagementPattern(codeFiles, specification.stateManagement)
  issues.push(...stateIssues)

  // Check dependency management
  const dependencyIssues = validateDependencies(codeFiles, specification.dependencies)
  issues.push(...dependencyIssues)

  score -= issues.reduce((total, issue) => {
    return total + (issue.impact || 5)
  }, 0)

  return {
    score: Math.max(0, score),
    issues,
    passed: issues.filter(i => i.severity === 'HIGH').length === 0
  }
}

const validateFileOrganization = (codeFiles) => {
  const issues = []
  const expectedStructure = getExpectedFileStructure()
  
  // Check for proper folder structure
  const actualStructure = analyzeFileStructure(codeFiles)
  const structureViolations = compareStructures(expectedStructure, actualStructure)
  
  structureViolations.forEach(violation => {
    issues.push({
      type: 'ARCHITECTURE_STRUCTURE',
      severity: 'MEDIUM',
      message: violation.message,
      fix: violation.suggestedFix
    })
  })

  return issues
}
```

---

## Quality Score Calculation

### 1. Weighted Quality Scoring

```javascript
const calculateQualityScore = (validationResults) => {
  const weights = {
    syntaxValidation: 0.20,        // Critical - must compile
    bestPracticesCompliance: 0.18, // Very important for maintainability
    securityValidation: 0.16,      // Critical for production apps
    performanceValidation: 0.14,   // Important for user experience
    accessibilityValidation: 0.12, // Important for inclusivity
    testCoverage: 0.10,           // Important for reliability
    codeComplexity: 0.06,         // Important for maintainability
    architectureCompliance: 0.04   // Nice to have
  }

  let totalScore = 0
  let totalWeight = 0

  Object.entries(weights).forEach(([category, weight]) => {
    if (validationResults[category]) {
      totalScore += validationResults[category].score * weight
      totalWeight += weight
    }
  })

  return Math.round(totalScore / totalWeight)
}

const identifyCriticalIssues = (validationResults) => {
  const critical = []
  
  Object.values(validationResults).forEach(result => {
    if (result.issues || result.violations || result.vulnerabilities) {
      const issues = result.issues || result.violations || result.vulnerabilities
      const criticalIssues = issues.filter(issue => 
        issue.severity === 'CRITICAL' || issue.severity === 'HIGH'
      )
      critical.push(...criticalIssues)
    }
  })

  return critical
}
```

---

## Quality Improvement Recommendations

### 1. Automated Fix Generation

```javascript
const generateAutomaticFixes = (validationResults) => {
  const fixes = []
  
  Object.entries(validationResults).forEach(([category, results]) => {
    if (results.issues) {
      results.issues.forEach(issue => {
        if (issue.autoFixable) {
          fixes.push({
            file: issue.file,
            line: issue.line,
            type: 'AUTOMATED_FIX',
            description: issue.message,
            fix: issue.fix,
            confidence: issue.fixConfidence || 'HIGH'
          })
        }
      })
    }
  })

  return fixes
}

const generateImprovementPlan = (validationResults) => {
  const plan = {
    immediate: [], // Critical fixes needed now
    shortTerm: [], // Important improvements
    longTerm: []   // Nice-to-have enhancements
  }

  const allIssues = extractAllIssues(validationResults)
  
  allIssues.forEach(issue => {
    const priority = determineIssuePriority(issue)
    plan[priority].push({
      description: issue.message,
      effort: estimateFixEffort(issue),
      impact: estimateFixImpact(issue),
      category: issue.category
    })
  })

  return plan
}
```

---

## Quality Standards Configuration

### 1. Quality Thresholds by App Category

```javascript
const getQualityThresholds = (appCategory) => {
  const thresholds = {
    game: {
      overall: 80,        // Games can be slightly more lenient
      performance: 90,    // But performance is critical
      security: 70,       // Lower security requirements
      accessibility: 75   // Standard accessibility
    },
    
    ecommerce: {
      overall: 90,        // High quality required
      performance: 85,    // Good performance needed
      security: 95,       // Security is critical
      accessibility: 85   // Important for inclusivity
    },
    
    dashboard: {
      overall: 85,        // Professional quality
      performance: 90,    // Performance critical for data
      security: 85,       // Data security important
      accessibility: 90   // Must be accessible
    },
    
    social: {
      overall: 85,        // Social apps need quality
      performance: 80,    // Performance moderately important
      security: 90,       // User data security critical
      accessibility: 85   // Inclusive design important
    }
  }

  return thresholds[appCategory] || {
    overall: 85,
    performance: 80,
    security: 80,
    accessibility: 80
  }
}
```

---

## Integration with Generation Pipeline

### 1. Quality Gates in Generation Process

```javascript
const implementQualityGates = (generationPipeline) => {
  return generationPipeline
    .addPreGenerationValidation(validateSpecificationQuality)
    .addMidGenerationValidation(validateProgressiveQuality)
    .addPostGenerationValidation(validateFinalQuality)
    .addDeploymentGate(validateProductionReadiness)
}

const validateProgressiveQuality = (partialCode, specification) => {
  // Run lightweight quality checks during generation
  const quickValidation = runQuickQualityChecks(partialCode)
  
  if (quickValidation.criticalIssues.length > 0) {
    return {
      shouldPause: true,
      issues: quickValidation.criticalIssues,
      recommendation: 'Fix critical issues before continuing generation'
    }
  }
  
  return { shouldPause: false }
}
```

---

## Documentation Integration (Development & Testing Phase)

**MANDATORY**: Create and maintain development log documentation during code generation:

```bash
# Copy and initialize development log template at start of development
cp instructions/templates/documentation/05_development_log_template.md \
   generated_apps/[app-name]/_agent_session/05_development_log.md
```

**Update throughout development with:**
- Each development phase and implementation decisions made
- Quality validation results and any issues resolved
- Architecture decisions and rationale for code patterns used
- Performance optimizations applied and testing results
- Security implementations and validation outcomes
- Final deliverable assessment and handoff information

This creates complete traceability from specification through final implementation.

---

## Success Criteria

### Code Quality Validation Success:
✅ Identifies and prevents critical code issues
✅ Ensures React best practices are followed
✅ Validates security measures are in place
✅ Confirms accessibility standards are met
✅ Provides actionable improvement recommendations
✅ Integrates seamlessly with generation pipeline
✅ Maintains professional code quality standards
✅ Complete development documentation created

### Quality Indicators:
- Generated code passes all critical quality gates
- Security vulnerabilities are automatically prevented
- Performance optimizations are applied where beneficial
- Accessibility standards are met or exceeded
- Code follows current React best practices
- Architecture adheres to SOLID principles
- Test coverage meets minimum thresholds

This comprehensive quality validation system ensures that all generated React applications meet professional, production-ready standards consistently.