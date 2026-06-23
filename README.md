# My Travel Map

This repo contains a self-contained HTML travel map in `index.html`.

## Open the map

For the map and discovery photos, you can open `index.html` directly in a browser.

For Google Photos sign-in, serve the folder over localhost instead of opening it as a `file://` URL:

```bash
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

## Connect Google Photos

Google no longer allows web apps to list your whole Photos library with the old `photoslibrary.readonly` scope. This page uses the supported Google Photos Picker API instead: you sign in, pick the photos you want to share with the page, and the selected photos are displayed in the drawer.

In Google Cloud Console:

1. Enable the **Google Photos Picker API** for your project.
2. Create or use an OAuth 2.0 **Web application** client.
3. Add this authorized JavaScript origin:

   ```text
   http://localhost:8000
   ```

4. If your OAuth consent screen is in testing mode, add your Google account as a test user.
5. Put that Web client ID in `GOOGLE_CLIENT_ID` inside `index.html`.

Then run the local server above, open `http://localhost:8000/`, click **Connect Google**, click a visited country, and use the Google Photos picker link in the drawer.

## Troubleshooting

### Google sign-in keeps failing

Check these first:

- The browser address must be exactly `http://localhost:8000/`.
- Do not use `file://`, `http://127.0.0.1:8000/`, or `http://0.0.0.0:8000/` unless that exact origin is also added in Google Cloud Console.
- `GOOGLE_CLIENT_ID` in `index.html` must be your OAuth Web application client ID.
- The **Google Photos Picker API** must be enabled for the same Google Cloud project as that client ID.
- If the OAuth consent screen is in testing mode, your Google account must be listed as a test user.
- Popups must be allowed for `localhost`.

If Google shows an error page, the most useful detail is usually the small error name, such as `origin_mismatch`, `access_denied`, or `redirect_uri_mismatch`.

### Unvisited-country photos do not load

The embedded previews come from Wikimedia Commons. If they do not load:

- Confirm your computer has internet access.
- Try a common country such as Spain, Canada, or Brazil.
- Use the **Open Google Images** button in the drawer as a fallback.
- Browser privacy extensions can block public image/API requests; try another browser or temporarily disable blockers for localhost.

## Notes

- The globe uses D3, TopoJSON, and map data from public CDNs, so it needs an internet connection.
- Visited countries are configured in the `VISITED` object inside `index.html`.
- Replace `GOOGLE_CLIENT_ID` in `index.html` with your own OAuth Web client ID if needed.
- Add `http://localhost:8000` as an allowed origin in Google Cloud Console for Google Photos sign-in.
- Embedded discovery previews use Wikimedia Commons by default. Optional Google Custom Search image keys can be added in `index.html`, but avoid exposing those keys on a public site.
