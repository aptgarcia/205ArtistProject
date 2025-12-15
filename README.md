Genius Quotes – CST205 Final Project

A Flask-based web application that retrieves random lyric excerpts for selected artists by combining the Genius API (for metadata)
with HTML scraping via BeautifulSoup (for actual lyric text). The app includes artist themed pages, dynamic quote loading, and browser-side TTS.

Installation & Setup Guide

Follow these steps before running the application.

1. Install Dependencies

All dependencies must be installed inside your CST205 virtual environment.

For Windows, access your venv:
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

Running the App on Windows

You should be at this step here:

(cst205env) PS C:\Users\user\Documents\cst205>

Navigate to your project directory
(cst205env) PS C:\Users\user\Documents\cst205> cd C:\Users\user\Documents\cst205\205Artist_Project\205ArtistProject


(Wherever you put the project folder after cloning the repo.)

Run Flask
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

Coordinates the entire workflow:

ID lookup: resolve_artist_id(artist_name)
→ song list: get_artist_songs(artist_id)
→ scraping: scrape_two_lyric_lines(song_url)
→ quote formatting


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

Global Genius-style color palette / Genius navbar

Homepage artist card grid

Per-artist themes

Joji’s drifting collage background, custom buttons, glass panel for layout

JavaScript (app.js)

Implements:

Quote updating logic

AJAX calls to the backend

Voice selection for browser-side TTS

Speak button functionality

How We Used ChatGPT in the Development Process

ChatGPT served as a guide and conceptual tutor, mainly for implementing the Genius API + BeautifulSoup webscraping logic in app.py and the Genius-like theming in site.css.
The team wrote, modified, and tested all actual code before finalization since there were many bugs/issues to work through.

1. Understanding Genius API limitations

ChatGPT clarified that the free Genius API does not return lyric text, which guided us toward combining API metadata with HTML webscraping.

2. Designing the architecture

ChatGPT helped us conceptualize a backend structure with small, focused helper functions rather than one large route.

Each of the suggestions corresponds to a specific function in our app.py:

API wrapper → genius_get()

Centralizes API requests and prevents duplicated request logic.

Artist resolution → resolve_artist_id()

Maps human-readable names to numeric Genius IDs, preferring exact matches when multiple hits appear.

Song filtering → get_artist_songs()

Filters out features, compilations, and tracks where the artist is not the primary performer.

Lyric scraping → scrape_two_lyric_lines()

Downloads the Genius page, identifies lyric containers, removes non-lyrical text, and returns cleaned lyric lines.

Final quote assembly → fetch_song_quote()

Coordinates the full pipeline and returns a dictionary suitable for both HTML templates and the /api/quote/<slug> endpoint.

This structure made the backend easier to maintain, test, and reuse.

3. Navigating dynamic JSON responses

ChatGPT explained Genius's nested JSON, why .get() prevents runtime errors, and how to reliably filter primary-artist songs.

4. Teaching BeautifulSoup scraping patterns

ChatGPT helped us identify:

div[data-lyrics-container="true"]

How to remove section headings

How to handle inconsistencies between Genius pages

5. Assisting with frontend TTS logic

ChatGPT explained asynchronous voice loading and recommended a simple pattern:

window.preferredVoiceName = "Microsoft Zira - English (United States)";

6. Debugging and step-by-step testing

ChatGPT guided us in testing:

Raw JSON output

Song lists

Scraped lines

Filtering logic

AJAX responses

TTS output

This helped validate each function as we encountered bugs.

7. Maintaining academic integrity

Although ChatGPT provided explanations and patterns, the team:

Wrote the final code

Integrated functions into the app

Debugged scraper and API edge cases

Adjusted HTML/CSS to fit the design

Verified all routing and behaviors independently

The final implementation reflects our understanding, with ChatGPT used as a conceptual resource for unfamiliar topics.

Conclusion

This project integrates Flask, HTML templates, CSS, JavaScript (including jQuery and TTS), the Genius API, and BeautifulSoup webscraping.
The combination of API metadata, real-time scraping, and browser-based text-to-speech creates an interactive lyric exploration application.

The structure, logic, and design decisions were developed by the team, with ChatGPT serving as a conceptual guide for understanding unfamiliar code structures, API behavior, and debugging strategies encountered during development.
