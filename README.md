# ai_wine_label_app - Sommelier
An ai tool to understand and validate wine labels. Called Sommelier, this useful ai tool can decipher your wine labels and tell you things about the wine, its origin and its authenticity.

- https://www.uksommelierassociation.com/about-courses


🔧 1. Tech Stack Overview
Frontend (Mobile App)

Framework: React Native (cross-platform iOS/Android)

Libraries: expo-camera, react-native-image-picker, axios

Backend (API + AI Integration)

Framework: FastAPI (Python)

Image Processing: OpenCV, Pillow

AI Model: Custom fine-tuned CLIP or Google Vision API / AWS Rekognition

Data: Wine databases (Vivino, Wine-Searcher, LCBO, Wine.com via scraping/API)

Database

PostgreSQL or MongoDB for wine metadata, user logs, reviews

Optional AI Enhancements

OCR: Tesseract for extracting text

GPT for generating tasting notes and food pairings based on wine description

📲 2. App Features
🏷️ Label Scan
Use camera or upload image

Detect and crop wine label

Match against known wines using image embedding + nearest-neighbor search

📄 Wine Details
Name, region, grape variety

Vintage

Alcohol content

Average rating and user reviews

Tasting notes

Suggested food pairings

Price and availability

📝 User Interaction
Add to “My Cellar”

Rate & review wines

Share wine with friends

Save favorites and tasting notes

🧠 AI-Powered Features
Recommend similar wines

Suggest wines based on meal/photo

Translate foreign wine labels

🏗️ 3. Project Setup – Phase 1 Scaffolding
Backend (FastAPI)
bash
Copy
Edit
fastapi
uvicorn
pillow
opencv-python
tqdm
transformers
scikit-learn
python
Copy
Edit
# main.py
from fastapi import FastAPI, File, UploadFile
from wine_recognizer import identify_wine

app = FastAPI()

@app.post("/scan/")
async def scan_wine_label(file: UploadFile = File(...)):
    image = await file.read()
    result = identify_wine(image)
    return result
Frontend (React Native)
tsx
Copy
Edit
// ScanScreen.tsx (simplified)
import { launchCameraAsync } from 'expo-image-picker';

const takePicture = async () => {
  const result = await launchCameraAsync();
  if (!result.cancelled) {
    const formData = new FormData();
    formData.append('file', {
      uri: result.uri,
      name: 'label.jpg',
      type: 'image/jpeg',
    });
    const response = await fetch('https://your-api/scan/', {
      method: 'POST',
      body: formData,
    });
    const wineData = await response.json();
    setWineResult(wineData);
  }
};

🔮 Future Expansion Ideas:
- AR wine label overlay (scan label → get live overlay info)

- Barcode/QR support for commercial wines

- Voice assistant to describe the wine

- Wine & food pairing wizard

- Social feed for wine lovers


// App.tsx
import * as ImagePicker from 'expo-image-picker';

export default function App() {
  const [imageUri, setImageUri] = React.useState<string | null>(null);
  const [result, setResult] = React.useState<any>(null);

  const pickImage = async () => {
    const result = await ImagePicker.launchCameraAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      quality: 1,
    });

    if (!result.canceled) {
      const localUri = result.assets[0].uri;
      setImageUri(localUri);

      const formData = new FormData();
      formData.append('file', {
        uri: localUri,
        name: 'wine.jpg',
        type: 'image/jpeg',
      } as any);

      const response = await fetch('http://localhost:8000/scan/', {
        method: 'POST',
        body: formData,
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      });

      const data = await response.json();
      setResult(data);
    }
  };

  return (
    <View className="flex-1 justify-center items-center bg-white">
      <Button title="Scan Wine Label" onPress={pickImage} />
      {imageUri && <Image source={{ uri: imageUri }} style={{ width: 200, height: 200 }} />}
      {result && (
        <View className="mt-4">
          <Text>Name: {result.name}</Text>
          <Text>Region: {result.region}</Text>
          <Text>Grape: {result.grape}</Text>
          <Text>Tasting Notes: {result.notes}</Text>
        </View>
      )}
    </View>
  );
}

🧠 Backend (FastAPI)

python
Copy
Edit
# main.py
from fastapi import FastAPI, UploadFile, File
from fastapi.middleware.cors import CORSMiddleware
from PIL import Image
import io

app = FastAPI()

# Enable CORS for mobile development
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.post("/scan/")
async def scan(file: UploadFile = File(...)):
    image = Image.open(io.BytesIO(await file.read()))

    # Dummy logic (replace with AI model + database lookup)
    width, height = image.size
    if width > 500:
        return {
            "name": "Château Margaux",
            "region": "Bordeaux, France",
            "grape": "Cabernet Sauvignon",
            "notes": "Elegant, full-bodied with notes of blackcurrant and oak."
        }
    else:
        return {
            "name": "Barolo DOCG",
            "region": "Piedmont, Italy",
            "grape": "Nebbiolo",
            "notes": "Complex with floral aromas and firm tannins."
        }
To run it locally:

bash
Copy
Edit
pip install fastapi uvicorn pillow
uvicorn main:app --reload
🚀 Deployment
You can deploy the backend using:

Render (free tier)

Railway.app

Vercel (via Serverless Function)

🔮 Next Steps - gen AI Model
To improve:

Integrate Google Vision API or CLIP for label embedding

Use FAISS for nearest-neighbor image search

Store wine metadata in PostgreSQL or MongoDB

Future pushes:

- Help in integrating a real wine database (e.g., Vivino)?

- Dockerfile or GitHub Actions for CI/CD?
