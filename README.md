# Impact Diaries 

> A full-stack blogging platform with AI-powered features, built as a collaborative open-source project under GSSoC (GirlScript Summer of Code).

---

 Project Overview

**Impact Diaries** is a feature-rich, multi-page blogging web application that allows users to read, write, and interact with blog posts across multiple categories such as Travel, Technology, Food, Fashion, and Lifestyle. The project integrates AI-powered tools — including a grammar correction API and a Gemini-based chatbot — to enhance the writing and reading experience.

The frontend is built entirely with vanilla HTML, CSS, and JavaScript. The backend is a lightweight Python Flask server exposing a REST API for grammar correction using a Hugging Face T5 transformer model.

---

## 🛠️ Tech Stack

| Layer      | Technology Used                                      |
|------------|------------------------------------------------------|
| Frontend   | HTML5, CSS3, Vanilla JavaScript                      |
| Backend    | Python, Flask, Flask-CORS                            |
| AI / NLP   | Hugging Face Transformers (T5 model), Google Gemini API |
| Styling    | Custom CSS, Font Awesome Icons, Swiper.js (carousel) |
| Storage    | Browser `localStorage` (bookmarks, dark mode state) |
| Build/Dist | Static HTML pages (no bundler / no framework)        |

---

# Project Structure

```
impactDiaries/
│
├── app.py                    # Flask backend — grammar correction REST API
│
├── frontend/                 # (folder) Frontend-specific assets
├── backend/                  # (folder) Backend-related files
├── assets/                   # Images, icons, and media assets
├── images/                   # Additional image assets
├── dist/                     # Distribution/build output files
│
├── index.html                # Landing/home page
├── blog.html                 # Main blog article page (1757 lines)
├── category.html             # Blog category listing page
├── tech.html                 # Technology category page
├── travel.html               # Travel category page
│
├── chatbot.html              # AI chatbot page (Google Gemini powered)
├── chatbot.gif               # Chatbot animation
│
├── start_writing.html        # Blog post creation/editor page
├── start_writing.js          # JS logic for the writing page
│
├── bookmarks.html            # Saved/bookmarked articles page
│
├── contact.html              # Contact page
├── contact_us.html           # Contact Us form page
├── contact_us.css            # Styles for contact page
├── contact_us.js             # Form validation and submission logic
│
├── comment1.html             # Individual comment pages (1–14)
├── ... (comment2–14.html)
├── comment.css               # Styles for comment section
├── comment.js                # Comment rendering logic
├── blog-comment.js           # Blog-specific comment logic
│
├── darkMode.js               # Dark/light mode toggle logic
├── Carousel.js               # Image/content carousel logic
│
├── style.css                 # Global stylesheet
├── styles.css                # Additional global styles
├── blog.css                  # Blog article-specific styles
├── category.css              # Category page styles
├── comment.css               # Comment section styles
├── testp.css / testp.js      # Test/prototype CSS and JS
│
├── sw.js                     # Service Worker (PWA support)
├── words.json                # Word/dictionary data (used for features)
├── 404.html                  # Custom 404 error page
│
├── CODE_OF_CONDUCT.md        # Community code of conduct (GSSoC)
├── CONTRIBUTING.md           # Contributor guidelines
└── __pycache__/              # Python bytecode cache
```

---

## ⚙️ Features

### 1. Blog Reading Platform
- Multi-page blog with articles across categories: Travel, Technology, Food, Fashion, Lifestyle.
- Reading progress bar on scroll (CSS + JS).
- Post navigation (Previous / Next post links).
- Post tags displayed per article.
- Author section with name, bio, and social media links (Facebook, Instagram, Twitter, GitHub).

### 2. User Authentication UI
- Login and Signup modal pop-ups.
- Password strength meter (Weak → Very Strong).
- Show/hide password toggle.
- "Remember Me" using `localStorage`.
- Forgot Password modal.
- Google Sign-In button (OAuth redirect).

### 3. Bookmarks System
- Bookmark/unbookmark articles using browser `localStorage`.
- Dedicated `bookmarks.html` page to view saved articles.
- Bookmark state persists across page reloads.

### 4. Comment System
- Nested comment threads (replies to comments).
- Like button on each comment (client-side counter).
- Dynamic avatar with color generated from the commenter's name using a hash function.
- Form validation: name field rejects numbers/symbols.
- Comments rendered client-side with JavaScript DOM manipulation.

### 5. Dark Mode
- Toggle between light and dark themes.
- Uses CSS variables and a `darkMode.js` script.
- State persisted using `localStorage`.

### 6. AI Chatbot (Google Gemini)
- `chatbot.html` integrates Google Gemini Pro API (`gemini-pro` model).
- Conversational multi-turn chat (history maintained in session).
- Supports Markdown rendering using `marked.js`.
- Code syntax highlighting using `highlight.js`.
- Safety settings configured to block high-harm content.
- Loading animation with bouncing dots while awaiting AI response.
- Keyboard shortcut: press Enter to send messages.

