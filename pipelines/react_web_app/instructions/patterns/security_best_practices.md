# Security Best Practices - React Application Security

## Purpose
Ensure all generated React applications follow security best practices to protect users from common vulnerabilities. Security is non-negotiable - these patterns must be applied to every generated app.

---

## Critical Security Principles

### 1. Input Validation and Sanitization
**NEVER trust user input - validate and sanitize everything**

```jsx
// ❌ DANGEROUS: Direct use of user input
const UserComment = ({ comment }) => (
  <div dangerouslySetInnerHTML={{ __html: comment }} />
)

// ❌ DANGEROUS: Unsanitized display
const SearchResults = ({ query }) => (
  <h2>Results for: {query}</h2>  {/* XSS vulnerability */}
)

// ✅ SAFE: Sanitized input with validation
import DOMPurify from 'dompurify'

const UserComment = ({ comment }) => {
  const sanitizedComment = DOMPurify.sanitize(comment, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: []
  })
  
  return (
    <div dangerouslySetInnerHTML={{ __html: sanitizedComment }} />
  )
}

// ✅ SAFE: React automatically escapes
const SearchResults = ({ query }) => (
  <h2>Results for: {query}</h2>  {/* React escapes automatically */}
)
```

### 2. XSS Prevention Patterns

```jsx
// ✅ Input validation hook
const useSecureInput = (initialValue = '', options = {}) => {
  const [value, setValue] = useState(initialValue)
  const [error, setError] = useState('')
  
  const { 
    maxLength = 1000,
    allowedChars = /^[a-zA-Z0-9\s.,!?-]*$/,
    sanitize = true
  } = options
  
  const handleChange = (input) => {
    // Length validation
    if (input.length > maxLength) {
      setError(`Maximum ${maxLength} characters allowed`)
      return
    }
    
    // Character validation
    if (!allowedChars.test(input)) {
      setError('Contains invalid characters')
      return
    }
    
    // Clear error and set value
    setError('')
    setValue(sanitize ? DOMPurify.sanitize(input) : input)
  }
  
  return { value, error, onChange: handleChange, isValid: !error }
}

// Usage
const CommentForm = ({ onSubmit }) => {
  const comment = useSecureInput('', { 
    maxLength: 500,
    allowedChars: /^[a-zA-Z0-9\s.,!?'"()-]*$/
  })
  
  const handleSubmit = (e) => {
    e.preventDefault()
    if (comment.isValid && comment.value.trim()) {
      onSubmit(comment.value)
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <textarea
        value={comment.value}
        onChange={(e) => comment.onChange(e.target.value)}
        placeholder="Enter your comment..."
      />
      {comment.error && <span className="error">{comment.error}</span>}
      <button type="submit" disabled={!comment.isValid}>
        Submit
      </button>
    </form>
  )
}
```

### 3. Secure API Communication

```jsx
// ✅ Secure API service with CSRF protection
class SecureApiService {
  constructor() {
    this.baseURL = process.env.REACT_APP_API_URL
    this.csrfToken = null
  }
  
  async request(endpoint, options = {}) {
    // Get CSRF token if needed
    if (!this.csrfToken && ['POST', 'PUT', 'DELETE'].includes(options.method)) {
      await this.getCsrfToken()
    }
    
    const config = {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        'X-Requested-With': 'XMLHttpRequest',
        ...(this.csrfToken && { 'X-CSRF-Token': this.csrfToken }),
        ...options.headers
      },
      credentials: 'include', // Include cookies for session
    }
    
    try {
      const response = await fetch(`${this.baseURL}${endpoint}`, config)
      
      // Check for auth errors
      if (response.status === 401) {
        this.handleAuthError()
        throw new Error('Authentication required')
      }
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`)
      }
      
      return await response.json()
    } catch (error) {
      console.error('API request failed:', error)
      throw error
    }
  }
  
  async getCsrfToken() {
    try {
      const response = await fetch(`${this.baseURL}/csrf-token`, {
        credentials: 'include'
      })
      const data = await response.json()
      this.csrfToken = data.token
    } catch (error) {
      console.error('Failed to get CSRF token:', error)
    }
  }
  
  handleAuthError() {
    // Clear sensitive data
    localStorage.removeItem('user')
    sessionStorage.clear()
    
    // Redirect to login
    window.location.href = '/login'
  }
}

// Usage in hook
const useSecureApi = () => {
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)
  
  const apiService = useMemo(() => new SecureApiService(), [])
  
  const request = useCallback(async (endpoint, options) => {
    setLoading(true)
    setError(null)
    
    try {
      const data = await apiService.request(endpoint, options)
      return data
    } catch (err) {
      setError(err.message)
      throw err
    } finally {
      setLoading(false)
    }
  }, [apiService])
  
  return { request, loading, error }
}
```

### 4. Secure Data Storage

```jsx
// ✅ Secure local storage utility
class SecureStorage {
  static encrypt(data) {
    // Simple obfuscation (use proper encryption in production)
    return btoa(JSON.stringify(data))
  }
  
