---
title: Projet Web Infrastructure Design — Tâche 1
---

# Infrastructure web distribuée

Diagramme (Mermaid)
---------------------
```mermaid
graph TD
  User[Utilisateur]
  Internet[Internet]
  LB[Load Balancer (HAProxy)]

  subgraph S1["Serveur 1"]
    S1_web[Nginx]
    S1_app[Application]
    S1_db[MySQL]
    S1_web --> S1_app
    S1_app --> S1_db
  end

  subgraph S2["Serveur 2"]
    S2_web[Nginx]
    S2_app[Application]
    S2_db[MySQL]
    S2_web --> S2_app
    S2_app --> S2_db
  end

  User --> Internet
  Internet --> LB
  LB --> S1_web
  LB --> S2_web
```

Explications
------------
- Load balancer : répartit les requêtes entre les serveurs pour éviter la surcharge et améliorer la disponibilité.
- Deux serveurs applicatifs : fournissent redondance et meilleure tolérance aux pannes (si un serveur tombe, l'autre peut continuer à servir).

Comportement du load balancer
-----------------------------
- Algorithme courant : Round Robin — les requêtes sont distribuées alternativement entre les nœuds.

Modes d'exploitation
---------------------
- Active-Active : les deux serveurs répondent simultanément aux requêtes (configuration illustrée ici).
- Active-Passive : un serveur est passif et prend le relais uniquement en cas de défaillance du serveur actif.

Base de données
----------------
- Pattern Primary-Replica : une instance primaire traite les écritures, les réplicas servent les lectures.
- Avantage : réduction de la charge de lecture et meilleure scalabilité. Inconvénient : latence de réplication et complexité opérationnelle.

Points faibles identifiés
-------------------------
- SPOF : load balancer unique (solution : HAProxy en pair, Keepalived ou service cloud avec IP flottante).
- Bases de données isolées : risque de perte de données ou incohérence entre serveurs.

Sécurité
--------
- Absence de firewall/filtrage réseau. Recommandation : configurer des règles iptables/nftables ou un firewall en amont.
- Pas de TLS configuré : activer HTTPS (certificats Let’s Encrypt ou gestion centralisée).
- Pas de DMZ ni de segmentation réseau entre couche web et BD. Recommandation : isoler la base de données sur un réseau interne.
- Protection applicative : déployer un WAF pour filtrer les attaques courantes (OWASP Top 10).

Monitoring et observabilité
---------------------------
- Aucun outil de supervision mentionné. Recommandation : exporter métriques (Prometheus), logs centralisés (ELK/EFK) et alerting (Alertmanager).