# InstantCMS 1.11.0

**InstantCMS 1.11.0** is an unofficial version of the free CMS
[InstantCMS](https://www.instantcms.ru/) **1.10.7** (2016), maintained by the community. It has been
adapted to run on modern **PHP 7.4 / 8.x** and **MySQL 8**.

The original 1.10.7 cannot be installed on PHP 7+ or MySQL 5.7/8. The installer fails on MySQL's strict mode, and the code uses functions that were removed from PHP. Version 1.11.0 is a *revival*: compatibility fixes only. Functionality, templates and the database structure are unchanged.

## Authors and copyright

* The original system is **InstantCMS**, © InstantSoft / InstantCMS Team, [www.instantcms.ru](https://www.instantcms.ru/),
  source: [instantsoft/icms1](https://github.com/instantsoft/icms1).
* **All original copyrights, authorship notices and license headers are kept unchanged.**
* Version 1.11.0 was prepared by new community maintainers ([StudiaHive](https://github.com/StudiaHive)).
  It is not an official InstantSoft release.

## Requirements

| Component | Version |
|---|---|
| PHP | **7.4 – 8.3** (tested on 7.4.33 and 8.3). PHP 8.4 has not been tested yet |
| PHP extensions | `mysqli`, `mbstring`, `iconv`, `json`, `simplexml`, `gd` |
| MySQL | **8.0** (tested on 8.0.40 with the default strict `sql_mode` and `caching_sha2_password`), 5.7, 5.x |
| Web server | Apache + `mod_rewrite` (`.htaccess`) |
| Storage engine | MyISAM must be available, as in the original |

## Installation

1. Download `instantCMS_20160726_v1.11.0.zip` from the [Releases](https://github.com/StudiaHive/instantCMS/releases) section, or clone the repository.
2. Unpack the contents of the `instantCMS_20160726_v1.11.0/` folder into the site root, including the `.htaccess` files.
3. Create a database (`utf8` / `utf8mb4`) and a user for it.
4. Give write permissions (for example 777 for directories, 644 for `.htaccess`) to:
   `/cache`, `/images`, `/upload`, and `/includes`. The `/includes` permission is needed only during installation and only on the directory itself.
5. Open `http://your-site/install/` and follow the installer.
6. After installation, **delete the `install` and `migrate` folders**.

## Upgrading from 1.10.7

* The database structure is the same as 1.10.7, so **no migration is needed**.
* Make a backup of your files and database.
* Replace the system files with the files from 1.11.0. Keep `includes/config.inc.php`, `/upload`, `/images` and any templates or customisations you changed.
* Clear the `/cache` folder.
* Sites on versions older than 1.10.7 should first upgrade to 1.10.7 with the original `migrate/`.

## What changed

* **MySQL 8:**
  * each connection sets the session `sql_mode = 'NO_ENGINE_SUBSTITUTION'`;
  * the reserved words `system` and `rows` are escaped;
  * mysqli exceptions (PHP 8.1+) are turned off;
  * connection errors are checked properly.
* **PHP 7/8:** these removed functions and constructs were replaced:
  * `create_function()`, `each()`, `get_magic_quotes_*()`, `split()`;
  * `$str{n}` string offsets;
  * PHP 4 constructors and `=& new`.
* **PHP 8 fatal errors fixed:** TypeError, ValueError and ArgumentCountError (time zone, plugins, Smarty, the admin panel, `cron.php`, registration).
* **PHP 8.1–8.3 deprecations removed:**
  * `#[\AllowDynamicProperties]` and `#[\ReturnTypeWillChange]` attributes;
  * `null` passed to string functions;
  * float→int conversion in GD;
  * an optional parameter before a required one.
* The version is now `1.11.0`, and the installer requires PHP 7.4+.

The full list of changed files and reasons is in [CHANGELOG-1.11.0.md](CHANGELOG-1.11.0.md).
The original change history is in `version_log.txt`.

## Known limitations

* The SQL dumps keep the original schema (MyISAM, zero dates). They are meant to be loaded by the installer, which sets the non-strict mode. Importing a dump by hand with strict `sql_mode` will fail.
* PHP 8.4 and MariaDB have not been tested yet.
* The bundled libraries (Smarty 3.1, PHPMailer 5, GeSHi, Jevix, CKEditor) were only patched for compatibility, not upgraded to new versions.
* This is old code from 2016. Before using it on a public site, keep the server up to date and follow security news.

## License

GNU General Public License v2. See [LICENSE](LICENSE) (identical to `license.txt`) and `license.rus.txt`.
The software is provided "AS IS", without warranty of any kind.

---

## English

**InstantCMS 1.11.0** is an unofficial, community-maintained revival of
[InstantCMS](https://www.instantcms.ru/) **1.10.7** (2016) by InstantSoft. It runs on **PHP 7.4–8.3** and **MySQL 8 / 5.7**.
It contains compatibility fixes only. Features, templates and the database schema are the same as in 1.10.7.
All original copyrights and license headers are kept unchanged. The license is GPL v2.

* Requirements: PHP 7.4–8.3 with mysqli, mbstring, iconv, json, simplexml and gd; MySQL 8.0 / 5.7; Apache with mod_rewrite.
* Install: unpack the release zip into the web root, create a database, open `/install/`, then delete `install/` and `migrate/`.
* Upgrade from 1.10.7: replace the files and keep `includes/config.inc.php`, `upload/` and `images/`. No database migration is needed.
* Details: [CHANGELOG-1.11.0.md](CHANGELOG-1.11.0.md).
