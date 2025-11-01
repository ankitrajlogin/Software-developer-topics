# 🧭 Pagination — Complete Notes (Interview-Ready)

## 🧩 1. What is Pagination?

### Definition
Pagination divides large datasets into smaller chunks (pages) for incremental fetching and display, improving:
- Performance
- User experience
- Server efficiency
- Resource utilization

### Example
Instead of loading 10,000 records at once:
```javascript
// Fetch 10 records at a time
GET /api/posts?page=1&limit=10
```

## 🚀 2. Why Use Pagination?

| Reason | Explanation |
|--------|-------------|
| Performance | Limits memory/CPU usage |
| Load times | Smaller data chunks load faster |
| Bandwidth | Reduces data transfer |
| UX | Smoother with lazy-loading |
| Scalability | Handles large datasets efficiently |

## ⚙️ 3. Types of Pagination

### A. Offset-based Pagination
```sql
SELECT * FROM posts LIMIT 10 OFFSET 20;
```
✅ Pros:
- Simple implementation
- Works for small datasets
- SQL-compatible

❌ Cons:
- Slow for large datasets
- Inconsistent with real-time data
- Performance degrades with offset size

### B. Cursor-based Pagination
```sql
SELECT * FROM posts
WHERE created_at < '2025-11-01T10:00:00'
ORDER BY created_at DESC
LIMIT 10;
```
✅ Pros:
- Fast for large datasets
- Consistent results
- Real-time friendly

❌ Cons:
- More complex
- Can't jump to random pages

### C. Seek Pagination
```sql
SELECT * FROM posts
WHERE (created_at, id) < (?, ?)
ORDER BY created_at DESC, id DESC
LIMIT 10;
```

### D. Infinite Scroll
```javascript
const observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting && hasMore) {
        loadNextPage();
    }
});
```

## 📝 Pagination Parameters

Common API parameters used in pagination:

| Parameter    | Meaning                      | Example           | Usage |
| ------------ | ---------------------------- | ----------------- | ----- |
| `page`       | Page number                  | `page=2`         | Offset-based pagination |
| `limit`      | Records per page             | `limit=10`       | Both offset and cursor |
| `offset`     | Skip count                   | `offset=20`      | Offset-based pagination |
| `cursor`     | Encoded pointer to next page | `cursor=MTIzNDU2`| Cursor-based pagination |
| `hasMore`    | Boolean flag for more data   | `true`          | Response metadata |
| `totalCount` | (Optional) Total records     | `1000`          | Response metadata |

Example API requests:
```javascript
// Offset-based
GET /api/posts?page=2&limit=10

// Cursor-based
GET /api/posts?cursor=MTIzNDU2&limit=10

// Example response
{
    "data": [...],
    "nextCursor": "MTIzNDU3",
    "hasMore": true,
    "totalCount": 1000
}
```

## 🧮 4. Implementation Examples

### Backend (Node.js + Express)

#### Offset Pagination:
```javascript
app.get("/api/posts", async (req, res) => {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;

    try {
        const [total, posts] = await Promise.all([
            Post.countDocuments(),
            Post.find()
                .sort({ createdAt: -1 })
                .skip(skip)
                .limit(limit)
        ]);

        res.json({
            data: posts,
            page,
            total,
            hasMore: skip + posts.length < total
        });
    } catch (error) {
        res.status(500).json({ error: "Server error" });
    }
});
```

#### Cursor Pagination:
```javascript
app.get("/api/posts", async (req, res) => {
    const limit = parseInt(req.query.limit) || 10;
    const cursor = req.query.cursor;

    try {
        const query = cursor 
            ? { createdAt: { $lt: new Date(cursor) }}
            : {};

        const posts = await Post.find(query)
            .sort({ createdAt: -1 })
            .limit(limit + 1);

        const hasMore = posts.length > limit;
        const nextCursor = hasMore 
            ? posts[limit - 1].createdAt.toISOString()
            : null;

        res.json({
            data: posts.slice(0, limit),
            nextCursor,
            hasMore
        });
    } catch (error) {
        res.status(500).json({ error: "Server error" });
    }
});
```

## 💻 5. Frontend Implementation

### React + IntersectionObserver
```typescript
const PostList: React.FC = () => {
    const [posts, setPosts] = useState<Post[]>([]);
    const [loading, setLoading] = useState(false);
    const [cursor, setCursor] = useState<string | null>(null);
    const [hasMore, setHasMore] = useState(true);

    const lastPostRef = useCallback(node => {
        if (loading) return;
        
        const observer = new IntersectionObserver(entries => {
            if (entries[0].isIntersecting && hasMore) {
                loadMore();
            }
        });

        if (node) observer.observe(node);
        
        return () => observer.disconnect();
    }, [loading, hasMore]);

    const loadMore = async () => {
        setLoading(true);
        try {
            const response = await fetch(
                `/api/posts?${cursor ? `cursor=${cursor}` : ''}`
            );
            const { data, nextCursor, hasMore: more } = await response.json();
            
            setPosts(prev => [...prev, ...data]);
            setCursor(nextCursor);
            setHasMore(more);
        } catch (error) {
            console.error('Failed to fetch posts:', error);
        } finally {
            setLoading(false);
        }
    };

    return (
        <div className="post-list">
            {posts.map((post, index) => (
                <div
                    key={post.id}
                    ref={index === posts.length - 1 ? lastPostRef : null}
                >
                    <h2>{post.title}</h2>
                    <p>{post.content}</p>
                </div>
            ))}
            {loading && <div>Loading...</div>}
        </div>
    );
};
```

