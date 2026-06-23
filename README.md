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
3. On the OAuth consent screen/Data Access page, add this scope:

   ```text
   https://www.googleapis.com/auth/photospicker.mediaitems.readonly
   ```

4. Add this authorized JavaScript origin:

   ```text
   http://localhost:8000
   ```

5. If your OAuth consent screen is in testing mode, add your Google account as a test user.
6. Put that Web client ID in `GOOGLE_CLIENT_ID` inside `index.html`.

Then run the local server above, open `http://localhost:8000/`, click **Connect Google**, click a visited country, and use the Google Photos picker link in the drawer.

The Google consent screen may describe this Picker permission as:

```text
View your Google Photos library
```

That wording is expected. The map still only receives the photos you choose in the picker.

## Troubleshooting

### Google sign-in keeps failing

Check these first:

- The browser address must be exactly `http://localhost:8000/`.
- Do not use `file://`, `http://127.0.0.1:8000/`, or `http://0.0.0.0:8000/` unless that exact origin is also added in Google Cloud Console.
- `GOOGLE_CLIENT_ID` in `index.html` must be your OAuth Web application client ID.
- The **Google Photos Picker API** must be enabled for the same Google Cloud project as that client ID.
- The OAuth consent screen/Data Access page must include this scope: `https://www.googleapis.com/auth/photospicker.mediaitems.readonly`.
- If the OAuth consent screen is in testing mode, your Google account must be listed as a test user.
- Popups must be allowed for `localhost`.

If Google shows an error page, the most useful detail is usually the small error name, such as `origin_mismatch`, `access_denied`, or `redirect_uri_mismatch`.

### Error says "Photos Library API has not been used"

That means the browser is running an older copy of the map. The current `index.html` uses the **Google Photos Picker API** at `photospicker.googleapis.com`, not the old Photos Library API at `photoslibrary.googleapis.com`.

To fix it:

1. Stop the local server by pressing `Ctrl+C` in the terminal.
2. Make sure you are editing/opening the newest `index.html`.
3. In `index.html`, search for:

   ```text
   photospicker.googleapis.com
   ```

   You should find it.

4. Also search for:

   ```text
   photoslibrary.googleapis.com
   ```

   You should not find it.

5. Start the server again:

   ```bash
   python3 -m http.server 8000
   ```

6. Reload the browser page. If needed, do a hard refresh:
   - Windows/Linux: `Ctrl+F5`
   - Mac: `Command+Shift+R`

You do not need to enable the old **Photos Library API** for this map.

### Error says "Request had insufficient authentication scopes"

That means Google accepted your login, but the token it gave the page does not include the Google Photos Picker permission.

Fix it in Google Cloud Console:

1. Open your project.
2. Go to **APIs & Services**.
3. Open **OAuth consent screen**.
4. Find the **Data Access** or **Scopes** section.
5. Add this scope:

   ```text
   https://www.googleapis.com/auth/photospicker.mediaitems.readonly
   ```

6. Save/publish the consent screen changes.
7. Make sure your Gmail is still added as a test user if the app is in testing mode.
8. Remove the old permission grant from your Google Account:
   - Open `https://myaccount.google.com/connections`
   - Find your Travel Map app/project
   - Remove its access
9. In the browser, hard refresh the map and click **Connect Google** again.

You can leave the old **Photos Library API** disabled; this map does not use it.

When the correct version of the map is running, this error should no longer appear by itself. If it still happens, the drawer should also show the required scope and the scopes Google actually granted.

If Google says the app "already has some access", it is reusing a previous grant. Use the **Reset Google connection** button in the orange setup box, or remove the app manually from `https://myaccount.google.com/connections`, then hard refresh and connect again.

If the error appears only after you select photos, make sure you are using the newest `index.html`. Picker image URLs must be fetched with an authorization header, so older versions of this file cannot display selected photos correctly.

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