### 7. Grammar Correction API (Flask + T5)
- `app.py` exposes a POST endpoint: `/grammar-correct`.
- Uses the `deep-learning-analytics/GrammarCorrector` T5 model from Hugging Face.
- Tokenizes input with `T5Tokenizer`, generates corrections using beam search (`num_beams=5`).
- CORS enabled via `flask-cors` to allow frontend requests.
- Runs on `localhost:5000` in debug mode.

### 8. Blog Writing Editor
- `start_writing.html` with `start_writing.js` provides a blog creation interface.

### 9. Service Worker / PWA
- `sw.js` registers a Service Worker enabling offline capabilities and caching.

### 10. Responsive Design
- Hamburger menu for mobile viewports.
- Responsive layout using CSS media queries.
- Swiper.js for touch-friendly carousels.

### 11. Categories Page
- `category.html` organizes posts by topic.
- Individual category pages: `tech.html`, `travel.html`.

---

## 🔌 Backend API

### `POST /grammar-correct`

Corrects grammar in the submitted text using a fine-tuned T5 model.

**Request Body:**
```json
{
  "text": "She go to school everyday."
}
```

**Response:**
```json
{
  "corrected_text": "She goes to school every day."
}
```

**How it works:**
1. The input is prefixed with `"grammar: "` (T5 task prefix convention).
2. The `T5Tokenizer` converts text to token IDs.
3. `T5ForConditionalGeneration.generate()` runs beam search (5 beams) up to 256 tokens.
4. The output token IDs are decoded back to text (special tokens skipped).
5. The corrected text is returned as JSON.

---

## AI Chatbot Architecture

- **Model**: Google Gemini Pro (`gemini-pro`) via `@google/generative-ai` ES module.
- **Multi-turn chat**: A single `chat` object is initialized on first message and reused for conversation history.
- **Safety settings**: `HARM_CATEGORY_HARASSMENT` set to `BLOCK_ONLY_HIGH`.
- **Max output tokens**: 4000 per response.
- **Markdown parsing**: `marked.js` converts AI markdown responses to HTML.
- **Syntax highlighting**: `highlight.js` styles code blocks in AI responses.

---

##  Key JavaScript Patterns Used

- **Event Delegation**: Comment like/reply buttons use a single event listener on the parent `#commentList`.
- **Recursive Data Traversal**: `addReply()` recursively walks the nested comment tree to find the parent comment.
- **Dynamic Color Generation**: Avatar colors generated via a string hashing function: `charCodeAt + bit-shift`.
- **LocalStorage State Management**: Bookmarks, dark mode preference, and "Remember Me" all use `localStorage`.
- **ES Module Import**: Chatbot uses native `<script type="module">` with `importmap` for the Gemini SDK.
- **Intersection/Scroll Events**: Reading progress bar and back-to-top button use `window.scrollY`.
- **Password Strength Meter**: Multi-criteria regex checks (lowercase, uppercase, digits, symbols, length).
- **Service Worker Registration**: `sw.js` for PWA offline caching.

---

##  How to Run

### Frontend Only
1. Clone the repository:
   ```bash
   git clone https://github.com/preeti404-hue/impactDiaries.git
   cd impactDiaries
   ```
2. Open `blog.html` or `index.html` in a browser (Live Server recommended for local dev).

### Backend (Grammar Correction API)
1. Install Python dependencies:
   ```bash
   pip install flask flask-cors transformers torch
   ```
2. Run the server:
   ```bash
   python app.py
   ```
3. Server starts at `http://localhost:5000`.

---

##  Pages Summary

| File | Purpose |
|------|---------|
| `index.html` | Home / Landing page |
| `blog.html` | Main blog article with full feature set |
| `category.html` | Browse posts by category |
| `tech.html` | Technology posts |
| `travel.html` | Travel posts |
| `chatbot.html` | Gemini AI chatbot interface |
| `start_writing.html` | Blog post editor/creator |
| `bookmarks.html` | Saved articles list |
| `contact_us.html` | Contact form with validation |
| `404.html` | Custom error page |
| `comment1–14.html` | Individual comment thread pages |

---

## 📦 External Libraries & CDNs

| Library | Version | Purpose |
|---------|---------|---------|
| Font Awesome | 6.4.0 / 6.5.1 | Icons (user, clock, bookmark, social) |
| Swiper.js | latest | Touch carousel for blog images |
| Tailwind CSS | 2.2.19 | Utility classes in chatbot page |
| Bootstrap | 5.3.0 | Grid and UI components |
| marked.js | 12.0.1 | Markdown to HTML parsing |
| highlight.js | 11.9.0 | Code syntax highlighting |
| Google Generative AI SDK | latest | Gemini Pro chatbot integration |
| Hugging Face Transformers | latest | T5 grammar correction model |

---

##  Contributing

This project is part of **GSSoC (GirlScript Summer of Code)**. Contributions are welcome following the guidelines in `CONTRIBUTING.md` and the community standards in `CODE_OF_CONDUCT.md`.

---

##  License

This project follows the Contributor Covenant Code of Conduct. See `CODE_OF_CONDUCT.md` for details.

---

##  Repository

[https://github.com/preeti404-hue/impactDiaries](https://github.com/preeti404-hue/impactDiaries)

---
