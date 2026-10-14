# Continuous tablet in a GitHub profile

The profile uses adjacent, top-aligned SVG image slices. Each slice shares the same paper color, side rails and texture origin. Links wrap individual slices, so the composition looks continuous while each project remains independently clickable.

## Alternatives investigated

| Approach | Finding | Decision |
| --- | --- | --- |
| HTML container with `style`, CSS classes or a style block | GitHub's Markdown API removes inline styles/classes and escapes the style block. Tested directly. | Cannot apply a README-wide background this way. |
| Table `bgcolor`, cell styles and zero spacing | Background and cell style attributes are removed. Cell padding is controlled by GitHub CSS even if old table spacing attributes survive. | A table cannot provide the requested custom background reliably. |
| iframe or object embedding | iframe is escaped and object removed in the tested render. | Not usable inside the profile. |
| Single SVG/GIF/PNG containing everything | Allows complete art direction and background, but the entire image is one click target. SVG links do not operate inside an HTML image. GIF also loses sharp vector text. | Would compromise project navigation. |
| SVG with `foreignObject` | Can render HTML/CSS inside the image, but still does not make its internal controls interactive as an image. | Adds complexity without solving multiple independent links. |
| Image maps | `usemap` survived our test but the associated map/area elements did not. | No usable multi-link image map. |
| Left-floating slices | Seams were zero, but GitHub adds 20 px of right padding to left-aligned images. | Rejected in favor of top alignment. |
| Ordinary inline images | Measured 5 px baseline gaps between full-width images in GitHub's actual page styles. | Not visually continuous. |
| Adjacent images with `align="top"` | Measured zero vertical gaps in GitHub page styles; preserves individual wrapping links. | Implemented. |
| GitHub Pages | Supports a complete HTML/CSS/JS site, but is a separate page rather than a profile README background. | A future option, unnecessary for the current result. |

## Implementation

- One HTML div containing adjacent `picture` elements, with no Markdown paragraphs between slices.
- Every image is 100% wide and uses `align="top"`.
- `picture` selects desktop/mobile assets using width-only media queries. Theme colors are handled inside each SVG with CSS variables and `prefers-color-scheme`, inheriting the embedding page’s color scheme.
- GitHub’s themed-picture component was observed overriding a combined width + dark-mode source query. Keeping width selection in HTML and theme selection in SVG avoids that desktop/mobile mismatch.
- Each slice is an independent SVG with its own viewBox. Mobile variants are recomposed rather than merely scaled down from desktop.
- The solar animation uses CSS inside the SVG and stops for `prefers-reduced-motion`.
- Screenshots are embedded data images, not external URLs. Their content remains the previously reviewed demonstration/educational views.
- No scripts, iframes, external fonts or private app code are published.
- Each meaningful image has descriptive alternative text. The collapsed text version includes selectable text, ordinary links and attribution.
- The outer GitHub page remains GitHub's own interface. This technique styles the profile's content area only.

## Sources

- [GitHub Markup: the sanitization stage](https://github.com/github/markup#github-markup)
- [GitHub: images and links in Markdown](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
- [MDN: SVG as an image and its restrictions](https://developer.mozilla.org/en-US/docs/Web/SVG/Guides/SVG_as_an_image)
- [MDN: links in SVG images are not interactive](https://developer.mozilla.org/en-US/docs/Web/SVG/Guides/Linking)
- [W3C: SVG embedded content and foreignObject](https://www.w3.org/TR/SVG/embedded.html)
- [GitHub Pages: separate static sites](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages)

The exact sanitization behavior was additionally checked with GitHub's Markdown API. Layout measurements were taken in Chromium with GitHub's actual page styles. These workarounds depend on GitHub's renderer and may need maintenance if it changes.
