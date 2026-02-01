## Goal
Understand how WordPress actually runs. Stop treating WordPress like magic and start seeing it as a system.

---

### Tracing WordPress

1. Entry point — `index.php`. When a browser requests a URL, the web server (Apache/Nginx) rewrites the request via `.htaccess` so it lands in the site's root `index.php`. `index.php` loads `wp-blog-header.php`, which bootstraps WordPress. `wp-blog-header.php` then loads `wp-load.php`, calls the `wp()` function (responsible for starting the main request cycle), and `template-loader.php` is loaded.

2. `wp-load.php` is the bootstrap engine. It sets the `ABSPATH` constant and loads the `wp-config.php` file when present. If `wp-config.php` exists in the current directory (or in the root when `wp-settings.php` does not), WordPress loads `wp-config.php`. This secondary check avoids nested-installation issues (e.g., root is WordPress A and `/blog/` is WordPress B). If neither file is present, WordPress redirects to the installation screen. `wp-settings.php` is loaded from here.

3. `wp-settings.php` loads the code required to run WordPress:
   - Loads must-use plugins (`mu-plugins`).
   - Loads active plugins.
   - Loads the active theme's `functions.php`.
   - Calls `setup_theme()` to run theme setup.
   - Requires `default-filters.php` (registers default filters and actions such as `admin_init`, `user_register`, `the_title`, `the_content`, etc.).
   - Requires `cron.php` to set up WP-Cron.
   - Requires `class-wp.php`.
   - Sets up `WP_Object_Cache`.
   - Fires `do_action('init')`, a major hook for initialization.
   - Requires `query.php` for parsing the request and building the main query.
