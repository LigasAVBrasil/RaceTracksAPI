# RaceTracksAPI

A free, open-source JSON API providing structured data and high-quality SVG maps for racing circuits around the world. This project includes both real-world and fictional tracks.

> 🚧 **Work in Progress:** This API is actively under development! We are continuously working on implementing new maps and expanding our database. Our current roadmap prioritizes real-world tracks in the following order: **1. British Touring Cars**, **2. Turismo Carretera**, and **3. GT World Challenge**. More real-world and fictional circuits will be added over time.

## 📌 Features

- **Accurate Data:** Coordinates (latitude/longitude), country codes, and alternative names for real-world circuits.
- **Fictional Tracks Included:** Metadata for fictional tracks from popular racing games, clearly separated from real-world data.
- **Custom SVG Maps:** Hand-drawn, high-quality SVG layouts for each track variation.
- **Predictable Structure:** A standardized data model ensuring all keys are present across all entries, making it highly friendly for typed languages.
- **Lightweight:** Completely static, serverless JSON hosted directly on GitHub Pages.

---

## 🚀 How to Use

### Fetching the JSON Data

You can fetch the main `tracks.json` file directly from our GitHub Pages environment. This is the recommended URL for production use:

> `https://ligasavbrasil.github.io/RaceTracksAPI/tracks.json`

### 🖼️ Fetching Track Images (SVG)

The API provides an `image_id` inside each layout object. To get the corresponding SVG map, replace `${image_id}` in the following URL structure:

> `https://ligasavbrasil.github.io/RaceTracksAPI/images/track_${image_id}.svg`

**Example:**
If the layout for _Autódromo José Carlos Pace (Interlagos)_ has `"image_id": "1.1"`, the image URL will be:
`https://ligasavbrasil.github.io/RaceTracksAPI/images/track_1.1.svg`

### 🕰️ Handling Historical Layouts

Some circuits have undergone physical modifications over the years without changing their layout designation. For example, the dataset might contain multiple layouts named `"Full Circuit"` for the same track.

To properly differentiate between classic and modern configurations, you must evaluate the `start_year` and `end_year` properties within the layout objects. An `end_year` of `null` indicates that the layout is the current, active version.

### 🏎️ Matching Categories to Historical Layouts

One of the most powerful features of this API is the ability to cross-reference the `years` a specific racing category visited a track with the historical `layouts`. 

When doing this, it's important to keep in mind that **a circuit can have multiple active layouts at the same time** (e.g., a "Full Circuit", a "Short Circuit", or alternative configurations with the exact same active years). Therefore, a simple `find` operation might not be enough if you need a specific configuration.

If you want to find the layouts that were active during **1991** at Interlagos, you should use `filter` to get all available options for that year, and then you can filter by the specific layout name:

```javascript
// Example: Finding the active layouts for a specific racing year
const targetYear = 1991;

const activeLayouts = track.layouts.filter(layout => {
    const isAfterStart = layout.start_year <= targetYear;
    const isBeforeEnd = layout.end_year === null || layout.end_year >= targetYear;
    
    return isAfterStart && isBeforeEnd;
});

if (activeLayouts.length > 0) {
    console.log(`Active layouts in ${targetYear}:`, activeLayouts.map(l => l.name));
    
    // From the active layouts, find the specific configuration you want
    const correctLayout = activeLayouts.find(l => l.name === "Full Circuit");
    
    if (correctLayout) {
        console.log(`The correct image ID for ${targetYear} is: ${correctLayout.image_id}`);
        // Output: The correct image ID for 1991 is: 1.1
    }
}
```

---

## 📄 Data Structure Example

We use a strict data contract. This means fields like `isFictional`, `fictionalSource`, and `categories` will **always** be present, even if empty or null. Fictional tracks use the geographic center coordinates of their lore-based country.

> 💡 **Note:** The "Dragon Trail" track in the example below is currently for demonstration purposes only. It illustrates how fictional circuits are structured in our schema and will be officially added to the database in a future update.

