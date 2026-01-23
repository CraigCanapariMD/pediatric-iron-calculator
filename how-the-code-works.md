# How the Iron Dosing Calculator Code Works

A plain-language explanation for non-programmers to understand how this calculator is built.

## The Big Picture

The calculator is a single HTML file that contains three types of code working together:
1. **HTML** - The structure (what elements exist on the page)
2. **CSS** - The styling (how it looks)
3. **JavaScript** - The logic (what happens when you click things)

Think of it like a house: HTML is the walls and rooms, CSS is the paint and decorations, and JavaScript is the electricity that makes things work.

---

## Part 1: HTML - The Structure

HTML uses "tags" to define elements. Tags look like `<something>` and usually have a closing tag `</something>`.

### Key HTML Elements in Our Calculator

```html
<select id="ageGroup">
    <option value="under12">Under 12 years</option>
    <option value="12plus">12 years and older</option>
</select>
```

This creates a dropdown menu. The `id="ageGroup"` gives it a name so JavaScript can find it later.

```html
<input type="number" id="weight" placeholder="Enter weight in kg">
```

This creates a number input field. `placeholder` is the gray text shown when empty.

```html
<button class="calculate-btn" id="calcBtn">Calculate Iron Doses</button>
```

This creates a clickable button. The `class` is used for styling, the `id` for JavaScript.

```html
<div class="results" id="results"></div>
```

This is an empty container where the results will appear. It starts hidden and gets filled by JavaScript when you click Calculate.

---

## Part 2: CSS - The Styling

CSS rules have a **selector** (what to style) and **properties** (how to style it).

### CSS Variables

At the top, we define reusable colors:
```css
:root {
    --primary-color: #1E73BE;  /* Blue */
    --success-color: #2d8a4e;  /* Green */
    --warning-color: #c9553d;  /* Red-orange */
}
```

Now anywhere we write `var(--primary-color)`, it uses that blue color. This makes it easy to change colors site-wide.

### How Styling Works

```css
.calculate-btn {
    background: linear-gradient(135deg, #1E73BE 0%, #165B9A 100%);
    padding: 16px 24px;
    border-radius: 8px;
}
```

This says: "For any element with `class="calculate-btn"`:
- Give it a gradient background (blue fading to darker blue)
- Add 16 pixels of space above/below, 24 pixels left/right
- Round the corners by 8 pixels"

### Showing/Hiding Elements

```css
.results {
    display: none;  /* Hidden by default */
}

.results.show {
    display: block;  /* Visible when 'show' class is added */
}
```

JavaScript adds the `show` class when there are results to display.

---

## Part 3: JavaScript - The Logic

JavaScript makes the calculator interactive. Our code is wrapped in an IIFE (Immediately Invoked Function Expression):

```javascript
(function() {
    // All our code goes here
})();
```

This pattern keeps our code isolated so it doesn't conflict with other scripts.

### Step 1: Finding Elements

First, we grab references to the HTML elements we need:

```javascript
var ageGroupSelect = document.getElementById('ageGroup');
var weightInput = document.getElementById('weight');
var calcBtn = document.getElementById('calcBtn');
var resultsDiv = document.getElementById('results');
```

`document.getElementById('ageGroup')` says "find the element with id='ageGroup'".

### Step 2: Defining the Iron Preparations

We store all the medication data in a structured object:

```javascript
var preparations = {
    liquids: [
        { name: 'NovaFerrum Yummy (raspberry)', mgPerMl: 15, needsVitC: true },
        { name: 'NovaFerrum Tasty (chocolate)', mgPerMl: 15, needsVitC: true },
        { name: 'Liquid ferrous sulfate', mgPerMl: 12, needsVitC: true }
    ],
    chewables: [
        { name: 'NovaFerrum Chewable Kids', mgPerTablet: 18, needsVitC: false },
        { name: "Nature's Plus Chewable", mgPerTablet: 27, needsVitC: false }
    ],
    tablets: [
        { name: 'Ferrous sulfate 325mg', mgPerTablet: 65, needsVitC: true }
    ]
};
```

This is like a database. Each preparation has:
- `name`: What to display
- `mgPerMl` or `mgPerTablet`: How much iron per unit
- `needsVitC`: Whether to recommend Vitamin C

### Step 3: Listening for User Actions

```javascript
ageGroupSelect.addEventListener('change', function() {
    if (this.value === 'under12') {
        weightGroup.style.display = 'block';  // Show weight input
    } else {
        weightGroup.style.display = 'none';   // Hide weight input
    }
});
```

This says: "When the age dropdown changes, check what was selected. If 'under12', show the weight field. Otherwise, hide it."

```javascript
calcBtn.addEventListener('click', calculateDose);
```

This says: "When the Calculate button is clicked, run the `calculateDose` function."

### Step 4: The Main Calculation Logic

The `calculateDose` function does the heavy lifting:

