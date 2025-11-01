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

| Edge Case | Explanation | Solution | Example Implementation |
|-----------|-------------|----------|----------------------|
| **1. Duplicates on re-fetch** | Overlapping pages | Deduplicate by ID | ```typescript
const newPosts = data.filter(post => 
  !existingPosts.some(ep => ep.id === post.id)
);``` |
| **2. Missing data** | Data deleted between requests | Use cursor-based pagination | ```sql
WHERE created_at < ? AND id < ?
ORDER BY created_at DESC, id DESC
``` |
| **3. Offset performance** | Large skip is slow | Switch to cursor pagination | _See cursor implementation above_ |
| **4. Race conditions** | Out-of-order API calls | Abort previous requests | ```typescript
currentRequest?.abort();
``` |
| **5. Network failure** | Request fails | Show retry UI | ```typescript
{error && <RetryButton onClick={retry} />}
``` |
| **6. Empty result** | No records | Show "No data" message | ```typescript
{posts.length === 0 && <NoDataMessage />}
``` |
| **7. Token expiry** | Auth expired mid-scroll | Refresh token or re-login | ```typescript
if (error.status === 401) {
    await refreshToken();
    retry();
}
``` |
| **8. New data insertion** | Results shift | Use snapshot version | ```typescript
WHERE created_at < ? AND version = ?
``` |
| **9. API limit exceeded** | Too many requests | Debounce scroll handler | ```typescript
const debouncedLoad = debounce(loadMore, 250);
``` |
| **10. UI lag** | Large DOM | Virtualize long lists | ```typescript
import { FixedSizeList } from 'react-window';
``` |
| **11. Accessibility** | Not keyboard-friendly | Add "Load more" button | ```typescript
<button onClick={loadMore}>Load More</button>
``` |
| **12. SEO** | Bots can't scroll | SSR first page | ```typescript
export async function getServerSideProps() {
    const initialData = await fetchFirstPage();
    return { props: { initialData } };
}
``` |
| **13. Sorting changes** | Results overlap | Reset pagination | ```typescript
const handleSortChange = () => {
    setPosts([]);
    setCursor(null);
};
``` |
| **14. Duplicate triggers** | Multiple fires | Debounce/disconnect | ```typescript
observer.current?.disconnect();
``` |
| **15. Cursor collisions** | Equal timestamps | Use compound key | ```typescript
const cursor = `${timestamp}_${id}`;
``` |

### Implementation Examples for Common Edge Cases

```typescript
// 1. Deduplication Helper
const deduplicateById = <T extends { id: string }>(
    existing: T[],
    incoming: T[]
): T[] => {
    const seen = new Set(existing.map(item => item.id));
    return incoming.filter(item => !seen.has(item.id));
};

// 2. Race Condition Handler
class RequestManager {
    private currentRequest: AbortController | null = null;

    async fetch(cursor: string) {
        this.currentRequest?.abort();
        this.currentRequest = new AbortController();

        try {
            return await fetch(`/api/data?cursor=${cursor}`, {
                signal: this.currentRequest.signal
            });
        } catch (error) {
            if (error.name === 'AbortError') return null;
            throw error;
        }
    }
}

// 3. Retry with Exponential Backoff
const retryWithBackoff = async (
    fn: () => Promise<any>,
    maxAttempts = 3
) => {
    for (let attempt = 0; attempt < maxAttempts; attempt++) {
        try {
            return await fn();
        } catch (error) {
            if (attempt === maxAttempts - 1) throw error;
            await new Promise(r => 
                setTimeout(r, Math.pow(2, attempt) * 1000)
            );
        }
    }
};

// 4. Virtualized List Example
import { FixedSizeList } from 'react-window';

const VirtualizedPostList: React.FC<{posts: Post[]}> = ({ posts }) => (
    <FixedSizeList
        height={400}
        width="100%"
        itemCount={posts.length}
        itemSize={50}
    >
        {({ index, style }) => (
            <div style={style}>
                {posts[index].title}
            </div>
        )}
    </FixedSizeList>
);

// 5. Debounced Scroll Handler
import { debounce } from 'lodash';

const debouncedLoadMore = debounce((callback: () => void) => {
    callback();
}, 250, { leading: true, trailing: false });
```

### Best Practices for Edge Case Handling

1. Always implement deduplication
2. Use compound keys for cursor pagination
3. Implement proper error boundaries
4. Add retry logic with backoff
5. Consider accessibility requirements
6. Monitor and log edge case occurrences
7. Test with large datasets
8. Implement proper loading states
9. Handle network errors gracefully
10. Consider SEO implications

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
