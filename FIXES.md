# What was fixed

## Backend (would not compile / would not work)
1. **Compile error** — `SessionAuthFilter` extended `OncePerRequestFilter` without importing it.
2. **Every logged-in request returned 403** — the filter was a `@Component`, so Spring Boot ran it as a plain servlet filter *and* Spring Security skipped it inside its chain. Now created only inside `SecurityConfig`.
3. **`/api/auth/me` threw a 500** when nobody was logged in (`findById(null)`). Now returns 401.
4. **Password hashes were sent to the browser** — every workout/measurement/record JSON contained the full `user` object incl. password. Added `@JsonIgnore`.
5. **Dashboard crashed (500)** after saving a measurement with any blank field (`Map.of` rejects `null`).
6. CORS origin was hard-coded to localhost; now read from `FRONTEND_URL` (comma-separated list allowed).
7. Personal Records were never created — logging a workout now updates them automatically.
8. Workout streak was always 0 — now calculated. "Total Workouts" counts sessions, not single exercises.
9. Register accepted a null password (NPE) and checked duplicate emails case-sensitively.
10. Java 25 → 21 (runs on 21 **or** 25). README referenced `mvnw.cmd` which didn't exist → uses `mvn`.
11. Added `PORT` support, `Dockerfile`, and a `/` health endpoint for hosting.

## Frontend
1. **My Workouts page crashed the whole app** — `History` was declared `async function`, which React can't render.
2. Every plan showed the Push Day exercises — each plan now has its own list.
3. Missing `key` props on all lists (console warnings).
4. "Complete Workout" with nothing ticked silently did nothing; errors now shown, button disabled while saving.
5. Logout could get stuck if the request failed.
6. API base is now `/api` + Vite dev proxy + `vercel.json` rewrite → no CORS or third-party-cookie problems in production.
7. Removed unused `recharts` dependency.
