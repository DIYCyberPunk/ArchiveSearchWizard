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

1. Introduction and Overview
The Archive Search Wizard is a web-based application designed to provide a searchable interface for files stored in the designated network archive (ARCHIVE_ROOT). It consists of a backend search index built by a daily script and a Flask-based web application served as a Windows service.

2. System Requirements
Operating System: Windows Server (or compatible Windows OS for service hosting).
Python Version: Python 3.9 or higher (Ensure this specific version is installed and accessible).
Network Access: The server hosting the application must have read access to the designated ARCHIVE_ROOT network share.

3. Application Structure
The application's core files are located in the C:\ArchiveSearchWizard directory. Maintaining this exact directory structure is crucial for the application's functionality.
app.py: The main Flask web application. It processes user search queries and serves the web interface.
config.py: Contains configurable parameters for the application, such as archive path, index location, and application name.
index_archive.py: A utility script responsible for building and updating the Whoosh search index.
static/: Contains static web assets.
static/style.css: Cascading Style Sheet for web page styling.
templates/: Contains HTML templates for the web interface.
templates/base.html: Base template defining common page structure.
templates/index.html: Homepage template.
templates/search_results.html: Template for displaying search results.
logs/: (Created by application) Directory for storing application log files.

4. Initial Setup and Deployment (For New Deployments or Rebuilds)
This section outlines the procedure for setting up the Archive Search Wizard from scratch.

4.1. Prerequisites
Install Python: Download and install Python 3.9+ from python.org. Ensure "Add Python to PATH" is selected during installation.
Create Application Directory: Create the C:\ArchiveSearchWizard directory and place all application files (app.py, config.py, index_archive.py, static/, templates/) within it.

4.2. Install Dependencies
Install Dependencies: Create a requirements.txt file in C:\ArchiveSearchWizard with the following content:
Flask
Whoosh
Waitress
Then, install them:
DOS
pip install -r requirements.txt

4.3. Configuration (config.py)
The config.py file holds critical operational parameters. Edit this file using a plain text editor (e.g., Notepad++, VS Code).
ARCHIVE_ROOT: Mandatory. Set this to the full network path of the archive to be indexed (e.g., ARCHIVE_ROOT = r'\\UCOMM-FS1.ad.wsu.edu\Ucomm\Project Folders\Archive'). The r prefix ensures the string is treated as a raw string, preventing issues with backslashes.
WHOOSH_INDEX_DIR: Mandatory. Set this to a local path where the search index files will be stored (e.g., WHOOSH_INDEX_DIR = r'C:\Temp\SearchIndexes'). This directory should be on a fast, local drive.
APP_NAME: Set this to the desired application name (e.g., APP_NAME = "Archive Search Wizard").

4.4. Initial Index Creation
Before the web application can function, the search index must be created.
Run Indexing Script:
DOS
python index_archive.py
Monitor Output: Observe the console for progress messages and any errors. The initial indexing run may take a considerable amount of time depending on the archive size.
Verify Index Directory: Confirm that the WHOOSH_INDEX_DIR (e.g., C:\Temp\SearchIndexes) is populated with Whoosh index files.

4.5. Scheduled Index Update Task (index_archive.py)
The index update script should run periodically to keep the search index synchronized with the archive. A daily schedule is recommended.
Open Task Scheduler: Search for "Task Scheduler" in the Windows Start Menu.
Create New Task:
Go to Task Scheduler Library > Create Basic Task... (or Create Task... for more options).
Name: ArchiveSearchWizard_IndexUpdate
Trigger: Daily, set desired time (e.g., 6:00 AM).
Action: Start a program
Program/script: Point this to the python.exe inside your virtual environment.
C:\ArchiveSearchWizard\venv\Scripts\python.exe
Add arguments (optional):
C:\ArchiveSearchWizard\index_archive.py
Start in (optional):
C:\ArchiveSearchWizard
User Account: Crucially, configure the task to run under a user account that has read access to the ARCHIVE_ROOT network share and read/write access to WHOOSH_INDEX_DIR. This often requires selecting "Run whether user is logged on or not" and providing credentials.

