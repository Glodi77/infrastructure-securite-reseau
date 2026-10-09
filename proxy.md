## Test 1 — Blocage de facebook.com

### Objectif
Vérifier que Squid bloque l'accès à facebook.com.

### Commande exécutée

```bash
curl -I -x http://localhost:3128 http://facebook.com
