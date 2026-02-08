# New Feature Development Workflow

## Overview

This workflow demonstrates how to use agents to develop a new feature from requirements to deployment.

## Scenario: Add Blog Commenting System

**Goal:** Add a commenting system to an existing blog platform
**Complexity:** Medium
**Estimated Time:** 2 weeks
**Team:** 2-3 developers

## Prerequisites

- Existing blog platform with posts
- User authentication system
- Database (PostgreSQL)
- Frontend (React)

## Workflow Steps

### Step 1: Requirements Analysis

**Agent:** Documentation Agent

**Task:** Analyze and document requirements

**Input:**
```
Analyze the requirements for a blog commenting system:
- Users can comment on blog posts
- Comments can be nested (replies)
- Comments require moderation
- Users can edit/delete their own comments
- Admin can moderate all comments
- Real-time comment updates
- Spam prevention
```

**Output:**
- Requirements document
- User stories
- Acceptance criteria
- Technical constraints

**Deliverable:** `docs/requirements/commenting-system.md`

---

### Step 2: System Design

**Agent:** Backend Architecture Agent

**Task:** Design the commenting system architecture

**Input:**
```
Design a commenting system with these requirements:
- Nested comments (up to 3 levels)
- Real-time updates using WebSocket
- Moderation workflow
- Spam detection
- Existing auth system integration
- PostgreSQL database
```

**Output:**
- Architecture diagram
- Component interactions
- Data flow design
- Technology recommendations
- Integration points

**Deliverable:** `docs/architecture/commenting-system.md`

**Key Decisions:**
- Use adjacency list model for nested comments
- WebSocket for real-time updates
- Redis for caching
- Spam detection service integration

---

### Step 3: Database Design

**Agent:** Database Agent

**Task:** Design database schema

**Input:**
```
Design database schema for commenting system:
- Comments table with nesting support
- Moderation status tracking
- Spam score tracking
- Soft delete support
- Audit trail
```

**Output:**
```sql
CREATE TABLE comments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    post_id UUID NOT NULL REFERENCES posts(id),
    user_id UUID NOT NULL REFERENCES users(id),
    parent_id UUID REFERENCES comments(id),
    content TEXT NOT NULL,
    moderation_status VARCHAR(20) DEFAULT 'pending',
    spam_score DECIMAL(3, 2) DEFAULT 0.0,
    is_deleted BOOLEAN DEFAULT false,
    deleted_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    
    CONSTRAINT check_nesting_level 
        CHECK (nesting_level <= 3)
);

CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_parent_id ON comments(parent_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
CREATE INDEX idx_comments_moderation ON comments(moderation_status);
```

**Deliverable:** `migrations/20260208_add_comments.sql`

---

### Step 4: API Specification

**Agent:** API Development Agent

**Task:** Define API endpoints

**Output:**
```yaml
openapi: 3.0.0
info:
  title: Comments API
  version: 1.0.0

paths:
  /api/v1/posts/{postId}/comments:
    get:
      summary: Get comments for a post
      parameters:
        - name: postId
          in: path
          required: true
        - name: sort
          in: query
          schema:
            type: string
            enum: [newest, oldest, popular]
      responses:
        200:
          description: List of comments
          
    post:
      summary: Create a new comment
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                content:
                  type: string
                  minLength: 1
                  maxLength: 5000
                parentId:
                  type: string
                  format: uuid
      responses:
        201:
          description: Comment created
        400:
          description: Validation error
        401:
          description: Unauthorized
          
  /api/v1/comments/{commentId}:
    get:
      summary: Get a specific comment
    put:
      summary: Update a comment
    delete:
      summary: Delete a comment
```

**Deliverable:** `docs/api/comments-api.yaml`

---

### Step 5: Backend Implementation

**Agent:** Business Logic Agent + API Development Agent

**Task:** Implement comment service and API

**Input:**
```
Implement the commenting system backend:
1. Comment service with CRUD operations
2. Nested comment support
3. Spam detection integration
4. Moderation workflow
5. Real-time event emission
6. API endpoints with validation
```

**Files Created:**
- `src/services/commentService.ts`
- `src/controllers/commentController.ts`
- `src/routes/commentRoutes.ts`
- `src/validators/commentValidator.ts`
- `src/events/commentEvents.ts`

