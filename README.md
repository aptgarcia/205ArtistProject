🎵 Genius Quotes – CST205 Final Project
A Flask-based web application that retrieves random lyric excerpts for selected artists by combining the Genius API (for metadata) 
with HTML scraping via BeautifulSoup (for actual lyric text). The app includes artist themed pages, dynamic quote loading, and browser-side TTS.

Installation & Setup Guide
Follow these steps before running the application.

All dependencies must be installed inside your CST205 virtual environment.
For windows access your venv:

PS C:\Users\user> cd C:\Users\user\Documents\cst205
PS C:\Users\user\Documents\cst205> .\cst205env\Scripts\Activate.ps1
(cst205env) PS C:\Users\user\Documents\cst205>

Make sure you have these installed:
Flask
Used for routing, template rendering, and serving the app.
python -m pip install flask

Requests
Used to call the Genius API and download song webpages.
python -m pip install requests

BeautifulSoup4
Used to scrape actual lyric content from Genius.
python -m pip install beautifulsoup4

Running the App on Windows:
You should be at this step here:
(cst205env) PS C:\Users\user\Documents\cst205>

(cst205env) PS C:\Users\user\Documents\cst205> cd C:\Users\user\Documents\cst205\205Artist_Project\205ArtistProject (wherever you put the project folder after cloning repo)
(cst205env) PS C:\Users\user\Documents\cst205\205Artist_Project\205ArtistProject> flask --app app --debug run

Open in browser by using:
http://127.0.0.1:5000/

Summary of How the Application Works
This application displays random lyric excerpts for the following artists: Taylor Swift, Joji, Travis Scott, and Finneas.

The backend pipeline proceeds as follows:
Genius API Lookup
The Genius API is used to:
Resolve the correct artist ID
Retrieve a list of that artist’s songs
Filter only songs where the artist is the primary performer

Random Song Selection
A song is randomly selected from the filtered metadata.

HTML Scraping for Lyrics
Because the free Genius API does not provide lyrics, the app:
Requests the official Genius webpage for the selected song
Parses HTML using BeautifulSoup
Extracts only <div data-lyrics-container="true"> content
Removes section headings (e.g., “[Chorus]”, “[Verse]”)
Removes filler words and short non-lyric lines
Returns two consecutive lyric lines as the final excerpt

Dynamic Quote Display
Clicking “New Quote” sends a jQuery request to a backend JSON endpoint:
/api/quote/<artist-slug>
The quote, song metadata, and Genius link are updated on the page without reloading.

Text-to-Speech Functionality
Each artist page specifies a preferred voice.
The browser’s Web Speech API reads only the displayed lyric text aloud.

Custom Artist Page Designs

Taylor Swift: List-based layout for albums and songs
Joji: Drifting collage background and glass panel UI
Travis Scott and Finneas: Themed layouts with custom styling

Project Structure
project/
├── app.py
├── celebrities.json
├── static/
│   ├── css/site.css
│   ├── js/app.js
│   └── img/
│       ├── joji_bg.jpg
│       ├── taylor.png
│       ├── joji.jpeg
│       ├── finneas2019.png
│       ├── travis1.webp
│       ├── travis2.jpeg
│       └── travis3.jpg
└── templates/
    ├── base.html
    ├── index.html
    ├── joji.html
    ├── taylor-swift.html
    ├── travis-scott.html
    └── finneas.html

Backend Overview (Flask)
genius_get(path, **params)
A wrapper around all Genius API calls. Attaches headers, manages JSON parsing, and performs error checking.

resolve_artist_id(artist_name)
Searches Genius using the /search endpoint and returns the correct artist ID, preferring exact matches.

get_artist_songs(artist_id)
Retrieves songs for a given artist and filters the list to include only primary-artist tracks.

scrape_two_lyric_lines(song_url)
Requests the song’s webpage and extracts lyric text using BeautifulSoup.

fetch_song_quote(artist_name)
Coordinates the entire workflow --
ID lookup: resolve_artist_id(artist_name) → 
song list: get_artist_songs(artist_id) → 
scraping: scrape_two_lyric_lines(song_url) → 
quote formatting.
Returns a consistent dictionary used by both HTML templates and the JSON API.

Routes
/ → Homepage
/celebrity/<slug> → Per-artist page
/api/quote/<slug> → JSON endpoint for dynamic quote updates

Frontend Overview
Templates

