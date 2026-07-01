/**
 * Vercel Edge Middleware — WAVES Board Auth Injection
 *
 * Set these environment variables in your Vercel project dashboard:
 *   WAVES_USER  →  your chosen username   (e.g.  waves )
 *   WAVES_PASS  →  your chosen password   (e.g.  MyStr0ngP@ss )
 *
 * The middleware intercepts requests for index.html and injects the
 * credentials as a <script> tag so the client-side login screen can
 * validate against them without exposing them in the source.
 *
 * NOTE: This is "soft" protection — suitable for restricting casual access.
 * For serious security, use Vercel's built-in Authentication (Pro plan).
 */

export const config = { matcher: ['/', '/index.html'] };

export default async function middleware(request) {
  const url = new URL(request.url);
  // Only inject into the root HTML
  if (url.pathname !== '/' && url.pathname !== '/index.html') {
    return;
  }

  const user = process.env.WAVES_USER || 'waves';
  const pass = process.env.WAVES_PASS || 'coaching2024';

  // Fetch the actual page
  const res = await fetch(request);
  let html = await res.text();

  // Inject credentials as a JS global BEFORE the closing </head>
  const inject = `<script>window.__WAVES_AUTH__={user:"${escapeJs(user)}",pass:"${escapeJs(pass)}"};</script>`;
  html = html.replace('</head>', inject + '</head>');

  return new Response(html, {
    headers: {
      'Content-Type': 'text/html; charset=utf-8',
      'Cache-Control': 'no-store, no-cache, must-revalidate',
      'X-Frame-Options': 'DENY',
    },
  });
}

function escapeJs(str) {
  return String(str)
    .replace(/\\/g, '\\\\')
    .replace(/"/g, '\\"')
    .replace(/</g, '\\u003c')
    .replace(/>/g, '\\u003e')
    .replace(/&/g, '\\u0026');
}
