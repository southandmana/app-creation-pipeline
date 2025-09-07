# Coding Principles Integration - SOLID, DRY, and Modularity

## Purpose
Apply fundamental software engineering principles to React application development. These principles ensure maintainable, scalable, and robust code generation.

---

## SOLID Principles in React

### 1. Single Responsibility Principle (SRP)
**"A component should have one reason to change"**

```jsx
// ❌ Bad: Component handles too many responsibilities
const UserDashboard = () => {
  const [user, setUser] = useState(null)
  const [notifications, setNotifications] = useState([])
  const [theme, setTheme] = useState('light')
  
  // User data fetching
  useEffect(() => { fetchUser().then(setUser) }, [])
  
  // Notification handling
  const markNotificationRead = (id) => { /* logic */ }
  const deleteNotification = (id) => { /* logic */ }
  
  // Theme switching
  const toggleTheme = () => { /* logic */ }
  
  // Rendering user profile, notifications, AND theme controls
  return (
    <div>
      {/* 100+ lines mixing concerns */}
    </div>
  )
}

// ✅ Good: Separate components with single responsibilities
const UserProfile = ({ user }) => {
  // Only handles user profile display
  return <div className="user-profile">{/* user content */}</div>
}

const NotificationCenter = ({ notifications, onMarkRead, onDelete }) => {
  // Only handles notifications
  return <div className="notifications">{/* notification content */}</div>
}

const ThemeToggle = ({ theme, onToggle }) => {
  // Only handles theme switching
  return <button onClick={onToggle}>Toggle Theme</button>
}

const UserDashboard = () => {
  // Only handles composition and data coordination
  const { user, loading: userLoading } = useUser()
  const { notifications, markAsRead, deleteNotification } = useNotifications()
  const { theme, toggleTheme } = useTheme()
  
  if (userLoading) return <LoadingSpinner />
  
  return (
    <div className="dashboard">
      <UserProfile user={user} />
      <NotificationCenter 
        notifications={notifications}
        onMarkRead={markAsRead}
        onDelete={deleteNotification}
      />
      <ThemeToggle theme={theme} onToggle={toggleTheme} />
    </div>
  )
}
```

### 2. Open/Closed Principle (OCP)
**"Open for extension, closed for modification"**

```jsx
// ✅ Good: Extensible component design
const Button = ({ variant = 'primary', size = 'medium', children, ...props }) => {
  return (
    <button 
      className={`btn btn--${variant} btn--${size}`}
      {...props}
    >
      {children}
    </button>
  )
}

// Extend without modifying the base Button
const IconButton = ({ icon, children, ...props }) => (
  <Button {...props}>
    <span className="btn-icon">{icon}</span>
    {children}
  </Button>
)

const LoadingButton = ({ loading, children, ...props }) => (
  <Button disabled={loading} {...props}>
    {loading ? <Spinner /> : children}
  </Button>
)

// CSS provides the extension mechanism
.btn--primary { background: blue; }
.btn--secondary { background: gray; }
.btn--danger { background: red; }
.btn--small { padding: 4px 8px; }
.btn--large { padding: 12px 24px; }
```

### 3. Liskov Substitution Principle (LSP)
**"Derived components should be substitutable for their base components"**

```jsx
// ✅ Good: All input variants follow same interface
const BaseInput = ({ label, error, ...props }) => (
  <div className="input-group">
    <label>{label}</label>
    <input {...props} />
    {error && <span className="error">{error}</span>}
  </div>
)

const EmailInput = (props) => (
  <BaseInput type="email" {...props} />
)

const PasswordInput = (props) => (
  <BaseInput type="password" {...props} />
)

const NumberInput = (props) => (
  <BaseInput type="number" {...props} />
)

// All can be used interchangeably
const LoginForm = () => (
  <form>
    <EmailInput label="Email" />      {/* Substitutable */}
    <PasswordInput label="Password" /> {/* Substitutable */}
  </form>
)
```

### 4. Interface Segregation Principle (ISP)
**"Don't force components to depend on props they don't use"**

