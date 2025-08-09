# Nextcloud Performance Testing avec JMeter & Monitoring

> **Projet de démonstration** : Tests de performance sur une application Nextcloud dockerisée avec stack de monitoring temps réel.

## 🎯 Objectifs

- Évaluer les performances d'une instance Nextcloud sous charge
- Identifier les goulots d'étranglement système  
- Démontrer une approche méthodologique de test de performance
- Implémenter un monitoring en temps réel (JMeter + InfluxDB + Grafana)

## 🏗️ Architecture

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│   JMeter    │───▶│  Nextcloud   │    │   Grafana   │
│             │    │   + MySQL    │    │ Dashboards │
└─────────────┘    └──────────────┘    └─────────────┘
                            │                   ▲
                            ▼                   │
                   ┌──────────────┐            │
                   │  InfluxDB    │────────────┘
                   │  (Métriques) │
                   └──────────────┘
```

## 🚀 Démarrage rapide

### Prérequis
- Docker & Docker Compose
- JMeter 5.6.3+
- 8GB RAM recommandés

### Installation
```bash
git clone https://github.com/georgesmookherjee/jmeter-nextcloud
cd jmeter-nextcloud

# Démarrer Nextcloud
docker-compose -f nextcloud.yml up -d

# Démarrer la stack monitoring  
docker-compose -f jmeter-nextcloud-monitoring.yml up -d

# Accès aux interfaces
# Nextcloud: http://localhost:8081 (credentials à définir selon vos besoin)
# Grafana: http://localhost:3003 (credentials à définir selon vos besoin)
```

## 📊 Scénarios de test

### Parcours utilisateur testé
1. **Page d'accueil** (`/login`)
2. **Authentification** (POST `/login`)
3. **Tableau de bord** (`/apps/dashboard/`)
4. **Application Photos** (`/apps/photos/`)
5. **Déconnexion** (`/logout`)

### Patterns de charge
- **Test de charge** : 1-15 utilisateurs simultanés
- **Durée** : 9 minutes par test

## 📈 Résultats clés

### Capacité identifiée
- **Utilisateurs simultanés supportés** : ~15 VU
- **Point de saturation** : ~18-20 VU  
- **Temps de réponse acceptable** : < 4s (sous 15 VU)
- **Taux d'erreur** : 0% (dans la zone verte)

### Goulot d'étranglement principal
- **Ressource critique** : CPU du conteneur Nextcloud (Apache/PHP)
- **Observation** : Saturation CPU ~150-200% à partir de 15 VU
- **Impact** : Dégradation progressive des temps de réponse

### Métriques de performance

| Charge | Temps moyen | 95e percentile | Throughput | CPU App mean| CPU App max | Erreurs |
|--------|-------------|----------------|------------|-------------|-------------|---------|
| 10 VU  | 3.2s        | 5.5s           | 8 req/s    | ~50%        | 177%          | 0%      |
| 15 VU  | 4.1s        | 6.8s           | 10 req/s   | ~120%       | 267%          | 0%      |

## 🛠️ Outils et technologies

- **Tests de performance** : Apache JMeter
- **Application sous test** : Nextcloud (Docker)
- **Base de données** : MySQL 8.0
- **Monitoring** : InfluxDB + Grafana
- **Corrélation** : Extraction de tokens CSRF
- **Orchestration** : Docker Compose

## 📂 Structure du projet

```
├── jmeter-nextcloud/
├── jmeter-nextcloud-monitoring/
│   └── jmeter-nextcloud-monitoring.yml     # Stack monitoring  
├── jmeter-nextcloud-monitoring/
│   ├── nextcloud.yml                       # Stack application
├── screenshots des résultats/
│   └── screenshots/                        # Captures Grafana
└── README.md
│   rapport-performance.md                  # Analyse détaillée
│   jmeter-nextcloud-v4.jmx                 # Script de test principal
```

## 🎯 Enseignements techniques

### Bonnes pratiques mises en œuvre
- **Corrélation dynamique** des tokens CSRF
- **Monitoring en temps réel** des métriques système
- **Approche progressive** de montée en charge
- **Identification méthodique** des bottlenecks

### Améliorations possibles
- Optimisation des paramètres PHP-FPM
- Mise en cache Redis/Memcached
- Load balancing horizontal
- Optimisation base de données

## 🔗 Ressources

- [Documentation JMeter](https://jmeter.apache.org/usermanual/)
- [Nextcloud Performance Tuning](https://docs.nextcloud.com/server/latest/admin_manual/installation/server_tuning.html)
- [Dashboard Grafana pour JMeter](https://grafana.com/grafana/dashboards/5496)

---

**Auteur** : Georges Mookherjee  
**Contact** : georgesmookherjee@gmail.com  
**LinkedIn** : [Georges Mookherjee](https://linkedin.com/in/georgesmookherjee)