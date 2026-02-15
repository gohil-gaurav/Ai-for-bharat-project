# Design: CodeMentor AI

## Overview

Full-stack web app with Django REST backend and React TypeScript frontend. Provides AI-powered code analysis and chat assistance through RESTful API.

**Architecture:** 3-layer separation
- Presentation: React + TypeScript
- API: Django REST + JWT auth
- Data: PostgreSQL/SQLite + Django ORM

## System Architecture

```
┌─────────────────────────────────────────┐
│   User Interface Layer (React)          │
│   - Monaco Editor                       │
│   - Dashboard & History                 │
│   - Chat Interface                      │
└──────────────┬──────────────────────────┘
               │ HTTPS/REST API
┌──────────────┴──────────────────────────┐
│   API Gateway (Django REST Framework)   │
│   ┌────────┬──────────┬────────┬──────┐│
│   │  Auth  │   Code   │  Chat  │ Doc  ││
│   │Service │ Service  │Service │Service││
│   └────────┴──────────┴────────┴──────┘│
└──────────────┬──────────────────────────┘
               │
      ┌────────┴────────┐
      │                 │
┌─────▼──────┐   ┌─────▼──────┐
│ PostgreSQL │   │  AI/ML     │
│ Database   │   │  Engine    │
│            │   │ (OpenAI)   │
└────────────┘   └────────────┘
```

## Technology Stack

**Backend:** Python 3.10+, Django 4.2+, DRF 3.14+, JWT auth, OpenAI SDK  
**Frontend:** React 18+, TypeScript 5+, Vite, React Router, Axios, Monaco Editor, Tailwind  
**Database:** PostgreSQL (prod), SQLite (dev)  
**Deployment:** Railway/Render (backend), Vercel/Netlify (frontend)

## API Endpoints

### Authentication
```
POST   /api/auth/register      - Create account
POST   /api/auth/login         - Login
POST   /api/auth/token/refresh - Refresh token
GET    /api/auth/user          - Get current user
```

### Code Analysis
```
POST   /api/code/explain       - Analyze code
GET    /api/code/history       - List snippets
GET    /api/code/history/:id   - Get snippet
DELETE /api/code/history/:id   - Delete snippet
```

### Chat
```
POST   /api/chat/sessions         - Create session
GET    /api/chat/sessions         - List sessions
POST   /api/chat/sessions/:id/message - Send message
GET    /api/chat/sessions/:id/messages - Get messages
```

## Database Schema

```
┌─────────────┐
│    User     │
├─────────────┤
│ id (PK)     │
│ email (UK)  │
│ username(UK)│
│ password    │
│ created_at  │
└──────┬──────┘
       │
       │ 1:N
       │
   ┌───┴────────────────┬──────────────┐
   │                    │              │
┌──▼──────────┐  ┌─────▼──────┐  ┌───▼────────┐
│CodeSnippet  │  │ChatSession │  │ APIUsage   │
├─────────────┤  ├────────────┤  ├────────────┤
│ id (PK)     │  │ id (PK)    │  │ id (PK)    │
│ user_id(FK) │  │ user_id(FK)│  │ user_id(FK)│
│ code_text   │  │ title      │  │ endpoint   │
│ language    │  │ created_at │  │ tokens     │
│ explanation │  │ updated_at │  │ cost       │
│ created_at  │  └─────┬──────┘  └────────────┘
└─────────────┘        │
                       │ 1:N
                       │
                 ┌─────▼──────┐
                 │ChatMessage │
                 ├────────────┤
                 │ id (PK)    │
                 │session_id  │
                 │ role       │
                 │ content    │
                 │ created_at │
                 └────────────┘
```

### Models

**User:** id, email (unique), username (unique), password_hash, created_at, last_login

**CodeSnippet:** id, user_id (FK), code_text, language (python/javascript/java/cpp), explanation (JSON), created_at

**ChatSession:** id, user_id (FK), title, created_at, updated_at

**ChatMessage:** id, session_id (FK), role (user/assistant), content, created_at

**APIUsage:** id, user_id (FK), endpoint, tokens_used, estimated_cost, created_at

## Correctness Properties

Properties are formal statements about system behavior that must hold true across all valid executions.

### Authentication (9 properties)

1. **Valid registration creates account with hashed password and tokens** - Registration with unique email/username stores hashed password and returns JWT tokens
2. **Duplicate email/username fails** - Registration with existing email/username returns validation error
3. **Valid login returns tokens** - Correct credentials return access + refresh tokens
4. **Invalid credentials rejected** - Wrong password/email returns auth error
5. **Token refresh works** - Valid refresh token returns new access token
6. **Invalid refresh rejected** - Invalid/expired refresh token requires re-auth
7. **Protected endpoints require auth** - Valid JWT grants access, invalid/missing JWT returns 401
8. **Tokens have correct expiration** - Access token expires in 1hr, refresh in 7 days
9. **Expired tokens require refresh** - Expired JWT rejected until refreshed

