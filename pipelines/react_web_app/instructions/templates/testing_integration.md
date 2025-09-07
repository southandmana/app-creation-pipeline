# Testing Integration Templates

## Purpose
Provide comprehensive testing templates and patterns for generated React applications. Every generated app must include appropriate testing infrastructure and test cases.

---

## Testing Strategy Framework

### 1. Testing Pyramid Implementation

```javascript
// Testing distribution for generated apps
const testingDistribution = {
  unit: 70,        // 70% unit tests - fast, isolated
  integration: 20, // 20% integration tests - component interactions  
  e2e: 10         // 10% end-to-end tests - user workflows
}

const getTestingStrategy = (appCategory, complexity) => {
  const baseStrategy = {
    unit: ['components', 'hooks', 'utilities', 'services'],
    integration: ['user-flows', 'api-integration', 'state-management'],
    e2e: ['critical-paths', 'user-journeys']
  }

  // Adjust based on app category
  const categoryAdjustments = {
    game: {
      unit: [...baseStrategy.unit, 'game-logic', 'ai-algorithms'],
      integration: [...baseStrategy.integration, 'game-state', 'multiplayer'],
      e2e: ['game-completion', 'score-tracking']
    },
    ecommerce: {
      unit: [...baseStrategy.unit, 'cart-logic', 'price-calculations'],
      integration: [...baseStrategy.integration, 'payment-flow', 'inventory'],
      e2e: ['purchase-journey', 'user-registration']
    },
    dashboard: {
      unit: [...baseStrategy.unit, 'data-processing', 'chart-logic'],
      integration: [...baseStrategy.integration, 'data-visualization', 'filters'],
      e2e: ['data-loading', 'export-functionality']
    }
  }

  return categoryAdjustments[appCategory] || baseStrategy
}
```

### 2. Test Framework Selection

```javascript
const selectTestingFramework = (systemTier, requirements) => {
  // Modern, fast testing setup
  if (systemTier === 'highEnd' || requirements.performanceTesting) {
    return {
      testRunner: 'vitest',
      reason: 'Fastest test runner, excellent Vite integration',
      testingLibrary: '@testing-library/react',
      e2eTool: 'playwright',
      coverage: 'c8'
    }
  }

  // Balanced, widely-supported setup
  if (systemTier === 'midRange') {
    return {
      testRunner: 'jest',
      reason: 'Mature, well-supported, extensive ecosystem',
      testingLibrary: '@testing-library/react',
      e2eTool: 'cypress',
      coverage: 'jest coverage'
    }
  }

  // Lightweight setup for budget systems
  return {
    testRunner: 'jest',
    reason: 'Standard React testing, minimal setup',
    testingLibrary: '@testing-library/react',
    e2eTool: 'basic-playwright',
    coverage: 'jest coverage'
  }
}
```

---

## Component Testing Templates

### 1. Basic Component Test Template

```jsx
// Template: Component Test
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { ComponentName } from './ComponentName'

describe('ComponentName', () => {
  // Test props and default values
  const defaultProps = {
    // Define default props for testing
  }

  // Helper function to render component with props
  const renderComponent = (props = {}) => {
    return render(<ComponentName {...defaultProps} {...props} />)
  }

  describe('Rendering', () => {
    it('renders without crashing', () => {
      renderComponent()
      expect(screen.getByRole('...')).toBeInTheDocument()
    })

    it('renders with required props', () => {
      const requiredProps = {
        // Define required props
      }
      renderComponent(requiredProps)
      expect(screen.getByText('...')).toBeInTheDocument()
    })

    it('applies custom className when provided', () => {
      const customClass = 'custom-class'
      renderComponent({ className: customClass })
      expect(screen.getByRole('...')).toHaveClass(customClass)
    })
  })

  describe('Interactions', () => {
    it('calls onClick handler when clicked', async () => {
      const user = userEvent.setup()
      const mockOnClick = jest.fn()
      
      renderComponent({ onClick: mockOnClick })
      
      await user.click(screen.getByRole('button'))
      expect(mockOnClick).toHaveBeenCalledTimes(1)
    })

    it('handles keyboard navigation', async () => {
      const user = userEvent.setup()
      renderComponent()
      
      const element = screen.getByRole('button')
      await user.tab()
      expect(element).toHaveFocus()
      
      await user.keyboard('{Enter}')
      // Assert expected behavior
    })
  })

  describe('Accessibility', () => {
    it('has proper ARIA attributes', () => {
      renderComponent()
      const element = screen.getByRole('...')
      expect(element).toHaveAttribute('aria-label')
    })

    it('supports screen readers', () => {
      renderComponent()
      expect(screen.getByLabelText('...')).toBeInTheDocument()
    })
  })

  describe('Error Handling', () => {
    it('handles missing required props gracefully', () => {
      // Should not crash and show appropriate fallback
      const { container } = render(<ComponentName />)
      expect(container).toBeInTheDocument()
    })

    it('displays error state when provided', () => {
      renderComponent({ error: 'Test error message' })
      expect(screen.getByRole('alert')).toHaveTextContent('Test error message')
    })
  })
})
```

