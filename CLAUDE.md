# Pediatric Iron Dosing Calculator for RLS

## Project Overview
A single-page HTML calculator for pediatric iron dosing in children with Restless Leg Syndrome (RLS) and Restless Sleep Disorder. Provider-facing tool that generates copy/paste text for EPIC in both English and Spanish.

## Live Site
- **URL:** https://craigcanaparimd.github.io/pediatric-iron-calculator/calculator.html
- **Repo:** https://github.com/CraigCanapariMD/pediatric-iron-calculator

## Key Design Decisions
- **Dosing:** 3 mg/kg/day with max 65 mg elemental iron daily
- **Age groups:** Under 12 (weight-based) and 12+ (fixed 65mg)
- **Output:** Dual English/Spanish text boxes for EPIC copy/paste
- **Provider-only:** Has disclaimer warning this is not for parents
- **License:** MIT (open source)
- **Analytics:** Goatcounter (privacy-friendly, no cookies) - dashboard at https://canapari.goatcounter.com

## Iron Preparations
### Liquids
- NovaFerrum Yummy (raspberry): 15 mg/mL
- NovaFerrum Tasty (chocolate): 15 mg/mL
- Liquid ferrous sulfate: 12 mg/mL

### Chewables (have built-in Vitamin C)
- NovaFerrum Chewable Kids: 18 mg/tablet
- Nature's Plus Chewable: 27 mg/tablet

### Tablets
- Ferrous sulfate 325mg: 65 mg/tablet

## Files
- `calculator.html` - Main calculator (single self-contained HTML file)
- `how-the-code-works.md` - Plain-language code explanation for non-coders
- `README.md` - Project documentation
- `LICENSE` - MIT license

## Styling
Matches the melatonin calculator design:
- Fonts: Libre Baskerville + Nunito Sans (Google Fonts)
- Primary color: #1E73BE
- Success: #2d8a4e
- Warning: #c9553d

## Roadmap / Future Considerations
- **Variable dosing option**: Currently fixed at 3 mg/kg/day, but recommended range is 3-6 mg/kg/day. Consider adding a dropdown or toggle for higher dosing.
- **Higher max dose**: Currently capped at 65 mg elemental iron. May need to allow higher max for larger children/adolescents.

## Related Project
- Pediatric Melatonin Calculator: https://github.com/CraigCanapariMD/pediatric-melatonin-calculator
