# Requirements: CodeMentor AI

## Overview

AI-powered learning platform for beginner developers. Provides code explanations, interactive chat assistance, and learning progress tracking. Built for a 48-hour hackathon.

**Target Users:** Beginner developers and coding students  
**Core Features:** Code analysis, AI chat, history tracking  
**Tech Stack:** Django REST + React + OpenAI GPT-3.5  
**Timeline:** 48 hours, deploy by hour 44

## Key Terms

- **User**: Registered beginner developer or student
- **Code_Snippet**: Source code submitted for AI analysis
- **Chat_Session**: Conversation thread with AI assistant
- **Supported_Language**: Python, JavaScript, Java, or C++

## Requirements

### 1. User Authentication

**User Story:** As a new user, I want to create an account and securely log in to access personalized features.

**Acceptance Criteria:**

1. Valid registration (email, username, password) creates account and returns JWT tokens
2. Duplicate email/username returns validation error
3. Valid login returns access + refresh tokens
4. Invalid credentials return authentication error
5. Valid refresh token issues new access token
6. Invalid/expired refresh token requires re-authentication
7. Passwords are hashed before storage
8. Valid JWT grants access to protected endpoints
9. Invalid/expired JWT returns authorization error

### 2. Code Analysis

**User Story:** As a beginner developer, I want to submit code and receive detailed explanations to understand how it works.

**Acceptance Criteria:**

1. Code in supported language (Python/JS/Java/C++) generates line-by-line explanation
2. Unsupported language returns error with supported language list
3. Empty code snippet returns validation error
4. Successful analysis stores code, language, and explanation with user association
5. Code history returns user's snippets ordered by date (newest first)
6. User can retrieve their own snippets by ID
7. User cannot access other users' snippets (returns 403)
8. User can delete their own snippets
9. System supports Python, JavaScript, Java, and C++
10. AI API failures don't store incomplete data

### 3. Interactive Chat

**User Story:** As a student, I want to chat with an AI assistant about programming questions for immediate help.

**Acceptance Criteria:**

1. Creating chat session returns unique session ID
2. Sending message stores user message, generates AI response, stores both
3. Sending to non-existent session returns error
4. User cannot send messages to other users' sessions (returns 403)
5. User's chat sessions returned ordered by last update (newest first)
6. Session messages returned in chronological order
7. Chat maintains conversation context within each session
8. AI API failures don't store incomplete conversation data
9. Messages stored with role (user/assistant) and timestamp

### 4. Rate Limiting

**User Story:** As admin, I want to control API usage to stay within OpenAI free tier budget.

**Acceptance Criteria:**

1. System logs warnings when approaching API credit limit
2. Excessive requests trigger rate limiting with error response
3. System tracks total API usage and remaining credits
4. Exhausted credits prevent further AI API calls with error message

### 5. Dashboard & History

**User Story:** As a user, I want to view past code explanations and chats from a dashboard.

**Acceptance Criteria:**

1. Dashboard displays overview of recent snippets and chat sessions
2. History page shows all saved snippets with preview info
3. Clicking history item displays complete snippet and explanation
4. Deleting history item removes it and updates display
5. Copy-to-clipboard functionality for snippets and explanations

### 6. Multi-Language Support

**User Story:** As a developer learning multiple languages, I want accurate analysis for Python, JS, Java, and C++.

**Acceptance Criteria:**

1. Language validated against supported list (python, javascript, java, cpp)
2. Code processed with language-specific context
3. Language-appropriate syntax highlighting in editor
4. Language identifier stored with code snippets

### 7. Responsive UI

**User Story:** As a user on different devices, I want the interface to work well on desktop, tablet, and mobile.

**Acceptance Criteria:**

1. Mobile displays optimized layout with appropriate touch targets
2. Tablet displays responsive layout utilizing available space
3. Desktop displays full-featured interface
4. No horizontal scrolling on any viewport size
5. Code editor accessible on mobile devices

### 8. Data Persistence