### 2. Form Component Test Template

```jsx
// Template: Form Component Test
import { render, screen, fireEvent, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { FormComponent } from './FormComponent'

describe('FormComponent', () => {
  const mockOnSubmit = jest.fn()

  beforeEach(() => {
    mockOnSubmit.mockClear()
  })

  const renderForm = (props = {}) => {
    return render(<FormComponent onSubmit={mockOnSubmit} {...props} />)
  }

  describe('Form Rendering', () => {
    it('renders all form fields', () => {
      renderForm()
      expect(screen.getByLabelText(/name/i)).toBeInTheDocument()
      expect(screen.getByLabelText(/email/i)).toBeInTheDocument()
      expect(screen.getByRole('button', { name: /submit/i })).toBeInTheDocument()
    })
  })

  describe('Form Validation', () => {
    it('shows validation errors for empty required fields', async () => {
      const user = userEvent.setup()
      renderForm()
      
      const submitButton = screen.getByRole('button', { name: /submit/i })
      await user.click(submitButton)
      
      expect(screen.getByText(/name is required/i)).toBeInTheDocument()
      expect(screen.getByText(/email is required/i)).toBeInTheDocument()
      expect(mockOnSubmit).not.toHaveBeenCalled()
    })

    it('validates email format', async () => {
      const user = userEvent.setup()
      renderForm()
      
      await user.type(screen.getByLabelText(/email/i), 'invalid-email')
      await user.click(screen.getByRole('button', { name: /submit/i }))
      
      expect(screen.getByText(/invalid email format/i)).toBeInTheDocument()
    })

    it('clears validation errors when field becomes valid', async () => {
      const user = userEvent.setup()
      renderForm()
      
      const emailField = screen.getByLabelText(/email/i)
      
      // Trigger validation error
      await user.type(emailField, 'invalid')
      await user.tab()
      expect(screen.getByText(/invalid email/i)).toBeInTheDocument()
      
      // Fix the error
      await user.clear(emailField)
      await user.type(emailField, 'valid@example.com')
      await user.tab()
      
      expect(screen.queryByText(/invalid email/i)).not.toBeInTheDocument()
    })
  })

  describe('Form Submission', () => {
    it('submits form with valid data', async () => {
      const user = userEvent.setup()
      renderForm()
      
      await user.type(screen.getByLabelText(/name/i), 'John Doe')
      await user.type(screen.getByLabelText(/email/i), 'john@example.com')
      await user.click(screen.getByRole('button', { name: /submit/i }))
      
      await waitFor(() => {
        expect(mockOnSubmit).toHaveBeenCalledWith({
          name: 'John Doe',
          email: 'john@example.com'
        })
      })
    })

    it('shows loading state during submission', async () => {
      const user = userEvent.setup()
      const slowSubmit = jest.fn(() => new Promise(resolve => setTimeout(resolve, 100)))
      
      renderForm({ onSubmit: slowSubmit })
      
      await user.type(screen.getByLabelText(/name/i), 'John Doe')
      await user.type(screen.getByLabelText(/email/i), 'john@example.com')
      await user.click(screen.getByRole('button', { name: /submit/i }))
      
      expect(screen.getByText(/submitting/i)).toBeInTheDocument()
      expect(screen.getByRole('button', { name: /submit/i })).toBeDisabled()
    })
  })
})
```

