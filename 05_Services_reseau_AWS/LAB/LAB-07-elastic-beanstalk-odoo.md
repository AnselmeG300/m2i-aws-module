# LAB 07 — Déploiement d'une Application Java avec AWS Elastic Beanstalk

## 🎯 Objectifs

À la fin de ce lab, vous serez capable de :
- ✅ Comprendre le fonctionnement d'**AWS Elastic Beanstalk**
- ✅ Déployer une application **Java** avec Elastic Beanstalk
- ✅ Utiliser une **application exemple** fournie par AWS
- ✅ Comprendre la différence entre **PaaS** et **IaaS**
- ✅ Surveiller une application via la console Elastic Beanstalk
- ✅ Mettre à jour une application déployée

---

## 📋 Prérequis

- ✅ Accès à la console AWS
- ✅ Région : **Virginia (us-east-1)**
- ✅ Navigateur web
- ❌ **Aucune connaissance Java requise**
- ❌ **Aucun fichier à télécharger** (nous utiliserons une application exemple AWS)

---

## 📚 Qu'est-ce qu'AWS Elastic Beanstalk ?

### Définition

**AWS Elastic Beanstalk** est un service **PaaS** (Platform as a Service) qui permet de **déployer et gérer des applications web sans gérer l'infrastructure**.

### Analogie Simple

Imaginez que vous voulez ouvrir un restaurant :

| Approche | Équivalent AWS | Responsabilités |
|----------|----------------|-----------------|
| **Construire le bâtiment** | EC2 (IaaS) | Vous gérez tout : serveurs, réseau, OS, sécurité |
| **Louer un local équipé** | Elastic Beanstalk (PaaS) | AWS gère l'infrastructure, vous gérez l'application |
| **Service de restauration** | Lambda (FaaS) | AWS gère tout, vous fournissez juste le code |

### Fonctionnement d'Elastic Beanstalk

```
┌─────────────────────────────────────────────┐
│         VOUS FOURNISSEZ :                   │
│         • Code de l'application             │
│         • Langage (Java, Python, etc.)      │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│      ELASTIC BEANSTALK GÈRE :               │
│      • Instances EC2                        │
│      • Load Balancer (ALB)                  │
│      • Auto Scaling                         │
│      • Surveillance (CloudWatch)            │
│      • Déploiement automatique              │
│      • Mise à jour de l'OS                  │
│      • Sécurité (Security Groups)           │
└─────────────────────────────────────────────┘
                    ↓
            APPLICATION DÉPLOYÉE !
```

### Avantages d'Elastic Beanstalk

| Avantage | Explication |
|----------|-------------|
| 🚀 **Rapidité** | Déploiement en quelques clics (5-10 minutes) |
| 🎯 **Simplicité** | Pas besoin de gérer EC2, ALB, ASG manuellement |
| 💰 **Coût** | **Service gratuit** (vous payez uniquement les ressources EC2, etc.) |
| 📈 **Auto Scaling** | Ajuste automatiquement le nombre d'instances selon la charge |
| 📊 **Monitoring** | CloudWatch intégré pour surveiller l'application |
| 🔄 **Mises à jour** | Déploiement de nouvelles versions en un clic |
| 🔧 **Personnalisable** | Accès aux ressources sous-jacentes (EC2, etc.) si besoin |

### Plateformes Supportées

Elastic Beanstalk supporte de nombreux langages :

| Langage | Exemples de frameworks |
|---------|------------------------|
| ☕ **Java** | Spring Boot, Tomcat, Java SE |
| 🐍 **Python** | Django, Flask |
| 🟢 **Node.js** | Express |
| 🐘 **PHP** | Laravel |
| 💎 **Ruby** | Rails |
| 🪟 **.NET** | ASP.NET |
| 🦦 **Go** | Go applications |
| 🐳 **Docker** | Conteneurs personnalisés |

**Pour ce lab, nous utiliserons Java avec Tomcat.**

---

## 🏗️ Architecture Créée par Elastic Beanstalk

