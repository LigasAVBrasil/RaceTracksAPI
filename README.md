# RaceTracksAPI

A free, open-source JSON API providing structured data and high-quality SVG maps for racing circuits around the world. This project includes both real-world and fictional tracks.

> 🚧 **Work in Progress:** This API is actively under development! We are continuously working on implementing new maps and expanding our database. Our current roadmap prioritizes real-world tracks in the following order: **1. North American Endurance**, **2. Brazilian Touring Cars**, **3. North American Open-Wheel**, **4. German Touring Cars**, **5. Japanese Open-Wheel**, **6. Australian Touring Cars**, **7. British Touring Cars**, and **8. Argentine Touring Cars**. More real-world and fictional circuits will be added over time.

## 📌 Features

- **Accurate Data:** Coordinates (latitude/longitude), country codes, and alternative names for real-world circuits.
- **Fictional Tracks Included:** Metadata for fictional tracks from popular racing games, clearly separated from real-world data.
- **Custom SVG Maps:** Hand-drawn, high-quality SVG layouts for each track variation.
- **Predictable Structure:** A standardized data model ensuring all keys are present across all entries, making it highly friendly for typed languages.
- **Lightweight:** Completely static, serverless JSON hosted directly on GitHub.

---

## 🚀 How to Use

### Fetching the JSON Data

You can fetch the main `tracks.json` file directly from this repository using the raw GitHub URL (make sure to use the correct branch/path to your JSON file).

### 🖼️ Fetching Track Images (SVG)

The API provides an `image_id` inside each layout object. To get the corresponding SVG map, replace `${image_id}` in the following URL structure:

> `https://raw.githubusercontent.com/LigasAVBrasil/RaceTracksAPI/refs/heads/main/images/track_${image_id}.svg`

**Example:**
If the layout for _Autódromo José Carlos Pace (Interlagos)_ has `"image_id": "1.1"`, the image URL will be:
`https://raw.githubusercontent.com/LigasAVBrasil/RaceTracksAPI/refs/heads/main/images/track_1.1.svg`

### 🕰️ Handling Historical Layouts

Some circuits have undergone physical modifications over the years without changing their layout designation. For example, the dataset might contain multiple layouts named `"Full Circuit"` for the same track.

To properly differentiate between classic and modern configurations, you must evaluate the `start_year` and `end_year` properties within the layout objects. An `end_year` of `null` indicates that the layout is the current, active version.

---

## 📄 Data Structure Example

We use a strict data contract. This means fields like `isFictional` and `fictionalSource` will **always** be present, even for real-world tracks (where they will be `false` and `null`, respectively). Fictional tracks use the geographic center coordinates of their lore-based country.

```json
[
	{
		"id": 1,
		"name": "Autódromo José Carlos Pace",
		"similarNames": ["Autódromo de Interlagos", "São Paulo Grand Prix"],
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
				"length": 4.309,
				"start_year": 1990,
				"end_year": null,
				"image_id": "1.1"
			}
		]
	},
	{
		"id": 2,
		"name": "Dragon Trail",
		"similarNames": ["Dragon Trail - Seaside"],
		"country": "HR",
		"region": "Asia",
		"latitude": "45.1000000",
		"longitude": "15.2000000",
		"isFictional": true,
		"fictionalSource": "Gran Turismo",
		"layouts": [
			{
				"layout_id": 1,
				"name": "Seaside",
				"length": 5.209,
				"start_year": 2017,
				"end_year": null,
				"image_id": "12.1"
			}
		]
	}
]
```

---

## 💻 TypeScript Models

If you are consuming this API in a TypeScript environment, you can use the following interfaces to strictly type your data responses:

```typescript
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
	similarNames?: string[];
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
