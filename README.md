# Sweetwood Harmony

Drupal 11 site for sweetwoodharmony.com, the gospel trio of Scot, Robby and Mandy.

## Local development

The site runs locally in DDEV as the `sweetwood` project.

```bash
ddev start
ddev composer install
ddev drush cim -y
```

The local URL is https://sweetwood.ddev.site.

## Layout

- `web/themes/custom/sweetwood` is the custom theme (one stylesheet, one page template).
- `config/sync` holds exported configuration. Export with `ddev drush cex -y`.

## Production

Production is the DigitalOcean droplet that also hosts randommountainramblers.com and scotself.com.

- Checkout: `/var/www/sweetwoodharmony` (owned by `sself`, group `www-data`)
- Webroot: `/var/www/sweetwoodharmony/web`
- Database: `sweetwood` on the droplet's MySQL 8.0
- DB credentials: `/var/www/sweetwoodharmony/private/db.php`, included by `settings.php` and never committed
- Apache vhosts: `sweetwoodharmony.conf` and `sweetwoodharmony-le-ssl.conf`

Deploy as `sself` in `/var/www/sweetwoodharmony`:

```bash
vendor/bin/drush sql:dump --gzip --result-file=/home/sself/db-backups/sweetwood-$(date +%Y%m%d%H%M).sql
vendor/bin/drush state:set system.maintenance_mode 1 --input-format=integer
git pull --ff-only origin main
composer install --no-dev
vendor/bin/drush updb -y
vendor/bin/drush config:status
vendor/bin/drush cim -y
vendor/bin/drush cr
vendor/bin/drush state:set system.maintenance_mode 0 --input-format=integer
```
