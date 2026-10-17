# ELK Stack avec Docker Compose

Ce projet permet de déployer la suite **ELK (Elasticsearch, Logstash, Kibana)** en local avec Docker Compose, utilisant les dernières versions (8.12.0).

## 📋 Prérequis

- [Docker](https://www.docker.com/) (version 20.10 ou supérieure)
- [Docker Compose](https://docs.docker.com/compose/) (version 1.29 ou supérieure)
- 4 Go de RAM minimum recommandé

## 🚀 Installation et démarrage

### 1. Cloner le dépôt
```bash
git clone https://github.com/malialili/ELK.git
cd ELK
```

### 2. Démarrer la stack ELK
```bash
docker-compose up -d
```

### 3. Vérifier que les conteneurs sont en cours d'exécution
```bash
docker-compose ps
```

### 4. Accéder aux interfaces

- **Kibana** : [http://localhost:5601](http://localhost:5601)
- **Elasticsearch** : [http://localhost:9200](http://localhost:9200)

## 📊 Configuration

### Structure des fichiers
```
ELK/
├── docker-compose.yml          # Configuration Docker Compose
├── config/
│   ├── elasticsearch/
│   │   └── elasticsearch.yml   # Configuration Elasticsearch
│   └── kibana/
│       └── kibana.yml          # Configuration Kibana
├── logstash.conf               # Pipeline Logstash
├── logstash.yml               # Configuration Logstash
└── README.md
```

### Configuration personnalisée

#### Elasticsearch
Le fichier `config/elasticsearch/elasticsearch.yml` contient la configuration pour un nœud unique. Vous pouvez modifier :
- `cluster.name` : Nom du cluster
- `node.name` : Nom du nœud
- `network.host` : Adresse de liaison
- `http.port` : Port HTTP

#### Logstash
Le fichier `logstash.conf` définit le pipeline de traitement des logs. Par défaut, il écoute sur :
- Port **5000** pour les logs syslog
- Port **5044** pour les beats (Filebeat, etc.)

#### Kibana
Le fichier `config/kibana/kibana.yml` configure l'interface Kibana. Par défaut, il se connecte à Elasticsearch sur `http://elasticsearch:9200`.

## 🔄 Redémarrer les services

```bash
# Arrêter la stack
docker-compose down

# Redémarrer
docker-compose up -d
```

## 🧹 Nettoyage

### Supprimer les conteneurs et volumes
```bash
# Arrêter et supprimer les conteneurs
docker-compose down

# Supprimer les volumes (attention : cela supprime les données !)
docker volume prune
```

### Réinitialiser Elasticsearch
```bash
# Supprimer les données d'Elasticsearch
docker volume rm elk_es_data

# Redémarrer
docker-compose up -d
```

## 📝 Configuration avancée

### Augmenter la mémoire
Pour Elasticsearch, vous pouvez augmenter la mémoire JVM en modifiant la variable d'environnement dans `docker-compose.yml` :
```yaml
environment:
  - "ES_JAVA_OPTS=-Xms2g -Xmx2g"
```

### Activer la sécurité
Pour activer la sécurité (recommandé en production), modifiez les variables d'environnement :
```yaml
environment:
  - xpack.security.enabled=true
  - xpack.security.http.ssl.enabled=true
```

## 🔍 Vérification

### Vérifier Elasticsearch
```bash
curl -X GET "http://localhost:9200/"
```

### Vérifier Logstash
```bash
# Envoyer un log de test
echo '<34>Oct 11 22:14:15 mymachine su: ID=test' | nc localhost 5000
```

### Vérifier Kibana
Ouvrez [http://localhost:5601](http://localhost:5601) dans votre navigateur et configurez un index pattern pour `logstash-*`.

## 🛠️ Dépannage

### Problème de mémoire
Si vous obtenez des erreurs de mémoire, augmentez la mémoire allouée à Docker ou réduisez les paramètres JVM dans `ES_JAVA_OPTS`.

### Problème de port
Vérifiez que les ports 9200, 9300, 5000, 5044 et 5601 sont disponibles.

### Logs des conteneurs
```bash
# Voir les logs d'un conteneur spécifique
docker-compose logs elasticsearch
docker-compose logs logstash
docker-compose logs kibana

# Voir tous les logs en temps réel
docker-compose logs -f
```

## 📚 Documentation officielle

- [Elasticsearch Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [Logstash Documentation](https://www.elastic.co/guide/en/logstash/current/index.html)
- [Kibana Documentation](https://www.elastic.co/guide/en/kibana/current/index.html)
- [Docker Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)

## 🎯 Cas d'usage

### Centralisation des logs
1. Configurez Filebeat sur vos serveurs pour envoyer les logs à Logstash (port 5044)
2. Utilisez Kibana pour visualiser et analyser les logs

### Analyse de logs Apache/Nginx
1. Configurez Logstash pour parser les logs web (voir `filter.conf`)
2. Créez des visualisations dans Kibana

### Monitoring système
1. Utilisez Metricbeat pour collecter les métriques système
2. Envoyez-les à Elasticsearch via Logstash ou directement

---

**Note** : Cette configuration utilise **Elasticsearch 8.12.0**, **Logstash 8.12.0** et **Kibana 8.12.0** avec la sécurité désactivée pour simplifier le développement local. Pour un environnement de production, activez la sécurité et configurez les certificats SSL.