**User Story:** As a user, I want my data reliably stored so I don't lose learning progress.

**Acceptance Criteria:**

1. Content persisted to database before confirming success
2. Database failures return error (don't report success)
3. Referential integrity maintained between users and their data
4. User deletion cascades to all associated data
5. Timestamps recorded for all entities
6. PostgreSQL in production, SQLite in development

### 9. Error Handling

**User Story:** As a user, I want clear error messages when something goes wrong.

**Acceptance Criteria:**

1. Validation errors indicate which field failed and why
2. Authentication errors don't expose security details
3. Authorization errors indicate insufficient permissions
4. Server errors return generic message, log details for debugging
5. AI API failures return user-friendly "service unavailable" message
6. Appropriate HTTP status codes (200, 201, 400, 401, 403, 404, 500)
7. Successful operations return confirmation with relevant data

### 10. Code Editor

**User Story:** As a user, I want a professional code editor with syntax highlighting.

**Acceptance Criteria:**

1. Code explanation page displays Monaco Editor
2. Syntax highlighting based on selected language
3. Changing language updates editor highlighting
4. Common editor shortcuts supported (copy, paste, undo, redo)
5. Pasted code preserves formatting and indentation

### 11. Security

**User Story:** As a security-conscious user, I want my session secure with automatic expiration.

**Acceptance Criteria:**

1. JWT token expires after 1 hour
2. Refresh token expires after 7 days
3. Expired JWT requires refresh or re-authentication
4. Secure HTTP-only cookies for token storage where applicable
5. CORS policies restrict API access to authorized origins
6. All user input validated to prevent injection attacks

### 12. Deployment

**User Story:** As a developer, I want clear environment separation for reliable deployment.

**Acceptance Criteria:**

1. Separate configuration for dev, staging, and production
2. Sensitive config (API keys, DB credentials) from environment variables
3. SQLite for development, PostgreSQL for production
4. Database migration scripts for schema management
5. Static frontend assets served efficiently in production
6. Deployable within 44 hours of hackathon start


---

## Why CodeMentor AI? (Competitive Differentiation)

This table shows how CodeMentor AI stands out from existing solutions for beginner developers:

| Feature | CodeMentor AI | ChatGPT | GitHub Copilot | Stack Overflow |
|---------|---------------|---------|----------------|----------------|
| **Code Explanation** | ✅ Specialized for learning | ⚠️ Generic answers | ⚠️ Auto-complete focus | ❌ Community Q&A only |
| **Learning History** | ✅ Saves all explanations | ❌ No history | ❌ No history | ❌ No personal tracking |
| **Language Support** | ✅ 4 beginner languages | ✅ All languages | ✅ All languages | ✅ All languages |
| **Interactive Chat** | ✅ Context-aware tutoring | ✅ General chat | ⚠️ Code suggestions | ❌ No chat |
| **Beginner-Focused** | ✅ Designed for learners | ⚠️ All skill levels | ⚠️ Professional devs | ⚠️ Mixed audience |
| **Progress Tracking** | ✅ Dashboard with history | ❌ No tracking | ❌ No tracking | ❌ No tracking |
| **Cost** | ✅ Free tier available | ⚠️ Limited free | ⚠️ Paid subscription | ✅ Free |
| **Built-in Editor** | ✅ Monaco Editor | ❌ Text box only | ✅ VS Code native | ❌ Text box only |

### Key Advantages for Hackathon Judges:

1. **Focused Solution**: Unlike ChatGPT's general-purpose approach, CodeMentor AI is specifically designed for beginner developers learning to code

2. **Learning Journey**: Tracks user progress with saved explanations and chat history - something no competitor offers

3. **All-in-One Platform**: Combines code editor, AI explanations, and interactive chat in one place (competitors require multiple tools)

4. **Beginner-First Design**: Explanations are tailored for learning, not just solving problems quickly

5. **Hackathon-Ready**: Built within 48 hours with clear MVP scope, deployable, and scalable architecture