  static decrypt(encryptedData) {
    try {
      return JSON.parse(atob(encryptedData))
    } catch {
      return null
    }
  }
  
  static setItem(key, value, options = {}) {
    const { encrypt = false, expiry = null } = options
    
    const item = {
      value: encrypt ? this.encrypt(value) : value,
      encrypted: encrypt,
      timestamp: Date.now(),
      expiry: expiry ? Date.now() + expiry : null
    }
    
    try {
      localStorage.setItem(key, JSON.stringify(item))
    } catch (error) {
      console.error('Storage quota exceeded:', error)
    }
  }
  
  static getItem(key) {
    try {
      const item = JSON.parse(localStorage.getItem(key))
      
      if (!item) return null
      
      // Check expiry
      if (item.expiry && Date.now() > item.expiry) {
        localStorage.removeItem(key)
        return null
      }
      
      // Decrypt if needed
      return item.encrypted ? this.decrypt(item.value) : item.value
    } catch {
      return null
    }
  }
  
  static removeItem(key) {
    localStorage.removeItem(key)
  }
  
  static clear() {
    localStorage.clear()
  }
}

// Secure storage hook
const useSecureStorage = (key, defaultValue = null) => {
  const [value, setValue] = useState(() => 
    SecureStorage.getItem(key) ?? defaultValue
  )
  
  const setStoredValue = useCallback((newValue, options) => {
    setValue(newValue)
    SecureStorage.setItem(key, newValue, options)
  }, [key])
  
  const removeStoredValue = useCallback(() => {
    setValue(defaultValue)
    SecureStorage.removeItem(key)
  }, [key, defaultValue])
  
  return [value, setStoredValue, removeStoredValue]
}
```

---

## Authentication and Authorization

### 1. Secure Authentication Flow

```jsx
// ✅ Secure auth context
const AuthContext = createContext(null)

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null)
  const [loading, setLoading] = useState(true)
  
  // Check auth status on mount
  useEffect(() => {
    checkAuthStatus()
  }, [])
  
  const checkAuthStatus = async () => {
    try {
      const response = await fetch('/api/auth/me', {
        credentials: 'include',
        headers: { 'X-Requested-With': 'XMLHttpRequest' }
      })
      
      if (response.ok) {
        const userData = await response.json()
        setUser(userData)
      }
    } catch (error) {
      console.error('Auth check failed:', error)
    } finally {
      setLoading(false)
    }
  }
  
  const login = async (credentials) => {
    try {
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'X-Requested-With': 'XMLHttpRequest'
        },
        credentials: 'include',
        body: JSON.stringify(credentials)
      })
      
      if (response.ok) {
        const userData = await response.json()
        setUser(userData)
        return { success: true }
      } else {
        const error = await response.json()
        return { success: false, error: error.message }
      }
    } catch (error) {
      return { success: false, error: 'Login failed' }
    }
  }
  
  const logout = async () => {
    try {
      await fetch('/api/auth/logout', {
        method: 'POST',
        credentials: 'include',
        headers: { 'X-Requested-With': 'XMLHttpRequest' }
      })
    } finally {
      setUser(null)
      // Clear sensitive data
      SecureStorage.clear()
    }
  }
  
  const value = {
    user,
    loading,
    login,
    logout,
    isAuthenticated: !!user
  }
  
  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  )
}

// Protected route component
const ProtectedRoute = ({ children, requiredRole = null }) => {
  const { user, loading } = useAuth()
  
  if (loading) return <LoadingSpinner />
  
  if (!user) {
    return <Navigate to="/login" replace />
  }
  
  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/unauthorized" replace />
  }
  
  return children
}
```

### 2. Role-Based Access Control

```jsx
// ✅ Permission-based component rendering
const usePermissions = () => {
  const { user } = useAuth()
  
  const permissions = useMemo(() => {
    if (!user) return new Set()
    
    // Role-based permissions
    const rolePermissions = {
      admin: ['read', 'write', 'delete', 'manage_users'],
      editor: ['read', 'write'],
      viewer: ['read']
    }
    
    return new Set(rolePermissions[user.role] || [])
  }, [user])
  
  const hasPermission = useCallback((permission) => {
    return permissions.has(permission)
  }, [permissions])
  
  const hasAnyPermission = useCallback((permissionList) => {
    return permissionList.some(permission => permissions.has(permission))
  }, [permissions])
  
  return { hasPermission, hasAnyPermission, permissions }
}

// Permission-controlled components
const PermissionGate = ({ permission, children, fallback = null }) => {
  const { hasPermission } = usePermissions()
  
  return hasPermission(permission) ? children : fallback
}

// Usage
const GameControls = () => (
  <div className="game-controls">
    <button>Play Game</button>
    
    <PermissionGate permission="write">
      <button>Edit Game Settings</button>
    </PermissionGate>
    
    <PermissionGate permission="delete">
      <button>Delete Game</button>
    </PermissionGate>
  </div>
)
```

---

## Environment and Configuration Security

### 1. Secure Environment Variables

```jsx
// ✅ Environment configuration with validation
class Config {
  static get apiUrl() {
    const url = process.env.REACT_APP_API_URL
    if (!url) {
      throw new Error('REACT_APP_API_URL is required')
    }
    return url
  }
  