```json
[
	{
		"id": 1,
		"name": "Autódromo José Carlos Pace",
		"similarNames": [
			"Autódromo de Interlagos",
			"São Paulo Grand Prix",
			"Interlagos Circuit"
		],
		"categories": [
			{
				"id": 3,
				"name": "Prototype",
				"subcategories": [
					{ "id": 16, "name": "Hypercar", "years": [2024, 2025, 2026] }
				]
			},
			{
				"id": 4,
				"name": "Touring Car",
				"subcategories": [
					{ "id": 23, "name": "Stock Car Pro Series", "years": [2023, 2024, 2025] },
					{ "id": 24, "name": "NASCAR Brasil Series", "years": null }
				]
			},
			{ "id": 6, "name": "Motorcycle Racing", "subcategories": [] },
			{ "id": 7, "name": "Truck Racing", "subcategories": [] },
			{
				"id": 1,
				"name": "Open Wheel",
				"subcategories": [
					{ "id": 8, "name": "Formula 1", "years": null },
					{ "id": 26, "name": "F4 Brazilian Championship", "years": [2022, 2023, 2024] }
				]
			},
			{
				"id": 2,
				"name": "GT",
				"subcategories": [{ "id": 17, "name": "LMGT3", "years": [2024, 2025, 2026] }]
			}
		],
		"country": "BR",
		"region": "South America",
		"latitude": "-23.702098578231480",
		"longitude": "-46.697717823023980",
		"isFictional": false,
		"fictionalSource": null,
		"layouts": [
			{
				"layout_id": 1,
				"name": "Full Circuit",
				"start_year": 1990,
				"end_year": null,
				"length": 4.309,
				"image_id": "1.1"
			},
			{
				"layout_id": 2,
				"name": "Full Circuit",
				"start_year": 1940,
				"end_year": 1989,
				"length": 7.874,
				"image_id": "1.2"
			}
		]
	},
	{
		"id": 999,
		"name": "Dragon Trail",
		"similarNames": null,
		"categories": [],
		"country": "HR",
		"region": "Europe",
		"latitude": "45.1000000",
		"longitude": "15.2000000",
		"isFictional": true,
		"fictionalSource": "Gran Turismo",
		"layouts": [
			{
				"layout_id": 1,
				"name": "Seaside",
				"start_year": 2017,
				"end_year": null,
				"length": 5.209,
				"image_id": "999.1"
			}
		]
	}
]
```

---

## 💻 TypeScript Models

If you are consuming this API in a TypeScript environment, you can use the following interfaces to strictly type your data responses:

```typescript
export interface Subcategory {
	id: number;
	name: string;
	years?: number[] | null; // Array of years the category raced here. Can be optional/null if nested subcategories exist.
	subcategories?: Subcategory[]; // Nested subcategories (e.g., Formula 4 -> F1 Academy)
}

export interface TrackCategory {
	id: number;
	name: string;
	subcategories: Subcategory[];
}

export interface TrackLayout {
	layout_id: number;
	name: string;
	length: number; // Track length in kilometers (km). To get miles, multiply by 0.621371
	start_year: number;
	end_year: number | null;
	image_id: string;
}

export interface Track {
	id: number;
	name: string;
	similarNames: string[] | null; // Null if no alternative names exist
	categories: TrackCategory[];
	country: string;
	region: string | null;
	latitude: string | null; // Null if no coordinates are provided
	longitude: string | null; // Null if no coordinates are provided
	isFictional: boolean;
	fictionalSource: string | null;
	layouts: TrackLayout[];
}
```

---

## ⚖️ Legal & Copyright Notice

**RaceTracksAPI is a non-commercial, fan-made open-source project.**

- **Fictional Tracks:** All fictional track layouts, names, and concepts are the intellectual property and trademarks of their respective owners.
- **Original Assets:** This project **does not** distribute any ripped, extracted, or original 3D assets/textures from any commercial game. All SVG maps provided in this repository are 100% original vector representations created from scratch solely for informational, educational, and fan-use purposes.
- **Real Tracks:** Real-world track names and event titles are trademarks of their respective organizing bodies.

If you are a copyright holder and believe your content is being infringed, please open an issue or contact us, and the specific data/map will be promptly removed.

---

## 🤝 Contributing

Contributions are welcome! If you want to add a new track or fix a layout, feel free to submit a Pull Request. Please make sure new SVG files follow the same style guidelines as the existing ones.
