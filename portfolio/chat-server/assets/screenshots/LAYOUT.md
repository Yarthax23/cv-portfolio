## LaTeX Usage Notes

All images in this directory are intended to be included using a single
reusable LaTeX macro (`\figureblock`).

Do not inline `\includegraphics` directly in the document body.

### Standard pattern

- Image and caption are treated as a single visual unit
- The caption width always matches the image width
- Captions are left-aligned text inside a centered block
- Image height is explicitly limited to avoid empty pages

### Diagram vs Screenshot

- Diagrams:
  - Exported as PDF (vector)
  - Bounding box must be cropped at export time
  - Scaled only in LaTeX

- Screenshots:
  - PNG format
  - Height limited in LaTeX if needed

### Rule of thumb

If a figure:
- pushes its caption to the next page
- leaves large blank areas
- looks blurry after scaling

then the problem is **not** the macro, but the source asset
(bounding box, format, or aspect ratio).

Use the existing `.tex` file as the canonical reference.
