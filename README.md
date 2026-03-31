# QRFlow — Setup Guide
## Multi-user Smart QR System (Lifetime Free)

---

## Ek baar ka setup (~15 min)

### STEP 1 — Supabase Project Banao (Free)

1. https://supabase.com → Sign up (free)
2. "New Project" → Name: `qrflow` → Region: Southeast Asia → Create
3. **SQL Editor** mein ye SQL run karo:

```sql
CREATE TABLE qr_links (
  id TEXT NOT NULL,
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  label TEXT,
  android_url TEXT,
  ios_url TEXT,
  fallback_url TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (id, user_id)
);

ALTER TABLE qr_links ENABLE ROW LEVEL SECURITY;

-- Har user sirf apne links dekhe
CREATE POLICY "user owns links" ON qr_links
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- Public read for redirect (r.html)
CREATE POLICY "public read redirect" ON qr_links
  FOR SELECT USING (true);
```

4. **Settings → API** se copy karo:
   - `Project URL` (e.g. https://abcxyz.supabase.co)
   - `anon / public` key

5. **Authentication → Providers → Email** → Enable karo
6. (Optional) **Authentication → Providers → Google** → Enable karo (OAuth)

---

### STEP 2 — Files mein Config Daalo

Teenon files mein ye 3 variables set karo:

**index.html** (line ~130):
```js
var SUPABASE_URL = 'https://XXXX.supabase.co';
var SUPABASE_ANON_KEY = 'eyJ...';
```

**app.html** (line ~3):
```js
var SUPABASE_URL = 'https://XXXX.supabase.co';
var SUPABASE_ANON_KEY = 'eyJ...';
var REDIRECT_BASE_URL = 'https://USERNAME.github.io/REPO/r.html';
```

**r.html** (line ~30):
```js
var SUPABASE_URL = 'https://XXXX.supabase.co';
var SUPABASE_ANON_KEY = 'eyJ...';
```

---

### STEP 3 — GitHub Pages Pe Host Karo

1. github.com → New Repository (Public) → Name: `qrflow`
2. Upload 4 files: `index.html`, `app.html`, `r.html`, `README.md`
3. Settings → Pages → Branch: main → Save
4. 2 min baad URLs milenge:
   - 🌐 App: `https://USERNAME.github.io/qrflow/`
   - 🔗 Redirect: `https://USERNAME.github.io/qrflow/r.html`

---

### STEP 4 — Supabase mein Site URL Set Karo

Supabase → Authentication → URL Configuration:
- **Site URL**: `https://USERNAME.github.io/qrflow/`
- **Redirect URLs**: `https://USERNAME.github.io/qrflow/app.html`

---

## Use Karna

1. `https://USERNAME.github.io/qrflow/` kholo
2. Account banao (Sign Up)
3. Login karo
4. "+ New QR Link" → Android + iOS URLs daalo → Save
5. QR download karo (PNG/SVG)
6. **Kabhi bhi link change karna ho** → Dashboard → Edit → Save ✅
   QR same rahega!

---

## Architecture

```
User → index.html (login/signup)
          ↓
       app.html (dashboard)
          ↓ save
       Supabase DB (qr_links table)
          ↑ fetch
       r.html (redirect page — QR encode karta hai isko)
          ↓
    Android → Play Store
    iOS → App Store
    Desktop → Website
```

---

## Free Tier Limits

| Service | Free Limit |
|---------|-----------|
| Supabase DB | 500MB |
| Supabase Auth | 50,000 users |
| Supabase Reads | Unlimited |
| GitHub Pages | Unlimited bandwidth |

**Koi subscription nahi. Koi expiry nahi. Lifetime free.**
# QRFlow
