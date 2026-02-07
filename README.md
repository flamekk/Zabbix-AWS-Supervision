# Zabbix-AWS-Supervision



# 📊 Infrastructure de Supervision Hybride avec Zabbix sur AWS

Ce projet présente la mise en œuvre d'une solution de supervision centralisée permettant de monitorer en temps réel des serveurs aux systèmes d'exploitation hétérogènes (Linux et Windows) hébergés sur le Cloud AWS.

## 🏗️ Architecture du Projet

L'infrastructure est déployée dans un **VPC dédié (10.0.0.0/16)** au sein de la région **us-east-1**.

| Composant | Instance EC2 | Système d'Exploitation | IP Privée |
| --- | --- | --- | --- |
| **Serveur Zabbix** | `t3.large` | Ubuntu 22.04 LTS | `10.0.0.130` |
| **Client Linux** | `t3.medium` | Ubuntu 22.04 LTS | `10.0.0.200` |
| **Client Windows** | `t3.large` | Windows Server 2022 | `10.0.0.253` |

## 🚀 Fonctionnalités Clés

* **Conteneurisation** : Serveur Zabbix déployé via **Docker-Compose** pour une portabilité et une isolation optimales.
* **Collecte de Métriques** : Monitoring CPU, mémoire, espace disque et état des services via les agents Zabbix.
* **Sécurité Réseau** : Configuration de Security Groups AWS et du pare-feu Windows (port **10050**) pour sécuriser les flux de données.
* **Alerting Proactif** : Détection automatique des pannes avec triggers configurés pour signaler l'indisponibilité des hôtes.



## 📂 Contenu du Dépôt

* `docker-compose.yml` : Configuration complète du serveur, de la base de données MySQL et de l'interface Web.
* `zabbix_agentd.conf` : Fichiers de configuration des agents optimisés pour la communication avec le serveur.
* `Rapport_TP.pdf` : Documentation détaillée incluant les captures d'écran des tableaux de bord et des tests de charge.

## 🛠️ Installation Rapide (Serveur)

1. **Cloner le dépôt** : `git clone <url-du-repo>`
2. **Lancer l'infrastructure** : `sudo docker-compose up -d` 
3. **Accès Web** : `http://44.203.122.143:8080` (Identifiants par défaut : Admin/zabbix).

---