4.6. Windows Service Setup (app.py)
The main web application (app.py) is designed to run continuously as a Windows service using waitress. Tools like NSSM (Non-Sucking Service Manager) or pywin32 can be used to create the service.
Example using NSSM (Recommended for ease of setup):
Download NSSM: Obtain nssm.exe from nssm.cc. Place it in a directory included in your system's PATH, or directly in C:\ArchiveSearchWizard.
Open Command Prompt (as Administrator):
DOS
cd C:\ArchiveSearchWizard
Install Service:
DOS
nssm install "Archive Search Wizard"
This will open an NSSM GUI. Configure as follows:
Path: C:\ArchiveSearchWizard\venv\Scripts\python.exe (Path to your virtual environment's Python interpreter)
Arguments: C:\ArchiveSearchWizard\app.py
Startup directory: C:\ArchiveSearchWizard
Service Account: On the "Log On" tab, set this to a user account that has read access to both WHOOSH_INDEX_DIR and ARCHIVE_ROOT (for potential access during startup or if the searcher needs direct file access). This is typically the same account used for the scheduled task.
I/O: On the "I/O" tab, configure output and error logs:
Output: C:\ArchiveSearchWizard\logs\app_stdout.log (or app.log)
Error: C:\ArchiveSearchWizard\logs\app_stderr.log (Ensure the logs directory exists in C:\ArchiveSearchWizard).
Exit Actions: Configure restart behavior (e.g., restart automatically on failure).
Start the Service:
DOS
nssm start "Archive Search Wizard"
Or use the Windows Services console.

5. Operation and Maintenance

5.1. Indexing Management (index_archive.py)
Purpose: Ensures the search index remains current by adding new files, updating changed files (based on modification date and size), and removing deleted files.
Scheduled Execution: Runs automatically via the "ArchiveSearchWizard_IndexUpdate" scheduled task (typically daily at 6:00 AM).
Manual Execution:
Open Task Scheduler.
Navigate to Task Scheduler Library.
Right-click "ArchiveSearchWizard_IndexUpdate".
Select "Run".
Impact of Archive Unavailability: If the ARCHIVE_ROOT (network share) is inaccessible during an indexing run, the script will log errors for each inaccessible file. Crucially, if the script completes a full scan while files are inaccessible, those files will be marked as "no longer existing" and removed from the index. Once access is restored, a subsequent run will re-index them.
Changing Schedule/Account: Modify the scheduled task properties in Task Scheduler. Ensure any new account has necessary network and local permissions.

5.2. Web Application Management (app.py)
Purpose: Provides the web interface for users to search the archive. It runs on port 5000 by default.
Automatic Startup: Configured as a Windows service ("Archive Search Wizard") to start automatically on server boot.
Accessing the Web Interface: Open a web browser and navigate to: http://localhost:5000 (if accessing from the server itself) or http://[Server_IP_Address]:5000 (if accessing from another machine on the network).
Manual Service Management:
Open Windows Services console (Search for "Services").
Locate "Archive Search Wizard".
Right-click and select "Start", "Restart", or "Stop" as needed.

6. Log Files
Log files are critical for monitoring application health and diagnosing issues.
C:\ArchiveSearchWizard\indexer.log (from index_archive.py):
Records indexing start/end times.
Logs when files are added, updated, or removed from the index.
Outputs errors encountered during scanning (e.g., PermissionError, FileNotFoundError).
Provides progress updates (Progress: Scanned X files...).
Reports total scan and commit durations.
C:\ArchiveSearchWizard\logs\app_stdout.log (from app.py service - Standard Output):
Contains general application messages from the web application, such as when users submit search queries and the number of results found.
Logs application startup messages.
C:\ArchiveSearchWizard\logs\app_stderr.log (from app.py service - Standard Error):
Crucial for troubleshooting. Captures unhandled Python exceptions, runtime errors, or diagnostic messages (like network access failures) from the app.py service. If the application isn't functioning correctly, this is often the first place to check for error tracebacks.
Note on Log Growth: These logs will grow over time. Implement a log rotation strategy (e.g., using Windows built-in tools or third-party log managers) if disk space becomes a concern for long-term operation.

7. Troubleshooting Common Issues
Application Web Interface is Inaccessible:
Check Service Status: Open Windows Services, find "Archive Search Wizard". Ensure its "Status" is "Running". If not, attempt to "Start" or "Restart" it.
Check Service Logs: Review C:\ArchiveSearchWizard\logs\app_stderr.log for any Python tracebacks or error messages indicating why the service failed to start or crashed.
Firewall: Ensure the server's firewall allows incoming connections on port 5000.
Network Connectivity: Verify network connectivity from the client to the server.
Search Results are Empty or Outdated:
Check Indexer Log: Review C:\ArchiveSearchWizard\indexer.log. Look for recent successful indexing runs.
Archive Accessibility: Verify that the ARCHIVE_ROOT network share is accessible from the server hosting the indexer. If not, address network connectivity or permissions.
Indexer Errors: Check indexer.log for PermissionError, FileNotFoundError, or other OSError messages during indexing, indicating issues accessing files in the archive.
Manual Index Update: Perform a manual index update as described in Section 5.1.
Index Directory: Ensure WHOOSH_INDEX_DIR (e.g., C:\Temp\SearchIndexes) exists and contains Whoosh index files. If empty, the index may have been cleared or corrupted, requiring a full re-index.
ModuleNotFoundError during manual execution or service startup: 
This indicates that a required Python library (e.g., Flask, Whoosh, Waitress) is not installed in the Python environment being used.
Install Dependencies: Run pip install -r requirements.txt within the active virtual environment.
Service Path: Verify that the Windows service is configured to use the python.exe from your virtual environment (e.g., C:\ArchiveSearchWizard\venv\Scripts\python.exe).
