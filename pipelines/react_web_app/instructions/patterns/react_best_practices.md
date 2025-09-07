# React Best Practices - Context-Rich Instructions

## Purpose
This file provides comprehensive guidance for generating high-quality, modern React code. Use these patterns when building any React application through the pipeline.

---

## Core React Principles

### 1. Component Design Philosophy
**Single Responsibility Principle**
```jsx
// ❌ Bad: Component doing too much
const UserDashboard = () => {
  // Handles user data, notifications, settings, and navigation
}

// ✅ Good: Focused components
const UserProfile = () => { /* Only user profile logic */ }
const NotificationCenter = () => { /* Only notifications */ }
const UserSettings = () => { /* Only settings */ }
```

**Composition Over Inheritance**
```jsx
// ✅ Good: Use composition patterns
const Layout = ({ children, sidebar }) => (
  <div className="layout">
    <aside>{sidebar}</aside>
    <main>{children}</main>
  </div>
)

const UserDashboard = () => (
  <Layout sidebar={<UserSidebar />}>
    <UserContent />
  </Layout>
)
```

### 2. Modern Hooks Usage

**State Management with useState**
```jsx
// ✅ Good: Separate state for separate concerns
const GameBoard = () => {
  const [board, setBoard] = useState(createEmptyBoard())
  const [currentPlayer, setCurrentPlayer] = useState('red')
  const [gameStatus, setGameStatus] = useState('playing')
  const [winner, setWinner] = useState(null)
}

// ❌ Bad: Everything in one state object
const GameBoard = () => {
  const [gameState, setGameState] = useState({
    board: [],
    currentPlayer: 'red',
    gameStatus: 'playing',
    winner: null,
    score: { red: 0, yellow: 0 },
    settings: { difficulty: 'medium' }
  })
}
```

**Effect Dependencies**
```jsx
// ✅ Good: Specific dependencies
const GameBoard = ({ gameId }) => {
  const [game, setGame] = useState(null)
  
  useEffect(() => {
    loadGame(gameId).then(setGame)
  }, [gameId]) // Only re-run when gameId changes
}

// ❌ Bad: Missing or incorrect dependencies  
useEffect(() => {
  loadGame(gameId).then(setGame)
}, []) // Missing gameId dependency
```

**Custom Hooks for Reusability**
```jsx
// ✅ Good: Extract reusable logic
const useLocalStorage = (key, initialValue) => {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      return initialValue
    }
  })

  const setValue = (value) => {
    try {
      setStoredValue(value)
      window.localStorage.setItem(key, JSON.stringify(value))
    } catch (error) {
      console.error('Error saving to localStorage', error)
    }
  }

  return [storedValue, setValue]
}

// Usage in component
const GameStats = () => {
  const [wins, setWins] = useLocalStorage('gameWins', 0)
  const [losses, setLosses] = useLocalStorage('gameLosses', 0)
}
```

---

## Component Architecture Patterns

### 1. Container/Presenter Pattern
```jsx
// ✅ Container (handles logic)
const GameContainer = () => {
  const [board, setBoard] = useState(createBoard())
  const [currentPlayer, setCurrentPlayer] = useState('red')
  
  const handleMove = (column) => {
    // Game logic here
    const newBoard = makeMove(board, column, currentPlayer)
    setBoard(newBoard)
    setCurrentPlayer(currentPlayer === 'red' ? 'yellow' : 'red')
  }
  
  return (
    <GamePresenter 
      board={board}
      currentPlayer={currentPlayer}
      onMove={handleMove}
    />
  )
}

// ✅ Presenter (handles display)
const GamePresenter = ({ board, currentPlayer, onMove }) => (
  <div className="game">
    <div className="current-player">Player: {currentPlayer}</div>
    <Board board={board} onMove={onMove} />
  </div>
)
```

### 2. Compound Components
```jsx
// ✅ Good: Flexible, composable structure
const Modal = ({ children, isOpen, onClose }) => {
  if (!isOpen) return null
  
  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>
  )
}

Modal.Header = ({ children }) => <div className="modal-header">{children}</div>
Modal.Body = ({ children }) => <div className="modal-body">{children}</div>  
Modal.Footer = ({ children }) => <div className="modal-footer">{children}</div>

// Usage
const GameOverModal = ({ isOpen, onClose, winner }) => (
  <Modal isOpen={isOpen} onClose={onClose}>
    <Modal.Header>Game Over!</Modal.Header>
    <Modal.Body>{winner} wins!</Modal.Body>
    <Modal.Footer>
      <button onClick={onClose}>Play Again</button>
    </Modal.Footer>
  </Modal>
)
```

---

## State Management Strategies

### When to Use Context API
```jsx
// ✅ Good for: App-wide settings, user auth, theme
const ThemeContext = createContext()

const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light')
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light')
  }
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}

const useTheme = () => {
  const context = useContext(ThemeContext)
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider')
  }
  return context
}
```

### When to Use External State Management
**Use Zustand/Redux for:**
- Complex state logic
- State shared across many components
- Time-travel debugging needed
- Middleware required

**Keep useState for:**
- Component-local state
- Simple toggle states
- Form inputs
- UI-only state

---

## Performance Optimization