```jsx
// ❌ Bad: Fat interface forces unused props
const MediaPlayer = ({ 
  src, 
  autoplay, 
  controls, 
  volume,
  playbackRate,
  currentTime,
  onPlay,
  onPause,
  onSeek,
  onVolumeChange,
  onRateChange,
  // ... 20+ more props
}) => {
  // Component forced to handle all media functionality
}

// ✅ Good: Segregated interfaces
const VideoPlayer = ({ src, poster, onPlay, onPause }) => (
  <video src={src} poster={poster} onPlay={onPlay} onPause={onPause} />
)

const VolumeControl = ({ volume, onVolumeChange }) => (
  <input 
    type="range" 
    min="0" 
    max="100" 
    value={volume} 
    onChange={onVolumeChange} 
  />
)

const PlaybackControls = ({ onPlay, onPause, onSeek }) => (
  <div className="controls">
    <button onClick={onPlay}>Play</button>
    <button onClick={onPause}>Pause</button>
    <SeekBar onSeek={onSeek} />
  </div>
)

// Compose as needed
const FullMediaPlayer = () => (
  <div>
    <VideoPlayer {...videoProps} />
    <PlaybackControls {...controlProps} />
    <VolumeControl {...volumeProps} />
  </div>
)
```

### 5. Dependency Inversion Principle (DIP)
**"Depend on abstractions, not concretions"**

```jsx
// ❌ Bad: Direct dependency on specific service
const UserProfile = () => {
  const [user, setUser] = useState(null)
  
  useEffect(() => {
    // Directly coupled to specific API implementation
    fetch('/api/users/current')
      .then(res => res.json())
      .then(setUser)
  }, [])
  
  return <div>{user?.name}</div>
}

// ✅ Good: Depend on abstraction (hook interface)
const useUserData = (apiService = defaultApiService) => {
  const [user, setUser] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    apiService.getCurrentUser()
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [apiService])
  
  return { user, loading, error }
}

const UserProfile = () => {
  const { user, loading, error } = useUserData()
  
  if (loading) return <LoadingSpinner />
  if (error) return <ErrorMessage error={error} />
  
  return <div>{user?.name}</div>
}

// Can inject different implementations
const AdminUserProfile = () => {
  const { user } = useUserData(adminApiService)  // Different service
  return <div>{user?.name}</div>
}
```

---

## DRY Principle (Don't Repeat Yourself)

### 1. Extract Common Logic into Custom Hooks
```jsx
// ❌ Bad: Repeated API logic
const UserList = () => {
  const [users, setUsers] = useState([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    setLoading(true)
    fetch('/api/users')
      .then(res => res.json())
      .then(setUsers)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [])
}

const ProductList = () => {
  const [products, setProducts] = useState([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    setLoading(true)
    fetch('/api/products')
      .then(res => res.json())
      .then(setProducts)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [])
}

// ✅ Good: Reusable hook
const useApiData = (url, dependencies = []) => {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    setLoading(true)
    setError(null)
    
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [url, ...dependencies])
  
  return { data, loading, error, refetch: () => {
    setData(null)
    setLoading(true)
    // ... refetch logic
  }}
}

// Usage
const UserList = () => {
  const { data: users, loading, error } = useApiData('/api/users')
  // Component-specific rendering
}

const ProductList = () => {
  const { data: products, loading, error } = useApiData('/api/products')  
  // Component-specific rendering
}
```

