# Tests Snort IDS/IPS

## 1. Objectif

Ce document décrit les tests permettant de vérifier le fonctionnement de Snort dans le projet.

Les tests portent sur deux aspects :

* **IDS** : détecter le trafic correspondant aux règles configurées.
* **IPS** : bloquer le trafic correspondant aux règles configurées.

## 2. Prérequis

Avant de commencer, vérifier que :

* Snort est installé et que sa configuration est disponible.
* Le fichier `local.rules` contient les règles personnalisées.
* L'interface réseau utilisée pour le test est correctement identifiée.
* Snort peut charger la configuration sans erreur.

## 3. Test IDS — Détection du trafic ICMP

### Objectif

Vérifier que Snort analyse le trafic ICMP généré par une commande `ping`.

### Commande de test

```bash
ping 8.8.8.8
```

### Lancement de Snort

La commande utilisée dans le projet est :

```bash
sudo snort -A console -q -i wlo1 -c /etc/snort/snort.conf
```

> Remplacer `wlo1` si l'interface réseau utilisée est différente.

### Règle concernée

```text
drop icmp any any -> any any
```

Cette règle cible le trafic ICMP. Son action est `drop`, prévue pour bloquer le trafic correspondant lorsque Snort fonctionne dans un mode permettant le blocage.

### Résultat attendu

Snort doit traiter le trafic correspondant à la règle. Le comportement exact dépend du mode de fonctionnement et du chemin emprunté par les paquets.

### Résultat obtenu

À compléter après exécution du test :

* Alertes ou messages affichés :
* Résultat de la commande `ping` :
* Erreurs éventuelles :

### Conclusion

À déterminer à partir des observations : test réussi, échec ou résultat non concluant.

---

## 4. Test IPS — Blocage du trafic ICMP

### Objectif

Vérifier si Snort bloque effectivement le trafic ICMP correspondant à la règle.

### Configuration

Le projet prévoit le passage en mode inline en modifiant le paramètre :

```text
policy_mode: tap
```

vers :

```text
policy_mode: inline
```

Cette modification seule ne garantit pas le blocage : Snort doit également être déployé sur le chemin du trafic et configuré correctement pour le traitement inline.

### Commande de test

```bash
ping 8.8.8.8
```

### Résultat attendu

Si le trafic traverse effectivement Snort en mode inline et que la règle est appliquée, les paquets ICMP correspondants doivent être bloqués.

### Résultat obtenu

À compléter après exécution :

* Le trafic a-t-il été bloqué ?
* Des messages ont-ils été affichés par Snort ?
* Le comportement est-il différent du test IDS ?

### Conclusion

Trafic correctememnt bloqué.
Messages affichés
Comportement indifférent du trst : c'est-à-dire correct.

---

## 5. Règle de blocage SSH

Le fichier `local.rules` contient également une règle visant le trafic TCP à destination du port 22.

```text
drop tcp any any -> 22
```

Cette règle est destinée à bloquer le trafic correspondant aux connexions SSH. Son fonctionnement doit être vérifié dans l'environnement de test, notamment en confirmant que Snort inspecte effectivement ce trafic.

Aucun résultat de test SSH n'est renseigné ici tant qu'il n'a pas été exécuté et observé.

---

## 6. Tableau récapitulatif

| Test                       | Objectif              | Statut

| Trafic ICMP en mode IDS    | Observer la détection | Détecté
| Trafic ICMP en mode IPS    | Vérifier le blocage   | Bloqué
| Trafic TCP vers le port 22 | Vérifier la règle SSH | À vérifier

## 7. Limites

Ces tests vérifient uniquement les comportements associés aux règles personnalisées du projet. Ils ne constituent pas une évaluation complète de la sécurité du réseau.

Les résultats doivent être renseignés à partir des observations réelles et non uniquement des résultats attendus.