## 🔒 6. Edge Cases & Solutions

### Race Conditions
```typescript
let currentRequest: AbortController | null = null;

const fetchData = async (cursor: string | null) => {
    if (currentRequest) {
        currentRequest.abort();
    }
    
    currentRequest = new AbortController();
    
    try {
        const response = await fetch(`/api/posts?cursor=${cursor}`, {
            signal: currentRequest.signal
        });
        // Process response
    } catch (error) {
        if (error.name === 'AbortError') {
            return; // Ignore aborted requests
        }
        throw error;
    }
};
```

## 🚨 Edge Cases & Solutions Matrix

### Common Edge Cases Table

| Edge Case | Problem | Solution | Implementation Approach |
|-----------|---------|----------|------------------------|
| **1. Duplicates** | Same items appear in multiple pages | Deduplicate by ID | Track seen IDs and filter |
| **2. Missing Data** | Records deleted during pagination | Use cursor-based pagination | Include stable identifier in cursor |
| **3. Performance** | Large offsets are slow | Switch to cursor pagination | Use indexed fields for cursors |
| **4. Race Conditions** | Out-of-order responses | Cancel previous requests | Use AbortController |
| **5. Network Failures** | Failed requests | Implement retry mechanism | Add retry with exponential backoff |
| **6. Empty Results** | No data available | Show user-friendly message | Add empty state UI |
| **7. Auth Expiry** | Token expires mid-pagination | Auto refresh token | Implement token refresh flow |
| **8. Data Changes** | New items affect pagination | Use snapshot isolation | Include version/timestamp in queries |
| **9. Rate Limiting** | Too many API calls | Implement throttling | Debounce scroll events |
| **10. DOM Performance** | Too many elements rendered | Virtual scrolling | Use react-window/react-virtualized |
| **11. Accessibility** | Not keyboard-friendly | Add button navigation | Include "Load More" button |
| **12. SEO** | Content not indexable | Server-side render first page | Implement SSR/static generation |
| **13. Sort/Filter** | Changes break pagination | Reset pagination state | Clear existing data on changes |
| **14. Scroll Events** | Multiple triggers | Debounce handler | Use IntersectionObserver properly |
| **15. Cursor Uniqueness** | Duplicate timestamps | Use compound keys | Combine timestamp with unique ID |

### Implementation Examples

```typescript
// 1. Deduplication
const deduplicateResults = (existing: Post[], incoming: Post[]): Post[] => {
    const seen = new Set(existing.map(p => p.id));
    return incoming.filter(p => !seen.has(p.id));
};

// 2. Cursor-based Query
const getCursorQuery = (cursor: string) => {
    const [timestamp, id] = cursor.split('_');
    return {
        $or: [
            { createdAt: { $lt: new Date(timestamp) } },
            {
                createdAt: new Date(timestamp),
                _id: { $lt: id }
            }
        ]
    };
};

// 3. Race Condition Handler
class PaginationManager {
    private controller: AbortController | null = null;

    async fetchPage(cursor: string) {
        this.controller?.abort();
        this.controller = new AbortController();

        try {
            return await fetch(`/api/data?cursor=${cursor}`, {
                signal: this.controller.signal
            });
        } catch (error) {
            if (error.name === 'AbortError') return null;
            throw error;
        }
    }
}

// 4. Retry Logic
const fetchWithRetry = async (
    fn: () => Promise<any>,
    maxRetries = 3
): Promise<any> => {
    for (let attempt = 0; attempt < maxRetries; attempt++) {
        try {
            return await fn();
        } catch (error) {
            if (attempt === maxRetries - 1) throw error;
            await new Promise(r => setTimeout(r, 2 ** attempt * 1000));
        }
    }
};

// 5. Virtual List Implementation
import { FixedSizeList } from 'react-window';

const VirtualizedList: React.FC<{items: any[]}> = ({ items }) => (
    <FixedSizeList
        height={600}
        width="100%"
        itemCount={items.length}
        itemSize={50}
    >
        {({ index, style }) => (
            <div style={style}>{items[index].title}</div>
        )}
    </FixedSizeList>
);

// 6. Debounced Scroll Handler
const useDebounced = (callback: () => void, delay: number) => {
    const timeoutRef = useRef<NodeJS.Timeout>();

    useEffect(() => {
        return () => {
            if (timeoutRef.current) {
                clearTimeout(timeoutRef.current);
            }
        };
    }, []);

    return useCallback(() => {
        if (timeoutRef.current) {
            clearTimeout(timeoutRef.current);
        }
        timeoutRef.current = setTimeout(callback, delay);
    }, [callback, delay]);
};
```