### Code Analysis (7 properties)

10. **Supported languages generate explanations** - Code in Python/JS/Java/C++ returns structured explanation
11. **Unsupported languages rejected** - Invalid language returns error with supported list
12. **Analysis persists to database** - Successful analysis stores code, language, explanation with user
13. **History ordered correctly** - User's snippets returned newest first, excludes others' snippets
14. **Retrieval respects ownership** - User can get own snippets, 403 for others' snippets
15. **Deletion respects ownership** - User can delete own snippets, 403 for others' snippets
16. **AI failures don't corrupt data** - API failures return error without storing incomplete data

### Chat (6 properties)

17. **Session creation returns ID** - Creating session returns unique identifier
18. **Messages stored with metadata** - User message and AI response stored with role, content, timestamp
19. **Operations respect ownership** - User can message/retrieve own sessions, 403 for others' sessions
20. **Sessions ordered by activity** - Sessions returned newest first by updated_at
21. **Messages in chronological order** - Messages returned oldest first by created_at
22. **AI failures don't corrupt chat** - API failures return error without storing incomplete data

### Rate Limiting & Usage (3 properties)

23. **Excessive requests throttled** - Requests exceeding limit return rate limit error
24. **Usage tracked** - AI calls record tokens and cost
25. **Exhausted credits prevent calls** - No credits returns service unavailable, no API call

### Data Integrity (3 properties)

26. **Referential integrity maintained** - Foreign keys valid and enforced
27. **Deletion cascades** - User deletion removes all associated data
28. **Entities have timestamps** - All entities have created_at, mutable have updated_at

### Error Handling (6 properties)

29. **Validation errors include details** - Validation failures indicate field and reason
30. **Auth errors don't leak info** - Auth failures don't reveal if email exists
31. **Authorization errors clear** - 403 responses indicate insufficient permissions
32. **Server errors safe** - Unexpected errors return generic message, log details
33. **Status codes match responses** - 2xx success, 4xx client error, 5xx server error
34. **Success returns confirmation** - Successful operations return confirmation + data

### Security (1 property)

35. **Input validated** - All user input validated and sanitized to prevent injection

## Error Handling

### Error Types & HTTP Status Codes

| Error Type | Status | Example Scenarios |
|------------|--------|-------------------|
| Validation | 400 | Empty email, weak password, unsupported language |
| Authentication | 401 | Invalid credentials, missing/expired token |
| Authorization | 403 | Accessing others' resources |
| Not Found | 404 | Non-existent snippet/session ID |
| Rate Limit | 429 | Too many requests |
| External Service | 502/503 | OpenAI API timeout/failure |
| Server Error | 500 | Unhandled exceptions |

### Response Format

```json
{
  "error": "Error Type",
  "message": "User-friendly description",
  "details": {
    "field_name": ["Error message"]
  }
}
```

## Testing Strategy

### Dual Approach: Unit + Property-Based Tests

**Unit Tests:** Specific examples, edge cases, component integration, UI behavior  
**Property Tests:** Universal properties across all inputs, randomized comprehensive coverage

Both are necessary and complementary.

### Backend Testing

**Framework:** pytest, pytest-django, Hypothesis (PBT), factory_boy, faker

**Property Test Format:**
```python
# Feature: codementor-ai, Property 1: Valid registration creates account with hashed password and tokens
@given(email=st.emails(), username=st.text(min_size=3), password=st.text(min_size=8))
@settings(max_examples=100)
def test_property_valid_registration(email, username, password):
    response = client.post('/api/auth/register', {...})
    assert response.status_code == 201
    assert 'access' in response.json()
    user = User.objects.get(email=email)
    assert user.check_password(password)  # Hashed correctly
```

### Frontend Testing

**Framework:** Vitest, React Testing Library, fast-check (PBT), MSW (API mocking)

**Example:**
```typescript
// Feature: codementor-ai, Property 33: HTTP status codes match response types
it('property: successful responses have 2xx status', () => {
  fc.assert(
    fc.asyncProperty(
      fc.record({email: fc.emailAddress(), ...}),
      async (userData) => {
        const response = await client.register(userData);
        expect(response.status).toBeGreaterThanOrEqual(200);
        expect(response.status).toBeLessThan(300);
      }
    ),
    { numRuns: 100 }
  );
});
```

### Coverage Goals

- Backend: 80% minimum
- Frontend: 70% minimum
- All 35 properties implemented
- All edge cases covered
- All major user flows tested

### Running Tests

```bash
# Backend
pytest                    # All tests
pytest --cov              # With coverage
pytest -k "property"      # Only property tests

# Frontend
npm run test              # All tests
npm run test:coverage     # With coverage
```