### Mode Single Instance (Ce que nous allons créer)

```
┌────────────────────────────────────────────────────────┐
│              AWS Elastic Beanstalk                     │
│                                                        │
│  ┌─────────────────────────────────────────────────┐  │
│  │         Instance EC2 (t3.micro)                 │  │
│  │                                                 │  │
│  │  ┌───────────────────────────────────────────┐ │  │
│  │  │  Amazon Linux 2023                        │ │  │
│  │  │  • Java 17 (Corretto)                     │ │  │
│  │  │  • Apache Tomcat 10                       │ │  │
│  │  │  • Votre application Java (.war)          │ │  │
│  │  └───────────────────────────────────────────┘ │  │
│  │                                                 │  │
│  │  Security Group : Port 80 ouvert               │  │
│  └─────────────────────────────────────────────────┘  │
│                                                        │
│  CloudWatch : Logs + Métriques                         │
└────────────────────────────────────────────────────────┘
                    │
                    ▼
              Internet (Port 80)
         http://[votre-app].elasticbeanstalk.com
```

**Ce qui est créé automatiquement :**
- ✅ 1 instance EC2 (t3.micro)
- ✅ 1 Security Group (autorisant le trafic HTTP sur le port 80)
- ✅ 1 Elastic IP (IP publique)
- ✅ CloudWatch Logs et Métriques
- ✅ Service IAM Roles

### Mode Load Balanced (Production)

```
┌────────────────────────────────────────────────────────┐
│              AWS Elastic Beanstalk                     │
│                                                        │
│         Application Load Balancer (ALB)               │
│                      │                                 │
│         ┌────────────┴────────────┐                    │
│         ▼                         ▼                    │
│  ┌─────────────┐          ┌─────────────┐             │
│  │   EC2 #1    │          │   EC2 #2    │             │
│  │   [Java]    │          │   [Java]    │             │
│  └─────────────┘          └─────────────┘             │
│                                                        │
│  Auto Scaling Group (Min: 2, Max: 4)                   │
└────────────────────────────────────────────────────────┘
```

**Pour ce lab, nous utiliserons le mode Single Instance (plus simple et gratuit).**

---

## 🚀 PARTIE 1 : Créer une Application Elastic Beanstalk

### Étape 1.1 : Accéder au Service Elastic Beanstalk

1. **Connectez-vous à la console AWS**

2. **Région** : Vérifiez que vous êtes sur **N. Virginia (us-east-1)**
   - En haut à droite de la console

3. **Recherchez "Elastic Beanstalk"** dans la barre de recherche

4. **Cliquez sur "Elastic Beanstalk"**

💡 **Remarque** : Si c'est votre première fois, vous verrez une page d'introduction.

---

### Étape 1.2 : Créer l'Application

1. **Cliquez sur "Create environment"** (bouton orange)

2. **Configure environment** :

   **Application name** :
   - Nom : `M2i-[VOTRE_PRENOM]-JavaApp`
   - Exemple : `M2i-Hannah-JavaApp`
   
   **Environment name** :
   - Nom : `M2i-[VOTRE_PRENOM]-JavaApp-env`
   - Exemple : `M2i-Hannah-JavaApp-env`
   - ℹ️ Ce nom sera utilisé dans l'URL : `m2i-hannah-javaapp-env.us-east-1.elasticbeanstalk.com`

   **Domain** :
   - Laissez le domaine proposé (généré automatiquement)
   - ⚠️ Si "Not available", ajoutez un chiffre : `M2i-Hannah-JavaApp-env-2`

3. **Platform** :
   - **Platform** : ✅ Sélectionnez **Java**
   - **Platform branch** : ✅ Sélectionnez **Corretto 17 running on 64bit Amazon Linux 2023**
   - **Platform version** : Laissez la version recommandée (dernière version)

4. **Application code** :
   - ✅ Sélectionnez **"Sample application"**
   - ℹ️ AWS fournit une application Java exemple qui affiche une page web simple