---

## Hook Testing Templates

### 1. Custom Hook Test Template

```jsx
// Template: Custom Hook Test
import { renderHook, act } from '@testing-library/react'
import { useCustomHook } from './useCustomHook'

describe('useCustomHook', () => {
  describe('Initial State', () => {
    it('returns initial values correctly', () => {
      const { result } = renderHook(() => useCustomHook())
      
      expect(result.current.value).toBe(null)
      expect(result.current.loading).toBe(false)
      expect(result.current.error).toBe(null)
      expect(typeof result.current.setValue).toBe('function')
    })

    it('accepts initial value parameter', () => {
      const initialValue = 'test initial'
      const { result } = renderHook(() => useCustomHook(initialValue))
      
      expect(result.current.value).toBe(initialValue)
    })
  })

  describe('State Updates', () => {
    it('updates value when setValue is called', () => {
      const { result } = renderHook(() => useCustomHook())
      
      act(() => {
        result.current.setValue('new value')
      })
      
      expect(result.current.value).toBe('new value')
    })

    it('handles loading state correctly', async () => {
      const { result } = renderHook(() => useCustomHook())
      
      act(() => {
        result.current.performAsyncAction()
      })
      
      expect(result.current.loading).toBe(true)
      
      await act(async () => {
        await new Promise(resolve => setTimeout(resolve, 0))
      })
      
      expect(result.current.loading).toBe(false)
    })
  })

  describe('Error Handling', () => {
    it('sets error state when operation fails', async () => {
      const { result } = renderHook(() => useCustomHook())
      
      await act(async () => {
        try {
          await result.current.performFailingAction()
        } catch (error) {
          // Expected to fail
        }
      })
      
      expect(result.current.error).toBeTruthy()
      expect(result.current.loading).toBe(false)
    })

    it('clears error when successful operation is performed', async () => {
      const { result } = renderHook(() => useCustomHook())
      
      // First, cause an error
      await act(async () => {
        try {
          await result.current.performFailingAction()
        } catch (error) {
          // Expected
        }
      })
      
      expect(result.current.error).toBeTruthy()
      
      // Then perform successful operation
      act(() => {
        result.current.clearError()
      })
      
      expect(result.current.error).toBe(null)
    })
  })
})
```

---

## Integration Testing Templates

### 1. API Integration Test Template

```jsx
// Template: API Integration Test
import { render, screen, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { rest } from 'msw'
import { setupServer } from 'msw/node'
import { ComponentWithAPI } from './ComponentWithAPI'

// Mock API server
const server = setupServer(
  rest.get('/api/data', (req, res, ctx) => {
    return res(
      ctx.json({
        data: [
          { id: 1, name: 'Test Item 1' },
          { id: 2, name: 'Test Item 2' }
        ]
      })
    )
  }),

  rest.post('/api/data', (req, res, ctx) => {
    return res(
      ctx.json({ id: 3, name: req.body.name, success: true })
    )
  }),

  rest.get('/api/data/error', (req, res, ctx) => {
    return res(ctx.status(500), ctx.json({ error: 'Server Error' }))
  })
)

describe('ComponentWithAPI Integration', () => {
  beforeAll(() => server.listen())
  afterEach(() => server.resetHandlers())
  afterAll(() => server.close())

  describe('Data Loading', () => {
    it('loads and displays data from API', async () => {
      render(<ComponentWithAPI />)
      
      expect(screen.getByText(/loading/i)).toBeInTheDocument()
      
      await waitFor(() => {
        expect(screen.getByText('Test Item 1')).toBeInTheDocument()
        expect(screen.getByText('Test Item 2')).toBeInTheDocument()
      })
      
      expect(screen.queryByText(/loading/i)).not.toBeInTheDocument()
    })

    it('handles API errors gracefully', async () => {
      // Override the handler to return an error
      server.use(
        rest.get('/api/data', (req, res, ctx) => {
          return res(ctx.status(500), ctx.json({ error: 'Server Error' }))
        })
      )
      
      render(<ComponentWithAPI />)
      
      await waitFor(() => {
        expect(screen.getByText(/error loading data/i)).toBeInTheDocument()
      })
    })
  })

  describe('Data Submission', () => {
    it('submits new data and updates UI', async () => {
      const user = userEvent.setup()
      render(<ComponentWithAPI />)
      
      // Wait for initial load
      await waitFor(() => {
        expect(screen.getByText('Test Item 1')).toBeInTheDocument()
      })
      
      // Submit new item
      await user.type(screen.getByLabelText(/name/i), 'New Item')
      await user.click(screen.getByRole('button', { name: /add/i }))
      
      await waitFor(() => {
        expect(screen.getByText('New Item')).toBeInTheDocument()
      })
    })

    it('shows loading state during submission', async () => {
      const user = userEvent.setup()
      
      // Add delay to POST request
      server.use(
        rest.post('/api/data', async (req, res, ctx) => {
          await new Promise(resolve => setTimeout(resolve, 100))
          return res(ctx.json({ id: 3, name: req.body.name }))
        })
      )
      
      render(<ComponentWithAPI />)
      
      await user.type(screen.getByLabelText(/name/i), 'New Item')
      await user.click(screen.getByRole('button', { name: /add/i }))
      
      expect(screen.getByText(/adding/i)).toBeInTheDocument()
    })
  })
})
```