**Key Implementation:**
```typescript
// src/services/commentService.ts
export class CommentService {
  async createComment(data: CreateCommentDto): Promise<Comment> {
    // Validate nesting level
    if (data.parentId) {
      await this.validateNestingLevel(data.parentId);
    }
    
    // Check spam score
    const spamScore = await this.spamDetectionService.check(data.content);
    
    // Create comment
    const comment = await this.repository.create({
      ...data,
      spamScore,
      moderationStatus: spamScore > 0.7 ? 'flagged' : 'pending'
    });
    
    // Emit real-time event
    this.eventEmitter.emit('comment:created', comment);
    
    return comment;
  }
  
  async getCommentTree(postId: string): Promise<CommentTree> {
    // Get all comments for post
    const comments = await this.repository.findByPost(postId);
    
    // Build tree structure
    return this.buildCommentTree(comments);
  }
}
```

---

### Step 6: Backend Testing

**Agent:** Testing Agent

**Task:** Create comprehensive test suite

**Output:**
```typescript
// tests/services/commentService.test.ts
describe('CommentService', () => {
  describe('createComment', () => {
    it('should create a top-level comment', async () => {
      const comment = await service.createComment({
        postId: 'post-1',
        userId: 'user-1',
        content: 'Great post!'
      });
      
      expect(comment).toBeDefined();
      expect(comment.parentId).toBeNull();
    });
    
    it('should create a nested comment', async () => {
      const parent = await service.createComment({...});
      const reply = await service.createComment({
        ...data,
        parentId: parent.id
      });
      
      expect(reply.parentId).toBe(parent.id);
    });
    
    it('should reject comments beyond max nesting', async () => {
      // Create 3 levels
      const level1 = await service.createComment({...});
      const level2 = await service.createComment({parentId: level1.id});
      const level3 = await service.createComment({parentId: level2.id});
      
      // Try to create level 4
      await expect(
        service.createComment({parentId: level3.id})
      ).rejects.toThrow('Maximum nesting level exceeded');
    });
  });
});
```

**Deliverable:** Complete test suite with 80%+ coverage

---

### Step 7: Frontend Component Design

**Agent:** UI/UX Design Agent + Component Development Agent

**Task:** Design and implement comment UI components

**Components:**
1. `CommentList` - Display comments with nesting
2. `CommentItem` - Individual comment display
3. `CommentForm` - Create/edit comment form
4. `CommentActions` - Action buttons (reply, edit, delete)
5. `ModerationBadge` - Show moderation status

**Implementation:**
```tsx
// components/CommentList.tsx
export const CommentList: React.FC<CommentListProps> = ({ 
  postId, 
  comments 
}) => {
  const [sortBy, setSortBy] = useState<'newest' | 'oldest' | 'popular'>('newest');
  
  return (
    <div className="comment-list">
      <div className="comment-list-header">
        <h3>{comments.length} Comments</h3>
        <CommentSort value={sortBy} onChange={setSortBy} />
      </div>
      
      <CommentForm postId={postId} />
      
      <div className="comment-list-items">
        {comments.map(comment => (
          <CommentThread key={comment.id} comment={comment} />
        ))}
      </div>
    </div>
  );
};

// components/CommentItem.tsx
export const CommentItem: React.FC<CommentItemProps> = ({ comment }) => {
  const [isEditing, setIsEditing] = useState(false);
  const [showReplyForm, setShowReplyForm] = useState(false);
  
  return (
    <div className="comment-item">
      <div className="comment-header">
        <Avatar user={comment.user} />
        <UserName user={comment.user} />
        <TimeAgo date={comment.createdAt} />
        {comment.moderationStatus === 'flagged' && (
          <ModerationBadge status="flagged" />
        )}
      </div>
      
      {isEditing ? (
        <CommentForm 
          comment={comment} 
          onSave={() => setIsEditing(false)} 
        />
      ) : (
        <div className="comment-content">{comment.content}</div>
      )}
      
      <CommentActions
        comment={comment}
        onReply={() => setShowReplyForm(true)}
        onEdit={() => setIsEditing(true)}
      />
      
      {showReplyForm && (
        <CommentForm 
          postId={comment.postId}
          parentId={comment.id}
          onCancel={() => setShowReplyForm(false)}
        />
      )}
    </div>
  );
};
```

---

### Step 8: Real-Time Integration

**Agent:** API Integration Agent

