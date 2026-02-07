
# 🏗️ Infrastructure Cloud de Supervision avec Zabbix sous AWS
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Zabbix](https://img.shields.io/badge/Zabbix-GPLv2-blue)](https://www.zabbix.com/licensing)
[![Docker](https://img.shields.io/badge/Docker-Apache%202.0-blue)](https://www.apache.org/licenses/LICENSE-2.0)

## 📋 Table des matières
- [📌 Aperçu du projet](#-aperçu-du-projet)
- [🎯 Objectifs](#-objectifs)
- [🏗️ Architecture](#️-architecture)
- [🛠️ Prérequis](#️-prérequis)
- [🚀 Déploiement étape par étape](#-déploiement-étape-par-étape)
- [📊 Monitoring & Validation](#-monitoring--validation)
- [👤 Auteur](#-auteur)

---

## 📌 Aperçu du projet

Déploiement d'une infrastructure de supervision centralisée dans AWS utilisant **Zabbix conteneurisé** pour surveiller un environnement hybride **Linux & Windows**.

**🔗 Lien GitHub :** [https://github.com/flamekk/Zabbix-AWS-Supervision.git](https://github.com/flamekk/Zabbix-AWS-Supervision.git)

**📅 Année universitaire :** 2025-2026  
**👨‍🏫 Encadrant :** Prof. Azeddine KHIAT

---

## 🎯 Objectifs

| Objectif | Description |
|----------|-------------|
| **Déploiement Cloud** | Mettre en œuvre une architecture AWS complète (VPC, EC2, Security Groups) |
| **Supervision Centralisée** | Déployer Zabbix en conteneurs Docker pour une gestion simplifiée |
| **Monitoring Hybride** | Surveiller des instances Linux (Ubuntu) et Windows (Server 2022) |
| **Alerting Proactif** | Implémenter des triggers pour détection d'anomalies en temps réel |
| **Validation** | Tester et valider la collecte de métriques et les fonctionnalités d'alerte |

---

## 🏗️ Architecture

### 🔧 Composants AWS
```
AWS Region: us-east-1 (N. Virginia)
├── VPC: 10.0.0.0/16
│   └── Subnet Public: 10.0.1.0/24 (us-east-1a)
│       └── Internet Gateway attaché
├── Security Groups:
│   ├── SG-Hiba-Zabbix (Serveur)
│   │   ├── Port 22 (SSH)
│   │   ├── Ports 80, 443 (HTTP/HTTPS)
│   │   └── Port 10051 (Zabbix Server)
│   └── Agents-SG (Clients)
│       ├── Port 10050 (Zabbix Agent)
│       ├── Port 22 (SSH - Linux)
│       └── Port 3389 (RDP - Windows)
└── Instances EC2:
    ├── Serveur Zabbix: t3.large - Ubuntu 22.04 LTS
    ├── Client Linux: t3.medium - Ubuntu 22.04 LTS
    └── Client Windows: t3.large - Windows Server 2022
```

### 🐳 Stack Docker Zabbix
```
Services Conteneurisés:
├── Zabbix Server (image: zabbix/zabbix-server-mysql)
├── Zabbix Web Interface (image: zabbix/zabbix-web-nginx-mysql)
├── Base de données MySQL (image: mysql:8.0)
└── Orchestration: Docker Compose
```

---

## 🛠️ Prérequis

### 📋 Avant de commencer
1. **Compte AWS Academy** avec accès au Learner Lab
2. **Connaissances de base** :
   - AWS EC2, VPC, Security Groups
   - Docker et Docker Compose
   - Administration Linux/Windows
   - Zabbix (concepts de base)

### ⚙️ Outils nécessaires
- Client SSH (ex: PuTTY, OpenSSH)
- Client RDP (pour Windows)
- Navigateur Web moderne
- Accès à la console AWS

---

## 🚀 Déploiement étape par étape

### 1. 📍 Configuration Réseau AWS
```bash
# Création du VPC et sous-réseau via console AWS
Région: us-east-1
VPC CIDR: 10.0.0.0/16
Subnet Public: 10.0.1.0/24 (us-east-1a)
Internet Gateway: Attaché au VPC
Table de routage: Route 0.0.0.0/0 vers IGW
```

### 2. 🛡️ Configuration Sécurité
**Security Group du Serveur Zabbix (SG-Hiba-Zabbix):**
```
Port 22   (SSH)        - 0.0.0.0/0
Port 80   (HTTP)       - 0.0.0.0/0
Port 443  (HTTPS)      - 0.0.0.0/0
Port 10051 (Zabbix)    - Agents-SG
```

**Security Group des Agents (Agents-SG):**
```
Port 10050 (Agent)     - SG-Hiba-Zabbix
Port 22    (SSH)       - Votre IP
Port 3389  (RDP)       - Votre IP
```

### 3. 🖥️ Lancement des Instances EC2
| Rôle | Type | OS | Taille | IP Privée |
|------|------|----|--------|-----------|
| Serveur Zabbix | t3.large | Ubuntu 22.04 | 8GB RAM | 10.0.1.10 |
| Client Linux | t3.medium | Ubuntu 22.04 | 4GB RAM | 10.0.1.20 |
| Client Windows | t3.large | Win Server 2022 | 8GB RAM | 10.0.1.30 |

### 4. 🐳 Installation Docker sur le Serveur
```bash
# Connexion SSH au serveur Zabbix
ssh -i "votre-key.pem" ubuntu@<IP_PUBLIQUE_SERVEUR>

# Installation Docker
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
```

### 5. 📦 Déploiement Zabbix avec Docker Compose
```bash
# Création du répertoire de travail
mkdir zabbix-docker && cd zabbix-docker

# Téléchargement du fichier docker-compose.yml
# (disponible dans le dossier /docker de ce dépôt)

# Lancement des services
docker-compose up -d

# Vérification
docker-compose ps
```

### 6. 🔗 Configuration des Agents

#### 🐧 Sur Client Linux
```bash
# Installation
sudo apt update
sudo apt install zabbix-agent -y

# Configuration
sudo nano /etc/zabbix/zabbix_agentd.conf
# Modifier:
Server=10.0.1.10  # IP du serveur Zabbix
ServerActive=10.0.1.10
Hostname=Linux-Client-01

# Redémarrage
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

#### 🪟 Sur Client Windows
```powershell
# Téléchargement de l'agent
# URL: https://www.zabbix.com/download_agents

# Installation silencieuse
msiexec /i zabbix_agent-6.4.9-windows-amd64-openssl.msi /qn

# Configuration via GUI ou fichier:
# C:\Program Files\Zabbix Agent\zabbix_agentd.conf
Server=10.0.1.10
ServerActive=10.0.1.10
Hostname=Windows-Client-01

# Redémarrage du service
Restart-Service ZabbixAgent
```

### 7. 🌐 Configuration Web Zabbix
1. Accéder à: `http://<IP_PUBLIQUE_SERVEUR>`
2. Identifiants par défaut:
   - Utilisateur: `Admin`
   - Mot de passe: `zabbix`
3. Ajouter les hôtes:
   - **Configuration → Hôtes → Créer un hôte**
   - Associer les templates appropriés

---

## 📊 Monitoring & Validation

### ✅ Test de connectivité
```bash
# Depuis le serveur, tester la connexion aux agents
telnet 10.0.1.20 10050  # Client Linux
telnet 10.0.1.30 10050  # Client Windows
```

### 🚨 Test d'alerte proactive
1. **Scénario**: Arrêt manuel du service Zabbix Agent sur Windows
2. **Trigger utilisé**: `Zabbix agent is not available`
3. **Résultat attendu**:
   - Changement d'état: Vert → Rouge
   - Alerte dans `Monitoring → Problems`
   - Notification visuelle immédiate

### 📈 Métriques surveillées
| Système | Métriques | Fréquence |
|---------|-----------|-----------|
| **Linux** | CPU, Mémoire, Disque, Réseau | 30 secondes |
| **Windows** | CPU, Mémoire, Services, Événements | 30 secondes |


---

## 👤 Auteur 

**🎓 Étudiante :** Hiba Zbari  
**📚 Filière :** Ingénierie Informatique Big Data & Intelligence Artificielle  
**🏫 Établissement :** HESTIM
**👨‍🏫 Encadrant :** Prof. Azeddine KHIAT  
**📅 Année :** 2025-2026


### 📄 Licence
Ce projet est réalisé dans un cadre académique. Consulter le fichier `LICENSE` pour plus d'informations.

---