---

## End-to-End Testing Templates

### 1. Critical User Journey Test

```javascript
// Template: E2E Test (Playwright)
import { test, expect } from '@playwright/test'

test.describe('Critical User Journey - Game App', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/')
  })

  test('User can play a complete game', async ({ page }) => {
    // Start new game
    await page.click('[data-testid="new-game-button"]')
    await expect(page.locator('[data-testid="game-board"]')).toBeVisible()
    
    // Make first move
    await page.click('[data-testid="column-0"]')
    await expect(page.locator('[data-testid="disc-red"]')).toBeVisible()
    
    // Verify turn switched
    await expect(page.locator('[data-testid="current-player"]')).toContainText('Yellow')
    
    // AI makes move
    await expect(page.locator('[data-testid="disc-yellow"]')).toBeVisible({ timeout: 3000 })
    
    // Verify game state is maintained
    await expect(page.locator('[data-testid="current-player"]')).toContainText('Red')
  })

  test('User can reset game mid-play', async ({ page }) => {
    // Start and play partial game
    await page.click('[data-testid="new-game-button"]')
    await page.click('[data-testid="column-0"]')
    await page.click('[data-testid="column-1"]')
    
    // Reset game
    await page.click('[data-testid="reset-button"]')
    
    // Verify game is reset
    await expect(page.locator('[data-testid="disc"]')).toHaveCount(0)
    await expect(page.locator('[data-testid="current-player"]')).toContainText('Red')
  })

  test('Game handles victory correctly', async ({ page }) => {
    // This would need specific moves to create a win condition
    // Implementation depends on game logic
    
    await page.click('[data-testid="new-game-button"]')
    
    // Make winning moves (example sequence)
    const winningMoves = [
      'column-0', 'column-1', 'column-0', 'column-1',
      'column-0', 'column-1', 'column-0'
    ]
    
    for (const move of winningMoves) {
      await page.click(`[data-testid="${move}"]`)
      await page.waitForTimeout(500) // Wait for animations
    }
    
    // Verify victory screen
    await expect(page.locator('[data-testid="victory-modal"]')).toBeVisible()
    await expect(page.locator('[data-testid="winner-announcement"]')).toContainText('Red Wins!')
  })

  test('Game saves and restores score', async ({ page }) => {
    // Play and win a game
    await page.click('[data-testid="new-game-button"]')
    // ... play game to victory
    
    // Check score updated
    await expect(page.locator('[data-testid="red-score"]')).toContainText('1')
    
    // Refresh page
    await page.reload()
    
    // Verify score persisted
    await expect(page.locator('[data-testid="red-score"]')).toContainText('1')
  })
})

// Template: E2E Test (Cypress)
describe('Critical User Journey - Dashboard App', () => {
  beforeEach(() => {
    cy.visit('/')
  })

  it('User can view and interact with dashboard data', () => {
    // Wait for data to load
    cy.get('[data-testid="loading-spinner"]').should('be.visible')
    cy.get('[data-testid="loading-spinner"]').should('not.exist')
    
    // Verify data is displayed
    cy.get('[data-testid="chart-container"]').should('be.visible')
    cy.get('[data-testid="data-table"]').should('contain', 'Data')
    
    // Test filter functionality
    cy.get('[data-testid="filter-select"]').select('Last 7 days')
    cy.get('[data-testid="apply-filter"]').click()
    
    // Verify filtered data
    cy.get('[data-testid="chart-container"]').should('be.visible')
    cy.url().should('include', 'filter=7days')
  })

  it('User can export data', () => {
    // Setup download detection
    cy.window().document().then(function (doc) {
      doc.addEventListener('click', () => {
        setTimeout(function () { doc.location.reload() }, 5000)
      })
    })
    
    // Trigger export
    cy.get('[data-testid="export-button"]').click()
    cy.get('[data-testid="export-csv"]').click()
    
    // Verify download initiated (implementation depends on how downloads are handled)
    cy.get('[data-testid="export-success"]').should('contain', 'Export completed')
  })
})
```

