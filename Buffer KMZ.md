### Short answer

You **can’t reliably reconstruct the original centerline** from a 15‑ft buffered line geometry that has been _saved as a LINE_ instead of a polygon. Once the buffer was converted to line (outer edge), the centerline information is basically lost.

But you _can_ do one of these:

1. If the buffer line is actually the **boundary of a true buffer polygon**, rebuild the polygon and then:
    
    - Use `Feature To Line` (if needed)
    - Then use `Feature To Point` plus some geometry tools, or more simply
    - Go back to the original centerline layer if you still have it.
2. If the 15‑ft “buffer” line is just a **parallel offset** of the true centerline (one side), you can use **offset / parallel logic** to approximate the centerline (e.g., using `Unsplit Line` + `Feature Vertices To Points` + custom geometry).
    

Below I’ll give the _practical_ ArcGIS Pro workflows depending on what you actually have.

---
![[Pasted image 20251201161504.png]]
### Step 1 – Figure out what your “buffer” line really is

Open the shapefile in ArcGIS Pro and look closely:

#### Case A – It looks like the **outline** of a 30‑ft wide corridor (both sides)

You see two roughly parallel lines on either side of where the centerline would be, like the ring of a polygon, not just one line.

In this case, at some point someone:

- Buffered a centerline (polygon), then
- Used `Polygon To Line` or `Feature To Line` to convert the polygon ring to lines, and
- Saved only those lines.

If you truly just have those outer edges, the geometric centerline is **not trivially recoverable** without assumptions. The only honest options:

- Try to get the **original centerline** (best).
- If not possible, you can:
    - Convert the line ring back into polygons: `Feature To Polygon` on the line shapefile.
    - Then compute an **approximate centerline** using a skeletonization/medial axis method (this is _not_ a standard ArcGIS geoprocessing tool; usually done with Python or another GIS library).

ArcGIS Pro by itself does _not_ have an out‑of‑the‑box “polygon to centerline” for arbitrary shapes that behaves like a true medial axis. For simple, thin, corridor‑like polygons, sometimes you can:

- Use `Feature To Line` then dissolve and generalize, but it’s still an approximation.

If you want, I can build a Python workflow to:

- Take your polygon (rebuilt from the outer line),
- Run a medial‑axis approximation using Python libraries, and
- Output a new centerline feature class.

---

#### Case B – It is just **one offset line** (parallel to the real centerline)

Sometimes people call a 15‑ft parallel line a “buffer” even though it’s actually a **single offset line**, not a polygon boundary.

If your line is just one side of the corridor, and you know the perpendicular offset distance (15 ft), then you can:

1. Use `Feature Vertices To Points` to get all vertices.
2. Derive segment bearings and **shift the vertices inward** by 15 ft perpendicular to the line (using a Python script with arcpy geometry or a field calculation).
3. Rebuild a line from those shifted vertices.

This recovers an approximate centerline, but:

- It assumes the line really is a constant perpendicular offset from the original,
- Curves and intersections may not be perfect.

This requires some scripting; it’s not a single geoprocessing button.

---

### Concrete ArcGIS Pro workflows you can try immediately

#### If you suspect it came from a buffer polygon:

1. Add the line shapefile to ArcGIS Pro.
2. Run `Feature To Polygon`:
    - Input Features: your buffer line shapefile
    - Output Feature Class: `Buffer_Polygons`
3. Inspect the polygon. If it looks like a 30‑ft wide strip around a route, you now have the **buffer polygon** back.

From here, true centerline extraction is **non‑trivial**. ArcGIS Pro alone does not give you a perfect centerline from that polygon. The two realistic paths:

- Get the original centerline from whoever created the buffer, or
- Let me help you script a medial‑axis / skeleton in Python and output a line feature.

#### If you have _any_ original centerline layer left:

The best answer is: **use the original centerline feature class** and discard the buffer line for centerline work. The buffer was meant for distance/impact/clearance calculations, not as a primary geometry source.

---

### What I’d recommend doing next

If you can upload the shapefile (or a sample of it), I can:

- Inspect whether it’s a ring (two edges) or a single offset line.
- Then either:
    - Give you a simple “rebuild polygon, then skeleton” Python recipe, or
    - Give you an offset–back‑to–centerline Python recipe for the single‑side case.

Without seeing the exact geometry, the key takeaway is:

- ArcGIS Pro cannot magically “un‑buffer” a line once it’s turned into an offset/outer line.
- You either go back to the source line or you compute an approximate centerline via custom tools.

## Settings

Dec 1, 04:13:4