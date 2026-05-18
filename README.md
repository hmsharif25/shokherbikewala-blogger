# Shokher Bikewala — Blogger edition

A static, **Blogger-native** rebuild of [shokherbikewala.com](https://www.shokherbikewala.com/) so you can run the whole store from inside Blogger:

- **Products** = Blogger **Posts** with the label `product`. Publish a post → product is live on the homepage and `/search/label/product` grid automatically.
- **Other pages** (checkout, about, contact, categories) = Blogger **Pages**. Paste the HTML snippets in this repo into Blogger > Pages.
- **No build step.** Tailwind CSS is loaded from the CDN. Just paste the files into Blogger.
- **Orders** go to the same Supabase `inquiries` table the React site uses. You can view them at the React admin panel.

---

## Repo layout

| File | What it is |
|---|---|
| `theme.xml` | Blogger custom theme. Upload once via *Theme → Edit HTML → Restore from XML*. |
| `pages/checkout.html` | Paste into Blogger > Pages > New page (URL slug `checkout` → `/p/checkout.html`). |
| `pages/about.html` | Paste into Blogger > Pages (URL slug `about`). |
| `pages/contact.html` | Paste into Blogger > Pages (URL slug `contact`). |
| `pages/categories.html` | Paste into Blogger > Pages (URL slug `categories`). Edit the JS list at the bottom to control which labels show up. |
| `post-template.html` | Copy/paste this into a new Blogger **post** when you add a product. Edit the JSON block + the description. |

---

## এক নজরে setup (Bengali quick start)

1. **Blogger.com** এ লগইন করো → তোমার Blog (Shokher Bikewala) সিলেক্ট করো।
2. **Theme → ⋮ → Restore** (Edit HTML-এর পাশে) → এই repo-র `theme.xml` upload করো → **Restore**।
3. **Pages → New page → HTML view (</>) →** `pages/checkout.html`-এর সব content paste করো → Settings (right sidebar) → Permalink → Custom permalink → `checkout` → Publish। একইভাবে `about.html`, `contact.html`, `categories.html` publish করো।
4. **Posts → New post → HTML view (</>)** → `post-template.html`-এর content paste করো → meta JSON edit করো (price, discount, images) → description লিখো → Labels: `product, helmets` (যে category সেটার slug) → Publish। প্রতিটা product = ১টা post।
5. ব্যাস! Homepage refresh করলে নতুন product card দেখাবে।

---

## Step-by-step (full)

### 1. Upload the theme

1. Open Blogger at <https://www.blogger.com/> and select your blog.
2. In the left sidebar, click **Theme**.
3. Click the **⋮** menu next to the orange *Customize* button → **Restore**.
4. Click **Upload** and pick `theme.xml` from this repo.
5. Click **Restore**. Your blog will now use the new theme.

> Tip: Before restoring, click **Backup** so you can roll back if needed.

### 2. Add the Pages

For each file in `pages/`:

1. Blogger → **Pages** → **New page**.
2. Click the **HTML view** button (`</>`) on the toolbar — *important*, otherwise Blogger will strip your markup.
3. Paste the entire file contents.
4. On the right sidebar:
   - Open **Permalink** → choose **Custom permalink** → type the slug shown in the file (e.g. `checkout`).
   - Leave Search Description blank or write something short.
5. Click **Publish**.

You should now have these URLs live:

- `/p/checkout.html`
- `/p/about.html`
- `/p/contact.html`
- `/p/categories.html`

The navbar in `theme.xml` already links to them.

### 3. Add a product (= a Blogger post)

Each product is one Blogger post with the label `product`.

1. Blogger → **Posts** → **New post**.
2. Set the title to the product name (e.g. *Steelbird SBA-21 GT Full Face Helmet*).
3. Click the **HTML view** button (`</>`).
4. Paste the contents of `post-template.html`.
5. Edit the JSON block at the top:
   ```html
   <pre class="sb-product-meta-raw" style="display:none">
   {
     "price": 3500,
     "discount_price": 2800,
     "in_stock": true,
     "badge": "New",
     "category": "Helmets",
     "images": [
       "https://...image1.jpg",
       "https://...image2.jpg"
     ]
   }
   </pre>
   ```
   - `price` — original price (BDT). Required for prices to show.
   - `discount_price` — optional, the discounted price you actually charge.
   - `in_stock` — `false` to display the “Out of stock” badge.
   - `badge` — optional small badge (e.g. `New`, `Hot`, `-20%`).
   - `images` — list of image URLs. First image is the main one. Use a public host (Imgur, Cloudinary, the React site's Supabase `assets` bucket, or even Blogger's built-in image uploader and copy the URL).
6. Update the description / features / "what is in the box" text below the JSON.
7. On the right sidebar:
   - **Labels** → type `product, helmets` (the second label is the category slug — must match a slug in `pages/categories.html`). The label `product` is **required**.
   - **Permalink** → Custom permalink → e.g. `steelbird-sba-21-gt` (clean, hyphenated).
8. Click **Publish**.

The product is now live on:
- Homepage **Featured products** grid.
- `/search/label/product` (Shop).
- `/search/label/helmets` (its category).

### 4. Verify Supabase orders work

The checkout + contact forms submit to `https://pfegrhsefyqqjzmbgurs.supabase.co/rest/v1/inquiries` using the public **anon key**. The anon key is meant to be public; RLS rules in the React repo's `supabase/migrations/0001_init.sql` allow anyone to **insert** into `inquiries` but only admins (`hmsharif2002@gmail.com`) can read them.

If you ever rotate the anon key, just open `pages/checkout.html` and `pages/contact.html` and replace the `SUPABASE_ANON_KEY` constant near the bottom of each `<script>` block.

To view incoming orders, log in to the **React** admin panel at <https://www.shokherbikewala.com/admin/login> → **Orders & Inquiries**.

---

## Customisation cheatsheet

| Want to change... | Edit this |
|---|---|
| Logo / brand text | search `logo.png` in `theme.xml` (top + drawer + footer) |
| Brand colours | `:root[data-theme='light']` and `:root[data-theme='dark']` blocks in `theme.xml` `<style>` |
| Homepage categories grid | the `categories` array inside the `<script id='sb-config'>` block in `theme.xml` |
| `/p/categories.html` grid | the `CATEGORIES` array inside `pages/categories.html` |
| WhatsApp number | search `8801518934708` everywhere |
| Social links | bottom of `theme.xml` (`footer` block) |
| Default product placeholder image | the `src="https://www.shokherbikewala.com/logo.png"` lines in `theme.xml` |
| Supabase project | `SUPABASE_URL` and `SUPABASE_ANON_KEY` in `pages/checkout.html` and `pages/contact.html` |

---

## How the theme finds products

The theme reads Blogger's public JSON feed at `/feeds/posts/summary/-/<label>?alt=json` to fill the homepage product grid and the category / search pages. It does **not** require any API key. As soon as you publish a post with the right label, it shows up — usually within seconds, sometimes a minute later if Blogger's cache is cold.

The product detail layout reads the JSON inside `<pre class="sb-product-meta-raw">` (added by `post-template.html`) and renders price, gallery and the Buy Now button accordingly.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| **Product post doesn't show up on the homepage.** | Make sure the post has the label `product` (exact spelling, lowercase). Hard refresh (Ctrl+F5). |
| **Price not showing on product page.** | Open the post, switch to **HTML view**, confirm the `<pre class="sb-product-meta-raw">` block is intact and the JSON is valid (no trailing commas). |
| **Images broken.** | Make sure image URLs are HTTPS and publicly accessible. Try the URL in a private browser tab. |
| **Checkout submit fails.** | Open the browser console (F12). If you see `401`/`403`, the RLS policy isn't allowing anonymous inserts — run `supabase/migrations/0001_init.sql` in the Supabase SQL editor. If you see CORS errors, double-check `SUPABASE_URL` has no trailing slash. |
| **Theme upload error: "could not parse template".** | Blogger's parser is strict about XML. Make sure you uploaded `theme.xml` unchanged. |
| **Custom permalink option is greyed out.** | Click **Publish** first to expose Permalink, then unpublish/edit if you need to change it. |

---

## Why not a full SSG export?

We considered exporting the React site as static HTML, but Blogger's strength is that you (the user) get to **manage products through the same interface you already use for blog posts** — no separate CMS, no rebuild required, no GitHub commits to add a new product. The Blogger feed + theme XML combo gives you a CDN-backed, free-to-host store you can edit from your phone.

---

Generated for [@hmsharif25](https://github.com/hmsharif25) by Devin. Original React app lives at <https://github.com/hmsharif25/shokherbikewala>.
