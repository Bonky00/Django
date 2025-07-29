# Part 1: Database Preparation

Before Django can connect to and understand your PostgreSQL database, we need to ensure the database itself is ready. This involves verifying its existence, creating a dedicated user (for better security and management), and most importantly, ensuring it contains the actual tables you want Django to interact with.

-----

### 1.1 Verify PostgreSQL is Running

Ensure your PostgreSQL server is active and accessible. If you can log into `pgAdmin4` or use `psql` from your terminal, it's likely running.

  * **Linux (Systemd-based):**

    ```bash
    sudo systemctl status postgresql
    ```

    You should see "active (running)" in the output.

  * **macOS (Homebrew):**

    ```bash
    brew services list
    ```

    Look for `postgresql` and confirm its status is `started`.

-----

### 1.2 Create a Dedicated PostgreSQL Database and User

It's a best practice to create a specific database and a dedicated user with minimal necessary privileges for your Django project. This enhances security and organization.

You can do this via `pgAdmin4` (recommended for its GUI) or using the `psql` command-line tool.

#### Using `pgAdmin4` (Recommended):

1.  **Open `pgAdmin4`** and connect to your PostgreSQL server.

2.  **Create a New Login/Group Role (User):**

      * In the left-hand browser, right-click on `Login/Group Roles` under your server.
      * Select `Create` \> `Login/Group Role...`.
      * **General Tab:**
          * **Name:** `django_user` (or any name you prefer)
      * **Definition Tab:**
          * **Password:** Choose a strong password (e.g., `your_strong_password`). Remember this\!
      * **Privileges Tab:**
          * Toggle `Can login?` to `YES`.
          * Toggle `Create database?` to `YES` (if you want this user to create databases, or leave as `NO` if you'll create the database with another user).
          * Toggle `Bypass RLS?` to `YES` (if needed, typically for superusers or special cases, often `NO` is fine for application users).
      * Click `Save`.

3.  **Create a New Database:**

      * Right-click on `Databases` under your server.
      * Select `Create` \> `Database...`.
      * **General Tab:**
          * **Database:** `mydjangodb` (or any name you prefer). Remember this name\!
          * **Owner:** Select the `django_user` you just created from the dropdown.
      * Click `Save`.

#### Using `psql` Command-Line:

1.  **Open your terminal.**

2.  **Log in to `psql` as the default `postgres` user:**

    ```bash
    sudo -u postgres psql
    ```

    (On Windows, you might just type `psql -U postgres` and enter your postgres password).

3.  **Create the user and database:**

    ```sql
    CREATE USER django_user WITH PASSWORD 'your_strong_password';
    CREATE DATABASE mydjangodb OWNER django_user;
    \q
    ```

    *Replace `django_user`, `your_strong_password`, and `mydjangodb` with your chosen values.*

-----

### 1.3 Ensure Your Database Has Existing Tables (Crucial for `inspectdb`)

This is arguably the most critical step for `inspectdb`. Django's `inspectdb` command is designed to look at your *existing, non-Django* database tables and generate models for them. If your database only contains tables that Django itself creates (like `auth_user`, `django_migrations`, etc.), `inspectdb` will produce an empty `models.py` file.

**Verification Steps:**

1.  **Open `pgAdmin4`.**

2.  **Connect to your PostgreSQL server.**

3.  In the left-hand browser, expand `Servers` \> `Your PostgreSQL Version` \> `Databases`.

4.  **Click on the database you just created** (e.g., `mydjangodb` or `PostgresDB` if you're reusing your previous one).

5.  Expand your database \> `Schemas` \> `public`.

6.  **Expand `Tables`.**

      * **If you see custom tables you expect** (e.g., `Courses`, `Programs`, `CourseOutcomes`, `inventory_item`, etc., like in the screenshot you provided), then you are good to go\!
      * **If you *only* see tables starting with `auth_` and `django_`**, or if the `Tables` list is empty, then your database does not yet contain any tables for `inspectdb` to find.

#### **Troubleshooting: If `inspectdb` gives blank `models.py` (No Custom Tables Found)**

If your database is empty of custom tables, you need to add some. Here's how to create a simple test table using `pgAdmin4`'s Query Tool:

1.  In `pgAdmin4`, right-click on your database name (e.g., `mydjangodb`) and select **`Query Tool`**.

2.  Paste the following SQL code into the editor:

    ```sql
    CREATE TABLE IF NOT EXISTS inventory_item (
        id SERIAL PRIMARY KEY,
        name VARCHAR(255) NOT NULL,
        quantity INTEGER NOT NULL DEFAULT 0,
        last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    );

    INSERT INTO inventory_item (name, quantity) VALUES
    ('Laptop', 50),
    ('Mouse', 200),
    ('Keyboard', 100);
    ```

3.  Click the "Execute/Refresh" button (the lightning bolt icon) or press `F5`.

4.  You should see "Query returned successfully" messages.

5.  In the `pgAdmin4` browser, right-click on the `Tables` list under your database and choose `Refresh`. You should now see the `inventory_item` table.

**Important:** If you have an existing database schema (e.g., a `.sql` dump file), now would be the time to import it into your newly created database.

-----

**Next up:** We'll proceed to **Part 2: Django Project & App Configuration**, where we start setting up your Django project to connect to this database.

-----