**Task:** Implement WebSocket integration

**Implementation:**
```typescript
// hooks/useComments.ts
export const useComments = (postId: string) => {
  const [comments, setComments] = useState<Comment[]>([]);
  const socket = useSocket();
  
  useEffect(() => {
    // Load initial comments
    loadComments(postId);
    
    // Subscribe to real-time updates
    socket.on(`post:${postId}:comment:created`, (comment) => {
      setComments(prev => [...prev, comment]);
    });
    
    socket.on(`post:${postId}:comment:updated`, (comment) => {
      setComments(prev => 
        prev.map(c => c.id === comment.id ? comment : c)
      );
    });
    
    socket.on(`post:${postId}:comment:deleted`, (commentId) => {
      setComments(prev => prev.filter(c => c.id !== commentId));
    });
    
    return () => {
      socket.off(`post:${postId}:comment:created`);
      socket.off(`post:${postId}:comment:updated`);
      socket.off(`post:${postId}:comment:deleted`);
    };
  }, [postId]);
  
  return { comments };
};
```

---

### Step 9: Frontend Testing

**Agent:** Testing Agent

**Task:** Create frontend test suite

**Tests:**
```typescript
// components/__tests__/CommentList.test.tsx
describe('CommentList', () => {
  it('renders comments', () => {
    render(<CommentList postId="1" comments={mockComments} />);
    expect(screen.getByText('3 Comments')).toBeInTheDocument();
  });
  
  it('handles adding a new comment', async () => {
    const { user } = render(<CommentList postId="1" />);
    
    await user.type(screen.getByPlaceholderText('Add a comment'), 'Great!');
    await user.click(screen.getByText('Submit'));
    
    await waitFor(() => {
      expect(screen.getByText('Great!')).toBeInTheDocument();
    });
  });
  
  it('handles nested replies', async () => {
    render(<CommentList postId="1" comments={mockComments} />);
    
    await user.click(screen.getAllByText('Reply')[0]);
    expect(screen.getByPlaceholderText('Write a reply')).toBeInTheDocument();
  });
});
```

---

### Step 10: Security Review

**Agent:** Security Agent

**Task:** Review security implications

**Checks:**
- ✅ Input sanitization (XSS prevention)
- ✅ SQL injection prevention (using ORM)
- ✅ Rate limiting on comment creation
- ✅ CSRF protection
- ✅ Authentication required
- ✅ Authorization checks
- ✅ Spam detection
- ✅ Content moderation

**Recommendations:**
- Add rate limiting: 10 comments per minute per user
- Implement content sanitization
- Add CAPTCHA for anonymous users
- Enable automated spam filtering

---

### Step 11: Performance Optimization

**Agent:** Performance Optimization Agent

**Tasks:**
1. Add caching for comment counts
2. Implement pagination for large threads
3. Optimize database queries
4. Add loading states
5. Implement optimistic updates

**Optimizations:**
```typescript
// Add Redis caching
const commentCount = await cache.get(`post:${postId}:comment:count`);
if (!commentCount) {
  const count = await countComments(postId);
  await cache.set(`post:${postId}:comment:count`, count, 300); // 5 min
}

// Pagination
const comments = await repository.findByPost(postId, {
  limit: 20,
  offset: page * 20,
  orderBy: 'created_at DESC'
});
```

---

### Step 12: Documentation

**Agent:** Documentation Agent

**Deliverables:**
1. API documentation
2. Component documentation
3. User guide
4. Admin moderation guide
5. Developer integration guide

---

### Step 13: Deployment

**Agent:** DevOps & Deployment Agent

**Tasks:**
1. Run migrations
2. Deploy backend changes
3. Deploy frontend changes
4. Monitor for issues
5. Rollback plan ready

**Checklist:**
- ✅ Migrations tested
- ✅ Backwards compatibility ensured
- ✅ Feature flag configured
- ✅ Monitoring alerts set up
- ✅ Rollback plan documented

---

## Results

**Metrics:**
- Development time: 10 days
- Test coverage: 85%
- Performance: < 100ms API response
- Security: All checks passed
- User feedback: Positive

**Files Changed:**
- Backend: 15 files
- Frontend: 12 files
- Tests: 20 files
- Documentation: 5 files

---

*This workflow demonstrates end-to-end feature development using the agent-driven approach.*
