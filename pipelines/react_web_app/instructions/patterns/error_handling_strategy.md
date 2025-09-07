# Comprehensive Error Handling Strategy

## Purpose
Implement robust error handling throughout React applications to prevent crashes, provide user-friendly feedback, and maintain application stability. Every generated app must handle errors gracefully.

---

## Error Handling Hierarchy

### 1. Error Boundaries (Application Level)
**Catch and handle JavaScript errors anywhere in the component tree**

```jsx
// ✅ Main App Error Boundary
class AppErrorBoundary extends Component {
  constructor(props) {
    super(props)
    this.state = {
      hasError: false,
      error: null,
      errorInfo: null,
      errorId: null
    }
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI
    return {
      hasError: true,
      errorId: `error_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`
    }
  }

  componentDidCatch(error, errorInfo) {
    // Log error details
    this.setState({
      error,
      errorInfo
    })

    // Report to monitoring service
    this.reportError(error, errorInfo)
  }

  reportError = (error, errorInfo) => {
    const errorReport = {
      id: this.state.errorId,
      message: error.message,
      stack: error.stack,
      componentStack: errorInfo.componentStack,
      timestamp: new Date().toISOString(),
      url: window.location.href,
      userAgent: navigator.userAgent
    }

    // Log to console in development
    if (process.env.NODE_ENV === 'development') {
      console.group('🚨 Application Error')
      console.error('Error:', error)
      console.error('Error Info:', errorInfo)
      console.error('Full Report:', errorReport)
      console.groupEnd()
    }

    // Send to error reporting service in production
    if (process.env.NODE_ENV === 'production') {
      this.sendErrorReport(errorReport)
    }
  }

  sendErrorReport = async (errorReport) => {
    try {
      await fetch('/api/errors', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(errorReport)
      })
    } catch (reportingError) {
      console.error('Failed to report error:', reportingError)
    }
  }

  handleRetry = () => {
    this.setState({
      hasError: false,
      error: null,
      errorInfo: null,
      errorId: null
    })
  }

  handleReload = () => {
    window.location.reload()
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary-fallback">
          <div className="error-container">
            <h1>😕 Something went wrong</h1>
            <p>We're sorry, but something unexpected happened.</p>
            
            <div className="error-actions">
              <button onClick={this.handleRetry} className="btn-primary">
                Try Again
              </button>
              <button onClick={this.handleReload} className="btn-secondary">
                Reload Page
              </button>
            </div>

            {process.env.NODE_ENV === 'development' && (
              <details className="error-details">
                <summary>Error Details (Development)</summary>
                <pre>{this.state.error && this.state.error.toString()}</pre>
                <pre>{this.state.errorInfo.componentStack}</pre>
              </details>
            )}
            
            <p className="error-id">Error ID: {this.state.errorId}</p>
          </div>
        </div>
      )
    }

    return this.props.children
  }
}

// Feature-specific Error Boundaries
const GameErrorBoundary = ({ children }) => (
  <ErrorBoundary
    fallback={
      <div className="game-error">
        <h3>Game Error</h3>
        <p>Something went wrong with the game. Please refresh to continue playing.</p>
        <button onClick={() => window.location.reload()}>
          Restart Game
        </button>
      </div>
    }
  >
    {children}
  </ErrorBoundary>
)
```

### 2. Hook-Based Error Handling
**Standardized error handling for components**