All HTML pages extend base.html, which includes:
Bootstrap
jQuery
Global site CSS
The Genius-themed navbar

Each artist template defines:
Custom design
The quote section
New Quote and Speak buttons
A preferred TTS voice in extra_scripts

CSS (site.css)

Defines:
Global Genius-style color palette/Genius navbar
Homepage artist card grid
Per-artist themes
Joji’s drifting collage background, custom buttons, glass panel for layout.

JavaScript (app.js)
Implements:

Quote updating logic
AJAX calls to the backend
Voice selection for browser-side TTS
Speak button functionality

How we used ChatGPT in the development process
ChatGPT served as a guide and conceptual tutor, mainly for implementing the Genius API + BeautifulSoup webscraping logic on app.py/site.css on how to implement the genius-like themes on every page by creating a navbar.
The team wrote, modified, and tested all actual code before finalization since there were many bugs/issues to work through.

1. Understanding Genius API limitations
ChatGPT clarified that the free Genius API does not return lyric text, which guided us toward combining API metadata with HTML webscraping.

2. Designing the architecture
ChatGPT helped us conceptualize a backend structure with small, focused helper functions rather than one large route.
Each of the suggestions corresponds to a specific function in our app.py:

API wrapper → genius_get()
Centralizes all Genius API requests in one place, adding the authorization header, passing query parameters, and returning parsed JSON. 
This prevents duplicated request logic throughout the code.

Artist resolution → resolve_artist_id()
Uses the Genius /search endpoint to convert a human-readable artist name (e.g., "Joji") into the numeric Genius artist ID needed by other endpoints. 
It also applies logic to prefer an exact name match when multiple hits are returned.

Song filtering → get_artist_songs()
Calls the Genius /artists/{id}/songs endpoint across multiple pages, then filters the results so that only songs where this artist is the primary artist are kept. 
This avoids pulling in random features, compilations, or appearances on other artists’ tracks.

Lyric scraping → scrape_two_lyric_lines()
Takes the Genius song URL returned in the metadata, downloads the HTML with requests, and uses BeautifulSoup to locate the lyric containers, 
strip out non-lyric labels (like [Chorus]), and return two consecutive, cleaned lyric lines.

Final quote assembly → fetch_song_quote()
Orchestrates the entire process by calling the previous helpers in order (resolve artist → get songs → pick one → scrape lyrics), then packages 
the result into a simple dictionary with content, author, song, and url fields that both the HTML templates and the /api/quote/<slug> endpoint can use.

Structuring the backend this way made the application easier to test, debug, and reuse, because each function has a clear single responsibility and can be verified independently.

3. Navigating dynamic JSON responses
ChatGPT helped explain how Genius structures its nested JSON, why .get() patterns prevent runtime errors, and how to filter primary-artist songs reliably.

4. Teaching BeautifulSoup scraping patterns
ChatGPT helped us identify:
Relevant HTML selectors (div[data-lyrics-container="true"])
How to remove non-lyric text (section headings, short filler lines)
How to handle inconsistencies across Genius pages

5. Assisting with frontend TTS logic
ChatGPT helped us understand asynchronous voice loading and recommended a simple pattern for per-artist voice selection using:
window.preferredVoiceName = "Microsoft Zira - English (United States)";

6. Debugging and step-by-step testing
ChatGPT guided us through testing:
Raw JSON output
Song lists
Scraped lines
Filtering logic
AJAX responses
TTS output

We used this to validate each function when we ran into bugs throughout the development of this project.

7. Maintaining academic integrity
Although ChatGPT provided explanations and example patterns, the team fully:
Wrote the final code
Integrated it into the project
Debugged scraper and API edge cases
Adjusted all HTML/CSS to fit our webapp design
Completed individually testing our webpages to make sure everything works/is routed properly.

The final implementation reflects our understanding, with ChatGPT used to support unfamiliar concepts we did not specifically cover in-depth.

Conclusion
This project integrates Flask, HTML templates, CSS, JavaScript (including jQuery and TTS), the Genius API, and BeautifulSoup webscraping. 
The combination of API metadata, real-time scraping, and browser-based text-to-speech gives a fun and interactive user experience.
The structure, logic, and design decisions throughout the project were developed by the team, 
with ChatGPT serving as a conceptual resource for understanding unfamiliar code structures, API structures, and debugging strategies for all the errors we encountered.