5. **Presets** :
   - ✅ Sélectionnez **"Haute disponibilité"**

6. **Cliquez sur "Next"**

---

### Étape 1.3 : Configurer le Service Access

1. **Service role** :
   - ✅ Sélectionnez **"Create and use new service role"**
   - Nom suggéré : `aws-elasticbeanstalk-service-role`

2. **EC2 key pair** :
   - ✅ Sélectionnez **"Proceed without an EC2 key pair"**
   - ℹ️ Pas besoin de clé SSH pour ce lab (nous n'allons pas nous connecter à l'instance)

3. **EC2 instance profile** :
   - ✅ Sélectionnez **"Create new instance profile"**
   - AWS créera automatiquement un profil IAM pour l'instance EC2

4. **Cliquez sur "Skip to review"**

---

### Étape 1.4 : Vérifier et Déployer

1. **Vérifiez la configuration** :
   - ✅ Application name : `M2i-[VOTRE_PRENOM]-JavaApp`
   - ✅ Environment name : `M2i-[VOTRE_PRENOM]-JavaApp-env`
   - ✅ Platform : Java 17 (Corretto)
   - ✅ Sample application
   - ✅ Single instance

2. **Cliquez sur "Submit"** (bouton orange en bas)

3. **Patientez pendant le déploiement** ⏱️ 5-10 minutes

**Pendant le déploiement, vous verrez les étapes suivantes :**

```
✅ Creating application...
✅ Creating environment...
⏳ Launching environment...
   ├─ Creating EC2 instance
   ├─ Configuring security groups
   ├─ Installing Java 17
   ├─ Installing Tomcat
   ├─ Deploying sample application
   └─ Running health checks
✅ Successfully launched environment
```

💡 **Astuce** : Vous pouvez suivre la progression dans la section "Recent events" en bas de la page.

---

## ✅ PARTIE 2 : Vérifier et Tester l'Application

### Étape 2.1 : Vérifier l'État de l'Environnement

1. **Une fois le déploiement terminé** :
   - L'état devrait afficher : **"Health: Ok"** avec un indicateur vert
   - Durée : ~5-10 minutes

2. **Informations affichées** :

   ```
   ┌─────────────────────────────────────────────────────┐
   │  M2i-Hannah-JavaApp-env                    Health: Ok│
   │  ────────────────────────────────────────────────── │
   │  URL: m2i-hannah-javaapp-env.us-east-1.elasticbean…│
   │  Platform: Java 17 running on Amazon Linux 2023     │
   │  Last updated: 2 minutes ago                        │
   └─────────────────────────────────────────────────────┘
   ```

---

### Étape 2.2 : Accéder à l'Application

1. **Cliquez sur l'URL de l'environnement** (en haut de la page) :
   - Format : `http://m2i-hannah-javaapp-env.us-east-1.elasticbeanstalk.com`

2. **Vous devriez voir une page avec** :
   - ✅ **"Congratulations"** en grand titre
   - ✅ Le logo AWS Elastic Beanstalk
   - ✅ Des informations sur votre environnement :
     - Environment ID
     - Platform
     - Version

3. **🎉 Félicitations !** Votre première application Java est déployée sur AWS !

---

### Étape 2.3 : Explorer la Console Elastic Beanstalk

#### Onglet "Environment overview"

```
┌─────────────────────────────────────────────────────┐
│  📊 Health: Ok                                      │
│  🔗 URL: http://m2i-hannah-javaapp-env.us-east-1... │
│  📦 Running version: Sample Application             │
│  🖥️  Platform: Java 17 / Amazon Linux 2023          │
└─────────────────────────────────────────────────────┘
```

#### Menu de gauche :

