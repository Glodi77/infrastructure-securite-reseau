# Network Security Stack

Mini-laboratoire de cybersécurité réseau basé sur **Squid** et **Snort**, permettant de mettre en place une chaîne de défense composée d'un proxy filtrant et d'un système IDS/IPS.

## Description

Ce projet a pour objectif de mettre en place une infrastructure réseau de test capable de :

* filtrer le trafic réseau à l'aide d'un proxy Squid ;
* détecter des activités réseau à l'aide de Snort ;
* bloquer certaines activités considérées comme suspectes ;
* expérimenter les mécanismes IDS et IPS ;
* isoler les différents composants à l'aide de Docker.

## Architecture

```text
Client
  │
  ▼
┌──────────────┐
│     Squid    │
│ Proxy :3128  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    Snort     │
│   IDS / IPS  │
└──────┬───────┘
       │
       ▼
    Internet
```

## Technologies

| Technologie | Rôle                                               |
| ----------- | -------------------------------------------------- |
| Linux       | Système d'exploitation utilisé pour le laboratoire |
| Docker      | Conteneurisation des services                      |
| Squid       | Proxy et filtrage du trafic                        |
| Snort       | Détection et prévention des intrusions             |
| curl        | Test du fonctionnement du proxy                    |

## Prérequis

* Linux
* Docker
* Snort
* Squid
* `curl`

## Installation

La procédure d'installation et de configuration est détaillée dans la documentation du projet.

## Configuration

Les fichiers de configuration sont organisés dans les répertoires suivants :

```text
squid/
└── squid.conf

snort/
├── snort.conf
└── local.rules
```

## Tests

Les différents composants du laboratoire sont validés à l'aide de tests permettant notamment de vérifier :

* le filtrage effectué par Squid ;
* la détection de trafic par Snort ;
* le fonctionnement du mode IDS ;
* le fonctionnement du mode IPS ;
* le blocage des paquets correspondant aux règles configurées.

## Documentation

La documentation détaillée du projet est disponible dans le répertoire [`docs/`](docs/).

## Structure du projet

```text
network-security-stack/
│
├── README.md
│
├── docs/
│   ├── architecture.md
│   ├── proxy-squid.md
│   └── snort-ids-ips.md
│
├── squid/
│   ├── Dockerfile
│   └── squid.conf
│
├── snort/
│   ├── snort.conf
│   └── local.rules
│
└── tests/
    ├── proxy.md
    └── ids-ips.md
```

## Limitations

Ce projet constitue un laboratoire de test réalisé dans un environnement Linux.

Les configurations et résultats présentés sont liés à l'environnement utilisé pour le déploiement.

## Auteur

**Glodi KATUZOLAKO**

Licence Informatique — Cybersécurité & Réseaux