```jsx
// ✅ Comprehensive error handling hook
const useErrorHandler = () => {
  const [error, setError] = useState(null)
  const [loading, setLoading] = useState(false)

  const handleAsync = useCallback(async (asyncFunction, options = {}) => {
    const {
      showLoading = true,
      retries = 3,
      retryDelay = 1000,
      onError = null,
      onSuccess = null
    } = options

    setError(null)
    if (showLoading) setLoading(true)

    let lastError
    for (let attempt = 1; attempt <= retries; attempt++) {
      try {
        const result = await asyncFunction()
        
        if (showLoading) setLoading(false)
        if (onSuccess) onSuccess(result)
        
        return result
      } catch (err) {
        lastError = err
        
        if (attempt < retries) {
          // Wait before retrying
          await new Promise(resolve => setTimeout(resolve, retryDelay * attempt))
        }
      }
    }

    // All retries failed
    const errorInfo = {
      message: lastError.message,
      type: lastError.constructor.name,
      timestamp: new Date().toISOString(),
      attempts: retries
    }

    setError(errorInfo)
    if (showLoading) setLoading(false)
    if (onError) onError(lastError)

    throw lastError
  }, [])

  const clearError = useCallback(() => {
    setError(null)
  }, [])

  const throwError = useCallback((errorMessage, errorType = 'UserError') => {
    const error = new Error(errorMessage)
    error.name = errorType
    throw error
  }, [])

  return {
    error,
    loading,
    handleAsync,
    clearError,
    throwError,
    hasError: !!error
  }
}

// ✅ API-specific error handling
const useApiError = () => {
  const { handleAsync } = useErrorHandler()

  const handleApiCall = useCallback(async (apiCall, options = {}) => {
    return handleAsync(async () => {
      try {
        return await apiCall()
      } catch (error) {
        // Transform API errors into user-friendly messages
        const userMessage = transformApiError(error)
        throw new Error(userMessage)
      }
    }, options)
  }, [handleAsync])

  return { handleApiCall }
}

const transformApiError = (error) => {
  if (!error.response) {
    return 'Unable to connect to the server. Please check your internet connection.'
  }

  const { status, data } = error.response

  switch (status) {
    case 400:
      return data.message || 'Invalid request. Please check your input.'
    case 401:
      return 'Please log in to continue.'
    case 403:
      return 'You do not have permission to perform this action.'
    case 404:
      return 'The requested resource was not found.'
    case 429:
      return 'Too many requests. Please wait a moment and try again.'
    case 500:
      return 'Server error. Please try again later.'
    case 503:
      return 'Service temporarily unavailable. Please try again later.'
    default:
      return data.message || 'An unexpected error occurred. Please try again.'
  }
}
```

### 3. Form Error Handling
**Comprehensive form validation and error display**

