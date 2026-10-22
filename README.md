# National Social Equity Framework (NSEF)

Static public-policy research site designed for GitHub Pages. It uses plain HTML, CSS and JavaScript so public reading works without a build process.

## Included

- Public pages: home, about, current system, disadvantage, Constitution and law, proposal, alternatives, education, roadmap, research library, FAQ, feedback, privacy and corrections.
- Responsive navigation, semantic structure, keyboard-visible focus, reduced-motion support, accessible feedback controls, search/filter for the research library, and a useful 404 page.
- An administrator entry point at `admin/`, deliberately excluded from search indexing.

## Important: backend status

No backend is connected in this repository. The feedback form does **not** transmit or retain data until the owner configures one. The admin interface does **not** authenticate, fetch, or manage submissions until the setup below is completed. This is intentional: GitHub Pages alone cannot securely provide private storage or administrator authorization.

## Recommended backend: Supabase

Supabase is appropriate here because its browser client can use a publishable/anon key while database Row Level Security (RLS) enforces access controls. Never commit a service-role key, an administrator password, or an authentication bypass.

1. Create a Supabase project and enable email/password authentication (or another appropriate provider).
2. Create administrator users manually in Supabase Auth. Do not expose self-service administrator registration.
3. Create a `feedback_submissions` table with columns such as `id uuid primary key`, `category text`, `subject text`, `message text`, `display_name text null`, `reply_email text null`, `status text`, `is_read boolean`, `admin_notes text`, `created_at timestamptz`, and `updated_at timestamptz`.
4. Create an `admin_users` table keyed by the Auth user id. Populate it only through a secure, owner-controlled process.
5. Enable RLS on both tables. A public visitor must never be able to select any submission. Only a server-side function with spam controls should insert feedback; only authenticated users whose id is in `admin_users` should select, update, archive, restore, or delete submissions.
6. Implement a Supabase Edge Function (or equivalent server endpoint) that validates length/category/email, rate-limits requests, uses CAPTCHA or another anti-abuse control, and performs inserts with server credentials. Validate again on the server.
7. Add `admin/config.js` locally by copying `admin/config.example.js`. The anon/publishable key may be exposed only after RLS is tested. Do not add `config.js` to source control unless it contains only intentionally public values.
8. Replace the demonstration JavaScript with Supabase Auth calls, redirect unauthenticated users away from `dashboard.html`, and obtain each inbox operation through RLS-protected queries or a server endpoint. Test as an anonymous visitor, an ordinary signed-in user, and an administrator.
9. Update `privacy.html` with the actual processor, data fields, retention period, deletion-request route, and confidentiality limits before enabling submissions.

## Deployment

1. Push this repository to GitHub.
2. In repository Settings → Pages, deploy from the desired branch and root folder.
3. Set the verified GitHub Pages URL in each page’s canonical and Open Graph URLs if those are added. A domain was intentionally not invented.
4. Replace the relative entries in `sitemap.xml` with absolute URLs using that verified Pages URL, then add every new public page as it is published.
5. Confirm that `/admin/` is disallowed in `robots.txt`; this is only discoverability guidance, never a security control.

## Maintenance

Check official programme rules before publishing eligibility details. Update legal links after source changes. Keep research entries traceable and label uncertainty rather than filling gaps with estimates. Review feedback architecture, RLS policies, and the privacy notice whenever the data flow changes.