1. **Configuration** :
   - Voir et modifier la configuration (type d'instance, scaling, etc.)

2. **Logs** :
   - Télécharger les logs de l'application et du serveur

3. **Health** :
   - Voir la santé de l'environnement et des instances

4. **Monitoring** :
   - Graphiques CloudWatch (CPU, Réseau, Requêtes, etc.)

5. **Alarms** :
   - Configurer des alarmes CloudWatch

6. **Managed updates** :
   - Activer les mises à jour automatiques de la plateforme

---

## 📊 PARTIE 3 : Monitoring et Logs

### Étape 3.1 : Consulter les Métriques CloudWatch

1. **Menu gauche → "Monitoring"**

2. **Vous verrez plusieurs graphiques** :

   **Environment health** :
   - Santé globale de l'environnement (Ok, Warning, Degraded, Severe)

   **Instances** :
   - Nombre d'instances en cours d'exécution

   **Requests** :
   - Nombre de requêtes HTTP reçues

   **Latency** :
   - Temps de réponse de l'application (en millisecondes)

   **CPU utilization** :
   - Utilisation du CPU de l'instance EC2

   **Network** :
   - Trafic réseau entrant et sortant

3. **Générez du trafic** :
   - Ouvrez l'URL de votre application plusieurs fois
   - Rafraîchissez la page 10-20 fois
   - Attendez 2-3 minutes
   - Revenez sur "Monitoring"
   - ✅ Vous devriez voir des graphiques avec des données

---

### Étape 3.2 : Consulter les Logs

1. **Menu gauche → "Logs"**

2. **Cliquez sur "Request logs" → "Last 100 Lines"**

3. **Attendez quelques secondes** (~10-30 secondes)

4. **Cliquez sur "Download"** une fois disponible

5. **Ouvrez le fichier ZIP téléchargé**

6. **Vous trouverez plusieurs fichiers de logs** :

   ```
   logs/
   ├── eb-engine.log          → Logs du déploiement Elastic Beanstalk
   ├── web.stdout.log         → Logs de l'application Java
   ├── nginx/access.log       → Logs des requêtes HTTP
   └── nginx/error.log        → Logs d'erreurs du serveur web
   ```

7. **Ouvrez `web.stdout.log`** :
   - Vous verrez les logs de votre application Java
   - Recherchez "Started" pour voir le démarrage de Tomcat

8. **Ouvrez `nginx/access.log`** :
   - Vous verrez les requêtes HTTP reçues
   - Format : IP, date, méthode, URL, statut HTTP

**Exemple de log** :
```
54.196.12.34 - - [09/Jan/2026:18:30:45 +0000] "GET / HTTP/1.1" 200 1234 "-" "Mozilla/5.0..."
```

---

### Étape 3.3 : Vérifier la Configuration

1. **Menu gauche → "Configuration"**

2. **Explorez les sections** :

   **Software** :
   - Platform : Java 17 (Corretto)
   - Container : Tomcat 10
   - Proxy server : Nginx

   **Instances** :
   - Instance type : t3.micro (1 vCPU, 1 GB RAM)
   - AMI : Amazon Linux 2023
   - Root volume : 10 GB (gp3)

   **Capacity** :
   - Environment type : Single instance
   - Auto Scaling : Désactivé (mode single instance)

   **Load balancer** :
   - Non applicable (single instance n'utilise pas de Load Balancer)

   **Security** :
   - Service role : aws-elasticbeanstalk-service-role
   - EC2 instance profile : aws-elasticbeanstalk-ec2-role

   **Monitoring** :
   - Health reporting : Enhanced
   - System : Basic

   **Notifications** :
   - Email : (optionnel)

---

## 🔄 PARTIE 4 : Déployer une Nouvelle Version (Optionnel)

**Cette section est optionnelle mais très instructive.**

### Étape 4.1 : Créer une Application Java Simple

**Option 1 : Télécharger l'application exemple AWS**

1. **Téléchargez l'application** :
   - [Sample Java Application](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/samples/java-tomcat-v3.zip)

2. **Décompressez le ZIP**

3. **Modifiez le fichier `index.jsp`** :
   ```jsp
   <h1>Bonjour de la part de [VOTRE_PRENOM] !</h1>
   <p>Déployé avec AWS Elastic Beanstalk</p>
   ```

4. **Recompressez en ZIP** : `my-java-app-v2.zip`

**Option 2 : Utiliser une application toute prête**

- Nous allons simplement redéployer l'application exemple pour voir le processus

---

### Étape 4.2 : Déployer la Nouvelle Version

1. **Elastic Beanstalk → Votre environnement**

2. **Cliquez sur "Upload and deploy"** (bouton en haut à droite)

3. **Cliquez sur "Choose file"**
   - Sélectionnez `my-java-app-v2.zip` (ou utilisez l'application exemple AWS)

4. **Version label** : `v2-personnalise`

5. **Cliquez sur "Deploy"**

6. **Attendez le déploiement** (~2-3 minutes)

**Vous verrez** :
```
✅ Uploading application version...
✅ Deploying new version...
⏳ Restarting application...
✅ Successfully deployed v2-personnalise
```

7. **Rafraîchissez l'URL de votre application**
   - ✅ Vous devriez voir les changements (si vous avez modifié le code)

---

## 🔍 PARTIE 5 : Comprendre les Ressources Créées

### Étape 5.1 : Vérifier les Ressources EC2

1. **EC2 → Instances**

2. **Vous verrez une instance** :
   - Nom : `M2i-Hannah-JavaApp-env` (ou similaire)
   - Type : t3.micro
   - État : Running
   - Public IP : Adresse IP publique
   - Security Group : `awseb-...`

3. **Cliquez sur l'instance → Onglet "Security"**

4. **Cliquez sur le Security Group**

5. **Onglet "Inbound rules"** :
   - ✅ Port 80 (HTTP) : `0.0.0.0/0` (accessible depuis Internet)
   - ℹ️ Elastic Beanstalk a créé ce Security Group automatiquement

---

### Étape 5.2 : Vérifier les Rôles IAM

1. **IAM → Roles**

2. **Recherchez "elasticbeanstalk"**

3. **Vous verrez 2 rôles** :

   **aws-elasticbeanstalk-service-role** :
   - Utilisé par Elastic Beanstalk pour gérer les ressources
   - Permissions : Créer EC2, CloudWatch, etc.

   **aws-elasticbeanstalk-ec2-role** :
   - Utilisé par l'instance EC2
   - Permissions : Écrire dans CloudWatch Logs, S3, etc.

---

### Étape 5.3 : Vérifier CloudWatch

1. **CloudWatch → Log groups**

2. **Recherchez votre environnement** :
   - `/aws/elasticbeanstalk/M2i-Hannah-JavaApp-env/var/log/...`

3. **Cliquez sur un log group → Log streams**

4. **Vous verrez les logs en temps réel** ✅

---

## 📖 PARTIE 6 : Concepts Clés

### Qu'est-ce que Elastic Beanstalk a créé pour nous ?

| Ressource | Description | Géré par |
|-----------|-------------|----------|
| **EC2 Instance** | Serveur virtuel qui exécute l'application | Elastic Beanstalk |
| **Security Group** | Firewall autorisant le port 80 | Elastic Beanstalk |
| **IAM Roles** | Permissions pour l'instance et Beanstalk | Elastic Beanstalk |
| **CloudWatch Logs** | Stockage des logs | Elastic Beanstalk |
| **CloudWatch Metrics** | Métriques (CPU, RAM, etc.) | Elastic Beanstalk |
| **Elastic IP** | IP publique (mode single instance) | Elastic Beanstalk |

**Total : ~6-7 ressources créées automatiquement en 5 minutes !**

---

### Comparaison : Elastic Beanstalk vs EC2 Manuel

| Tâche | EC2 Manuel | Elastic Beanstalk |
|-------|------------|-------------------|
| Créer une instance EC2 | ✋ Manuel | ✅ Automatique |
| Installer Java | ✋ SSH + apt/yum install | ✅ Automatique |
| Installer Tomcat | ✋ Télécharger + configurer | ✅ Automatique |
| Configurer Security Group | ✋ Créer manuellement | ✅ Automatique |
| Déployer l'application | ✋ SCP + copier .war | ✅ Upload ZIP |
| Configurer CloudWatch | ✋ Installer agent | ✅ Automatique |
| Mises à jour Java/Tomcat | ✋ Manuelles | ✅ Automatiques |
| Scaling | ✋ Créer ASG manuellement | ✅ Cliquer sur "Edit capacity" |
| **Temps total** | ⏱️ 1-2 heures | ⏱️ 5-10 minutes |

---

### Cas d'Usage d'Elastic Beanstalk

| Scénario | Beanstalk adapté ? | Raison |
|----------|-------------------|--------|
| **Application web Java** | ✅ Oui | Parfait pour des apps Spring, Tomcat |
| **API REST** | ✅ Oui | Déploiement simple d'APIs |
| **Site WordPress** | ⚠️ Possible | Préférer Lightsail ou EC2 |
| **Application avec base de données** | ✅ Oui | Peut se connecter à RDS |
| **Microservices** | ⚠️ Complexe | Préférer ECS ou EKS |
| **Fonction serverless** | ❌ Non | Utiliser Lambda |
| **Infrastructure custom** | ❌ Non | Utiliser EC2 |

---

## 🧹 PARTIE 7 : Nettoyage — ⚠️ TRÈS IMPORTANT

### ⚠️ IMPORTANT : Supprimez toujours vos ressources après le lab !

Elastic Beanstalk crée des ressources EC2 qui **consomment votre Free Tier**.

---

### Étape 7.1 : Supprimer l'Environnement

1. **Elastic Beanstalk → Environments**

2. **Sélectionnez votre environnement** : `M2i-[VOTRE_PRENOM]-JavaApp-env`

3. **Actions → Terminate environment**

4. **Confirmez** en tapant le nom de l'environnement

5. **Cliquez sur "Terminate"**

6. **Attendez 5-10 minutes** que l'environnement soit supprimé

**Ce qui sera supprimé automatiquement** :
- ✅ Instance EC2
- ✅ Security Group
- ✅ Elastic IP
- ✅ CloudWatch Logs (optionnel)
- ✅ Application versions (optionnel)

---

### Étape 7.2 : Supprimer l'Application

1. **Elastic Beanstalk → Applications**

2. **Sélectionnez votre application** : `M2i-[VOTRE_PRENOM]-JavaApp`

3. **Actions → Delete application**

4. **Cochez "Delete versions" et "Delete logs"**

5. **Confirmez**

---

### Étape 7.3 : Vérifier la Suppression

1. **EC2 → Instances**
   - ✅ Vérifiez qu'aucune instance Elastic Beanstalk ne tourne

2. **EC2 → Security Groups**
   - ✅ Les Security Groups `awseb-...` devraient être supprimés automatiquement

3. **CloudWatch → Log groups**
   - ✅ Les logs Elastic Beanstalk peuvent rester (pas de coût)
   - Si vous voulez les supprimer : Sélectionnez → Actions → Delete

---

## ✅ Validation du Lab

### Questions de Compréhension

1. **Qu'est-ce qu'AWS Elastic Beanstalk ?**
   - ✅ Réponse : Un service PaaS qui déploie et gère automatiquement l'infrastructure pour vos applications

2. **Quelle est la différence entre PaaS et IaaS ?**
   - ✅ PaaS (Beanstalk) : AWS gère l'infrastructure, vous gérez l'application
   - ✅ IaaS (EC2) : Vous gérez tout (OS, runtime, application)

3. **Quelles ressources AWS sont créées automatiquement par Beanstalk ?**
   - ✅ Réponse : EC2, Security Groups, IAM Roles, CloudWatch Logs/Metrics

4. **Payez-vous pour Elastic Beanstalk ?**
   - ✅ Réponse : Non, le service Beanstalk est **gratuit**. Vous payez uniquement les ressources (EC2, etc.)

5. **Quelle est la différence entre "Single instance" et "Load balanced" ?**
   - ✅ Single instance : 1 instance EC2, pas de Load Balancer (gratuit Free Tier)
   - ✅ Load balanced : Plusieurs instances + ALB + Auto Scaling (payant)

6. **Combien de temps faut-il pour déployer une application ?**
   - ✅ Réponse : ~5-10 minutes pour le premier déploiement, ~2-3 minutes pour une mise à jour

7. **Peut-on accéder à l'instance EC2 créée par Beanstalk ?**
   - ✅ Réponse : Oui, via SSH (si vous avez configuré une clé SSH) ou via AWS Systems Manager

8. **Que se passe-t-il si on supprime l'environnement ?**
   - ✅ Réponse : Toutes les ressources sont supprimées (EC2, Security Groups, etc.)

---

## 🎓 Concepts Clés à Retenir

| Concept | Explication |
|---------|-------------|
| **PaaS** | Platform as a Service — AWS gère l'infrastructure |
| **IaaS** | Infrastructure as a Service — Vous gérez tout (EC2) |
| **Environment** | Ensemble de ressources AWS qui exécutent une version de l'application |
| **Application** | Conteneur logique qui regroupe plusieurs environnements |
| **Platform** | Langage + runtime (ex: Java 17 + Tomcat) |
| **Sample application** | Application exemple fournie par AWS pour tester |
| **Deployment** | Processus de mise à jour de l'application |

---

## 📊 Comparaison des Services AWS

| Service | Type | Cas d'usage | Complexité |
|---------|------|-------------|------------|
| **EC2** | IaaS | Infrastructure personnalisée | ⚠️ Élevée |
| **Elastic Beanstalk** | PaaS | Applications web (Java, Python, etc.) | ✅ Faible |
| **Lambda** | FaaS | Fonctions événementielles | ✅ Très faible |
| **ECS** | CaaS | Conteneurs Docker | ⚠️ Moyenne |
| **EKS** | CaaS | Kubernetes | ⚠️ Très élevée |
| **Lightsail** | PaaS | Sites web simples (WordPress) | ✅ Très faible |

---

## 💰 Coûts Estimés

### Mode Single Instance (Free Tier)

| Ressource | Coût | Free Tier |
|-----------|------|-----------|
| **EC2 t3.micro** | $0.0104/heure | ✅ 750h/mois gratuit (12 mois) |
| **Elastic Beanstalk** | $0 | ✅ Gratuit |
| **Data transfer OUT** | $0.09/GB | ✅ 100 GB/mois gratuit |
| **CloudWatch** | $0 | ✅ Gratuit (métriques de base) |

**Total si vous restez dans le Free Tier : $0/mois** ✅

**Total si vous dépassez le Free Tier : ~$7.50/mois** (1 instance t3.micro 24/7)

### Mode Load Balanced (Production)

| Ressource | Coût estimé |
|-----------|-------------|
| EC2 (2× t3.small) | ~$30/mois |
| Application Load Balancer | ~$16/mois |
| **Total** | ~$46/mois |

**⚠️ Pour ce lab, utilisez uniquement "Single instance" pour rester gratuit !**

---

## 🚀 Pour Aller Plus Loin

### 1. Déployer Votre Propre Application Java

**Créez une application Spring Boot** :

```bash
# Installer Spring Boot CLI
curl -s "https://get.sdkman.io" | bash
sdk install springboot

# Créer une nouvelle application
spring init --dependencies=web myapp
cd myapp

# Ajouter un contrôleur
cat > src/main/java/com/example/demo/HelloController.java <<EOF
package com.example.demo;
import org.springframework.web.bind.annotation.*;

@RestController
public class HelloController {
    @GetMapping("/")
    public String hello() {
        return "Bonjour depuis Spring Boot sur AWS !";
    }
}
EOF

# Compiler l'application
./mvnw package

# Le fichier .jar est dans target/
# Déployez-le sur Elastic Beanstalk !
```

### 2. Activer le Load Balancer et l'Auto Scaling

1. **Configuration → Capacity → Edit**

2. **Environment type** : Changez de `Single instance` à `Load balanced`

3. **Instances** :
   - Min : 2
   - Max : 4

4. **Scaling triggers** :
   - Metric : CPU Utilization
   - Target : 70%

5. **Cliquez sur "Apply"**

**Résultat** : Elastic Beanstalk créera un ALB et un Auto Scaling Group automatiquement !

### 3. Connecter une Base de Données RDS

1. **Configuration → Database → Edit**

2. **Engine** : MySQL ou PostgreSQL

3. **Instance class** : db.t3.micro

4. **Username/Password** : Choisissez

5. **Cliquez sur "Apply"**

**Résultat** : Beanstalk créera une base de données RDS et configurera les variables d'environnement automatiquement !

### 4. Configurer un Nom de Domaine Personnalisé

1. **Route 53 → Hosted zones → Votre domaine**

2. **Create record** :
   - Record type : A
   - Alias : Yes
   - Alias target : Elastic Beanstalk environment

---

## 📚 Ressources Supplémentaires

- [AWS Elastic Beanstalk Documentation](https://docs.aws.amazon.com/elasticbeanstalk/)
- [Elastic Beanstalk Java Platform](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/java-se-platform.html)
- [Sample Applications](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/tutorials.html)
- [Best Practices](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/best-practices.html)

---

## 🐛 Troubleshooting

### Problème 1 : L'environnement reste en "Degraded"

**Symptôme** : L'état de santé est rouge ou orange

**Solutions** :
1. Consultez les logs : Logs → Request Logs → Last 100 Lines
2. Vérifiez les "Recent events" en bas de la page
3. Vérifiez que le port 80 est bien ouvert dans le Security Group

### Problème 2 : "502 Bad Gateway"

**Cause** : L'application Java ne démarre pas correctement

**Solutions** :
1. Vérifiez que le fichier .war est valide
2. Consultez `web.stdout.log` pour voir les erreurs Java
3. Vérifiez que votre application écoute sur le port 5000 (attendu par Beanstalk)

### Problème 3 : Le déploiement prend trop de temps (>15 minutes)

**Cause** : Problème réseau ou de ressources

**Solutions** :
1. Attendez encore 5-10 minutes
2. Si toujours bloqué : Terminate l'environnement et recréez-le
3. Vérifiez que votre compte AWS n'a pas de limites dépassées

### Problème 4 : "The environment name is not available"

**Cause** : Le nom est déjà utilisé (par vous ou quelqu'un d'autre)

**Solution** : Ajoutez un chiffre ou votre nom : `M2i-Hannah-JavaApp-env-2`

---

## 📝 Notes Importantes

⚠️ **Suppression des ressources** :
- **Toujours supprimer l'environnement après le lab** pour éviter les frais
- La suppression prend 5-10 minutes
- Vérifiez dans EC2 que l'instance est bien "Terminated"

💡 **Bonnes pratiques** :
- Utilisez "Single instance" pour dev/test (gratuit)
- Utilisez "Load balanced" pour production (payant mais haute disponibilité)
- Activez les "Managed updates" pour les mises à jour automatiques de sécurité
- Utilisez RDS pour les bases de données (au lieu de MySQL sur l'instance)

🎯 **Cas d'usage** :
- ✅ Applications web (Spring Boot, Django, Express)
- ✅ APIs REST
- ✅ Sites corporate
- ⚠️ Applications stateful (préférer ECS ou EKS)
- ❌ Fonctions événementielles (utiliser Lambda)

---

**Durée estimée du lab** : 45 minutes - 1h

🎉 **Félicitations !** Vous savez maintenant déployer des applications avec AWS Elastic Beanstalk !

Vous avez appris :
- ✅ Ce qu'est un service PaaS
- ✅ Comment déployer une application Java en 5 minutes
- ✅ Comment surveiller une application
- ✅ Comment mettre à jour une application
- ✅ Les ressources créées automatiquement par Beanstalk
- ✅ La différence entre PaaS et IaaS