### Best Practices for Edge Cases

1. **Data Consistency**
   - Always use stable identifiers in cursors
   - Include version/timestamp for snapshot isolation
   - Handle data changes gracefully

2. **Performance**
   - Implement virtual scrolling for large lists
   - Use proper indexes for cursor fields
   - Cache results when appropriate

3. **User Experience**
   - Show loading states
   - Provide clear error messages
   - Add retry mechanisms
   - Consider accessibility

4. **Error Handling**
   - Implement proper error boundaries
   - Add retry logic with backoff
   - Handle network failures gracefully
   - Monitor error rates

## 📊 7. Best Practices

1. **Input Validation**
```typescript
const validatePaginationParams = (
    page: number,
    limit: number
): boolean => {
    return (
        Number.isInteger(page) &&
        Number.isInteger(limit) &&
        page > 0 &&
        limit > 0 &&
        limit <= 100  // Maximum limit
    );
};
```

2. **Error Handling**
```typescript
try {
    // Pagination logic
} catch (error) {
    if (error instanceof DatabaseError) {
        return { error: 'Database error', code: 500 };
    }
    if (error instanceof ValidationError) {
        return { error: 'Invalid parameters', code: 400 };
    }
    return { error: 'Unknown error', code: 500 };
}
```

## 🎯 8. Performance Optimizations

1. **Database Indexes**
```javascript
// MongoDB
db.posts.createIndex({ createdAt: -1 });
db.posts.createIndex({ createdAt: -1, _id: -1 });

// SQL
CREATE INDEX idx_posts_created_at ON posts(created_at DESC);
```

2. **Caching**
```typescript
const cache = new Map();

const getCachedPage = async (key: string) => {
    if (cache.has(key)) {
        return cache.get(key);
    }
    
    const data = await fetchPage(key);
    cache.set(key, data);
    return data;
};
```

## 🎯 Common Interview Questions

| Question | Core Points to Mention |
|----------|----------------------|
| **Q1. What is pagination and why is it needed?** | - Split large data → improves performance & UX<br>- Reduces server load<br>- Better user experience<br>- Efficient resource usage |
| **Q2. Difference between offset and cursor pagination?** | - Offset uses skip/limit; cursor uses unique field<br>- Cursor is faster & consistent<br>- Offset better for random access<br>- Cursor better for real-time data |
| **Q3. Why is OFFSET inefficient for large datasets?** | - DB scans and skips rows internally<br>- Performance degrades with offset size<br>- Memory overhead increases<br>- Can miss/duplicate records if data changes |
| **Q4. How to handle data consistency issues?** | - Use snapshot tokens<br>- Implement cursor-based queries<br>- Add version/timestamp tracking<br>- Handle data changes during pagination |
| **Q5. What is infinite scroll pagination?** | - Auto-load data as user scrolls<br>- Uses IntersectionObserver<br>- Provides seamless UX<br>- Common in social media feeds |
| **Q6. How to optimize pagination APIs?** | - Implement proper indexing<br>- Use caching strategies<br>- Validate input limits<br>- Add data prefetching |
| **Q7. How to prevent N+1 query problem in pagination?** | - Use JOINs in SQL<br>- Implement eager loading<br>- Use DataLoader in GraphQL<br>- Batch related queries |
| **Q8. How to make pagination SEO-friendly?** | - SSR first page<br>- Add static routes<br>- Provide "Load more" fallback<br>- Include proper meta tags |
| **Q9. How to handle changing filters during pagination?** | - Cancel old requests<br>- Reset pagination state<br>- Clear existing data<br>- Update cursor/offset |
| **Q10. How to design REST API for pagination?** | - Return `{ data, nextCursor, hasMore }`<br>- Include total count if needed<br>- Add proper error handling<br>- Document API patterns |

## ✅ Final Checklist

- [ ] Implement proper validation
- [ ] Add error handling
- [ ] Create necessary indexes
- [ ] Handle edge cases
- [ ] Add loading states
- [ ] Implement retry logic
- [ ] Add proper TypeScript types
- [ ] Document API responses
- [ ] Test with large datasets
- [ ] Monitor performance

## 📚 Additional Resources

- [MongoDB Pagination Docs](https://www.mongodb.com/docs/manual/reference/method/cursor.skip/)
- [React Window](https://react-window.vercel.app/)
- [GraphQL Cursor Connections Specification](https://relay.dev/graphql/connections.htm)