  static get isDevelopment() {
    return process.env.NODE_ENV === 'development'
  }
  
  static get isProduction() {
    return process.env.NODE_ENV === 'production'
  }
  
  static get enableDebugLogs() {
    return this.isDevelopment && process.env.REACT_APP_DEBUG_LOGS === 'true'
  }
  
  // Never expose sensitive config in client
  static validateConfig() {
    const required = ['REACT_APP_API_URL']
    
    required.forEach(key => {
      if (!process.env[key]) {
        throw new Error(`Missing required environment variable: ${key}`)
      }
    })
    
    // Warn about development-only features in production
    if (this.isProduction && this.enableDebugLogs) {
      console.warn('Debug logs should not be enabled in production')
    }
  }
}

// Validate on app start
Config.validateConfig()
```

### 2. Content Security Policy Headers

```jsx
// ✅ CSP configuration (to be set by server)
const CSP_DIRECTIVES = {
  'default-src': ["'self'"],
  'script-src': ["'self'", "'unsafe-inline'"], // Be more restrictive in production
  'style-src': ["'self'", "'unsafe-inline'", 'https://fonts.googleapis.com'],
  'font-src': ["'self'", 'https://fonts.gstatic.com'],
  'img-src': ["'self'", 'data:', 'https:'],
  'connect-src': ["'self'", process.env.REACT_APP_API_URL],
  'frame-ancestors': ["'none'"],
  'base-uri': ["'self'"],
  'form-action': ["'self'"]
}

// CSP violation reporting
const handleCSPViolation = (violationEvent) => {
  console.error('CSP Violation:', violationEvent.violatedDirective)
  
  // Report to monitoring service in production
  if (Config.isProduction) {
    fetch('/api/csp-violations', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        directive: violationEvent.violatedDirective,
        blockedURI: violationEvent.blockedURI,
        lineNumber: violationEvent.lineNumber,
        sourceFile: violationEvent.sourceFile,
        timestamp: new Date().toISOString()
      })
    }).catch(console.error)
  }
}

// Set up CSP violation listener
document.addEventListener('securitypolicyviolation', handleCSPViolation)
```

---

## Security Utilities

### 1. Safe Component Rendering

```jsx
// ✅ Safe dynamic content renderer
const SafeRenderer = ({ content, allowedTags = ['b', 'i', 'strong', 'em'] }) => {
  const sanitizedContent = useMemo(() => {
    return DOMPurify.sanitize(content, {
      ALLOWED_TAGS: allowedTags,
      ALLOWED_ATTR: ['href', 'target'],
      ALLOWED_URI_REGEXP: /^(?:(?:https?|mailto|tel):|[^a-z]|[a-z+.\-]+(?:[^a-z+.\-:]|$))/i
    })
  }, [content, allowedTags])
  
  return (
    <div 
      dangerouslySetInnerHTML={{ __html: sanitizedContent }}
      className="safe-content"
    />
  )
}
```

### 2. Rate Limiting Hook

```jsx
// ✅ Client-side rate limiting
const useRateLimit = (maxRequests = 5, windowMs = 60000) => {
  const requests = useRef([])
  
  const isAllowed = useCallback(() => {
    const now = Date.now()
    
    // Remove old requests outside the window
    requests.current = requests.current.filter(
      timestamp => now - timestamp < windowMs
    )
    
    // Check if we're under the limit
    if (requests.current.length < maxRequests) {
      requests.current.push(now)
      return true
    }
    
    return false
  }, [maxRequests, windowMs])
  
  return { isAllowed }
}

// Usage
const ContactForm = () => {
  const { isAllowed } = useRateLimit(3, 300000) // 3 requests per 5 minutes
  const [message, setMessage] = useState('')
  
  const handleSubmit = async (e) => {
    e.preventDefault()
    
    if (!isAllowed()) {
      alert('Too many requests. Please wait before submitting again.')
      return
    }
    
    // Process form submission
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <textarea 
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        maxLength={1000}
      />
      <button type="submit">Send Message</button>
    </form>
  )
}
```

---

## Security Checklist for Generated Code

### Always Include:
- [ ] Input validation and sanitization on all user inputs
- [ ] XSS protection using React's built-in escaping or DOMPurify
- [ ] CSRF protection for API requests
- [ ] Secure storage practices (no sensitive data in localStorage)
- [ ] Authentication and authorization checks
- [ ] Environment variable validation
- [ ] Error handling that doesn't leak sensitive information
- [ ] Rate limiting on user actions
- [ ] Content Security Policy considerations
- [ ] Secure API communication patterns

### Never Do:
- [ ] Use dangerouslySetInnerHTML without sanitization
- [ ] Store passwords or tokens in localStorage
- [ ] Expose API keys in client code
- [ ] Trust user input without validation
- [ ] Log sensitive information to console
- [ ] Use eval() or Function() with user data
- [ ] Allow unlimited requests without rate limiting

These security patterns must be integrated into every generated application to ensure user safety and data protection.