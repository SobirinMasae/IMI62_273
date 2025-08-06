# API Documentation

This section contains complete documentation for all public APIs.

## 🌐 REST API

### Base URL
```
https://api.your-domain.com/v1
```

### Authentication

#### API Key Authentication
```javascript
const headers = {
  'Authorization': 'Bearer YOUR_API_KEY',
  'Content-Type': 'application/json'
};
```

#### OAuth 2.0
```javascript
const headers = {
  'Authorization': 'Bearer ACCESS_TOKEN',
  'Content-Type': 'application/json'
};
```

### Endpoints

#### Users API

##### GET /users
Retrieve a list of users.

**Parameters:**
- `page` (integer, optional): Page number (default: 1)
- `limit` (integer, optional): Items per page (default: 10, max: 100)
- `search` (string, optional): Search query
- `status` (string, optional): Filter by status (`active`, `inactive`)

**Request Example:**
```javascript
const response = await fetch('/api/v1/users?page=1&limit=20&status=active', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

**Response Example:**
```json
{
  "data": [
    {
      "id": "user_123",
      "name": "John Doe",
      "email": "john@example.com",
      "status": "active",
      "created_at": "2023-01-15T10:30:00Z",
      "updated_at": "2023-01-20T14:45:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "has_more": true
  }
}
```

##### POST /users
Create a new user.

**Request Body:**
```json
{
  "name": "string (required)",
  "email": "string (required, valid email)",
  "password": "string (required, min 8 chars)",
  "role": "string (optional, default: 'user')"
}
```

**Request Example:**
```javascript
const response = await fetch('/api/v1/users', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'Jane Smith',
    email: 'jane@example.com',
    password: 'securePassword123',
    role: 'admin'
  })
});

const user = await response.json();
```

##### GET /users/{id}
Retrieve a specific user by ID.

**Parameters:**
- `id` (string, required): User ID

**Request Example:**
```javascript
const response = await fetch('/api/v1/users/user_123', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY'
  }
});

const user = await response.json();
```

##### PUT /users/{id}
Update an existing user.

**Parameters:**
- `id` (string, required): User ID

**Request Body:** (partial update supported)
```json
{
  "name": "string (optional)",
  "email": "string (optional)",
  "status": "string (optional)"
}
```

##### DELETE /users/{id}
Delete a user.

**Parameters:**
- `id` (string, required): User ID

**Request Example:**
```javascript
const response = await fetch('/api/v1/users/user_123', {
  method: 'DELETE',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY'
  }
});
```

### Error Handling

All API endpoints return consistent error responses:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request parameters",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ],
    "request_id": "req_abc123"
  }
}
```

**Common Error Codes:**
- `400` - Bad Request (VALIDATION_ERROR, INVALID_REQUEST)
- `401` - Unauthorized (AUTHENTICATION_REQUIRED, INVALID_TOKEN)
- `403` - Forbidden (INSUFFICIENT_PERMISSIONS)
- `404` - Not Found (RESOURCE_NOT_FOUND)
- `429` - Too Many Requests (RATE_LIMIT_EXCEEDED)
- `500` - Internal Server Error (INTERNAL_ERROR)

### Rate Limiting

- **Rate Limit:** 1000 requests per hour per API key
- **Headers:** 
  - `X-RateLimit-Limit`: Total requests allowed per hour
  - `X-RateLimit-Remaining`: Requests remaining in current window
  - `X-RateLimit-Reset`: Unix timestamp when limit resets

## 🔌 GraphQL API

### Endpoint
```
https://api.your-domain.com/graphql
```

### Schema Example

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  status: UserStatus!
  posts: [Post!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  published: Boolean!
  createdAt: DateTime!
}

enum UserStatus {
  ACTIVE
  INACTIVE
  SUSPENDED
}

type Query {
  user(id: ID!): User
  users(
    first: Int = 10
    after: String
    filter: UserFilter
  ): UserConnection!
  
  post(id: ID!): Post
  posts(
    first: Int = 10
    after: String
    filter: PostFilter
  ): PostConnection!
}

type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
  deleteUser(id: ID!): Boolean!
  
  createPost(input: CreatePostInput!): Post!
  updatePost(id: ID!, input: UpdatePostInput!): Post!
  deletePost(id: ID!): Boolean!
}
```

### Query Examples

```javascript
// Fetch user with posts
const query = `
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
      email
      status
      posts {
        id
        title
        published
        createdAt
      }
    }
  }
`;

const response = await fetch('/graphql', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    query,
    variables: { id: 'user_123' }
  })
});
```

### Mutation Examples

```javascript
// Create a new user
const mutation = `
  mutation CreateUser($input: CreateUserInput!) {
    createUser(input: $input) {
      id
      name
      email
      status
      createdAt
    }
  }
`;

const response = await fetch('/graphql', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    query: mutation,
    variables: {
      input: {
        name: 'John Doe',
        email: 'john@example.com',
        password: 'securePassword123'
      }
    }
  })
});
```

## 🔌 WebSocket API

### Connection
```javascript
const ws = new WebSocket('wss://api.your-domain.com/ws');

// Authentication
ws.onopen = () => {
  ws.send(JSON.stringify({
    type: 'auth',
    token: 'YOUR_API_KEY'
  }));
};
```

### Message Format
```javascript
// Incoming messages
{
  "type": "message_type",
  "data": { /* message data */ },
  "timestamp": "2023-01-15T10:30:00Z"
}

// Outgoing messages
{
  "type": "action_type",
  "data": { /* action data */ }
}
```

### Event Types

#### User Events
```javascript
// Subscribe to user events
ws.send(JSON.stringify({
  type: 'subscribe',
  data: {
    channel: 'users',
    filters: { status: 'active' }
  }
}));

// User created event
{
  "type": "user.created",
  "data": {
    "user": {
      "id": "user_123",
      "name": "John Doe",
      "email": "john@example.com"
    }
  },
  "timestamp": "2023-01-15T10:30:00Z"
}
```

## 📊 Client Libraries

### JavaScript/TypeScript
```bash
npm install @your-package/api-client
```

```javascript
import { ApiClient } from '@your-package/api-client';

const client = new ApiClient({
  apiKey: 'YOUR_API_KEY',
  baseUrl: 'https://api.your-domain.com/v1'
});

// Usage
const users = await client.users.list({ limit: 20 });
const user = await client.users.create({
  name: 'John Doe',
  email: 'john@example.com'
});
```

### Python
```bash
pip install your-package-api-client
```

```python
from your_package import ApiClient

client = ApiClient(api_key='YOUR_API_KEY')

# Usage
users = client.users.list(limit=20)
user = client.users.create(name='John Doe', email='john@example.com')
```

## 🧪 Testing

### Postman Collection
Download our [Postman Collection](./postman_collection.json) for easy API testing.

### cURL Examples
```bash
# Get users
curl -X GET "https://api.your-domain.com/v1/users" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Create user
curl -X POST "https://api.your-domain.com/v1/users" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "securePassword123"
  }'
```

---

**Need Help?** Check our [API Support Guide](../guides/api-support.md) or contact support.