# Architecture

## 1. Vue d'ensemble

Ce projet consiste à mettre en place un mini système de cybersécurité réseau permettant de filtrer, détecter et bloquer certains trafics réseau.

L'architecture repose principalement sur deux composants :

* **Squid** : utilisé comme proxy filtrant ;
* **Snort** : utilisé comme IDS/IPS pour détecter et bloquer certaines activités réseau.

L'utilisation de Docker permet d'isoler les différents composants et de faciliter leur déploiement.

## 2. Architecture réseau

Le flux général du projet peut être représenté ainsi :

```text
Client
   │
   ▼
┌─────────────────┐
│      Squid      │
│ Proxy filtrant  │
│    TCP : 3128   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│      Snort      │
│     IDS / IPS   │
└────────┬────────┘
         │
         ▼
      Internet
```

Le proxy intervient en premier pour contrôler les requêtes HTTP/HTTPS. Le trafic est ensuite analysé par Snort afin de détecter certaines activités considérées comme suspectes.

## 3. Rôle des composants

### Squid

Squid est utilisé comme proxy filtrant.

Dans la configuration du projet, Squid écoute sur le port **3128**. Une règle permet notamment de bloquer l'accès au domaine `facebook.com`.

### Snort

Snort est utilisé pour analyser le trafic réseau et fonctionner selon deux modes :

* **IDS** : détection et génération d'alertes ;
* **IPS** : détection et blocage du trafic correspondant aux règles configurées.

Le projet utilise notamment des règles personnalisées dans `local.rules`.

## 4. Isolation avec Docker

Docker est utilisé pour isoler les composants du système.

L'objectif est notamment de faciliter :

* l'isolation des services ;
* la portabilité de l'environnement ;
* la reproductibilité du déploiement ;
* la maintenance des composants.

## 5. Flux de filtrage

Le fonctionnement général est le suivant :

1. Le client génère une requête réseau.
2. La requête passe par le proxy Squid.
3. Squid applique ses règles de filtrage.
4. Le trafic autorisé poursuit son chemin.
5. Snort analyse le trafic réseau.
6. Selon le mode utilisé et les règles configurées, Snort peut générer une alerte ou bloquer le trafic.

## 6. Composants du dépôt

Les fichiers principaux associés à cette architecture sont organisés ainsi :

```text
network-security-stack/
├── README.md
├── docs/
│   ├── architecture.md
│   ├── proxy-squid.md
│   └── snort-ids-ips.md
├── squid/
│   ├── Dockerfile
│   └── squid.conf
├── snort/
│   ├── snort.conf
│   └── local.rules
└── tests/
    ├── proxy.md
    └── ids-ips.md
```

Cette organisation sépare la documentation, les configurations des services et les tests.

## 7. Limites

Cette documentation décrit l'architecture réalisée dans le cadre du projet. Elle ne constitue pas une architecture de production et les configurations présentées sont destinées à un environnement de laboratoire.

