# n8n YouTube Video Info & Subtitle Extraction


This repository contains two Python scripts designed for use within n8n workflows to extract information and subtitles from YouTube videos.  These scripts can be integrated into your n8n flows to automate video analysis, content creation, and other tasks.  Use them to enhance your n8n workflows and unlock the power of YouTube data.

## Scripts Overview

### 1. `yt_json_download.py`

This script downloads video information and subtitles from a YouTube video URL. It leverages the `yt-dlp` library for robust video information extraction and subtitle handling.

**Key Features:**

*   **Downloads Subtitles:** Automatically downloads available subtitles for a given YouTube video, prioritizing languages specified in the script (default: zh-Hant, zh-TW, zh-CN, zh, en, ja, ko, th, vi). It also attempts to download automatic subtitles if regular subtitles are not available.
*   **Extracts Video Metadata:** Retrieves the video title, description, chapters (if available), and URL.
*   **Cleans and Standardizes Subtitles:**  Removes redundant subtitle files, prioritizes English subtitles when available, and renames the chosen subtitle file to `subtitle.vtt` for consistent access.
*   **Error Handling:** Implements robust error handling, providing informative messages via `stderr` if issues occur during download or processing.  It always returns a valid JSON object, even in case of errors, ensuring predictable behavior within n8n.
*   **JSON Output:**  Outputs all extracted data, including subtitles file path, in JSON format for easy integration into n8n workflows. The JSON is printed to `stdout`.
*   **Clears Download Folder:** Empties the download folder before each run to avoid conflicts and ensure only the most recent subtitles are processed.
*   **Custom Logger:**  Uses a custom logger to redirect warnings and errors to `stderr`, allowing n8n to capture them for debugging.
*   **Command-line Argument:** Accepts the YouTube video URL as a command-line argument.

**How to Use in n8n:**

1.  **Install `yt-dlp`:**  Ensure `yt-dlp` is installed in your n8n environment. You can install it using `pip install yt-dlp`.
2.  **Create an n8n Execute Command Node:**  Add an "Execute Command" node to your n8n workflow.
3.  **Configure the Command:**
    *   **Command:**  Specify the path to the `yt_json_download.py` script followed by the YouTube video URL as an argument.  For example: `python /path/to/yt_json_download.py https://www.youtube.com/watch?v=YOUR_VIDEO_ID`.  Replace `/path/to/yt_json_download.py` with the actual path to your script.
    *   **Output:** Configure the "Output" settings to capture both `stdout` and `stderr`.
4.  **Parse the JSON Output:**  Connect a "Function" node to the "Execute Command" node.  In the Function node, use `JSON.parse($input.all()[0].json)` to parse the JSON output from `stdout` and access the video information.  Handle potential errors reported in `stderr` if necessary.
5.  **Access Video Information:**  You can now access the video title, description, chapters, and subtitle file path from the parsed JSON object within your n8n workflow.

### 2. `vtt_to_json.py`

This script converts a VTT (WebVTT) subtitle file to a JSON format. It handles both simple and complex VTT formats and removes duplicate subtitle entries for cleaner output.

**Key Features:**

* VTT Parsing: Parses VTT subtitle files, extracting timestamps and subtitle text.

* Complex VTT Support: Handles complex VTT formats with inline timestamps and tags (<c>).

* Duplicate Removal: Removes consecutive duplicate subtitle entries to avoid redundancy.

* Timestamp Conversion: Converts timestamps from HH:MM:SS.milliseconds format to seconds (floating-point).

* Error Handling: Provides error messages to stderr if the VTT file is not found or cannot be parsed. Returns None on failure.

* JSON Output: Outputs the subtitles in JSON format, including start and end times for each subtitle. The JSON is printed to stdout.

* Automatic Deletion: Deletes the input VTT file after successful JSON conversion. This behavior can be adjusted if needed.
