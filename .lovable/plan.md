

# Admin CSV Export Page for Database Backup

## Overview
Build an admin-only page at `/admin/data-export` that lets admins select and download any database table as CSV files for local backup.

## Tables to Export
All public tables: `profiles`, `associations`, `association_managers`, `association_requests`, `companies`, `company_admins`, `company_invitations`, `company_requests`, `connections`, `members`, `member_invitations`, `events`, `event_registrations`, `event_landing_pages`, `event_coupons`, `event_coupon_usages`, `posts`, `post_likes`, `post_comments`, `post_shares`, `post_bookmarks`, `post_mentions`, `notifications`, `chats`, `chat_participants`, `messages`, `email_lists`, `email_list_recipients`, `email_campaigns`, `email_campaign_recipients`, `email_conversations`, `email_messages`, `email_templates`, `whatsapp_lists`, `whatsapp_list_recipients`, `analytics_events`, `audit_logs`, `skills`, `work_experience`, `education`, `certifications`, `key_functionaries`, `admin_users`.

## Implementation

### 1. New page: `src/pages/admin/DataExport.tsx`
- Admin-only page following existing admin page patterns (BackButton, Card layout, admin check via `admin_users` table)
- Display a list of all tables with checkboxes and a "Download CSV" button per table, plus a "Download All" button
- For each table, query all rows using the Supabase client (paginating past the 1000-row limit using `.range()`) and convert to CSV client-side
- Trigger browser download of the generated CSV file
- Show row count per table after fetching

### 2. CSV generation logic
- Utility function that takes an array of objects, extracts headers from keys, escapes values (commas, quotes, newlines), and produces a downloadable `.csv` blob
- Handle pagination: fetch in batches of 1000 using `.range(from, to)` until no more rows

### 3. Route registration in `src/App.tsx`
- Add route `/admin/data-export` wrapped in `<ProtectedRoute>`

### 4. Navigation link in `AdminActions.tsx`
- Add a "Data Export" card/button linking to `/admin/data-export`

### Files to create/modify
- **Create**: `src/pages/admin/DataExport.tsx`
- **Modify**: `src/App.tsx` (add route)
- **Modify**: `src/pages/admin/AdminActions.tsx` (add navigation card)

No database changes needed — this reads existing tables using the admin user's existing RLS permissions.

