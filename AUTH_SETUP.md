# YEMS Authentication Setup

## Overview

YEMS (Yeshua Educational Management System) now supports **hybrid authentication**:
- **Remote API Authentication** (JWT Bearer tokens)
- **Local Storage Fallback** (when API is unavailable)

## API Configuration

### Base URL
```
https://kennedi-ungnostic-unconvulsively.ngrok-free.dev/api
```

### Authentication Endpoints

| Method | Endpoint | Description | Request Body |
|--------|----------|-------------|--------------|
| `POST` | `/auth/login` | User login | `{"email": "user@example.com", "password": "min8chars"}` |
| `POST` | `/auth/refresh` | Refresh token | `{"refreshToken": "min32chars"}` |
| `GET` | `/auth/me` | Get current user | Bearer token required |

### Authentication Flow

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│   User      │────▶│  Login API   │────▶│   JWT       │
│  Credentials│     │  /auth/login │     │   Token     │
└─────────────┘     └──────────────┘     └─────────────┘
                                              │
                                              ▼
                                       ┌──────────────┐
                                       │  Store in    │
                                       │  Session/    │
                                       │  LocalStorage│
                                       └──────────────┘
                                              │
                                              ▼
                                       ┌──────────────┐
                                       │  Protected   │
                                       │  API Requests│
                                       │  Bearer Token│
                                       └──────────────┘
```

## Configuration

### `js/config.js`

```javascript
window.YEMS_CONFIG = {
  API: {
    baseUrl: 'https://kennedi-ungnostic-unconvulsively.ngrok-free.dev/api',
    timeout: 30000,
    useLocalStorage: true,  // Fallback when API unavailable
    debug: false
  },
  AUTH: {
    tokenRefreshThreshold: 300,
    autoLogoutOnExpiry: true
  }
};
```

## Usage

### Login

```javascript
// Async login with remote API
const result = Auth.login(email, password, rememberMe);

// Result is either:
// 1. { ok: true, pending: true } - Async login in progress
// 2. { ok: true, user: {...}, redirect: 'home' } - Local login success
// 3. { ok: false, err: 'error message' } - Login failed
```

### Get Current User

```javascript
const user = Auth.current();
// Returns: { id, name, email, role, token, ... }
```

### Get Auth Token

```javascript
const token = Auth.getToken();
// Use in API requests: Authorization: Bearer <token>
```

### Protected API Request

```javascript
// API automatically includes auth token
const exams = await API.exams.getAll();

// Or manually:
const token = Auth.getToken();
const response = await fetch('/api/protected', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

### Logout

```javascript
Auth.logout();
// Clears all tokens and session data
```

## Token Storage

| Storage | Key | Purpose |
|---------|-----|---------|
| SessionStorage | `yep_session` | User session data |
| SessionStorage | `yep_token` | JWT access token |
| SessionStorage | `yep_refresh_token` | JWT refresh token |
| LocalStorage | Same keys | When "Remember me" is checked |

## Role-Based Access

```javascript
const ROLE_ROUTES = {
  student: 'home',
  teacher: 'teacher-home',
  admin: 'admin-home',
  platform_admin: 'admin-home',
  technician: 'technician-home'
};
```

### Guard Protected Routes

```javascript
// Check if user is logged in
if (!Auth.guard()) {
  Router.go('login');
  return;
}

// Check specific role
if (!Auth.guard(['teacher'])) {
  // Redirect if not teacher
  return;
}
```

## API Modules

### Auth
```javascript
API.auth.login(email, password)
API.auth.logout()
API.auth.refreshToken(token)
API.auth.me()
```

### Admin
```javascript
API.admin.getUsers()
API.admin.createUser({ email, name, password, role })
API.admin.updateUser(id, { email, name })
API.admin.deleteUser(id)
API.admin.updateUserRole(id, role)
```

### Teacher
```javascript
API.teacher.getExams()
```

### Student
```javascript
API.student.getExams()
```

### Exams
```javascript
API.exams.getAll()
API.exams.getById(id)
API.exams.create(data)
API.exams.start(examId, studentId)
API.exams.submit(examId, studentId, answers)
```

### Notes
```javascript
API.notes.getAll()
API.notes.create({ title, content, subjectId })
API.notes.delete(id)
```

### Health
```javascript
API.health.check()
API.health.getMetrics()
```

## Error Handling

```javascript
try {
  const result = await API.auth.login(email, password);
  // Success
} catch (error) {
  // Error handling
  console.error('Login failed:', error.message);
  
  // Fallback to local storage is automatic
  if (error.message.includes('timeout')) {
    // Use local credentials
  }
}
```

## Debug Mode

Enable debug logging:

```javascript
// In config.js
API: {
  debug: true
}

// Or at runtime
API.configure({ debug: true });
```

## Testing

### Test Login with Demo Credentials

| Role | Email | Password |
|------|-------|----------|
| Student | student@yeshua.edu | student123 |
| Teacher | teacher@yeshua.edu | teacher |
| Admin | admin@yeshua.edu | admin |

### Test API Connection

```javascript
const connected = await API.checkConnection();
console.log('API Connected:', connected);
```

### Test Auth Flow

```javascript
// 1. Login
Auth.login('teacher@yeshua.edu', 'teacher', false);

// 2. Wait for async auth
setTimeout(() => {
  const user = Auth.current();
  console.log('Logged in as:', user);
  
  // 3. Make authenticated request
  API.exams.getAll().then(result => {
    console.log('Exams:', result);
  });
}, 1000);
```

## File Structure

```
YEMS/
├── js/
│   ├── config.js      # API configuration
│   ├── auth.js        # Authentication module
│   ├── api.js         # API service layer
│   ├── sync.js        # Data synchronization
│   ├── app.js         # Student portal
│   ├── teacher.js     # Teacher portal
│   └── admin.js       # Admin portal
├── index.html         # Student portal
├── teacher.html       # Teacher portal
└── admin.html         # Admin portal
```

## Security Considerations

1. **Token Security**: Tokens are stored in browser storage (session/local)
2. **HTTPS**: Always use HTTPS in production
3. **Token Expiry**: Implement token refresh before expiry
4. **Logout**: Clear all tokens on logout
5. **Input Validation**: Validate all user inputs

## Troubleshooting

### Login Not Working
1. Check browser console for errors
2. Verify API is accessible: `API.checkConnection()`
3. Check network tab for failed requests
4. Ensure credentials are correct

### Token Expired
- Automatic refresh is attempted
- If refresh fails, user must re-login
- Check `Auth.ensureValidToken()`

### API Unavailable
- System automatically falls back to local storage
- Data syncs when API becomes available again
- Check `SyncService.getStatus()`

## Support

For issues or questions, check:
1. Browser console logs
2. Network tab in DevTools
3. API documentation: `/api/docs`
