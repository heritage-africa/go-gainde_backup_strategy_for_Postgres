# Neon PostgreSQL Backup → S3 Compatible (GitHub Actions)

Ce  **workflow GitHub Actions** permet de sauvegarder une base **PostgreSQL hébergée sur Neon.tech** vers un **stockage S3-compatible** (Ceph, NooBaa, Cloudflare R2, etc.).

---

## Objectif

- Sauvegarde automatisée et sécurisée
- Compatible avec **S3**

---

## Méthode de sauvegarde

- Outil : **pg_dump PostgreSQL 17**
- Format : **Custom (-Fc)**  
- Compression : **gzip**
- Upload : **stream direct (pipe) vers S3**

```bash
/usr/lib/postgresql/17/bin/pg_dump -F c \
  -h $NEON_HOST \
  -U $NEON_USERNAME \
  -d $NEON_DBNAME \
| gzip \
| s3cmd put - s3://<bucket>/neon-db-$(date +%Y%m%d%H%M).dump.gz
```
## Restauration

```bash
gunzip -c neon-db-YYYYMMDDHHMM.dump.gz | pg_restore \
  --verbose \
  --clean --if-exists \
  --create \
  --dbname="postgresql://user:password@host:5432/postgres"
```