```jsx
// ✅ Form error handling hook
const useFormErrors = (validationRules = {}) => {
  const [errors, setErrors] = useState({})
  const [touched, setTouched] = useState({})

  const validateField = useCallback((name, value) => {
    const rules = validationRules[name]
    if (!rules) return null

    for (const rule of rules) {
      const error = rule(value)
      if (error) return error
    }
    return null
  }, [validationRules])

  const validateForm = useCallback((values) => {
    const newErrors = {}
    
    Object.keys(validationRules).forEach(field => {
      const error = validateField(field, values[field])
      if (error) newErrors[field] = error
    })

    setErrors(newErrors)
    return Object.keys(newErrors).length === 0
  }, [validateField, validationRules])

  const setFieldError = useCallback((field, error) => {
    setErrors(prev => ({ ...prev, [field]: error }))
  }, [])

  const clearFieldError = useCallback((field) => {
    setErrors(prev => {
      const newErrors = { ...prev }
      delete newErrors[field]
      return newErrors
    })
  }, [])

  const setFieldTouched = useCallback((field, isTouched = true) => {
    setTouched(prev => ({ ...prev, [field]: isTouched }))
  }, [])

  const getFieldError = useCallback((field) => {
    return touched[field] ? errors[field] : null
  }, [errors, touched])

  return {
    errors,
    touched,
    validateForm,
    validateField,
    setFieldError,
    clearFieldError,
    setFieldTouched,
    getFieldError,
    hasErrors: Object.keys(errors).length > 0
  }
}

// ✅ Example usage in a form component
const ContactForm = () => {
  const [values, setValues] = useState({ name: '', email: '', message: '' })
  const { handleApiCall, error: submitError } = useApiError()
  const [isSubmitting, setIsSubmitting] = useState(false)

  const validationRules = {
    name: [
      (value) => !value?.trim() ? 'Name is required' : null,
      (value) => value?.length < 2 ? 'Name must be at least 2 characters' : null
    ],
    email: [
      (value) => !value?.trim() ? 'Email is required' : null,
      (value) => !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? 'Invalid email format' : null
    ],
    message: [
      (value) => !value?.trim() ? 'Message is required' : null,
      (value) => value?.length < 10 ? 'Message must be at least 10 characters' : null
    ]
  }

  const {
    validateForm,
    getFieldError,
    setFieldTouched,
    hasErrors
  } = useFormErrors(validationRules)

  const handleSubmit = async (e) => {
    e.preventDefault()
    
    // Mark all fields as touched for validation display
    Object.keys(values).forEach(field => setFieldTouched(field, true))
    
    if (!validateForm(values)) {
      return
    }

    setIsSubmitting(true)
    
    try {
      await handleApiCall(() => 
        fetch('/api/contact', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(values)
        })
      )
      
      // Success - reset form
      setValues({ name: '', email: '', message: '' })
      alert('Message sent successfully!')
      
    } catch (error) {
      // Error handled by useApiError
    } finally {
      setIsSubmitting(false)
    }
  }

  const handleChange = (field) => (e) => {
    setValues(prev => ({ ...prev, [field]: e.target.value }))
    setFieldTouched(field, true)
  }

  return (
    <form onSubmit={handleSubmit} className="contact-form">
      <div className="form-field">
        <label htmlFor="name">Name</label>
        <input
          id="name"
          type="text"
          value={values.name}
          onChange={handleChange('name')}
          className={getFieldError('name') ? 'error' : ''}
        />
        {getFieldError('name') && (
          <span className="field-error">{getFieldError('name')}</span>
        )}
      </div>

      <div className="form-field">
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={values.email}
          onChange={handleChange('email')}
          className={getFieldError('email') ? 'error' : ''}
        />
        {getFieldError('email') && (
          <span className="field-error">{getFieldError('email')}</span>
        )}
      </div>

      <div className="form-field">
        <label htmlFor="message">Message</label>
        <textarea
          id="message"
          value={values.message}
          onChange={handleChange('message')}
          className={getFieldError('message') ? 'error' : ''}
        />
        {getFieldError('message') && (
          <span className="field-error">{getFieldError('message')}</span>
        )}
      </div>

      {submitError && (
        <div className="form-error">
          <p>{submitError.message}</p>
        </div>
      )}

      <button 
        type="submit" 
        disabled={isSubmitting || hasErrors}
        className="submit-button"
      >
        {isSubmitting ? 'Sending...' : 'Send Message'}
      </button>
    </form>
  )
}
```

---

## Loading and Network States

### 1. Loading State Management

```jsx
// ✅ Loading state hook with timeout
const useLoadingState = (initialLoading = false) => {
  const [loading, setLoading] = useState(initialLoading)
  const [timeoutId, setTimeoutId] = useState(null)

  const startLoading = useCallback((timeout = 30000) => {
    setLoading(true)
    
    // Set timeout to prevent infinite loading
    const id = setTimeout(() => {
      setLoading(false)
      console.warn('Loading timeout reached')
    }, timeout)
    
    setTimeoutId(id)
  }, [])

  const stopLoading = useCallback(() => {
    setLoading(false)
    if (timeoutId) {
      clearTimeout(timeoutId)
      setTimeoutId(null)
    }
  }, [timeoutId])

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      if (timeoutId) clearTimeout(timeoutId)
    }
  }, [timeoutId])

  return { loading, startLoading, stopLoading }
}

// ✅ Smart loading component
const LoadingSpinner = ({ 
  message = "Loading...", 
  size = "medium",
  timeout = 10000 
}) => {
  const [showSlowWarning, setShowSlowWarning] = useState(false)

  useEffect(() => {
    const timer = setTimeout(() => {
      setShowSlowWarning(true)
    }, timeout)

    return () => clearTimeout(timer)
  }, [timeout])

  return (
    <div className={`loading-spinner loading-spinner--${size}`}>
      <div className="spinner" />
      <p className="loading-message">{message}</p>
      {showSlowWarning && (
        <p className="slow-warning">
          This is taking longer than expected. Please check your connection.
        </p>
      )}
    </div>
  )
}
```

