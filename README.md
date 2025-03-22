# Next.js 15 & React Cheat Sheet
The cheat sheet includes:

Component Rendering: Function components, conditional rendering, list rendering, fragments, and client vs server components in Next.js 15

useEffect and Other Hooks: Detailed examples of useState, useEffect (with different dependency patterns), useRef, useMemo, useCallback, useReducer, and useId

Routing in Next.js 15: App router structure, basic routing, navigation (both Link and programmatic), dynamic routes, layouts, loading states, error handling, and route handlers

Context: Creating context, using Context.Consumer, and using the useContext hook

Provider: Basic setup, providers with state, combining multiple contexts, custom hooks for context, and the context-reducer pattern

## Component Rendering

### Function Components
```jsx
// Basic component
function Greeting() {
  return <h1>Hello, world!</h1>;
}

// With props
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Usage
<Greeting name="John" />
```

### Conditional Rendering
```jsx
function ConditionalComponent({ isLoggedIn }) {
  // Using ternary operator
  return isLoggedIn ? <UserDashboard /> : <LoginPrompt />;
  
  // Using && operator
  return isLoggedIn && <UserDashboard />;
  
  // Using if/else within component
  if (isLoggedIn) {
    return <UserDashboard />;
  } else {
    return <LoginPrompt />;
  }
}
```

### List Rendering
```jsx
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

### Fragment Syntax
```jsx
// When you need to return multiple elements without a wrapper div
function Component() {
  return (
    <>
      <Header />
      <Main />
      <Footer />
    </>
  );
}
```

### Client vs Server Components (Next.js 15)
```jsx
// Server Component (default in Next.js 15)
// Cannot use hooks, browser APIs, or interactive features
function ServerComponent() {
  return <h1>I render on the server</h1>;
}

// Client Component
"use client"; // Must be at the top of the file

function ClientComponent() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

## useEffect and Other Useful Hooks

### useState
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>Decrement</button>
    </div>
  );
}
```

### useEffect
```jsx
// Run on every render
useEffect(() => {
  console.log('Component rendered');
});

// Run only on mount (empty dependency array)
useEffect(() => {
  console.log('Component mounted');
  return () => {
    console.log('Component unmounted');
  };
}, []);

// Run when dependencies change
useEffect(() => {
  console.log(`Count changed to: ${count}`);
  document.title = `Count: ${count}`;
}, [count]);

// Cleanup function
useEffect(() => {
  const subscription = subscribeToData();
  return () => {
    subscription.unsubscribe();
  };
}, []);
```

### useRef
```jsx
function InputFocus() {
  const inputRef = useRef(null);
  
  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={() => inputRef.current.focus()}>
        Focus Input
      </button>
    </>
  );
}
```

### useMemo
```jsx
// Memoize expensive calculations
const memoizedValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);
```

### useCallback
```jsx
// Memoize functions to prevent unnecessary re-renders
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

### useReducer
```jsx
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}
```

### useId (React 18+)
```jsx
function LabeledInput() {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Email</label>
      <input id={id} type="email" />
    </>
  );
}
```

## Routing in Next.js 15

### App Router Structure
```
app/
├── page.js              # Home route (/)
├── about/
│   └── page.js          # About route (/about)
├── blog/
│   ├── page.js          # Blog index (/blog)
│   └── [slug]/
│       └── page.js      # Blog post (/blog/post-slug)
└── layout.js            # Root layout (applies to all routes)
```

### Basic Routing
```jsx
// app/page.js - Home page (/)
export default function Home() {
  return <h1>Home Page</h1>;
}

// app/about/page.js - About page (/about)
export default function About() {
  return <h1>About Page</h1>;
}
```

### Navigation
```jsx
import Link from 'next/link';
import { useRouter } from 'next/navigation'; // For client components

// Static navigation with Link
function Navigation() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog/post-1">Blog Post</Link>
    </nav>
  );
}

// Programmatic navigation (client components only)
"use client";
function LoginButton() {
  const router = useRouter();
  
  const handleLogin = async () => {
    // After login logic
    router.push('/dashboard');
    // Or refresh the current page
    router.refresh();
  };
  
  return <button onClick={handleLogin}>Login</button>;
}
```

### Dynamic Routes
```jsx
// app/blog/[slug]/page.js
export default function BlogPost({ params }) {
  const { slug } = params;
  return <h1>Blog Post: {slug}</h1>;
}
```

### Layouts
```jsx
// app/layout.js - Root layout
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <header>Global Header</header>
        {children}
        <footer>Global Footer</footer>
      </body>
    </html>
  );
}

// app/blog/layout.js - Nested layout for blog section
export default function BlogLayout({ children }) {
  return (
    <div>
      <nav>Blog Navigation</nav>
      <main>{children}</main>
    </div>
  );
}
```

### Loading States
```jsx
// app/blog/loading.js - Shows during page loading
export default function Loading() {
  return <div>Loading blog content...</div>;
}
```

### Error Handling
```jsx
// app/blog/error.js - Catches errors in the blog route
"use client";
export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

### Route Handlers (API Routes)
```jsx
// app/api/hello/route.js
export async function GET() {
  return Response.json({ message: 'Hello World' });
}

export async function POST(request) {
  const data = await request.json();
  return Response.json({ received: data });
}
```

## Context

### Creating Context
```jsx
// Create a context with default value
const ThemeContext = createContext('light');

// With a more complex default value
const UserContext = createContext({
  user: null,
  login: () => {},
  logout: () => {},
});
```

### Using Context Consumer
```jsx
function ThemedButton() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={theme}>
          I am styled by the theme context!
        </button>
      )}
    </ThemeContext.Consumer>
  );
}
```

### Using useContext Hook
```jsx
function ThemedButton() {
  const theme = useContext(ThemeContext);
  
  return (
    <button className={theme}>
      I am styled by the theme context!
    </button>
  );
}
```

## Provider

### Basic Provider Setup
```jsx
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <MainContent />
    </ThemeContext.Provider>
  );
}
```

### Provider with State
```jsx
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
  };
  
  const value = {
    theme,
    toggleTheme,
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// Usage
function App() {
  return (
    <ThemeProvider>
      <MainContent />
    </ThemeProvider>
  );
}
```

### Combining Multiple Contexts
```jsx
function AppProviders({ children }) {
  return (
    <ThemeProvider>
      <UserProvider>
        <DataProvider>
          {children}
        </DataProvider>
      </UserProvider>
    </ThemeProvider>
  );
}

// Usage
function App() {
  return (
    <AppProviders>
      <MainContent />
    </AppProviders>
  );
}
```

### Custom Hook for Context
```jsx
// Create a custom hook for easier context usage
function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

// Usage
function ThemedButton() {
  const { theme, toggleTheme } = useTheme();
  return (
    <button 
      className={theme}
      onClick={toggleTheme}
    >
      Toggle Theme
    </button>
  );
}
```

### Context with Reducer Pattern
```jsx
const initialState = { count: 0 };

function counterReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

const CounterContext = createContext();

function CounterProvider({ children }) {
  const [state, dispatch] = useReducer(counterReducer, initialState);
  
  const value = { state, dispatch };
  
  return (
    <CounterContext.Provider value={value}>
      {children}
    </CounterContext.Provider>
  );
}

// Custom hook
function useCounter() {
  const context = useContext(CounterContext);
  if (context === undefined) {
    throw new Error('useCounter must be used within a CounterProvider');
  }
  return context;
}

// Usage
function CounterDisplay() {
  const { state } = useCounter();
  return <div>Count: {state.count}</div>;
}

function CounterButtons() {
  const { dispatch } = useCounter();
  return (
    <>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}
```
