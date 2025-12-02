

Great topic—this maps perfectly to two angles you’ll store on the point:

- **Heading (azimuth)**: direction on the map, 0° = North, 90° = East, 180° = South, 270° = West (clockwise from North).
    
- **Tilt (elevation / depression)**: angle of the camera relative to **horizontal**. Positive = up, negative = down (you can flip this if you prefer “down = positive,” see note).
    

If a camera is pointing **due north**, its **heading = 0°**.  
(For reference: E=90°, S=180°, W=270°.)

---

# Formulas you’ll use

Assume you have camera and pond coordinates in a projected system (feet/meters), and elevations:

- Camera point: (Ec,Nc,Zc)(E_c, N_c, Z_c)(Ec​,Nc​,Zc​)
    
- Target point on pond (edge/center/whatever you choose): (Et,Nt,Zt)(E_t, N_t, Z_t)(Et​,Nt​,Zt​)
    

**1) Horizontal deltas & distance**

ΔE=Et−Ec,ΔN=Nt−Nc\Delta E = E_t - E_c,\quad \Delta N = N_t - N_cΔE=Et​−Ec​,ΔN=Nt​−Nc​ H=(ΔE)2+(ΔN)2H = \sqrt{(\Delta E)^2 + (\Delta N)^2}H=(ΔE)2+(ΔN)2​

**2) Heading (deg, clockwise from North)**

heading=(atan2⁡(ΔE, ΔN)×180π)    360\text{heading} = \big(\operatorname{atan2}(\Delta E,\ \Delta N)\times\frac{180}{\pi}\big)\ \bmod\ 360heading=(atan2(ΔE, ΔN)×π180​) mod 360

> Tip: use `atan2(ΔE, ΔN)` (note the order) to get standard azimuth.

**3) Vertical delta & tilt (deg)**

ΔZ=Zt−Zc\Delta Z = Z_t - Z_cΔZ=Zt​−Zc​ tilt=atan2⁡(ΔZ, H)×180π\text{tilt} = \operatorname{atan2}(\Delta Z,\ H)\times\frac{180}{\pi}tilt=atan2(ΔZ, H)×π180​

- If the pond surface is at **the same ground elevation** as the camera’s ground and your camera is **h** feet above ground, then Zt=ZgroundZ_t = Z_{\text{ground}}Zt​=Zground​ and Zc=Zground+hZ_c = Z_{\text{ground}} + hZc​=Zground​+h, so ΔZ=−h\Delta Z = -hΔZ=−h.  
    In that common case: tilt=arctan⁡2(−h, H)\text{tilt} = \arctan2(-h,\ H)tilt=arctan2(−h, H) (a small **negative** angle—looking downward).
    

---

# Worked example (with numbers)

**Scenario**

- Camera is mounted on a tripod **6 ft** above ground at location (Ec,Nc)=(1000, 1000)(E_c, N_c) = (1000,\ 1000)(Ec​,Nc​)=(1000, 1000) ft.
    
- Pond point you care about is at (Et,Nt)=(1040, 980)(E_t, N_t) = (1040,\ 980)(Et​,Nt​)=(1040, 980) ft.
    
- Ground is flat, so pond water surface ≈Zground\approx Z_{\text{ground}}≈Zground​.  
    Then ΔZ=Zt−Zc=0−6=−6\Delta Z = Z_t - Z_c = 0 - 6 = -6ΔZ=Zt​−Zc​=0−6=−6 ft.
    

**Compute heading**

- ΔE=40, ΔN=−20\Delta E=40,\ \Delta N=-20ΔE=40, ΔN=−20 → southeast-ish.
    
- heading=atan2(40,−20)\text{heading} = \text{atan2}(40, -20)heading=atan2(40,−20) in degrees ≈ **116.6°**.  
    (Compass bearing form: **S 63.4° E**.)
    

**Compute horizontal distance**

- H=402+(−20)2≈44.72H=\sqrt{40^2+(-20)^2} \approx 44.72H=402+(−20)2​≈44.72 ft.
    

**Compute tilt**

- ΔZ=−6\Delta Z = -6ΔZ=−6 ft (down).
    
- tilt=atan2(−6,44.72)≈−7.64∘\text{tilt} = \text{atan2}(-6, 44.72) \approx \mathbf{-7.64^\circ}tilt=atan2(−6,44.72)≈−7.64∘.  
    (Negative means the camera is pitched downward ~7.6° from level.)
    

**Result to store**

- `camera_heading_deg = 116.6`
    
- `camera_tilt_deg = -7.64` (if using “up positive / down negative”)
    

---

# Interpreting “what degree would the ground be from the camera?”

- The **ground plane** itself is “0°” relative to horizontal, but the **line of sight to a ground point** is the **depression angle** we just computed (e.g., −7.6°).
    
- If you place the target on the **near pond edge** vs the **center**, only HHH changes, and thus the tilt changes slightly.
    

---

# Practical notes

- Use a **projected CRS** (state plane/UTM/feet) for clean ΔE,ΔN\Delta E, \Delta NΔE,ΔN math. Don’t do this in lat/long directly.
    
- If you have only **range and bearing** from a survey, convert bearing to azimuth (heading), compute ΔE,ΔN\Delta E, \Delta NΔE,ΔN via sines/cosines, then apply the same formulas.
    
- **Sign convention** for tilt: in oil & gas/GIS I often see “up=+ / down=−”. If you prefer “down=positive,” just store `camera_tilt_deg_down = -tilt`.



# Idea
One camera will be 4 feet high off the found in feet. I will be 9 feet from the pond, It will be directly South of the pond its azimuth basically true north and tilited as a test just slight above center only tilting up 5 degrees

1	Point	Select-Boundary-000X	Test Location	<Null>