**1. Get the inputs:**
```javascript
var ageGroup = ageGroupSelect.value;
var weight = parseFloat(weightInput.value);
```

**2. Calculate the target dose:**
```javascript
if (ageGroup === 'under12') {
    targetDose = Math.min(weight * 3, 130);  // 3 mg/kg, max 130 mg
} else {
    targetDose = 65;  // Fixed dose for 12+
}
```

`Math.min(weight * 3, 130)` means "multiply weight by 3, but cap at 130."

**3. Calculate doses for each preparation:**
```javascript
var liquidDoses = preparations.liquids.map(function(p) {
    var ml = Math.round(targetDose / p.mgPerMl);
    return { name: p.name, dose: ml + ' mL', needsVitC: p.needsVitC };
});
```

For each liquid preparation:
- Divide target dose by mg per mL
- Round to nearest whole number
- Return the name, calculated dose, and Vitamin C info

### Step 5: Rounding Functions

**For liquids (round to nearest 1 mL):**
```javascript
function roundLiquid(ml) {
    return Math.round(ml);
}
```

**For tablets (round to nearest 0.5):**
```javascript
function roundTablet(tabs) {
    return Math.round(tabs * 2) / 2;
}
```

The tablet rounding trick: multiply by 2, round, divide by 2. This converts:
- 1.2 tablets → 2.4 → 2 → 1.0 tablet
- 1.7 tablets → 3.4 → 3 → 1.5 tablets
- 2.3 tablets → 4.6 → 5 → 2.5 tablets

### Step 6: Building the Output Text

We build two versions - English and Spanish:

```javascript
var plainTextEN = 'IRON SUPPLEMENTATION FOR RLS/RESTLESS SLEEP DISORDER\n';
plainTextEN += '================================================\n\n';
plainTextEN += 'Target dose: ' + Math.round(targetDose) + ' mg elemental iron...\n';
```

The `\n` creates a new line. We concatenate (combine) strings with `+`.

### Step 7: Displaying Results

We build HTML as a string and insert it:

```javascript
var html = '<div class="result-card success">' +
    '<h3>Target Dose</h3>' +
    '<span class="dose">' + Math.round(targetDose) + ' mg</span>' +
    '</div>';

resultsDiv.innerHTML = html;  // Put the HTML inside the results div
resultsDiv.classList.add('show');  // Add the 'show' class to make it visible
```

### Step 8: Copy to Clipboard

When the Copy button is clicked:

```javascript
copyBtnEN.addEventListener('click', function() {
    navigator.clipboard.writeText(plainTextEN).then(function() {
        // Success! Change button text
        copyBtnEN.innerHTML = '... Copied!';
        setTimeout(function() {
            // After 2 seconds, change it back
            copyBtnEN.innerHTML = '... Copy English';
        }, 2000);
    });
});
```

`navigator.clipboard.writeText()` copies text to the clipboard. `setTimeout()` runs code after a delay (2000 milliseconds = 2 seconds).

---

## How It All Fits Together

1. **Page loads** → HTML creates the form, CSS makes it pretty
2. **User selects age** → JavaScript shows/hides weight field
3. **User enters weight** → Value stored in the input field
4. **User clicks Calculate** → JavaScript:
   - Reads the inputs
   - Calculates target dose based on age/weight
   - Calculates doses for all preparations
   - Builds English and Spanish text
   - Displays the results cards
   - Shows the output boxes with copy buttons
5. **User clicks Copy** → JavaScript copies text to clipboard

---

## Key Programming Concepts Used

| Concept | What It Does | Example |
|---------|-------------|---------|
| Variables | Store data | `var weight = 20;` |
| Functions | Reusable code blocks | `function calculateDose() {...}` |
| Conditionals | Make decisions | `if (age === 'under12') {...}` |
| Arrays | Lists of items | `[prep1, prep2, prep3]` |
| Objects | Grouped data | `{name: 'NovaFerrum', mgPerMl: 15}` |
| Event Listeners | Respond to user actions | `button.addEventListener('click', ...)` |
| DOM Manipulation | Change the page | `element.innerHTML = '...'` |

---

## Making Changes

### To add a new iron preparation:

Find the `preparations` object and add a new entry:
```javascript
liquids: [
    { name: 'NovaFerrum Yummy (raspberry)', mgPerMl: 15, needsVitC: true },
    // Add new preparation here:
    { name: 'New Product Name', mgPerMl: 10, needsVitC: false },
]
```

### To change the dosing formula:

Find this line in `calculateDose`:
```javascript
targetDose = Math.min(weight * 3, 130);
```
Change `3` to a different mg/kg or `130` to a different max.

### To change colors:

Find the CSS variables at the top:
```css
:root {
    --primary-color: #1E73BE;  /* Change this hex code */
}
```

---

## Questions?

This calculator was built with help from Claude AI. The full conversation and development process is documented in the project repository.