---

## Test Configuration Templates

### 1. Jest Configuration

```javascript
// Template: jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
  moduleNameMapping: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
    '^@/(.*)$': '<rootDir>/src/$1'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx}',
    '!src/index.js',
    '!src/reportWebVitals.js',
    '!src/**/*.stories.{js,jsx}',
    '!src/**/*.test.{js,jsx}'
  ],
  coverageReporters: ['text', 'lcov', 'html'],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  testMatch: [
    '<rootDir>/src/**/__tests__/**/*.{js,jsx}',
    '<rootDir>/src/**/*.{test,spec}.{js,jsx}'
  ],
  transform: {
    '^.+\\.(js|jsx)$': ['babel-jest', { presets: ['@babel/preset-react'] }]
  }
}
```

### 2. Testing Library Setup

```javascript
// Template: setupTests.js
import '@testing-library/jest-dom'
import { server } from './mocks/server'

// Mock IntersectionObserver for components that use it
global.IntersectionObserver = class IntersectionObserver {
  constructor() {}
  disconnect() {}
  observe() {}
  unobserve() {}
}

// Mock ResizeObserver
global.ResizeObserver = class ResizeObserver {
  constructor() {}
  disconnect() {}
  observe() {}
  unobserve() {}
}

// Mock matchMedia
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
})

// Mock localStorage
const localStorageMock = {
  getItem: jest.fn(),
  setItem: jest.fn(),
  removeItem: jest.fn(),
  clear: jest.fn(),
}
global.localStorage = localStorageMock

// Mock sessionStorage
global.sessionStorage = localStorageMock

// Start MSW server for API mocking
beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

// Clean up after each test
afterEach(() => {
  jest.clearAllMocks()
  localStorageMock.getItem.mockClear()
  localStorageMock.setItem.mockClear()
  localStorageMock.removeItem.mockClear()
  localStorageMock.clear.mockClear()
})
```

---

## Testing Standards for Generated Code

### Always Include:
- [ ] Unit tests for all components with props variations
- [ ] Unit tests for custom hooks with edge cases
- [ ] Unit tests for utility functions
- [ ] Integration tests for API interactions
- [ ] Integration tests for user workflows  
- [ ] E2E tests for critical user journeys
- [ ] Accessibility testing with jest-axe
- [ ] Error boundary testing
- [ ] Loading state testing
- [ ] Form validation testing

### Test Coverage Requirements:
- **Minimum 80% code coverage** across all metrics
- **100% coverage** for utility functions and business logic
- **All error paths tested** with appropriate error handling
- **All user interactions tested** including edge cases
- **Accessibility compliance verified** through automated tests

### Testing Best Practices:
- Use data-testid attributes instead of fragile selectors
- Test behavior, not implementation details
- Mock external dependencies and APIs
- Test error states and edge cases
- Include performance and load testing for critical paths
- Maintain tests alongside feature development

This comprehensive testing integration ensures generated applications are reliable, maintainable, and production-ready.