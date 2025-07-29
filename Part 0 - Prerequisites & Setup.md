# Part 0: Prerequisites & Setup

Before we dive into connecting Django with an existing PostgreSQL database, it's essential to ensure your development environment is correctly set up. This section will guide you through installing the necessary tools and preparing your project workspace.

-----

### 0.1 System Requirements

Ensure your system meets the following basic requirements:

  * **Python:** Version 3.9 or higher is recommended. You can check your Python version by running:
    ```bash
    python3 --version
    ```
  * **pip:** The Python package installer should come bundled with Python 3.9+.
  * **PostgreSQL:** An installed and running PostgreSQL database server. We'll be interacting with an existing database, so having the server ready is crucial.
      * **PostgreSQL Client Libraries:** Depending on your operating system, you might need specific development headers for `psycopg2` (the PostgreSQL adapter for Python).
          * **Debian/Ubuntu:** `sudo apt-get install libpq-dev`
          * **Fedora/RHEL:** `sudo dnf install postgresql-devel`
          * **macOS (with Homebrew):** `brew install libpq` (usually handled by Homebrew when installing `postgresql`)
  * **pgAdmin4 (or similar GUI tool):** A graphical user interface (GUI) for managing your PostgreSQL databases is highly recommended. It makes it easier to inspect tables, run queries, and manage users.
  * **Code Editor:** A good code editor like [VS Code](https://code.visualstudio.com/), Sublime Text, or PyCharm.
  * **Git:** Essential for version control and cloning this repository. If you don't have it, install it from [git-scm.com](https://git-scm.com/downloads).
  * **GitHub Account:** (Optional, but recommended) For pushing your code and tracking changes.

-----

### 0.2 Initial Project Workspace Setup

Let's create a dedicated directory for your Django project and set up a virtual environment to manage dependencies cleanly.

1.  **Create Your Project Directory:**
    Choose a suitable location on your system for your project.

    ```bash
    mkdir django_pg_project
    cd django_pg_project
    ```

    *(You can name this directory anything you like. For consistency, we'll assume `django_pg_project`.)*

2.  **Set Up a Python Virtual Environment (`.venv`):**
    A virtual environment isolates your project's Python dependencies from your system-wide Python installation, preventing conflicts.

    ```bash
    python3 -m venv .venv
    ```

    This command creates a directory named `.venv` (a common convention) inside your `django_pg_project` folder.

3.  **Activate Your Virtual Environment:**
    You **must** activate the virtual environment every time you open a new terminal session to work on this project.

      * **On Linux/macOS:**
        ```bash
        source .venv/bin/activate
        ```
      * **On Windows (Command Prompt):**
        ```cmd
        .venv\Scripts\activate.bat
        ```
      * **On Windows (PowerShell):**
        ```powershell
        .venv\Scripts\Activate.ps1
        ```
      * **Verification:** Your terminal prompt should now show `(.venv)` or similar before your path, indicating the virtual environment is active.

4.  **Install Django and PostgreSQL Driver (`psycopg2-binary`):**
    With your virtual environment activated, install the core Django framework and the `psycopg2-binary` library, which allows Django to communicate with PostgreSQL.

    ```bash
    pip install django psycopg2-binary
    ```

    This will install the latest stable versions of both packages into your virtual environment.

-----

**Next up:** We'll move into **Part 1: Database Preparation**, where we ensure your PostgreSQL database is ready for Django's introspection.

-----
