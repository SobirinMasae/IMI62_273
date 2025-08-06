# Function Documentation

This section contains comprehensive documentation for all public functions.

## 📚 Table of Contents

- [Core Utilities](#core-utilities)
- [Data Processing](#data-processing)
- [Validation Functions](#validation-functions)
- [Helper Functions](#helper-functions)
- [Configuration Functions](#configuration-functions)
- [Error Handling](#error-handling)
- [Performance Considerations](#performance-considerations)

## 🔧 Core Utilities

### `formatDate(date, format)`

Formats a date according to the specified format string.

**Parameters:**
- `date` (Date | string | number): The date to format
- `format` (string): Format string using standard tokens

**Returns:** `string` - Formatted date string

**Throws:** `TypeError` - If date is invalid or format is not a string

**Example:**
```javascript
import { formatDate } from 'your-package';

// Basic usage
const formatted = formatDate(new Date(), 'YYYY-MM-DD');
console.log(formatted); // "2023-12-15"

// With time
const withTime = formatDate(new Date(), 'YYYY-MM-DD HH:mm:ss');
console.log(withTime); // "2023-12-15 14:30:00"

// From timestamp
const fromTimestamp = formatDate(1702650000000, 'MMM DD, YYYY');
console.log(fromTimestamp); // "Dec 15, 2023"

// Custom format
const custom = formatDate(new Date(), 'dddd, MMMM Do YYYY');
console.log(custom); // "Friday, December 15th 2023"
```

**Format Tokens:**
- `YYYY` - 4-digit year
- `MM` - 2-digit month
- `DD` - 2-digit day
- `HH` - 2-digit hour (24h)
- `mm` - 2-digit minute
- `ss` - 2-digit second
- `MMM` - Short month name
- `MMMM` - Full month name
- `dddd` - Full day name

---

### `debounce(func, delay, options)`

Creates a debounced function that delays invoking `func` until after `delay` milliseconds have elapsed since the last time the debounced function was invoked.

**Parameters:**
- `func` (Function): The function to debounce
- `delay` (number): The delay in milliseconds
- `options` (Object, optional): Options object
  - `leading` (boolean): Trigger on leading edge (default: false)
  - `trailing` (boolean): Trigger on trailing edge (default: true)
  - `maxWait` (number): Maximum delay between invocations

**Returns:** `Function` - Debounced function with cancel method

**Example:**
```javascript
import { debounce } from 'your-package';

// Basic debouncing
const debouncedSave = debounce((data) => {
  console.log('Saving:', data);
}, 500);

// Usage in React
const handleInputChange = debounce((value) => {
  searchApi(value);
}, 300);

// With leading edge
const debouncedClick = debounce(handleClick, 1000, { 
  leading: true, 
  trailing: false 
});

// Cancel debounced function
debouncedSave.cancel();

// Check if function is pending
if (debouncedSave.pending()) {
  console.log('Function is waiting to execute');
}
```

---

### `throttle(func, limit, options)`

Creates a throttled function that only invokes `func` at most once per every `limit` milliseconds.

**Parameters:**
- `func` (Function): The function to throttle
- `limit` (number): The time limit in milliseconds
- `options` (Object, optional): Options object
  - `leading` (boolean): Trigger on leading edge (default: true)
  - `trailing` (boolean): Trigger on trailing edge (default: true)

**Returns:** `Function` - Throttled function

**Example:**
```javascript
import { throttle } from 'your-package';

// Throttle scroll handler
const handleScroll = throttle(() => {
  console.log('Scroll position:', window.scrollY);
}, 100);

window.addEventListener('scroll', handleScroll);

// Throttle API calls
const throttledSearch = throttle(async (query) => {
  const results = await searchApi(query);
  updateResults(results);
}, 1000);
```

## 📊 Data Processing

### `groupBy(array, keyOrFunction)`

Groups array elements by a key or the result of a function.

**Parameters:**
- `array` (Array): The array to group
- `keyOrFunction` (string | Function): Property key or grouping function

**Returns:** `Object` - Object with grouped elements

**Example:**
```javascript
import { groupBy } from 'your-package';

const users = [
  { name: 'John', role: 'admin', age: 30 },
  { name: 'Jane', role: 'user', age: 25 },
  { name: 'Bob', role: 'admin', age: 35 }
];

// Group by property
const byRole = groupBy(users, 'role');
console.log(byRole);
// {
//   admin: [{ name: 'John', role: 'admin', age: 30 }, { name: 'Bob', role: 'admin', age: 35 }],
//   user: [{ name: 'Jane', role: 'user', age: 25 }]
// }

// Group by function
const byAgeGroup = groupBy(users, user => user.age >= 30 ? 'senior' : 'junior');
console.log(byAgeGroup);
// {
//   senior: [{ name: 'John', role: 'admin', age: 30 }, { name: 'Bob', role: 'admin', age: 35 }],
//   junior: [{ name: 'Jane', role: 'user', age: 25 }]
// }
```

---

### `sortBy(array, keyOrFunction, order)`

Sorts an array by a key or the result of a function.

**Parameters:**
- `array` (Array): The array to sort
- `keyOrFunction` (string | Function): Property key or sorting function
- `order` (string, optional): 'asc' or 'desc' (default: 'asc')

**Returns:** `Array` - New sorted array (original array is unchanged)

**Example:**
```javascript
import { sortBy } from 'your-package';

const products = [
  { name: 'Laptop', price: 999, rating: 4.5 },
  { name: 'Phone', price: 599, rating: 4.8 },
  { name: 'Tablet', price: 299, rating: 4.2 }
];

// Sort by price (ascending)
const byPrice = sortBy(products, 'price');
console.log(byPrice[0].name); // "Tablet"

// Sort by rating (descending)
const byRating = sortBy(products, 'rating', 'desc');
console.log(byRating[0].name); // "Phone"

// Sort by custom function
const byNameLength = sortBy(products, p => p.name.length);
console.log(byNameLength.map(p => p.name)); // ["Phone", "Laptop", "Tablet"]
```

---

### `filterBy(array, criteria)`

Filters an array based on multiple criteria.

**Parameters:**
- `array` (Array): The array to filter
- `criteria` (Object): Filter criteria object

**Returns:** `Array` - Filtered array

**Example:**
```javascript
import { filterBy } from 'your-package';

const products = [
  { name: 'Laptop', price: 999, category: 'electronics', inStock: true },
  { name: 'Book', price: 20, category: 'books', inStock: false },
  { name: 'Phone', price: 599, category: 'electronics', inStock: true }
];

// Filter by multiple criteria
const filtered = filterBy(products, {
  category: 'electronics',
  inStock: true,
  price: price => price < 800
});
console.log(filtered); // [{ name: 'Phone', ... }]

// Advanced filtering with operators
const advanced = filterBy(products, {
  price: { $gte: 500, $lte: 1000 },
  category: { $in: ['electronics', 'books'] }
});
```

## ✅ Validation Functions

### `isEmail(email)`

Validates if a string is a valid email address.

**Parameters:**
- `email` (string): Email address to validate

**Returns:** `boolean` - True if valid email

**Example:**
```javascript
import { isEmail } from 'your-package';

console.log(isEmail('user@example.com')); // true
console.log(isEmail('invalid.email')); // false
console.log(isEmail('user+tag@domain.co.uk')); // true
```

---

### `validateSchema(data, schema)`

Validates data against a schema definition.

**Parameters:**
- `data` (any): Data to validate
- `schema` (Object): Schema definition

**Returns:** `Object` - Validation result with `valid` boolean and `errors` array

**Example:**
```javascript
import { validateSchema } from 'your-package';

const userSchema = {
  name: { type: 'string', required: true, minLength: 2 },
  email: { type: 'email', required: true },
  age: { type: 'number', min: 0, max: 120 },
  role: { type: 'string', enum: ['user', 'admin'] }
};

const userData = {
  name: 'John Doe',
  email: 'john@example.com',
  age: 30,
  role: 'user'
};

const result = validateSchema(userData, userSchema);
if (result.valid) {
  console.log('Data is valid');
} else {
  console.log('Validation errors:', result.errors);
}
```

---

### `sanitizeInput(input, options)`

Sanitizes user input to prevent XSS and other security issues.

**Parameters:**
- `input` (string): Input string to sanitize
- `options` (Object, optional): Sanitization options

**Returns:** `string` - Sanitized string

**Example:**
```javascript
import { sanitizeInput } from 'your-package';

const userInput = '<script>alert("xss")</script>Hello World';
const sanitized = sanitizeInput(userInput);
console.log(sanitized); // "Hello World"

// With custom options
const customSanitized = sanitizeInput(userInput, {
  allowedTags: ['b', 'i', 'em'],
  allowedAttributes: { 'a': ['href'] }
});
```

## 🛠️ Helper Functions

### `deepClone(obj)`

Creates a deep copy of an object or array.

**Parameters:**
- `obj` (any): Object to clone

**Returns:** `any` - Deep copy of the object

**Example:**
```javascript
import { deepClone } from 'your-package';

const original = {
  name: 'John',
  address: {
    city: 'New York',
    coordinates: { lat: 40.7128, lng: -74.0060 }
  },
  hobbies: ['reading', 'swimming']
};

const cloned = deepClone(original);
cloned.address.city = 'Boston';
console.log(original.address.city); // "New York" (unchanged)
console.log(cloned.address.city); // "Boston"
```

---

### `mergeDeep(target, ...sources)`

Performs a deep merge of objects.

**Parameters:**
- `target` (Object): Target object to merge into
- `...sources` (Object[]): Source objects to merge from

**Returns:** `Object` - Merged object

**Example:**
```javascript
import { mergeDeep } from 'your-package';

const defaults = {
  theme: 'light',
  features: {
    notifications: true,
    analytics: false
  }
};

const userSettings = {
  theme: 'dark',
  features: {
    analytics: true
  }
};

const settings = mergeDeep({}, defaults, userSettings);
console.log(settings);
// {
//   theme: 'dark',
//   features: {
//     notifications: true,
//     analytics: true
//   }
// }
```

---

### `pick(obj, keys)`

Creates an object with only the specified keys.

**Parameters:**
- `obj` (Object): Source object
- `keys` (string[]): Array of keys to pick

**Returns:** `Object` - New object with picked keys

**Example:**
```javascript
import { pick } from 'your-package';

const user = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
  password: 'secret123',
  role: 'admin'
};

const publicUser = pick(user, ['id', 'name', 'email']);
console.log(publicUser);
// { id: 1, name: 'John Doe', email: 'john@example.com' }
```

---

### `omit(obj, keys)`

Creates an object without the specified keys.

**Parameters:**
- `obj` (Object): Source object
- `keys` (string[]): Array of keys to omit

**Returns:** `Object` - New object without omitted keys

**Example:**
```javascript
import { omit } from 'your-package';

const user = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
  password: 'secret123',
  role: 'admin'
};

const safeUser = omit(user, ['password']);
console.log(safeUser);
// { id: 1, name: 'John Doe', email: 'john@example.com', role: 'admin' }
```

## ⚙️ Configuration Functions

### `loadConfig(path, options)`

Loads configuration from various sources.

**Parameters:**
- `path` (string): Configuration file path
- `options` (Object, optional): Loading options

**Returns:** `Promise<Object>` - Configuration object

**Example:**
```javascript
import { loadConfig } from 'your-package';

// Load from JSON file
const config = await loadConfig('./config.json');

// Load with environment variable overrides
const configWithEnv = await loadConfig('./config.json', {
  envPrefix: 'APP_',
  envSeparator: '__'
});

// Load with defaults
const configWithDefaults = await loadConfig('./config.json', {
  defaults: {
    port: 3000,
    host: 'localhost'
  }
});
```

---

### `getEnvVar(name, defaultValue, options)`

Gets an environment variable with type casting and validation.

**Parameters:**
- `name` (string): Environment variable name
- `defaultValue` (any, optional): Default value if not found
- `options` (Object, optional): Parsing options

**Returns:** `any` - Parsed environment variable value

**Example:**
```javascript
import { getEnvVar } from 'your-package';

// Basic usage
const port = getEnvVar('PORT', 3000, { type: 'number' });
const isDev = getEnvVar('NODE_ENV', 'development') === 'development';

// With validation
const dbUrl = getEnvVar('DATABASE_URL', null, {
  required: true,
  validate: url => url.startsWith('postgresql://')
});

// Array parsing
const allowedOrigins = getEnvVar('ALLOWED_ORIGINS', [], {
  type: 'array',
  separator: ','
});
```

## 🚨 Error Handling

### `createError(message, code, metadata)`

Creates a standardized error object.

**Parameters:**
- `message` (string): Error message
- `code` (string, optional): Error code
- `metadata` (Object, optional): Additional error metadata

**Returns:** `Error` - Enhanced error object

**Example:**
```javascript
import { createError } from 'your-package';

// Basic error
const error = createError('User not found');

// Error with code
const notFoundError = createError('User not found', 'USER_NOT_FOUND');

// Error with metadata
const validationError = createError(
  'Validation failed',
  'VALIDATION_ERROR',
  {
    field: 'email',
    value: 'invalid-email',
    rule: 'email-format'
  }
);

throw validationError;
```

---

### `handleAsync(promise)`

Wraps async functions to handle errors without try-catch.

**Parameters:**
- `promise` (Promise): Promise to handle

**Returns:** `Promise<[Error|null, any]>` - Tuple of [error, result]

**Example:**
```javascript
import { handleAsync } from 'your-package';

// Instead of try-catch
async function fetchUser(id) {
  const [error, user] = await handleAsync(
    fetch(`/api/users/${id}`).then(r => r.json())
  );
  
  if (error) {
    console.error('Failed to fetch user:', error);
    return null;
  }
  
  return user;
}

// Multiple async operations
async function processData() {
  const [dbError, data] = await handleAsync(database.query('SELECT * FROM users'));
  if (dbError) return handleDatabaseError(dbError);
  
  const [apiError, enriched] = await handleAsync(enrichData(data));
  if (apiError) return handleApiError(apiError);
  
  return enriched;
}
```

## ⚡ Performance Considerations

### Memory Usage
- Functions like `deepClone` and `mergeDeep` can be memory-intensive for large objects
- Use `pick` and `omit` instead of manual object manipulation for better performance
- `debounce` and `throttle` hold references to functions; ensure proper cleanup

### Best Practices
```javascript
// Good: Reuse debounced functions
const debouncedSave = debounce(saveFunction, 500);
// Use debouncedSave throughout component lifecycle

// Bad: Creating new debounced function on each render
function Component() {
  const handleChange = debounce(onChange, 500); // Creates new function each time
}

// Good: Use useMemo in React
function Component() {
  const debouncedChange = useMemo(() => debounce(onChange, 500), [onChange]);
}
```

### Benchmarks
| Function | 1,000 items | 10,000 items | 100,000 items |
|----------|-------------|--------------|---------------|
| `sortBy` | ~1ms | ~15ms | ~200ms |
| `groupBy` | ~2ms | ~25ms | ~300ms |
| `filterBy` | ~1ms | ~10ms | ~150ms |
| `deepClone` | ~5ms | ~50ms | ~800ms |

## 🧪 Testing

All functions include comprehensive test coverage. Example test patterns:

```javascript
import { formatDate, debounce } from 'your-package';

describe('formatDate', () => {
  test('formats date correctly', () => {
    const date = new Date('2023-12-15T14:30:00Z');
    expect(formatDate(date, 'YYYY-MM-DD')).toBe('2023-12-15');
  });
  
  test('throws error for invalid date', () => {
    expect(() => formatDate('invalid', 'YYYY-MM-DD')).toThrow(TypeError);
  });
});

describe('debounce', () => {
  test('delays function execution', async () => {
    const fn = jest.fn();
    const debounced = debounce(fn, 100);
    
    debounced();
    debounced();
    debounced();
    
    expect(fn).not.toHaveBeenCalled();
    await new Promise(resolve => setTimeout(resolve, 150));
    expect(fn).toHaveBeenCalledTimes(1);
  });
});
```

---

**Need Help?** Check our [Function API Guide](../guides/function-usage.md) or [Examples](../examples/functions.md).