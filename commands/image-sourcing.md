---
description: Find images for a subject at a usable size and check they actually load
---

Source images for a subject.

Ask me for the subject and the minimum size if I have not given them.

Then:

1. Build the `tbs` string from what I asked for. Size goes in as `isz:lt,islt:qsvga` for 400x300, `islt:2mp` or `islt:4mp` for larger floors, or `isz:ex,iszw:1000,iszh:1000` for an exact size. Use those codes rather than inventing one, because an unrecognised value is ignored without complaint. Colour goes in as `ic:gray` or `ic:specific,isc:red`, and type as `itp:photo`, `itp:face`, `itp:clipart`, `itp:lineart` or `itp:animated`. State which filters you assembled.
2. Call `hasdata_google_images_images_getImageSearchResults` with `q` and that `tbs`.
3. Confirm the filter did something. Compare `originalWidth` and `originalHeight` against what I asked for, and if results came back under it, tell me the filter was ignored rather than pretending it held.
4. Give me ten candidates: `title`, the `source` site, dimensions, the page `link` and the direct `original` URL.
5. Fetch each `original` and drop the ones that do not return an image. Say how many you dropped, because some of these URLs will be dead or hotlink-blocked.

Do not page unless I ask. One call already returned a hundred results, and `ijn` starts at zero, so the second page is `ijn: 1`.

Point at the source page for each image and say that usage rights are not part of this data.
