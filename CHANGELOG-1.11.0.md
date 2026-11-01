# InstantCMS 1.11.0 — community revival

This is an **unofficial, community-maintained** release by new maintainers, based on
**InstantCMS 1.10.7** (2016-07-26, GNU/GPL v2), source:
[instantsoft/icms1](https://github.com/instantsoft/icms1) tag `1.10.7` (commit `93daaee`).

All original copyrights, authorship notices and license headers of the InstantCMS Team
(www.instantcms.ru) are kept **unchanged**. The license is still GNU/GPL v2 (see `license.txt`).
The goal of 1.11.0 is to make the 1.10.7 code base run on current PHP and MySQL. It adds no new features.

Неофициальная версия, которую поддерживает сообщество (новые разработчики), на основе InstantCMS 1.10.7 (GNU/GPL v2).
Копирайты и лицензия оригинальных разработчиков сохранены без изменений.

## Requirements

| | 1.10.7 | 1.11.0 |
|---|---|---|
| PHP | 5.3 – 5.6 | **7.4, 8.x** (tested on 7.4.33 and 8.3) |
| MySQL | 5.x (non-strict) | 5.x, **8.0** (tested on 8.0.40 with the default strict `sql_mode` and `caching_sha2_password`) |

## MySQL 8 compatibility

* `core/classes/db.class.php`
  * Each connection sets the session `sql_mode = 'NO_ENGINE_SUBSTITUTION'`. The schema and code depend on non-strict mode: zero dates, columns without defaults, and long MyISAM keys. Without this, the installer fails on MySQL 5.7/8 with `Field 'form_id' doesn't have a default value`.
  * Adds `mysqli_report(MYSQLI_REPORT_OFF)`, because PHP 8.1+ mysqli throws exceptions by default.
  * Connection errors are checked properly.
  * Result helpers are guarded against non-result values.
* `core/cms.php`, `core/cms_admin.php`: quote the column `` `system` ``, which is a reserved word in MySQL 8.
* `admin/applets/repairnested.php`: quote the alias `` `rows` ``, also a reserved word in MySQL 8.

## Removed PHP functions and syntax (PHP 7 / 8)

* `create_function()` was replaced with closures in:
  * `admin/applets/userbanlist.php`, `admin/applets/usergroups.php`;
  * `includes/bbcode/bbcode.lib.php`, `includes/geshi/geshi.php`;
  * `includes/smarty/libs/plugins/function.city_input.php`.
* `each()` was replaced with `foreach` / `key()` + `next()` in:
  * `includes/bbcode/bbcode.lib.php`;
  * `includes/smarty/libs/sysplugins/smarty_internal_compilebase.php`;
  * `includes/phpmailer/class.smtp.php`.
* Calls to `get_magic_quotes_gpc()` and `get_magic_quotes_runtime()` are guarded in `core/cms.php` and `includes/phpmailer/class.phpmailer.php`.
* String offsets with `$str{n}` were changed to `$str[n]` in:
  * `components/users/files.php`;
  * `core/classes/idna_convert.class.php`, `core/classes/jevix.class.php`;
  * `plugins/p_kcaptcha/codegen/kcaptcha.php`;
  * `includes/excel/excel_reader2.php`.
* PHP 4 style constructors became `__construct()` in `plugins/p_ping/IXR_Library.php`, `includes/geshi/geshi.php` and `includes/excel/excel_reader2.php`. The latter also had `=& new` and `split()` removed.

## PHP 7.1+ / 8.x fatal errors fixed

* `admin/includes/cp.php`
  * `cpAddPathway()` gets a default `$link`, fixing an ArgumentCountError on the admin module edit page.
  * `cpComponentHasConfig()` had a string-offset bug.
* `core/cms.php`
  * Arithmetic on the string `timediff` is fixed (TypeError).
  * `detectURI()` works under CLI, so `cron.php` runs (`strpos` ValueError).
  * `setcookie()` argument types are fixed.
* `components/rssfeed/frontend.php`: `timediff` arithmetic fixed (TypeError).
* `core/classes/plugin.class.php`: `array_merge()` with a null config (TypeError).
* `core/classes/form.class.php`: `getFieldsHtml()` now returns an array, not `''`. This fixes a TypeError on `/registration` when no profile form exists.
* `components/registration/frontend.php`: uninitialised `$item`.
* `includes/smarty/libs/Smarty.class.php`: redeclared `$line` property in `SmartyCompilerException`, a fatal error on PHP 8.1+.

## PHP 8.x deprecations removed

* `#[\AllowDynamicProperties]` was added to classes that use dynamic properties. This is for PHP 8.2+. On PHP 7.4 the attribute is just a comment.
* `plugins/p_kcaptcha/codegen/kcaptcha.php`, `includes/graphic.inc.php`: explicit int casts for GD coordinates/sizes (float→int deprecation on 8.1+; image size is never 0, which would be a ValueError on PHP 8).
* `#[\ReturnTypeWillChange]` was added to the `ArrayAccess` methods of the Smarty parsers.
* The Smarty lexers passed `null` as the `preg_match()` flags. The template compiler used `strftime()`, now replaced with `date()`.
* `htmlspecialchars(null)` is avoided in:
  * `admin/applets/cats.php`, `admin/applets/modules.php`;
  * `plugins/p_ckeditor/plugin.php`;
  * `includes/smarty/libs/plugins/modifiercompiler.escape.php`.
* An optional parameter came before a required one in:
  * `core/classes/blog.class.php`, `core/classes/photo.class.php`;
  * `components/board/model.php`;
  * `core/classes/db.class.php`;
  * `includes/phpmailer/class.pop3.php`;
  * `includes/excel/excel_reader2.php`.

## Other

* `core/cms.php`: `CORE_VERSION` is now `1.11.0`. `CORE_VERSION_DATE` is still `2016-07-26`.
* `install/function.php`: the installer now requires PHP 7.4 or newer.
* `readme.txt`, `version_log.txt`: version and requirements updated.
* `migrate/`: unchanged. It is the 1.10.6 → 1.10.7 DB migration. 1.11.0 has no schema changes, so a 1.10.7 database works as is.

## Known limitations / TODO

* The SQL dumps still use MyISAM with zero-date defaults, and they rely on the non-strict session `sql_mode` set by `db.class.php`. Do not import them by hand with strict mode on.
* PHP 8.4 and MariaDB have not been tested yet.
* Bundled third-party libraries (Smarty 3.1.x, PHPMailer 5.x, GeSHi, Jevix) were only patched for compatibility, not upgraded.
