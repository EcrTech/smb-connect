

## Add `auth.users` Export to Data Export

The `auth.users` table lives in a protected schema and can't be queried via `supabase.from('users')`. However, the Edge Function already uses the service role key, which has access to the admin auth API (`supabase.auth.admin.listUsers()`).

### Plan

**1. Update Edge Function (`supabase/functions/export-table-csv/index.ts`)**

- Add `"auth_users"` to the `ALLOWED_TABLES` array as a special-case identifier.
- When `table === "auth_users"`, instead of querying a public table, use `supabaseAdmin.auth.admin.listUsers()` with pagination to fetch all users.
- Map each user to a flat object with useful columns: `id`, `email`, `email_confirmed_at`, `phone`, `created_at`, `last_sign_in_at`, `updated_at`, plus any `user_metadata` fields (full_name, etc.).
- Convert to CSV using the existing `convertToCSV` helper and return with the same headers.

**2. Update Frontend (`src/pages/admin/DataExport.tsx`)**

- Add `"auth_users"` to the table list shown in the UI so admins can select and export it like any other table.

### Security
- Same admin-only gate applies — the function already verifies the caller is an active admin before proceeding.
- The service role key is used server-side only; no client exposure.