### 2. Create Reusable Components
```jsx
// ✅ Good: Generic data display component
const DataTable = ({ 
  data, 
  columns, 
  loading, 
  error, 
  emptyMessage = "No data available",
  onRowClick 
}) => {
  if (loading) return <TableSkeleton />
  if (error) return <ErrorMessage error={error} />
  if (!data?.length) return <EmptyState message={emptyMessage} />
  
  return (
    <table className="data-table">
      <thead>
        <tr>
          {columns.map(col => (
            <th key={col.key}>{col.title}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map((row, index) => (
          <tr key={row.id || index} onClick={() => onRowClick?.(row)}>
            {columns.map(col => (
              <td key={col.key}>
                {col.render ? col.render(row[col.key], row) : row[col.key]}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  )
}

// Reuse for different data types
const UserTable = ({ users, loading, error }) => (
  <DataTable
    data={users}
    loading={loading}
    error={error}
    columns={[
      { key: 'name', title: 'Name' },
      { key: 'email', title: 'Email' },
      { key: 'role', title: 'Role', render: (role) => <Badge>{role}</Badge> }
    ]}
    onRowClick={(user) => navigateTo(`/users/${user.id}`)}
  />
)
```

---

## Modularity Principles

### 1. Feature-Based Module Organization
```
src/
├── features/
│   ├── authentication/
│   │   ├── components/
│   │   │   ├── LoginForm.jsx
│   │   │   ├── SignupForm.jsx
│   │   │   └── index.js
│   │   ├── hooks/
│   │   │   ├── useAuth.js
│   │   │   └── useLogin.js
│   │   ├── services/
│   │   │   └── authService.js
│   │   ├── types/
│   │   │   └── auth.types.js
│   │   └── index.js          # Public API
│   ├── gameLogic/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── index.js
│   └── userProfile/
├── shared/                   # Reusable across features
│   ├── components/
│   ├── hooks/
│   ├── services/
│   └── utils/
```

### 2. Public API Design
```jsx
// features/gameLogic/index.js - Feature's public interface
export { GameBoard } from './components/GameBoard'
export { GameControls } from './components/GameControls'
export { useGameState } from './hooks/useGameState'
export { useGameAI } from './hooks/useGameAI'
export { gameService } from './services/gameService'

// Other parts of app only import from feature index
import { GameBoard, useGameState } from 'features/gameLogic'

// Never import internal implementation
// import { GameBoard } from 'features/gameLogic/components/GameBoard' ❌
```

### 3. Dependency Direction
```jsx
// ✅ Good: Dependencies flow inward
// UI Layer -> Business Logic Layer -> Data Layer

// Data Layer (innermost)
const gameRepository = {
  saveGame: (game) => localStorage.setItem('game', JSON.stringify(game)),
  loadGame: () => JSON.parse(localStorage.getItem('game') || 'null')
}

// Business Logic Layer
const gameService = {
  createNewGame: () => ({ board: createBoard(), currentPlayer: 'red' }),
  makeMove: (game, column) => {
    // Game logic
    return newGameState
  },
  checkWinner: (board) => {
    // Win detection logic
    return winner
  }
}

// UI Layer (outermost)
const GameContainer = () => {
  const [game, setGame] = useState(() => 
    gameRepository.loadGame() || gameService.createNewGame()
  )
  
  const handleMove = (column) => {
    const newGame = gameService.makeMove(game, column)
    setGame(newGame)
    gameRepository.saveGame(newGame)
  }
  
  return <GameBoard game={game} onMove={handleMove} />
}
```

---

## Principle Application Guidelines

### When Generating Code, Always:

1. **Apply SRP**: Each component/function has one clear responsibility
2. **Use OCP**: Design for extension through props, CSS classes, composition
3. **Follow LSP**: Ensure component variants are truly interchangeable  
4. **Practice ISP**: Create focused prop interfaces, avoid "god components"
5. **Apply DIP**: Depend on abstractions (hooks, services) not implementations

6. **Eliminate Repetition**: Extract common patterns into reusable hooks/components
7. **Create Clear Modules**: Organize by feature, expose clean public APIs
8. **Maintain Dependency Direction**: UI → Business Logic → Data

### Code Review Checklist:
- [ ] Each component has a single, clear responsibility
- [ ] Common logic is extracted into reusable utilities
- [ ] Components are composable and extensible
- [ ] Dependencies flow in the correct direction
- [ ] Public APIs are clean and focused
- [ ] No repetitive code patterns
- [ ] Features are properly modularized

These principles ensure generated code is maintainable, testable, and scalable - the hallmark of professional development.