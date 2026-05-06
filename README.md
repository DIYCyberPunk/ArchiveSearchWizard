# ArchiveSearchWizard
A lightweight, self-hosted web interface designed for rapid file discovery within large-scale local or network-attached storage (NAS). Built with Python and Flask, it leverages the Whoosh search engine library to provide instantaneous search capabilities across millions of files without the overhead of a full database or file-content indexing.

🚀 Overview
In environments with massive file archives (e.g., University project folders or department shares), standard OS file explorers often struggle with speed and indexing. This tool solves that by:
    Metadata Indexing: Scanning a target directory (ARCHIVE_ROOT) and cataloging file metadata (names, paths, sizes, and modification dates) into a high-performance local index.
    Web-Based Search: Providing a clean, responsive UI for users to query the archive, sort results by relevance, date, or size, and directly download files.
    Enterprise-Ready Deployment: Designed to run as a persistent Windows Service with automated, scheduled index updates.

✨ Key Features
    Blazing Fast Performance: Indexes only file metadata, avoiding the bottleneck of reading file contents or copying data locally.
    Relevance-Based Search: Uses Whoosh's scoring algorithms to find the most relevant files based on user queries.
    Dynamic Sorting: Users can toggle between relevance, filename, file size, and last-modified date.
    Automated Maintenance: Includes a standalone indexing script designed for Windows Task Scheduler to keep the "catalog" up to date daily.
    Production-Grade Server: Uses Waitress as a production WSGI server, ensuring stability when running as a background service.

🛠️ Tech Stack
    Backend: Python 3.13+, Flask
    Search Engine: Whoosh
    WSGI Server: Waitress
    Frontend: HTML5, CSS3 (using Jinja2 templates)
    Logging: Robust logging for both the web application and the indexing process.

📁 Repository Structure
    app.py: The core Flask application and search logic.
    index_archive.py: The high-efficiency indexing engine.
    config.py: Centralized configuration for paths and application settings.
    templates/: HTML structures for the search interface.
    static/: CSS and visual assets.
