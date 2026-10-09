# Snort — IDS/IPS

## 1. Présentation

**Snort** est utilisé dans ce projet pour analyser le trafic réseau et mettre en place un mécanisme de détection et de blocage.

Le projet exploite deux modes :

* **IDS** : détection et génération d'alertes ;
* **IPS** : détection et blocage du trafic correspondant aux règles configurées.

L'objectif est de pouvoir observer le comportement de Snort avant d'activer le blocage.

## 2. Configuration

La configuration principale de Snort utilisée dans le projet se trouve dans :

```text
/etc/snort/snort.conf
```

Le réseau interne utilisé dans la configuration est défini avec :

```conf
HOME_NET [172.20.10.0/28]
```

Cette variable permet d'indiquer à Snort le réseau considéré comme réseau local dans le cadre du projet.

Les règles personnalisées sont placées dans :

```text
/etc/snort/local.rules
```

Dans le dépôt GitHub, le fichier correspondant est organisé comme suit :

```text
snort/
└── local.rules
```

## 3. Règles personnalisées

Le fichier `local.rules` contient actuellement deux règles :

```conf
drop icmp any any -> any any (msg:"[LUMPINI] Ping ICMP bloqué"; sid:2000001; rev:1;)

drop tcp any any -> 22 (msg:"[IPS] Tentative de connexion SSH bloquée"; sid:2000002; rev:1;)
```

### 3.1 Blocage du trafic ICMP

```conf
drop icmp any any -> any any (msg:"[LUMPINI] Ping ICMP bloqué"; sid:2000001; rev:1;)
```

Cette règle cible le trafic **ICMP**.

Lorsqu'une correspondance est détectée, l'action définie est :

```text
drop
```

La règle possède l'identifiant :

```text
sid:2000001
```

Son message est :

```text
[LUMPINI] Ping ICMP bloqué
```

### 3.2 Blocage des connexions SSH

```conf
drop tcp any any -> 22 (msg:"[IPS] Tentative de connexion SSH bloquée"; sid:2000002; rev:1;)
```

Cette règle cible le trafic TCP à destination du port **22**, utilisé ici pour représenter les connexions SSH.

L'action configurée est également :

```text
drop
```

Son identifiant est :

```text
sid:2000002
```

Le message associé est :

```text
[IPS] Tentative de connexion SSH bloquée
```

## 4. Mode IDS

En mode IDS, Snort analyse le trafic et génère des alertes lorsqu'une règle correspond au trafic observé.

La commande utilisée dans le projet est :

```bash
sudo snort -A console -q -i wlo1 -c /etc/snort/snort.conf
```

Les principales options utilisées sont :

* `-A console` : affichage des alertes dans la console ;
* `-q` : fonctionnement en mode silencieux pour limiter les informations affichées ;
* `-i wlo1` : interface réseau analysée ;
* `-c /etc/snort/snort.conf` : utilisation du fichier de configuration indiqué.

Le projet utilise notamment un test avec une requête `ping` afin d'observer la détection du trafic ICMP.

## 5. Test de détection

Le test réalisé consiste à générer du trafic ICMP :

```bash
ping 8.8.8.8
```

Snort analyse alors le trafic passant par l'interface configurée.

Dans le cadre du projet, cette activité permet de vérifier que la règle ICMP personnalisée est bien prise en compte.

## 6. Passage en mode IPS

Après le test en mode IDS, le projet configure Snort pour fonctionner en mode IPS.

Le paramètre suivant est modifié dans la configuration :

```text
policy_mode: tap
```

vers :

```text
policy_mode: inline
```

Le mode `inline` permet à Snort de se placer sur le chemin du trafic afin d'appliquer les actions définies par les règles.

Dans le projet, les règles utilisent notamment l'action :

```text
drop
```

pour bloquer le trafic correspondant.

## 7. `alert` et `drop`

Les deux actions importantes présentées dans le projet sont :

| Action  | Comportement                               |
| ------- | ------------------------------------------ |
| `alert` | Signale le trafic correspondant à la règle |
| `drop`  | Bloque le trafic correspondant à la règle  |

Ainsi, une règle utilisant `alert` permet principalement d'observer et de signaler une activité, tandis qu'une règle utilisant `drop` est utilisée dans le cadre du blocage.

## 8. Interfaces réseau

Le projet utilise également des interfaces virtuelles pour permettre le fonctionnement du trafic en mode inline.

Les interfaces mentionnées dans le rapport sont :

```text
veth0
veth1
```

Elles permettent de créer un chemin réseau virtuel autour du mécanisme de filtrage.

## 9. Organisation des fichiers

Dans le dépôt :

```text
snort/
├── snort.conf
└── local.rules
```

Lors du déploiement sur le système, les fichiers sont utilisés depuis les emplacements de configuration de Snort, notamment :

```text
/etc/snort/snort.conf
/etc/snort/local.rules
```

Le dépôt conserve donc les fichiers nécessaires à la configuration, tandis que leur emplacement système dépend de l'environnement de déploiement.

## 10. Limites

La configuration présentée correspond à un environnement de laboratoire.

Les règles personnalisées sont volontairement simples et ciblent notamment :

* le trafic ICMP ;
* les connexions TCP vers le port 22.

Cette configuration ne constitue donc pas un ensemble complet de signatures de sécurité réseau.

Les tests documentés servent principalement à démontrer le fonctionnement du mécanisme IDS/IPS dans le contexte du projet.

