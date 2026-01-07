# Module Amazon Web Services — Objectifs pédagogiques et rythme (3 jours)

**Durée**: 3 jours • **Activités Type (AT)**: 1, 3 • **Compétences (CP)**: 3, 4, 9

**Objectifs Globaux**
- Comprendre les fondamentaux du cloud computing et des modèles de service.
- Découvrir et utiliser les services clés d'AWS (Compute, Storage, Network).
- Mettre en œuvre une infrastructure sécurisée sur AWS (IAM, VPC, SGs).
- Déployer une application simple avec les services managés d'AWS.

**Objectifs Détaillés (avec définitions)**
1. Fondamentaux du Cloud
	- Comprendre les principes du cloud computing: notions de service à la demande, élasticité, paiement à l'usage et résilience.
	- Modèles IaaS/PaaS/SaaS: IaaS (infrastructure virtualisée), PaaS (plateforme de déploiement), SaaS (application prête à l'emploi).

2. Plateforme AWS et Accès
	- Infrastructure globale: régions, zones de disponibilité, zones locales et réseau mondial AWS.
	- Comptes AWS et Organizations: gestion multi-comptes, contrôle centralisé des politiques et de la facturation.
	- Console, CLI et API: interfaces graphiques et programmatiques pour administrer et automatiser AWS.

3. Sécurité et Contrôle d’Accès (IAM)
	- Utilisateurs, groupes, rôles: identités et délégation d'accès pour personnes, applications et services.
	- Stratégies (policies) IAM: documents JSON définissant des autorisations de manière fine (Allow/Deny, conditions).

4. Réseaux Virtuels (Amazon VPC)
	- Création et gestion d’un VPC: espace réseau isolé avec sous-réseaux, tables de routage et passerelles.
	- Réseaux publics, privés, NAT: exposition contrôlée à Internet et sortie sécurisée depuis des sous-réseaux privés.
	- Sécurisation avec Security Groups: pare-feu d'état au niveau instance/ENI pour filtrer le trafic.

5. Calcul Élastique (Amazon EC2)
	- Création et gestion d’instances: choix des AMI, types d’instances, clés SSH, EBS, démarrage/arrêt et sécurité.

6. Stockage Objet (Amazon S3)
	- Création et gestion de buckets: classes de stockage, versioning, lifecycle et chiffrement.
	- Contrôle d’accès (IAM et Bucket Policies): gouvernance d’accès au niveau identité et ressource.

7. Déploiement Applicatif (AWS Elastic Beanstalk)
	- Déployer une application: plateforme managée pour provisionner, déployer et scaler sans gérer l’infrastructure.

**Rythme Pédagogique (3 jours)**
- Jour 1 — Fondamentaux, Plateforme AWS & IAM
  - Objectifs visés: 1, 2, 3.
  - Activités: panorama du cloud; régions/AZ; création de l’environnement de labo; prise en main Console/CLI; création d’utilisateurs/groupes/rôles; MFA; rédaction et test de policies de base.
  - Livrables: environnement CLI opérationnel, fiche IaaS/PaaS/SaaS, politiques IAM validées.

- Jour 2 — Réseaux & Compute/Storage: VPC, EC2, S3
  - Objectifs visés: 4, 5, 6.
  - Activités: création d’un VPC (subnets publics/privés, routes, IGW/NAT); Security Groups; lancement d’instances EC2 (AMIs, types, EBS, SSH); création d’un bucket S3 (versioning, lifecycle, encryption) et mise en place d’une Bucket Policy.
  - Livrables: diagramme VPC, instance EC2 fonctionnelle, bucket S3 sécurisé avec règles de cycle de vie.

- Jour 3 — Déploiement Managé & Synthèse: Elastic Beanstalk
  - Objectifs visés: 7 + consolidation sécurité/ops.
  - Activités: déploiement d’une application sur Elastic Beanstalk; variables d’environnement; logs/monitoring; bonnes pratiques transverses (sécurité, coûts, tagging); mini-projet de synthèse.
  - Livrables: application déployée, check-list de bonnes pratiques et plan d’amélioration.

**Capacités Attendues**
- Comprendre les avantages du modèle cloud computing (agilité, coûts, élasticité).
- Appréhender les services fondamentaux d’AWS et leurs cas d’usage.
- Exploiter les services clés (Compute, Storage, Network) de manière sécurisée.

Si vous souhaitez, je peux adapter le rythme à votre public (débutant/confirmé), ajouter des labs guidés ou fournir des supports d’évaluation (quiz, barème du mini-projet).