### 2. Network Status Handling

```jsx
// ✅ Network status hook
const useNetworkStatus = () => {
  const [isOnline, setIsOnline] = useState(navigator.onLine)
  const [wasOffline, setWasOffline] = useState(false)

  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true)
      if (wasOffline) {
        // Show reconnection message
        setTimeout(() => setWasOffline(false), 3000)
      }
    }

    const handleOffline = () => {
      setIsOnline(false)
      setWasOffline(true)
    }

    window.addEventListener('online', handleOnline)
    window.addEventListener('offline', handleOffline)

    return () => {
      window.removeEventListener('online', handleOnline)
      window.removeEventListener('offline', handleOffline)
    }
  }, [wasOffline])

  return { isOnline, wasOffline }
}

// ✅ Offline indicator component
const NetworkStatus = () => {
  const { isOnline, wasOffline } = useNetworkStatus()

  if (isOnline && !wasOffline) return null

  return (
    <div className={`network-status ${isOnline ? 'online' : 'offline'}`}>
      {isOnline ? (
        <div className="reconnected">
          ✅ Back online
        </div>
      ) : (
        <div className="disconnected">
          ⚠️ No internet connection
        </div>
      )}
    </div>
  )
}
```

---

## User-Friendly Error Components

### 1. Error Display Components

```jsx
// ✅ Contextual error messages
const ErrorMessage = ({ 
  error, 
  title = "Something went wrong",
  onRetry = null,
  onDismiss = null,
  variant = "error" // error, warning, info
}) => {
  const errorMessage = typeof error === 'string' ? error : error?.message || 'Unknown error'
  
  return (
    <div className={`error-message error-message--${variant}`} role="alert">
      <div className="error-content">
        <h4 className="error-title">{title}</h4>
        <p className="error-description">{errorMessage}</p>
        
        <div className="error-actions">
          {onRetry && (
            <button onClick={onRetry} className="btn btn--primary">
              Try Again
            </button>
          )}
          {onDismiss && (
            <button onClick={onDismiss} className="btn btn--secondary">
              Dismiss
            </button>
          )}
        </div>
      </div>
    </div>
  )
}

// ✅ Empty state component
const EmptyState = ({ 
  title = "No data found",
  description = "There's nothing to show here yet.",
  action = null,
  illustration = "📭"
}) => (
  <div className="empty-state">
    <div className="empty-illustration">{illustration}</div>
    <h3 className="empty-title">{title}</h3>
    <p className="empty-description">{description}</p>
    {action && <div className="empty-action">{action}</div>}
  </div>
)

// ✅ Retry component
const RetryAction = ({ 
  onRetry, 
  message = "Something went wrong",
  retryText = "Try Again",
  isRetrying = false 
}) => (
  <div className="retry-action">
    <p className="retry-message">{message}</p>
    <button 
      onClick={onRetry}
      disabled={isRetrying}
      className="retry-button"
    >
      {isRetrying ? 'Retrying...' : retryText}
    </button>
  </div>
)
```

---

## Error Handling Standards

### Code Generation Guidelines:

#### Always Include:
1. **Error boundaries** around major feature areas
2. **Loading states** for all async operations
3. **Network status** indicators
4. **Form validation** with user-friendly messages
5. **Retry mechanisms** for failed operations
6. **Error reporting** (development logging, production monitoring)
7. **Graceful degradation** when features fail
8. **Timeout handling** for long-running operations

#### Error Message Guidelines:
- **Be specific but user-friendly**: "Failed to save your game progress" not "HTTP 500 error"
- **Provide actionable solutions**: "Check your internet connection and try again"
- **Avoid technical jargon**: "Something went wrong" not "Uncaught TypeError"
- **Include retry options**: Always give users a way to recover
- **Show loading states**: Never leave users wondering if something is happening

#### Development vs Production:
- **Development**: Show detailed error information, stack traces, component names
- **Production**: Show user-friendly messages, log technical details privately

This comprehensive error handling strategy ensures that generated applications are resilient, user-friendly, and maintainable even when things go wrong.