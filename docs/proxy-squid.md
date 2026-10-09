# Proxy Squid

## 1. Présentation

**Squid** est utilisé dans ce projet comme proxy filtrant.

Son rôle est d'intercepter les requêtes passant par le proxy et d'appliquer les règles de filtrage définies dans sa configuration.

Dans cette configuration, Squid écoute sur le port **3128** et bloque l'accès au domaine `facebook.com`.

## 2. Configuration

Le fichier de configuration utilisé est :

```text
squid/squid.conf
```

Configuration actuelle :

```conf
http_port 3128
visible_hostname squidproxy

acl blocked_sites dstdomain .facebook.com
http_access deny blocked_sites
http_access allow all

access_log /var/log/squid/access.log squid
```

## 3. Explication de la configuration

### Port d'écoute

```conf
http_port 3128
```

Cette directive configure Squid pour écouter les connexions entrantes sur le port **3128**.

### Nom de l'hôte

```conf
visible_hostname squidproxy
```

Cette directive définit le nom d'hôte visible utilisé par Squid.

### Création de l'ACL

```conf
acl blocked_sites dstdomain .facebook.com
```

Cette ligne définit une ACL nommée `blocked_sites`.

Elle cible le domaine :

```text
.facebook.com
```

L'ACL est ensuite utilisée par les règles de contrôle d'accès.

### Blocage du domaine

```conf
http_access deny blocked_sites
```

Cette règle refuse les requêtes correspondant à l'ACL `blocked_sites`.

Dans le cadre du projet, elle permet donc de bloquer l'accès à `facebook.com`.

### Autorisation des autres requêtes

```conf
http_access allow all
```

Cette règle autorise les requêtes qui ne sont pas bloquées par la règle précédente.

L'ordre des règles `http_access` est donc important dans cette configuration.

### Journalisation

```conf
access_log /var/log/squid/access.log squid
```

Cette directive configure le fichier utilisé pour enregistrer les accès traités par Squid.

Le journal est écrit dans :

```text
/var/log/squid/access.log
```

## 4. Test du proxy

Le fonctionnement du proxy peut être vérifié avec `curl`.

La commande utilisée dans le projet est :

```bash
curl -I -x http://localhost:3128 http://facebook.com
```

Les options utilisées permettent notamment de :

* `-I` : effectuer une requête permettant d'obtenir les en-têtes HTTP ;
* `-x` : utiliser Squid comme proxy ;
* `http://localhost:3128` : définir l'adresse et le port du proxy.

## 5. Résultat attendu

Lorsque la requête vers `facebook.com` passe par le proxy, Squid applique l'ACL `blocked_sites`.

Le rapport de projet indique que le test retourne :

```text
403 Forbidden
```

Ce résultat confirme, dans le cadre du test réalisé, que la requête vers le domaine configuré est refusée par Squid.

## 6. Flux du test

```text
Client
   │
   │ Requête HTTP
   ▼
┌───────────────────┐
│ Squid             │
│ localhost:3128    │
└─────────┬─────────┘
          │
          │ Vérification ACL
          ▼
   .facebook.com ?
       │
   ┌───┴───┐
   │       │
  Oui     Non
   │       │
   ▼       ▼
  DENY    ALLOW
   │       │
   ▼       ▼
  403    Requête
Forbidden autorisée
```

Le test documenté dans le projet utilise cette logique pour vérifier le fonctionnement du filtrage.

## 7. Fichier associé

```text
squid/
├── Dockerfile
└── squid.conf
```

Le fichier `squid.conf` contient les règles de configuration du proxy.

## 8. Limites de cette configuration

La configuration actuelle constitue une configuration de laboratoire.

Le filtrage présenté se limite notamment au domaine configuré dans l'ACL :

```text
.facebook.com
```

Cette documentation décrit le comportement effectivement configuré dans le projet et ne prétend pas couvrir l'ensemble des possibilités de filtrage offertes par Squid.

