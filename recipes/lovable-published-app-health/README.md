# lovable-published-app-health

> Works in the editor preview. Breaks after you hit Publish.

## Symptoms

- Blank screen or infinite spinner on the published URL.
- Auth redirect loop on login or sign-up.
- Data visible in the editor is missing or empty in the live app.
- A webhook stops working after a republish.

## Failure modes

- Supabase env vars not set for the published project.
- RLS policies block reads for real (non-service-role) users.
- Auth redirect URL not added to Supabase allowed-redirect-URLs after custom domain setup.
- Webhook secret or signing key rotated but not updated in the published environment.

## Checks

Five monitors cover the main publish-path failure modes:

1. ** — published URL responds.** Confirms the Lovable-published URL and your custom domain answer.
2. ** — real content rendered.** Loads the live app and asserts a meaningful element exists — catches blank-screen and broken-init failures.
3. ** — Supabase data readable.** Reads data as an authenticated user; exposes RLS-blocked tables and missing env vars.
4. ** — webhook endpoint acknowledges a probe.** Catches signing-key drift before your payment provider gives up retrying.
5. ** — custom-domain certificate valid.** Confirms the TLS cert for your custom domain.

## Triage

1. Open the published URL in incognito; watch the network panel for 401/403 on Supabase calls.
2. Supabase: Settings → Authentication → URL Configuration — is your custom domain in Redirect URLs?
3. Check RLS policies — do they grant access to real users (not just service role)?
4. Compare env vars in Lovable project settings vs what the published build expects.
5. Rotate any webhook secret that may have been reset by a republish.

## Run this continuously

[Nightlamp](https://nightlamp.app/?utm_source=recipes&utm_medium=readme&utm_campaign=lovable-published-app-health) packages these monitors for no-code and AI-built apps.

Companion guide: [Lovable app broken after publish?](https://nightlamp.app/guides/lovable-app-broken)
