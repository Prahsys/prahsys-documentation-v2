---
title: API Keys | Authentication
excerpt: >-
  Learn how to authenticate with Prahsys using API keys. Get started with
  sandbox testing and move to production with confidence.
deprecated: false
hidden: false
icon: far fa-key-skeleton
link:
  new_tab: false
metadata:
  title: API Keys | Prahsys Documentation
  description: >
    Learn how to authenticate API requests with Prahsys API keys, including
    sandbox and live environments, implementation best practices, and security
    guidelines.
  keywords:
    - Prahsys API keys
    - API authentication
    - sandbox environment
    - test API keys
    - live API keys
    - API key security
    - key rotation
    - API troubleshooting
    - bearer token authentication
  robots: index
---
# Getting Started with API Keys

Think of API keys as your digital passport - they let Prahsys know who you are when you make requests. Without a valid key, you'll get a 401 error (basically, "Who are you again?").

## Test First, Go Live Later

We've set up two environments to make your life easier:

### Sandbox (Your Safe Testing Ground)

The Sandbox is like a practice room where you can break things without consequences. Here's what makes it special:

**🔒 Completely Isolated**  
Everything you create here stays here. Your test merchants, users, and transactions won't mix with your real data.

**🔑 Test Keys Only**  
Only API keys starting with `sk_test_` work in Sandbox. No accidents with real money!

**📡 Full Feature Access**  
Every API endpoint that works in production works here too. Test everything!

**🎭 Realistic Simulations**  
We'll simulate real responses as closely as possible. You'll get authentic-looking data without any real-world side effects.

### How to Use Sandbox

It's straightforward:
- Use the same API endpoints as production
- Just swap in your test API keys
- Everything gets simulated (no real money moves)
- Test error scenarios and edge cases
- Make sure webhooks work
- Get comfortable before going live

## Understanding Your API Keys

All API keys follow this pattern: `sk_[ENVIRONMENT]_[RANDOM_HASH]`

| Environment | When to Use It | What Happens | Your Key Starts With |
|-------------|----------------|--------------|---------------------|
| **Sandbox** | Development & testing | Everything is simulated but realistic | `sk_test_` |
| **Live** | Production apps | Real transactions and charges | `sk_live_` |

> **heads up**: You can only see a live API key once when you create it. If you lose it, you'll need to make a new one.

## Making the Jump to Production

Ready to go live? Here's all you need to do:

1. **Finish testing** - Make sure everything works perfectly in Sandbox
2. **Swap your key** - Replace `sk_test_...` with `sk_live_...`

That's it! 

> **Pro tip**: Test keys (`sk_test_...`) actually work with live merchant accounts too, but they'll still simulate everything. The test data will just show up alongside your live data, which can be handy for ongoing testing.

## Using Your API Keys

### The Authentication Header

Every API request needs your key in the Authorization header:

```yaml
Authorization: Bearer sk_test_your_key_here
```

Just replace `sk_test_your_key_here` with your actual API key.

### Quick Example

Here's how to check your API status:

```bash
curl -X GET https://api.prahsys.com/merchant/status \
-H "Authorization: Bearer $PRAHSYS_API_KEY" \
-H "Content-Type: application/json"
```

## Managing Your Keys

Find all your keys in the dashboard:

1. Go to **Dashboard** → **Developers** → **API Keys**
2. View existing keys
3. Create new ones
4. Delete old ones

### Keep Your Keys Safe

Here are the golden rules:

- **Never share secret keys** or put them in client-side code
- **Use environment variables** or secure key managers
- **One key per app** limits damage if something goes wrong  
- **Rotate keys regularly** as part of good security hygiene
- **Sandbox keys for testing only** - avoid mixing test and live data

## When Things Go Wrong

| Error | What It Usually Means | How to Fix It |
|-------|---------------------|---------------|
| `401 Unauthorized` | Wrong or expired key | Double-check you're using the right key |
| `403 Forbidden` | Your key doesn't have permission | Contact support to adjust permissions |
| `404 Not Found` | Mixing sandbox and live data | Make sure your keys match your environment |

## Key Rotation (When You Need a Fresh Start)

If a key gets compromised or you just want to refresh:

1. **Create a new key** in the dashboard
2. **Update your apps** with the new key
3. **Test everything** works with the new key
4. **Delete the old key** once you're confident

## Need Help?

Something not working? Our support team is here to help: [Prahsys Support](https://help.prahsys.com)

---

*Remember: Start with Sandbox, test thoroughly, then go live with confidence. Your API keys are the bridge between your application and our platform - keep them safe!*