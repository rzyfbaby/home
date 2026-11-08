I want photo-realistic mockups of REAL sofas I can buy, placed in my own living room, plus the exact product links. Use the Gemini API image-editing model ("Nano Banana") with my key from the GEMINI_API_KEY environment variable. Never print the key.

## My room (photos are in ./room-photos/)
- `lamp-wall.jpg`: taken from the window corner. It shows the long blank wall with two traditional lamps on iron plant stands, two grey velvet chairs with a small round side table and a monstera between them, the fireplace on the left and the white pillar and entry hallway on the right.
- `fireplace.jpg`: taken from the entry. It shows the white panelled fireplace with an arched niche and a tapestry, the window wall with plants on the left and the lamp wall on the right.
- `window.jpg`: a top-down view of the tall window, lots of plants (fiddle-leaf fig, monstera, dracaena), two grey wicker chairs, a walnut plank coffee table and a grey shag rug.
- The ceilings are tall and the floor is light maple. Walls are white with crown moulding. Grey is already everywhere (rug, velvet chairs, wicker chairs, floor cushions). The decor is warm and traditional: Moroccan lanterns, a calligraphy tapestry and brass.
- The room is about 14 × 17 ft (my estimate). The gap between the two lamp stands is about 8 ft.

## The look I want
A blend of formal and cozy ("transitional"). Think tailored shapes such as rolled or track arms, light tufting, a tight or semi-tight back and exposed or turned legs, combined with soft deep seats, warm fabrics (velvet, linen-look, chenille, bouclé) and throw pillows. Preferred colours are oatmeal, cream or warm beige, deep green, cognac or rust, or warm taupe. Avoid plain grey.

## Budget and location
I live in Surrey, BC, Canada. Sofas should be about $900 CAD or less. One or two "stretch" picks up to about $1,200 CAD are OK if clearly labelled. Width should be about 78–88 in so it fits between the lamps.

## Step 1: find 6–8 real sofas with exact links
Use Canadian retailer sites only: thebrick.com, ikea.com/ca/en, structube.com/en_ca, article.com (CAD), costco.ca, wayfair.ca, leons.ca, urbanbarn.com, eq3.com and ashleyhomestore.ca. Open each product page to confirm:
- the exact product URL (not a category page)
- the current price in CAD (and the regular price if it's on sale)
- the width
- the colour and fabric
- the nearest store to Surrey

Also download the main product image (the og:image meta tag works well) into ./products/.

Starting candidates to verify:
- The Brick Sawyer 79" linen-look sofa (~$699.95)
- The Brick Tula 88" rolled-arm fabric sofa (~$799.95)
- Structube KINSEY velvet 3-seater (~$799, sale from $999): https://www.structube.com/en_ca/kinsey-velvet-3seater-sofa-45-38-25
- IKEA UPPLAND sofa (beige ~$739 on sale until Nov 24, 2026)
- IKEA KIVIK sofa, Tibbleby beige/grey (~$749). Note it's about 90" wide.
- Costco Thomasville fabric sofa (~$799.99): https://www.costco.ca/thomasville-fabric-sofa.product.4000294085.html
- Stretch: Article Ceni 83" in Hemlock Green (~$1,099): https://www.article.com/pla/25355/ceni-83-sofa-hemlock-green

Also look for more formal-cozy options, such as IKEA ESSEBODA, LINANÄS or EKTORP, rolled-arm or Chesterfield-style sofas at Structube, The Brick, Leon's or Wayfair.ca, and tufted velvet sofas in green or cognac. Drop anything out of stock, discontinued or over budget.

## Step 2: generate the mockups with Gemini
1. Call `GET https://generativelanguage.googleapis.com/v1beta/models` with the header `x-goog-api-key: $GEMINI_API_KEY`. Choose the newest model that can output images, such as `gemini-3-pro-image-preview`. If that isn't available, use `gemini-2.5-flash-image`.
2. For each sofa, call `POST https://generativelanguage.googleapis.com/v1beta/models/<MODEL>:generateContent` with:
   - Header: `x-goog-api-key: $GEMINI_API_KEY`
   - Body:
     ```json
     {"contents":[{"parts":[
        {"text":"<PROMPT>"},
        {"inline_data":{"mime_type":"image/jpeg","data":"<base64 of room-photos/lamp-wall.jpg>"}},
        {"inline_data":{"mime_type":"image/jpeg","data":"<base64 of the product image>"}}
     ]}],
      "generationConfig":{"responseModalities":["IMAGE"],"imageConfig":{"aspectRatio":"3:4"}}}
     ```
   - The image comes back base64-encoded in `candidates[0].content.parts[i].inlineData.data` (it may be `inline_data`). Save it as ./mockups/<sofa-slug>-lamp-wall.png.
3. Use this prompt template, filling in the details for each sofa:
   > Edit the FIRST image (a real photo of my living room). Remove the two grey velvet chairs, the small round side table and the potted monstera that sit against the long white wall between the two tall lamps on iron stands. In their place, put the exact sofa shown in the SECOND image (<NAME>, <WIDTH> inches wide, <COLOUR/FABRIC>), centred between the two lamp stands with its back against the wall, facing into the room. Keep the two lamps and stands exactly where they are, so they act as end tables on each side. Match the sofa's design exactly: arm shape, legs, cushions, tufting, fabric texture and colour. Scale it realistically: a standard sofa about 34 inches tall, a little lower than the lamp-stand tops. Add 2–3 throw pillows in warm tones (rust, cream, deep green) and a folded knit throw for a cozy but formal look. Keep everything else in the photo identical: the camera angle, the fireplace, the walls, the crown moulding, the floor, the rug, the coffee table, the wicker chair in the foreground, the hallway and the lighting. It must look like a real, unretouched photo, with correct perspective, contact shadows on the floor and soft daylight matching the room.
4. Also make 2 or 3 versions from `fireplace.jpg` for the top picks, with the sofa on the right-hand (lamp) wall in the same way.
5. Look at every output. Regenerate (up to 2 retries) if the room changed, the lamps moved or disappeared, the sofa doesn't match the product, the colour is wrong, the scale is off or there are obvious AI artifacts. Don't keep a bad image.

## Step 3: build a page I can look through
Make one self-contained HTML file, `sofa-mockups.html`, with images embedded as base64 or saved next to the file. Include:
- At the top, my original `lamp-wall.jpg` labelled "Now".
- One card per sofa with:
  - the mockup image or images
  - the product photo
  - the name and retailer
  - the exact product link
  - the price in CAD (with the sale price and end date if any)
  - the width
  - the colour and fabric
  - the nearest store to Surrey
  - one line on why it fits a formal-and-cozy look
- Order the cards from best fit to worst.
- A small comparison table at the bottom.
- A note that the mockups are AI-generated previews and that prices were checked on today's date.

When you finish, list the top 3 picks with their links, the total cost, and any product you couldn't verify.
