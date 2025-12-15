# Genius Quotes – CST205 Final Project Documentation

### A Flask-based web application that retrieves random lyrics for pre-selected artists by combining the Genius API (for metadata) with HTML scraping via BeautifulSoup (for actual lyric text), 
### ChatGPT was used during development and is further explained in this section below: 

### How We Used ChatGPT in the Development Process

## The app includes artist themed pages, dynamic quote loading, and browser-side TTS.

## Installation & Setup Guide

Follow these steps before running the application.

## Install Dependencies

All dependencies must be installed inside your CST205 virtual environment.

## Activate your venv (Windows Example)
PS C:\Users\user> cd C:\Users\user\Documents\cst205

PS C:\Users\user\Documents\cst205> .\cst205env\Scripts\Activate.ps1

(cst205env) PS C:\Users\user\Documents\cst205>

## Make sure you have these installed:
### Flask
Used for routing, template rendering, and serving the app.

python -m pip install flask

### Requests
Used to call the Genius API and download song webpages.

python -m pip install requests

### BeautifulSoup4
Used to scrape actual lyric content from Genius.

python -m pip install beautifulsoup4

## Running the App on Windows
### You should be at this step here:
(cst205env) PS C:\Users\user\Documents\cst205>

### Navigate to your project directory
cd C:\Users\user\Documents\cst205\205Artist_Project\205ArtistProject
(Wherever you put the project folder after cloning the repo)

### Run Flask
flask --app app --debug run

### Open in your browser
http://127.0.0.1:5000/

## Summary of How the Application Works

### This application displays random lyric excerpts for the following artists:
### Taylor Swift, Joji, Travis Scott, and Finneas.

## Backend Pipeline Overview
### 1. Genius API Lookup
The Genius API is used to:

Resolve the correct artist ID

Retrieve a list of that artist’s songs

Filter only songs where the artist is the primary performer

### 2. Random Song Selection
A song is randomly selected from the filtered metadata.

### 3. HTML Scraping for Lyrics
Because the free Genius API does not provide lyrics, the app:

Requests the official Genius webpage

Parses HTML using BeautifulSoup

Extracts only div data-lyrics-container="true" content

Removes section headings (e.g., “[Chorus]”, “[Verse]”)

Removes filler words and short non-lyric lines

Returns two consecutive lyric lines

### 4. Dynamic Quote Display
Clicking New Quote sends a jQuery request to a backend JSON endpoint:

/api/quote/artist-slug

The lyric, song metadata, and Genius link update without reloading the page.

### 5. Text-to-Speech Functionality
Each artist page specifies a preferred voice.

The browser’s Web Speech API reads only the displayed lyric text aloud.

Custom Artist Page Designs

Taylor Swift: List-based layout for albums and songs

Joji: Drifting collage background and glass panel UI

Travis Scott & Finneas: Themed layouts with custom styling

# Project Structure
# project/
### ├── app.py
### ├── celebrities.json
# ├── static/
### │   ├── css/site.css
### │   ├── js/app.js
### │   └── img/
│       ├── joji_bg.jpg
│       ├── taylor.png
│       ├── joji.jpeg
│       ├── finneas2019.png
│       ├── travis1.webp
│       ├── travis2.jpeg
│       └── travis3.jpg
# └── templates/
### ├── base.html
### ├── index.html
### ├── joji.html
### ├── taylor-swift.html
### ├── travis-scott.html
### └── finneas.html

## Backend Overview (Flask)
### genius_get(path, **params)
A wrapper around all Genius API calls. Attaches headers, manages JSON parsing, and performs error checking.

### resolve_artist_id(artist_name)
Searches Genius using the /search endpoint and returns the correct artist ID, preferring exact matches.

### get_artist_songs(artist_id)
Retrieves songs for a given artist and filters the list to include only primary-artist tracks.

### scrape_two_lyric_lines(song_url)
Requests the song’s webpage and extracts lyric text using BeautifulSoup.

### fetch_song_quote(artist_name)
Coordinates the entire workflow:

ID lookup resolve_artist_id(artist_name) →

get songs get_artist_songs(artist_id) → 

pick one → 

scrape lyrics scrape_two_lyric_lines(song_url)→ 

format quote fetch_song_quote(artist_name)

Returns a dictionary used by both the HTML templates and the /api/quote/slug endpoint.

## Routes

/ → Homepage

/celebrity-name/slug → Per-artist page

/api/quote/slug → JSON endpoint for dynamic lyric updates

## Frontend Overview
### Templates
All HTML pages extend base.html, which includes:

Bootstrap
jQuery
Global CSS
Genius-style navbar

### Each artist page defines:
Custom design
New Quote and Speak buttons
Quote display
A preferred TTS voice in extra_scripts

### CSS (site.css)
Defines:
Genius-style color palette and navbar
Homepage artist card grid
Per-artist themes
Joji’s drifting collage background and glass UI elements

### JavaScript (app.js)
Implements:
AJAX quote updating
Voice selection for browser-side TTS
Speak button functionality

## How We Used ChatGPT in the Development Process

### ChatGPT served as a conceptual guide for:
### Genius API limitations
### BeautifulSoup scraping logic
### Backend architectural design
### JSON parsing strategies
### TTS behavior and voice selection
### Debugging errors we encountered with webscraping logic, routing, TTS, etc.

### All final code was written, modified, and tested by the team.

## 1. Understanding Genius API Limitations
### ChatGPT clarified that the free Genius API does not return lyric text, requiring metadata retrieval plus HTML scraping.

## 2. Designing the Architecture
### ChatGPT suggested structuring app.py with small, focused helper functions that map directly to:

genius_get() – API wrapper

resolve_artist_id() – Artist lookup

get_artist_songs() – Filtered song list retrieval

scrape_two_lyric_lines() – Lyric extraction

fetch_song_quote() – Fully utilizes helper functions for our desired workflow process.

This separation improved maintainability, readability, and debugging.

## 3. Navigating Dynamic JSON Responses

### ChatGPT explained:
The nested structure of Genius JSON responses

Why .get() prevents runtime errors

How to reliably filter primary-artist songs

## 4. BeautifulSoup Scraping Patterns
### ChatGPT helped identify:
div[data-lyrics-container="true"]

How to remove section headers and filler lines

How to deal with page inconsistencies

## 5. Frontend TTS Logic
### ChatGPT clarified asynchronous voice loading and suggested using:

window.preferredVoiceName = "Microsoft Zira - English (United States)";

Because voices load asynchronously, each artist page specifies a preferredVoiceName, and app.js selects that voice after the browser finishes loading available voices. 

## 6. Debugging and Step-by-Step Testing
### ChatGPT guided us through:
Inspecting JSON

Validating scraper output

Testing AJAX updates (stands for Asynchronous JavaScript and XML)

This is defined as: Updating parts of a webpage by sending background requests to the server without reloading the entire page.

Verifying TTS functionality

## 7. Maintaining Academic Integrity
### Although ChatGPT provided conceptual support, the team:
Wrote all final code

Integrated all components

Debugged scraper and API issues

Finalized all HTML/CSS/JS on our own depending on how we wanted to customize our webpage(specific fonts, padding, colors, margins, etc.)

## Conclusion
This project integrates Flask, HTML templates, CSS, JavaScript (including jQuery and TTS), the Genius API, and BeautifulSoup webscraping. 

The combination of API metadata, real-time scraping, and browser-based text-to-speech gives a fun and interactive user experience. 

The structure, logic, and design decisions throughout the project were developed by the team, with ChatGPT serving as a conceptual resource for understanding unfamiliar code structures, API structures, and debugging strategies for all the errors we encountered.
