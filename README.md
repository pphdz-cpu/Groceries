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

## Notes

- The globe uses D3, TopoJSON, and map data from public CDNs, so it needs an internet connection.
- Visited countries are configured in the `VISITED` object inside `index.html`.
- Replace `GOOGLE_CLIENT_ID` in `index.html` with your own OAuth Web client ID if needed.
- Add `http://localhost:8000` as an allowed origin in Google Cloud Console for Google Photos sign-in.
- Embedded discovery previews use Wikimedia Commons by default. Optional Google Custom Search image keys can be added in `index.html`, but avoid exposing those keys on a public site.