### 1. Memoization Patterns
```jsx
// ✅ Memo for expensive components
const ExpensiveGameBoard = memo(({ board, onMove }) => {
  // Heavy rendering logic
  return <div>{/* Complex board rendering */}</div>
})

// ✅ useMemo for expensive calculations
const GameStats = ({ moves }) => {
  const analytics = useMemo(() => {
    return calculateComplexAnalytics(moves) // Expensive operation
  }, [moves])
  
  return <div>{analytics.summary}</div>
}

// ✅ useCallback for stable function references
const Game = () => {
  const [board, setBoard] = useState(createBoard())
  
  const handleMove = useCallback((column) => {
    setBoard(prev => makeMove(prev, column))
  }, [])
  
  return <GameBoard board={board} onMove={handleMove} />
}
```

### 2. Code Splitting
```jsx
// ✅ Lazy load heavy components
const GameAnalytics = lazy(() => import('./GameAnalytics'))

const Game = () => (
  <div>
    <GameBoard />
    <Suspense fallback={<div>Loading analytics...</div>}>
      <GameAnalytics />
    </Suspense>
  </div>
)
```

---

## Error Handling Patterns

### 1. Error Boundaries
```jsx
class GameErrorBoundary extends Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false, error: null }
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error }
  }

  componentDidCatch(error, errorInfo) {
    console.error('Game error:', error, errorInfo)
    // Log to error reporting service
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-fallback">
          <h2>Something went wrong with the game</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Try Again
          </button>
        </div>
      )
    }

    return this.props.children
  }
}
```

### 2. Async Error Handling
```jsx
const useAsyncError = () => {
  const [error, setError] = useState(null)
  const [loading, setLoading] = useState(false)
  
  const execute = async (asyncFunction) => {
    try {
      setLoading(true)
      setError(null)
      const result = await asyncFunction()
      return result
    } catch (error) {
      setError(error)
      throw error
    } finally {
      setLoading(false)
    }
  }
  
  return { execute, error, loading }
}
```

---

## Accessibility Best Practices

### 1. Semantic HTML
```jsx
// ✅ Good: Semantic structure
const GameBoard = ({ board, onMove }) => (
  <table role="grid" aria-label="Connect Four Game Board">
    <tbody>
      {board.map((row, rowIndex) => (
        <tr key={rowIndex} role="row">
          {row.map((cell, colIndex) => (
            <td 
              key={colIndex}
              role="gridcell"
              aria-label={`Row ${rowIndex + 1}, Column ${colIndex + 1}, ${cell || 'empty'}`}
            >
              <button onClick={() => onMove(colIndex)}>
                {cell && <div className={`disc ${cell}`} />}
              </button>
            </td>
          ))}
        </tr>
      ))}
    </tbody>
  </table>
)
```

### 2. Keyboard Navigation
```jsx
const GameControls = () => {
  const handleKeyDown = (event) => {
    switch (event.key) {
      case 'ArrowLeft':
        // Move selection left
        break
      case 'ArrowRight':
        // Move selection right
        break
      case 'Space':
      case 'Enter':
        // Make move
        break
    }
  }
  
  return (
    <div 
      tabIndex={0} 
      onKeyDown={handleKeyDown}
      role="application"
      aria-label="Game controls"
    >
      {/* Game content */}
    </div>
  )
}
```

---

## File Organization Standards

```
src/
├── components/
│   ├── common/           # Reusable UI components
│   │   ├── Button/
│   │   ├── Modal/
│   │   └── index.js
│   ├── features/         # Feature-specific components  
│   │   ├── Game/
│   │   │   ├── GameBoard.jsx
│   │   │   ├── GameControls.jsx
│   │   │   ├── index.js
│   │   │   └── Game.styles.css
│   │   └── UserProfile/
│   └── layout/           # Layout components
│       ├── Header/
│       ├── Footer/
│       └── Layout.jsx
├── hooks/                # Custom hooks
│   ├── useLocalStorage.js
│   ├── useGameLogic.js
│   └── index.js
├── services/             # API and external services
│   ├── api.js
│   ├── gameEngine.js
│   └── analytics.js
├── utils/                # Pure utility functions
│   ├── gameHelpers.js
│   ├── validators.js
│   └── constants.js
├── styles/               # Global styles
│   ├── globals.css
│   ├── variables.css
│   └── themes.css
└── App.jsx
```

---

## Code Generation Guidelines

### When generating components, ALWAYS:
1. **Use functional components** with hooks (not class components)
2. **Include PropTypes or TypeScript** for prop validation  
3. **Add accessibility attributes** (aria-labels, roles, etc.)
4. **Implement error boundaries** for complex features
5. **Use semantic HTML** elements
6. **Include loading and error states**
7. **Follow naming conventions** (PascalCase for components, camelCase for functions)
8. **Add key props** for lists
9. **Optimize with memo/useMemo** when appropriate
10. **Include helpful comments** for complex logic

### File naming conventions:
- Components: `PascalCase.jsx` (GameBoard.jsx)
- Hooks: `camelCase.js` (useGameLogic.js)  
- Utils: `camelCase.js` (gameHelpers.js)
- Styles: `ComponentName.styles.css`

This comprehensive guide ensures all generated React code follows modern best practices and professional standards.