🚀 Chandan Saroj - Interactive Developer Portfolio
A high-performance, single-page portfolio website featuring real-time database integration, dynamic theming, and interactive UI elements. Built with a focus on MERN Stack principles but optimized for static hosting via GitHub Pages.
🔗 Live Demo: chandansaroj.in
✨ Key Features
 * 🎨 Dynamic Theme Engine: Users can switch between 6+ modes (Modern, Coder, Manga, Cybertron, etc.) and customize color palettes.
 * ☁️ Cloud Persistence: Theme settings are saved to Firebase Firestore, ensuring the site remembers your preferences across devices (authenticated via Anonymous Auth).
 * 💬 Wall of Thoughts: A real-time public message board where visitors can leave notes.
 * ❤️ Live Interactions:
   * Instagram-Style Like Button: Real-time global like counter.
   * Draggable Bubbles: Hero section message bubbles can be dragged around the screen (powered by GSAP).
   * Global View Counter: Tracks total hits to the page.
 * ⚡ Performance: Zero-dependency routing, optimized assets, and GSAP-powered animations.
🛠️ Technology Stack
Frontend
 * HTML5: Semantic structure.
 * CSS3: Heavy use of CSS Variables (--primary, --secondary) for the real-time theming engine.
 * JavaScript (ES6+): Module-based architecture for Firebase connection and logic.
Libraries & Tools
 * GSAP (GreenSock): Used for the timeline animations (scroll triggers) and Draggable plugin for the message bubbles.
 * FontAwesome: Iconography.
 * Google Fonts: Multiple font families loaded dynamically for theme modes (e.g., Rubik Glitch for Cybertron mode).
Backend (Serverless)
 * Google Firebase (v11.6.1):
   * Firestore: NoSQL database for storing messages, stats, and theme state.
   * Authentication: Anonymous Auth to secure database writes.
📂 Project Structure
This project follows a Single-File Component architecture optimized for static hosting simplicity.
root/
│
├── index.html            # 🧠 The Brain. Contains DOM, CSS, and JS Logic.
│
├── assets/
│   └── img/
│       └── chandan-saroj.png  # Profile image
│
├── .github/
│   └── workflows/
│       └── sync.yml      # (Optional) GitHub Action for automated backups
│
└── README.md             # This documentation

Why Single File?
For a portfolio, separating files adds network requests. By keeping CSS/JS internal (or critical CSS inline), the "First Contentful Paint" (FCP) is near-instantaneous.
⚙️ Logic & Process Flow
1. The Theme System
 * State: The app maintains a currentTheme object { primary, secondary, font }.
 * CSS Sync: A function applyTheme() updates the CSS Variables on the :root element.
 * Cloud Sync: When "Save to Cloud" is clicked, it writes this JSON object to website_state/global_theme in Firestore.
2. Interaction Counters (Likes/Views)
 * Views: On load, a Transaction runs. It reads the stats/global_views document, increments the count by 1, and writes it back atomically to prevent race conditions.
 * Likes: Clicking the heart triggers a similar transaction on stats/likes.
3. The "Wall of Thoughts"
 * Reading: A Firestore onSnapshot listener connects to the messages collection. It automatically pushes new messages to the UI whenever anyone posts, without refreshing the page (WebSockets).
 * Writing: The "Post" button sends a request to add a new document to the collection.
🚀 Setup & Installation Guide
If you want to clone this for your own portfolio:
1. Clone the Repository
git clone [https://github.com/chandanXP/chandanXP.github.io.git](https://github.com/chandanXP/chandanXP.github.io.git)
cd chandanXP.github.io

2. Set up Firebase
 * Go to the Firebase Console.
 * Create a project.
 * Build -> Authentication: Enable Anonymous sign-in.
 * Build -> Firestore Database: Create a database (Start in Test Mode).
3. Configure Database Rules (Crucial)
Go to Firestore -> Rules and paste this to secure your app while allowing the features to work:
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 1. Global Theme
    match /website_state/global_theme {
      allow read: if true;
      allow write: if request.resource.data.keys().hasAll(['primary', 'secondary', 'font']);
    }
    // 2. Messages
    match /messages/{messageId} {
      allow read: if true;
      allow write: if request.resource.data.text is string && request.resource.data.text.size() < 300;
    }
    // 3. Stats (Likes & Global Views)
    match /stats/{document} {
      allow read: if true;
      allow write: if request.resource.data.count is number;
    }
    // 4. Daily Views (Optional)
    match /page_views/{date} {
      allow read: if true;
      allow write: if request.resource.data.views is number;
    }
  }
}

4. Connect Your Code
 * In Firebase Console, go to Project Settings.
 * Copy the firebaseConfig object.
 * Open index.html, scroll to the bottom <script>, and replace the const firebaseConfig = { ... } with your keys.
🎨 Customizing Themes
To add a new color palette or font mode:
 * Add Font: Import the font in the <head> section via Google Fonts.
 * Add UI Button: In the #themePanel HTML section, add a new <div class="font-btn" data-font="...">Name</div>.
 * No JS Changes Needed: The logic automatically reads the data-font attribute and updates the CSS variable.
📄 License
© 2025 Chandan Saroj. Open for personal use and learning.
