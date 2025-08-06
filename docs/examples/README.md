# Examples and Tutorials

Practical examples and tutorials for using APIs, functions, and components effectively.

## 📚 Table of Contents

- [Quick Start Examples](#quick-start-examples)
- [API Integration Examples](#api-integration-examples)
- [Function Usage Examples](#function-usage-examples)
- [Component Examples](#component-examples)
- [Common Patterns](#common-patterns)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## 🚀 Quick Start Examples

### Basic Application Setup

```javascript
// main.js
import { createApp } from 'vue'; // or your framework
import App from './App.vue';
import { ApiClient, ThemeProvider } from 'your-package';

// Initialize API client
const apiClient = new ApiClient({
  baseUrl: 'https://api.your-domain.com/v1',
  apiKey: process.env.VUE_APP_API_KEY
});

// Create app with global configuration
const app = createApp(App);
app.provide('apiClient', apiClient);
app.mount('#app');
```

### React Application Setup

```jsx
// App.js
import React from 'react';
import { ThemeProvider, ApiProvider } from 'your-component-library';
import { createTheme } from 'your-component-library/theme';
import Dashboard from './components/Dashboard';

const theme = createTheme({
  colors: {
    primary: '#007bff',
    secondary: '#6c757d'
  }
});

function App() {
  return (
    <ThemeProvider theme={theme}>
      <ApiProvider 
        baseUrl="https://api.your-domain.com/v1"
        apiKey={process.env.REACT_APP_API_KEY}
      >
        <Dashboard />
      </ApiProvider>
    </ThemeProvider>
  );
}

export default App;
```

### TypeScript Configuration

```typescript
// types/index.ts
export interface User {
  id: string;
  name: string;
  email: string;
  role: 'admin' | 'user';
  createdAt: string;
  updatedAt: string;
}

export interface ApiResponse<T> {
  data: T;
  message?: string;
  errors?: string[];
}

// config/api.ts
import { ApiClient } from 'your-package';

export const apiClient = new ApiClient({
  baseUrl: process.env.VITE_API_BASE_URL!,
  apiKey: process.env.VITE_API_KEY!,
  timeout: 10000,
  retries: 3
});

// Add request interceptor for authentication
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('authToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

## 🌐 API Integration Examples

### User Management

```javascript
// services/userService.js
import { apiClient } from '../config/api';
import { handleAsync } from 'your-package/utils';

export class UserService {
  // Get all users with pagination
  static async getUsers(params = {}) {
    const [error, response] = await handleAsync(
      apiClient.get('/users', { params })
    );
    
    if (error) {
      console.error('Failed to fetch users:', error);
      throw error;
    }
    
    return response.data;
  }

  // Create a new user
  static async createUser(userData) {
    const [error, response] = await handleAsync(
      apiClient.post('/users', userData)
    );
    
    if (error) {
      if (error.status === 400) {
        throw new Error('Invalid user data provided');
      }
      throw new Error('Failed to create user');
    }
    
    return response.data;
  }

  // Update user profile
  static async updateUser(userId, updates) {
    const [error, response] = await handleAsync(
      apiClient.put(`/users/${userId}`, updates)
    );
    
    if (error) {
      throw error;
    }
    
    return response.data;
  }

  // Delete user
  static async deleteUser(userId) {
    const [error] = await handleAsync(
      apiClient.delete(`/users/${userId}`)
    );
    
    if (error) {
      throw error;
    }
    
    return true;
  }
}
```

### Real-time Updates with WebSocket

```javascript
// services/websocketService.js
export class WebSocketService {
  constructor(url) {
    this.url = url;
    this.ws = null;
    this.reconnectAttempts = 0;
    this.maxReconnectAttempts = 5;
    this.reconnectDelay = 1000;
    this.subscribers = new Map();
  }

  connect() {
    try {
      this.ws = new WebSocket(this.url);
      
      this.ws.onopen = () => {
        console.log('WebSocket connected');
        this.reconnectAttempts = 0;
        
        // Authenticate
        this.send({
          type: 'auth',
          token: localStorage.getItem('authToken')
        });
      };

      this.ws.onmessage = (event) => {
        try {
          const message = JSON.parse(event.data);
          this.handleMessage(message);
        } catch (error) {
          console.error('Failed to parse WebSocket message:', error);
        }
      };

      this.ws.onclose = () => {
        console.log('WebSocket disconnected');
        this.reconnect();
      };

      this.ws.onerror = (error) => {
        console.error('WebSocket error:', error);
      };
    } catch (error) {
      console.error('Failed to connect WebSocket:', error);
      this.reconnect();
    }
  }

  send(message) {
    if (this.ws?.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify(message));
    }
  }

  subscribe(channel, callback) {
    if (!this.subscribers.has(channel)) {
      this.subscribers.set(channel, new Set());
      
      // Subscribe to channel
      this.send({
        type: 'subscribe',
        channel
      });
    }
    
    this.subscribers.get(channel).add(callback);
  }

  unsubscribe(channel, callback) {
    const callbacks = this.subscribers.get(channel);
    if (callbacks) {
      callbacks.delete(callback);
      
      if (callbacks.size === 0) {
        this.subscribers.delete(channel);
        this.send({
          type: 'unsubscribe',
          channel
        });
      }
    }
  }

  handleMessage(message) {
    const { type, channel, data } = message;
    
    if (type === 'message' && channel) {
      const callbacks = this.subscribers.get(channel);
      if (callbacks) {
        callbacks.forEach(callback => callback(data));
      }
    }
  }

  reconnect() {
    if (this.reconnectAttempts < this.maxReconnectAttempts) {
      this.reconnectAttempts++;
      
      setTimeout(() => {
        console.log(`Reconnecting... (${this.reconnectAttempts}/${this.maxReconnectAttempts})`);
        this.connect();
      }, this.reconnectDelay * this.reconnectAttempts);
    }
  }

  disconnect() {
    if (this.ws) {
      this.ws.close();
      this.ws = null;
    }
  }
}

// Usage in React component
import { useEffect, useRef } from 'react';

function useWebSocket(url) {
  const wsRef = useRef(null);

  useEffect(() => {
    wsRef.current = new WebSocketService(url);
    wsRef.current.connect();

    return () => {
      wsRef.current?.disconnect();
    };
  }, [url]);

  return wsRef.current;
}
```

### GraphQL Integration

```javascript
// services/graphqlService.js
import { handleAsync } from 'your-package/utils';

export class GraphQLService {
  constructor(endpoint, apiKey) {
    this.endpoint = endpoint;
    this.apiKey = apiKey;
  }

  async query(query, variables = {}) {
    const [error, response] = await handleAsync(
      fetch(this.endpoint, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${this.apiKey}`
        },
        body: JSON.stringify({
          query,
          variables
        })
      }).then(r => r.json())
    );

    if (error) {
      throw error;
    }

    if (response.errors) {
      throw new Error(response.errors[0].message);
    }

    return response.data;
  }

  // User queries
  async getUser(id) {
    const query = `
      query GetUser($id: ID!) {
        user(id: $id) {
          id
          name
          email
          posts {
            id
            title
            createdAt
          }
        }
      }
    `;

    return this.query(query, { id });
  }

  async createUser(input) {
    const mutation = `
      mutation CreateUser($input: CreateUserInput!) {
        createUser(input: $input) {
          id
          name
          email
          createdAt
        }
      }
    `;

    return this.query(mutation, { input });
  }
}
```

## 🔧 Function Usage Examples

### Data Processing Pipeline

```javascript
// utils/dataProcessor.js
import { 
  groupBy, 
  sortBy, 
  filterBy, 
  debounce,
  pick,
  formatDate 
} from 'your-package/utils';

export class DataProcessor {
  constructor(data) {
    this.data = data;
    this.filters = {};
    this.sortConfig = null;
  }

  // Process sales data
  static processSalesData(salesData) {
    // Group by month
    const byMonth = groupBy(salesData, sale => 
      formatDate(sale.date, 'YYYY-MM')
    );

    // Calculate monthly totals
    const monthlyTotals = Object.entries(byMonth).map(([month, sales]) => ({
      month,
      total: sales.reduce((sum, sale) => sum + sale.amount, 0),
      count: sales.length,
      averageOrderValue: sales.reduce((sum, sale) => sum + sale.amount, 0) / sales.length
    }));

    // Sort by month
    return sortBy(monthlyTotals, 'month');
  }

  // Filter and search users
  static createUserFilter() {
    let users = [];
    let filteredUsers = [];

    const debouncedSearch = debounce((searchTerm) => {
      filteredUsers = users.filter(user => 
        user.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
        user.email.toLowerCase().includes(searchTerm.toLowerCase())
      );
    }, 300);

    return {
      setUsers(newUsers) {
        users = newUsers;
        filteredUsers = users;
      },

      search(term) {
        debouncedSearch(term);
      },

      getFilteredUsers() {
        return filteredUsers;
      },

      filterByRole(roles) {
        filteredUsers = filterBy(users, {
          role: { $in: roles }
        });
      },

      filterByDateRange(startDate, endDate) {
        filteredUsers = filterBy(users, {
          createdAt: date => {
            const userDate = new Date(date);
            return userDate >= startDate && userDate <= endDate;
          }
        });
      }
    };
  }

  // Advanced data transformation
  static transformUserData(users) {
    return users.map(user => ({
      // Pick essential fields
      ...pick(user, ['id', 'name', 'email', 'role']),
      
      // Add computed fields
      displayName: `${user.firstName} ${user.lastName}`,
      initials: `${user.firstName[0]}${user.lastName[0]}`,
      memberSince: formatDate(user.createdAt, 'MMM YYYY'),
      isActive: user.lastLoginAt && 
        new Date(user.lastLoginAt) > new Date(Date.now() - 30 * 24 * 60 * 60 * 1000),
      
      // Transform permissions
      permissions: groupBy(user.permissions, 'resource')
    }));
  }
}
```

### Form Validation and Handling

```javascript
// utils/formUtils.js
import { validateSchema, sanitizeInput, debounce } from 'your-package/utils';

export class FormValidator {
  constructor(schema) {
    this.schema = schema;
    this.errors = {};
    this.touched = {};
  }

  // Validate single field
  validateField(name, value) {
    const fieldSchema = this.schema[name];
    if (!fieldSchema) return null;

    const result = validateSchema({ [name]: value }, { [name]: fieldSchema });
    return result.errors[0] || null;
  }

  // Validate entire form
  validate(data) {
    const result = validateSchema(data, this.schema);
    this.errors = result.errors.reduce((acc, error) => {
      acc[error.field] = error.message;
      return acc;
    }, {});
    
    return result.valid;
  }

  // Mark field as touched
  touch(fieldName) {
    this.touched[fieldName] = true;
  }

  // Get error for field (only if touched)
  getFieldError(fieldName) {
    return this.touched[fieldName] ? this.errors[fieldName] : null;
  }
}

// React hook for form handling
import { useState, useCallback } from 'react';

export function useForm(initialValues, validationSchema) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const validator = new FormValidator(validationSchema);

  // Debounced validation
  const debouncedValidate = useCallback(
    debounce((fieldName, value) => {
      const error = validator.validateField(fieldName, value);
      setErrors(prev => ({
        ...prev,
        [fieldName]: error
      }));
    }, 300),
    [validator]
  );

  const setValue = useCallback((name, value) => {
    // Sanitize input
    const sanitized = typeof value === 'string' ? sanitizeInput(value) : value;
    
    setValues(prev => ({
      ...prev,
      [name]: sanitized
    }));

    // Validate field if touched
    if (touched[name]) {
      debouncedValidate(name, sanitized);
    }
  }, [touched, debouncedValidate]);

  const setFieldTouched = useCallback((name) => {
    setTouched(prev => ({
      ...prev,
      [name]: true
    }));

    // Validate immediately when touched
    const error = validator.validateField(name, values[name]);
    setErrors(prev => ({
      ...prev,
      [name]: error
    }));
  }, [validator, values]);

  const handleSubmit = useCallback(async (onSubmit) => {
    setIsSubmitting(true);
    
    // Mark all fields as touched
    const allFieldsTouched = {};
    Object.keys(validationSchema).forEach(field => {
      allFieldsTouched[field] = true;
    });
    setTouched(allFieldsTouched);

    // Validate entire form
    const isValid = validator.validate(values);
    setErrors(validator.errors);

    if (isValid) {
      try {
        await onSubmit(values);
      } catch (error) {
        console.error('Form submission error:', error);
      }
    }

    setIsSubmitting(false);
  }, [values, validator, validationSchema]);

  return {
    values,
    errors,
    touched,
    isSubmitting,
    setValue,
    setFieldTouched,
    handleSubmit,
    getFieldProps: (name) => ({
      value: values[name] || '',
      onChange: (e) => setValue(name, e.target.value),
      onBlur: () => setFieldTouched(name),
      error: touched[name] ? errors[name] : null
    })
  };
}
```

## 🎨 Component Examples

### Complete Dashboard Example

```jsx
// components/Dashboard.jsx
import React, { useState, useEffect } from 'react';
import {
  Container,
  Grid,
  Card,
  Table,
  Button,
  Input,
  Select,
  Modal,
  Tabs,
  Tab,
  Badge,
  Flex
} from 'your-component-library';
import { UserService } from '../services/userService';
import { useForm } from '../utils/formUtils';

const userValidationSchema = {
  name: { required: true, minLength: 2 },
  email: { required: true, type: 'email' },
  role: { required: true, enum: ['admin', 'user'] }
};

function Dashboard() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [showUserModal, setShowUserModal] = useState(false);
  const [editingUser, setEditingUser] = useState(null);
  const [currentPage, setCurrentPage] = useState(1);
  const [searchTerm, setSearchTerm] = useState('');
  const [roleFilter, setRoleFilter] = useState('all');

  const userForm = useForm(
    { name: '', email: '', role: 'user' },
    userValidationSchema
  );

  // Load users
  useEffect(() => {
    loadUsers();
  }, [currentPage, searchTerm, roleFilter]);

  const loadUsers = async () => {
    setLoading(true);
    try {
      const params = {
        page: currentPage,
        limit: 10,
        search: searchTerm,
        ...(roleFilter !== 'all' && { role: roleFilter })
      };
      
      const response = await UserService.getUsers(params);
      setUsers(response.data);
    } catch (error) {
      console.error('Failed to load users:', error);
    } finally {
      setLoading(false);
    }
  };

  const handleCreateUser = () => {
    setEditingUser(null);
    userForm.values = { name: '', email: '', role: 'user' };
    setShowUserModal(true);
  };

  const handleEditUser = (user) => {
    setEditingUser(user);
    userForm.values = { ...user };
    setShowUserModal(true);
  };

  const handleSaveUser = async (userData) => {
    try {
      if (editingUser) {
        await UserService.updateUser(editingUser.id, userData);
      } else {
        await UserService.createUser(userData);
      }
      
      setShowUserModal(false);
      loadUsers();
    } catch (error) {
      console.error('Failed to save user:', error);
    }
  };

  const handleDeleteUser = async (userId) => {
    if (confirm('Are you sure you want to delete this user?')) {
      try {
        await UserService.deleteUser(userId);
        loadUsers();
      } catch (error) {
        console.error('Failed to delete user:', error);
      }
    }
  };

  const columns = [
    { key: 'id', title: 'ID', width: '80px' },
    { key: 'name', title: 'Name', sortable: true },
    { key: 'email', title: 'Email', sortable: true },
    {
      key: 'role',
      title: 'Role',
      render: (role) => (
        <Badge variant={role === 'admin' ? 'primary' : 'secondary'}>
          {role}
        </Badge>
      )
    },
    {
      key: 'actions',
      title: 'Actions',
      render: (_, user) => (
        <Flex gap="8px">
          <Button 
            size="sm" 
            variant="outline"
            onClick={() => handleEditUser(user)}
          >
            Edit
          </Button>
          <Button 
            size="sm" 
            variant="danger"
            onClick={() => handleDeleteUser(user.id)}
          >
            Delete
          </Button>
        </Flex>
      )
    }
  ];

  return (
    <Container>
      <Grid columns="1fr" gap="24px">
        {/* Header */}
        <Card>
          <Flex justify="space-between" align="center">
            <h1>User Management Dashboard</h1>
            <Button onClick={handleCreateUser}>
              Add User
            </Button>
          </Flex>
        </Card>

        {/* Filters */}
        <Card>
          <Grid columns="1fr 200px" gap="16px">
            <Input
              placeholder="Search users..."
              value={searchTerm}
              onChange={(e) => setSearchTerm(e.target.value)}
            />
            <Select
              options={[
                { value: 'all', label: 'All Roles' },
                { value: 'admin', label: 'Admin' },
                { value: 'user', label: 'User' }
              ]}
              value={roleFilter}
              onChange={setRoleFilter}
            />
          </Grid>
        </Card>

        {/* Data Table */}
        <Card>
          <Table
            data={users}
            columns={columns}
            loading={loading}
            pagination={{
              page: currentPage,
              pageSize: 10,
              total: 100, // from API response
              onPageChange: setCurrentPage
            }}
          />
        </Card>
      </Grid>

      {/* User Modal */}
      <Modal
        isOpen={showUserModal}
        onClose={() => setShowUserModal(false)}
        title={editingUser ? 'Edit User' : 'Create User'}
        size="md"
      >
        <form onSubmit={(e) => {
          e.preventDefault();
          userForm.handleSubmit(handleSaveUser);
        }}>
          <Grid columns="1fr" gap="16px">
            <Input
              label="Name"
              {...userForm.getFieldProps('name')}
              required
            />
            <Input
              label="Email"
              type="email"
              {...userForm.getFieldProps('email')}
              required
            />
            <Select
              label="Role"
              options={[
                { value: 'user', label: 'User' },
                { value: 'admin', label: 'Admin' }
              ]}
              value={userForm.values.role}
              onChange={(value) => userForm.setValue('role', value)}
            />
            
            <Flex gap="12px" justify="flex-end">
              <Button 
                type="button"
                variant="outline"
                onClick={() => setShowUserModal(false)}
              >
                Cancel
              </Button>
              <Button 
                type="submit"
                loading={userForm.isSubmitting}
              >
                {editingUser ? 'Update' : 'Create'}
              </Button>
            </Flex>
          </Grid>
        </form>
      </Modal>
    </Container>
  );
}

export default Dashboard;
```

### Responsive Layout Example

```jsx
// components/ResponsiveLayout.jsx
import React from 'react';
import {
  Container,
  Grid,
  Card,
  Flex,
  Button
} from 'your-component-library';

function ResponsiveLayout() {
  return (
    <Container>
      {/* Mobile-first responsive grid */}
      <Grid 
        columns={{
          base: '1fr',
          md: 'repeat(2, 1fr)',
          lg: 'repeat(3, 1fr)',
          xl: 'repeat(4, 1fr)'
        }}
        gap="16px"
      >
        {/* Feature cards */}
        {features.map((feature, index) => (
          <Card key={index} variant="outlined" hoverable>
            <Flex direction="column" gap="12px">
              <div className="feature-icon">
                {feature.icon}
              </div>
              <h3>{feature.title}</h3>
              <p>{feature.description}</p>
              <Button variant="outline" size="sm">
                Learn More
              </Button>
            </Flex>
          </Card>
        ))}
      </Grid>

      {/* Complex responsive layout */}
      <Grid
        columns={{
          base: '1fr',
          lg: '300px 1fr',
          xl: '350px 1fr 250px'
        }}
        gap="24px"
        style={{ marginTop: '48px' }}
      >
        {/* Sidebar */}
        <Card>
          <h2>Sidebar</h2>
          <p>Navigation or filters</p>
        </Card>

        {/* Main content */}
        <Card>
          <h2>Main Content</h2>
          <p>Primary content area</p>
        </Card>

        {/* Right panel (hidden on smaller screens) */}
        <Card className="hidden xl:block">
          <h2>Additional Info</h2>
          <p>Secondary content</p>
        </Card>
      </Grid>
    </Container>
  );
}
```

## 📋 Common Patterns

### Error Boundary with Retry

```jsx
// components/ErrorBoundary.jsx
import React from 'react';
import { Card, Button, Flex } from 'your-component-library';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
    
    // Log to error reporting service
    if (this.props.onError) {
      this.props.onError(error, errorInfo);
    }
  }

  handleRetry = () => {
    this.setState({ hasError: false, error: null });
  };

  render() {
    if (this.state.hasError) {
      return (
        <Card variant="outlined" style={{ textAlign: 'center', padding: '40px' }}>
          <h2>Something went wrong</h2>
          <p>We encountered an unexpected error. Please try again.</p>
          {this.props.showDetails && (
            <details style={{ marginTop: '16px' }}>
              <summary>Error Details</summary>
              <pre style={{ textAlign: 'left', overflow: 'auto' }}>
                {this.state.error?.stack}
              </pre>
            </details>
          )}
          <Flex gap="12px" justify="center" style={{ marginTop: '24px' }}>
            <Button variant="outline" onClick={() => window.location.reload()}>
              Reload Page
            </Button>
            <Button onClick={this.handleRetry}>
              Try Again
            </Button>
          </Flex>
        </Card>
      );
    }

    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary onError={(error, errorInfo) => {
      // Report to error tracking service
      console.error('Application error:', error, errorInfo);
    }}>
      <Dashboard />
    </ErrorBoundary>
  );
}
```

### Loading States and Skeletons

```jsx
// components/LoadingStates.jsx
import React from 'react';
import { Card, Grid, Flex } from 'your-component-library';

export function TableSkeleton({ rows = 5 }) {
  return (
    <div className="animate-pulse">
      {Array.from({ length: rows }, (_, i) => (
        <Flex key={i} gap="16px" style={{ padding: '12px 0' }}>
          <div className="h-4 bg-gray-200 rounded w-16"></div>
          <div className="h-4 bg-gray-200 rounded flex-1"></div>
          <div className="h-4 bg-gray-200 rounded w-32"></div>
          <div className="h-4 bg-gray-200 rounded w-20"></div>
        </Flex>
      ))}
    </div>
  );
}

export function CardSkeleton() {
  return (
    <Card className="animate-pulse">
      <div className="h-4 bg-gray-200 rounded w-3/4 mb-4"></div>
      <div className="h-4 bg-gray-200 rounded w-1/2 mb-2"></div>
      <div className="h-4 bg-gray-200 rounded w-2/3"></div>
    </Card>
  );
}

export function GridSkeleton({ items = 6 }) {
  return (
    <Grid columns="repeat(auto-fit, minmax(300px, 1fr))" gap="16px">
      {Array.from({ length: items }, (_, i) => (
        <CardSkeleton key={i} />
      ))}
    </Grid>
  );
}

// Usage with suspense
function DataGrid() {
  const [loading, setLoading] = useState(true);
  const [data, setData] = useState([]);

  useEffect(() => {
    loadData().then(result => {
      setData(result);
      setLoading(false);
    });
  }, []);

  if (loading) {
    return <TableSkeleton rows={10} />;
  }

  return <Table data={data} columns={columns} />;
}
```

## ✅ Best Practices

### Performance Optimization

```jsx
// components/OptimizedTable.jsx
import React, { useMemo, useCallback } from 'react';
import { Table, Input } from 'your-component-library';
import { debounce, sortBy, filterBy } from 'your-package/utils';

function OptimizedTable({ data, columns }) {
  const [search, setSearch] = useState('');
  const [sortConfig, setSortConfig] = useState({ key: null, direction: 'asc' });

  // Memoize expensive operations
  const processedData = useMemo(() => {
    let result = data;

    // Filter
    if (search) {
      result = filterBy(result, {
        $or: columns.map(col => ({
          [col.key]: value => 
            String(value).toLowerCase().includes(search.toLowerCase())
        }))
      });
    }

    // Sort
    if (sortConfig.key) {
      result = sortBy(result, sortConfig.key, sortConfig.direction);
    }

    return result;
  }, [data, search, sortConfig, columns]);

  // Debounced search
  const debouncedSearch = useMemo(
    () => debounce((term) => setSearch(term), 300),
    []
  );

  const handleSort = useCallback((key) => {
    setSortConfig(prev => ({
      key,
      direction: prev.key === key && prev.direction === 'asc' ? 'desc' : 'asc'
    }));
  }, []);

  return (
    <div>
      <Input
        placeholder="Search..."
        onChange={(e) => debouncedSearch(e.target.value)}
      />
      <Table
        data={processedData}
        columns={columns}
        onSort={handleSort}
        sortConfig={sortConfig}
      />
    </div>
  );
}

// Memoize the component
export default React.memo(OptimizedTable);
```

### Accessibility Implementation

```jsx
// components/AccessibleModal.jsx
import React, { useEffect, useRef } from 'react';
import { Modal, Button } from 'your-component-library';

function AccessibleModal({ isOpen, onClose, title, children }) {
  const initialFocusRef = useRef();
  const titleId = `modal-title-${Math.random().toString(36)}`;
  const descId = `modal-desc-${Math.random().toString(36)}`;

  // Focus management
  useEffect(() => {
    if (isOpen && initialFocusRef.current) {
      initialFocusRef.current.focus();
    }
  }, [isOpen]);

  // Keyboard handling
  useEffect(() => {
    const handleKeyDown = (e) => {
      if (e.key === 'Escape' && isOpen) {
        onClose();
      }
    };

    if (isOpen) {
      document.addEventListener('keydown', handleKeyDown);
      return () => document.removeEventListener('keydown', handleKeyDown);
    }
  }, [isOpen, onClose]);

  return (
    <Modal
      isOpen={isOpen}
      onClose={onClose}
      aria-labelledby={titleId}
      aria-describedby={descId}
      role="dialog"
      aria-modal="true"
    >
      <h2 id={titleId}>{title}</h2>
      <div id={descId}>{children}</div>
      
      <Button
        ref={initialFocusRef}
        onClick={onClose}
        aria-label="Close modal"
      >
        Close
      </Button>
    </Modal>
  );
}
```

## 🔍 Troubleshooting

### Common Issues and Solutions

#### API Request Failures

```javascript
// utils/apiErrorHandler.js
export function handleApiError(error) {
  // Network errors
  if (!error.response) {
    return {
      message: 'Network error. Please check your connection.',
      type: 'NETWORK_ERROR',
      retryable: true
    };
  }

  // HTTP errors
  const { status, data } = error.response;
  
  switch (status) {
    case 400:
      return {
        message: data.message || 'Invalid request',
        type: 'VALIDATION_ERROR',
        details: data.errors,
        retryable: false
      };
      
    case 401:
      // Redirect to login
      window.location.href = '/login';
      return {
        message: 'Authentication required',
        type: 'AUTH_ERROR',
        retryable: false
      };
      
    case 403:
      return {
        message: 'You do not have permission to perform this action',
        type: 'PERMISSION_ERROR',
        retryable: false
      };
      
    case 429:
      return {
        message: 'Too many requests. Please try again later.',
        type: 'RATE_LIMIT_ERROR',
        retryable: true,
        retryAfter: error.response.headers['retry-after']
      };
      
    case 500:
      return {
        message: 'Server error. Please try again later.',
        type: 'SERVER_ERROR',
        retryable: true
      };
      
    default:
      return {
        message: data.message || 'An unexpected error occurred',
        type: 'UNKNOWN_ERROR',
        retryable: false
      };
  }
}
```

#### Component Rendering Issues

```jsx
// Debug component for troubleshooting
function DebugWrapper({ children, label }) {
  useEffect(() => {
    console.log(`${label} mounted`);
    return () => console.log(`${label} unmounted`);
  }, [label]);

  return (
    <div data-debug={label}>
      {children}
    </div>
  );
}

// Performance monitoring
function useRenderCount(componentName) {
  const renderCount = useRef(0);
  
  useEffect(() => {
    renderCount.current++;
    console.log(`${componentName} rendered ${renderCount.current} times`);
  });
  
  return renderCount.current;
}
```

#### Memory Leak Prevention

```javascript
// utils/cleanup.js
export function useCleanup() {
  const cleanupFunctions = useRef([]);

  const addCleanup = useCallback((fn) => {
    cleanupFunctions.current.push(fn);
  }, []);

  useEffect(() => {
    return () => {
      cleanupFunctions.current.forEach(fn => fn());
      cleanupFunctions.current = [];
    };
  }, []);

  return addCleanup;
}

// Usage
function MyComponent() {
  const addCleanup = useCleanup();

  useEffect(() => {
    const subscription = subscribeToData(handleData);
    addCleanup(() => subscription.unsubscribe());

    const timer = setInterval(updateData, 1000);
    addCleanup(() => clearInterval(timer));

    const ws = new WebSocket(url);
    addCleanup(() => ws.close());
  }, [addCleanup]);

  return <div>Content</div>;
}
```

---

**Need more examples?** Check out our [GitHub repository](https://github.com/your-org/examples) for complete working examples and sample applications.