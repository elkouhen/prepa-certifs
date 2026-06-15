# AWS Solutions Architect Professional - Ultimate Cheat Sheet

Légende: 🔥⭐ = fréquence/importance estimée à l'examen (1 à 5 étoiles).

---

# Partie 1 - Organisation multi-compte & Gouvernance

## AWS Organizations

Définition: Service permettant de gérer de façon centralisée plusieurs comptes AWS regroupés en Organizational Units (OUs), avec facturation consolidée (consolidated billing) et application de politiques (SCP) à l'échelle de l'organisation.

Points clés:
- Compte de management (master) : ne doit héberger AUCUNE charge applicative, uniquement la gestion (best practice).
- Consolidated Billing : un seul mode de paiement, remises de volume (Reserved Instances/Savings Plans partagés entre comptes via RI/SP sharing).
- Permet l'utilisation d'autres services "Organization-wide" : Control Tower, RAM, Config, GuardDuty, Security Hub, Backup multi-compte.

🔥⭐⭐⭐⭐⭐

---

## Service Control Policies (SCP)

Définition: Politiques appliquées au niveau d'une OU ou d'un compte qui définissent les permissions MAXIMALES possibles (garde-fous), même si un utilisateur a une politique IAM permissive. Une SCP ne donne JAMAIS de droits, elle ne fait que restreindre.

Piège: SCP != IAM Policy
- IAM Policy : donne (ou refuse) des permissions à un utilisateur/rôle.
- SCP : plafonne ce qu'il est possible d'autoriser dans un compte, même pour le root du compte membre. Ne s'applique PAS au compte de management.

Mnémo: SCP = "filtre" appliqué AVANT toute évaluation IAM. Si SCP ne permet pas une action, aucune policy IAM ne pourra la débloquer.

🔥⭐⭐⭐⭐⭐

---

## AWS Control Tower

Définition: Service qui automatise la mise en place d'un "landing zone" multi-compte conforme aux best practices : création d'OUs (Security, Sandbox...), comptes de log centralisés (Log Archive) et d'audit, application de "Guardrails" (= SCP + Config Rules préconfigurées) préventifs et détectifs.

Guardrails:
- Préventif = implémenté via SCP (empêche une action).
- Détectif = implémenté via AWS Config (détecte une non-conformité après coup).

Landing zone "clé en main" basée sur Organizations.

🔥⭐⭐⭐⭐

---

## AWS Resource Access Manager (RAM)

Définition: Service permettant de partager des ressources AWS (subnets VPC, Transit Gateway, License Manager, Route 53 Resolver rules, clusters...) entre comptes d'une même Organization (ou avec des comptes externes), sans dupliquer les ressources ni utiliser de peering.

Cas d'usage typique: VPC partagé (Shared VPC) - un compte "réseau" possède le VPC et partage des subnets avec des comptes "applicatifs" qui y déploient leurs ressources (EC2, RDS...) mais ne peuvent pas modifier le réseau.

Partager des ressources entre comptes sans les dupliquer.

🔥⭐⭐⭐⭐

---

## AWS Service Catalog

Définition: Permet de créer un catalogue de produits IT pré-approuvés (basés sur des templates CloudFormation) que les utilisateurs peuvent déployer en self-service, en respectant les standards de conformité/sécurité de l'entreprise sans avoir les droits IAM complets.

Self-service catalogue de ressources approuvées.

🔥⭐⭐⭐

---

## AWS Config

Définition: Service qui enregistre en continu les configurations des ressources AWS et leurs changements (historique), et évalue leur conformité par rapport à des règles (Config Rules, managées ou custom via Lambda).

Points clés:
- Conformance Packs : ensembles de Config Rules + remédiations, déployables sur plusieurs comptes/régions via Organizations.
- Remédiation automatique possible via SSM Automation Documents.
- Multi-account/multi-region aggregator pour vue centralisée.

Audit de configuration + conformité continue.

🔥⭐⭐⭐⭐

---

## AWS IAM Identity Center (ex AWS SSO)

Définition: Point d'accès unique (SSO) pour gérer l'accès des utilisateurs/groupes à plusieurs comptes AWS et applications cloud, via un identity provider externe (Active Directory, Okta, Azure AD) ou un annuaire interne. Utilise des "Permission Sets" (équivalents de rôles IAM) appliqués via Organizations.

SSO centralisé multi-compte.

🔥⭐⭐⭐

---

## Permissions Boundaries vs SCP vs IAM Policy

Piège classique de l'examen - 3 mécanismes de limitation différents:
- IAM Policy (identity/resource-based) : accorde des permissions.
- Permissions Boundary : limite MAXIMALE des permissions qu'un rôle/utilisateur IAM peut avoir (définie au niveau de l'entité IAM, utile pour déléguer la création de rôles sans donner trop de pouvoir).
- SCP : limite MAXIMALE au niveau du COMPTE entier (Organizations), s'applique à tout le monde y compris root.

Mnémo: Permissions Boundary = "laisse" individuelle posée sur un utilisateur/rôle ; SCP = "mur" autour de tout le compte.

🔥⭐⭐⭐⭐⭐

---

## Fédération d'identités (SAML 2.0, OIDC, Amazon Cognito)

Définition: Permet à des utilisateurs externes (annuaire d'entreprise, IdP tiers, utilisateurs d'une app mobile/web) d'obtenir des accès temporaires à AWS sans créer d'utilisateurs IAM dédiés, via STS (Security Token Service).

Points clés:
- SAML 2.0 : fédération avec un IdP d'entreprise (Active Directory FS, Okta, Azure AD) -> `AssumeRoleWithSAML`, rôle IAM endossé avec des permissions définies. Souvent utilisé pour la console AWS (SSO entreprise).
- Web Identity Federation / OIDC : fédération avec des IdP publics (Google, Facebook, Amazon, ou tout IdP OIDC) -> `AssumeRoleWithWebIdentity`. Recommandé via Amazon Cognito plutôt qu'en direct.
- Amazon Cognito User Pools : annuaire d'utilisateurs managé pour applications (sign-up/sign-in, MFA, fédération avec IdP externes SAML/OIDC), retourne des JWT.
- Amazon Cognito Identity Pools (Federated Identities) : échange une identité (User Pool, IdP externe, ou "guest" non authentifié) contre des credentials AWS temporaires (rôles IAM) pour accéder directement à des ressources AWS (S3, DynamoDB...) depuis une app mobile/web.
- IAM Identity Center vs fédération SAML directe : Identity Center est la solution recommandée pour l'accès multi-compte des employés ; la fédération SAML/OIDC directe via STS est plus utilisée pour des accès programmatiques ou des applications custom.

Piège: User Pool = authentification (qui êtes-vous, JWT) ; Identity Pool = autorisation AWS (quels droits AWS, credentials IAM temporaires). Les deux sont souvent utilisés ensemble.

🔥⭐⭐⭐⭐

---

# Partie 2 - Networking avancé

## VPC Peering vs Transit Gateway vs PrivateLink

Définition et différences (très fréquent à l'examen):

- VPC Peering : connexion point-à-point entre 2 VPC, non transitive (si A<->B et B<->C, A ne peut pas joindre C via B). Pas de "overlapping CIDR". Simple et gratuit (hors transfert de données).
- Transit Gateway (TGW) : hub central régional (extensible multi-région via peering de TGW) qui connecte VPC, VPN, Direct Connect de façon transitive. Tables de routage par "attachment" pour isoler des domaines de routage.
- AWS PrivateLink (VPC Endpoint de type Interface) : expose un service (le vôtre via NLB, ou un service AWS/SaaS partenaire) dans le VPC du consommateur via une ENI privée, sans passer par internet, sans peering, pas de problème de CIDR qui se chevauchent.

Piège: 
- Beaucoup de VPC à interconnecter de façon transitive + on-prem -> Transit Gateway.
- Exposer un service à des clients (autres VPC/comptes) sans exposer tout le VPC -> PrivateLink.
- CIDR qui se chevauchent entre 2 VPC -> Peering impossible, utiliser PrivateLink.

🔥⭐⭐⭐⭐⭐

---

## AWS Direct Connect (DX)

Définition: Connexion réseau dédiée privée entre le datacenter on-premise et AWS (via un AWS Direct Connect Partner ou colocation), offrant une bande passante constante et une latence plus faible/stable qu'une connexion internet classique.

Points clés:
- Direct Connect Gateway : permet de connecter une connexion DX à plusieurs VPC dans différentes régions (via Transit Gateway ou Virtual Private Gateway).
- LAG (Link Aggregation Group) : agrège plusieurs connexions DX physiques pour augmenter la bande passante / résilience.
- MACsec : chiffrement de couche 2 sur les connexions DX dédiées.
- DX seul = pas chiffré nativement -> combiner avec un VPN (Site-to-Site VPN over DX, ou VPN privé sur Transit VIF) si chiffrement requis.
- DX Resiliency : pour une haute résilience, utiliser 2 connexions DX dans des locations différentes (et idéalement 2 partenaires différents).

Piège: DX = bande passante/latence stable, pas chiffré par défaut. Si chiffrement obligatoire (compliance) -> VPN sur DX ou MACsec.

🔥⭐⭐⭐⭐

---

## Site-to-Site VPN vs Client VPN vs DX

- Site-to-Site VPN : connexion chiffrée IPSec entre un Virtual Private Gateway (ou TGW) et un customer gateway on-premise, via internet. Rapide à mettre en place, bande passante variable (dépend d'internet), peut servir de backup à DX.
- AWS Client VPN : VPN OpenVPN managé pour que des utilisateurs individuels (postes de travail) se connectent au VPC.
- Direct Connect : ligne dédiée, performances stables, mise en place plus longue (semaines).

Mnémo: VPN = rapide à déployer mais variable ; DX = long à déployer mais stable. Combo DX (primaire) + VPN (backup) = best practice résilience.

🔥⭐⭐⭐⭐

---

## Amazon Route 53 - Routing Policies

Définition: DNS managé proposant plusieurs politiques de routage utilisées pour la haute disponibilité, le DR et la performance:

- Simple : une seule ressource (ou liste, choix aléatoire côté client).
- Weighted : répartit le trafic selon des poids (ex : tests A/B, déploiements canary, migration progressive).
- Latency-based : route vers la région offrant la latence la plus faible pour l'utilisateur.
- Failover : actif/passif, bascule automatique vers le secondaire si le health check du primaire échoue (DR).
- Geolocation : route selon la localisation géographique de l'utilisateur (conformité, contenu localisé).
- Geoproximity : route selon la proximité géographique avec un biais configurable (nécessite Route 53 Traffic Flow).
- Multi-value answer : retourne plusieurs valeurs avec health checks (alternative légère à un load balancer).

🔥⭐⭐⭐⭐⭐

---

## Amazon CloudFront avancé

Définition: CDN qui met en cache du contenu dans des Edge Locations. Pour l'examen, retenir surtout les fonctionnalités avancées:

- Origin Access Control (OAC) : restreint l'accès direct à une origine S3, seul CloudFront peut y accéder.
- Lambda@Edge / CloudFront Functions : exécution de code à la périphérie. CloudFront Functions = léger, haute échelle, JS uniquement, latence <1ms (manipulation de requêtes/réponses simples). Lambda@Edge = plus de ressources, runtimes multiples, peut appeler des services externes, latence plus élevée.
- Origin Groups (Failover) : bascule automatique vers une origine secondaire si l'origine primaire échoue.
- Field-Level Encryption : chiffre des champs spécifiques (ex : données de carte bancaire) entre l'utilisateur et l'origine, lisibles uniquement par certains composants applicatifs.
- Signed URLs / Signed Cookies : accès restreint à du contenu privé.

Piège: CloudFront Functions (léger/rapide, à l'edge) vs Lambda@Edge (plus puissant, peut s'exécuter aux régional edge caches).

🔥⭐⭐⭐⭐

---

## AWS Global Accelerator

Définition: Service qui attribue des adresses IP statiques anycast servant de point d'entrée fixe vers des endpoints (ALB, NLB, EC2, IP Elastic) dans plusieurs régions. Le trafic est routé via le réseau privé AWS (backbone) jusqu'au point le plus proche de l'utilisateur, améliorant performance et permettant un failover rapide entre régions.

Piège: Global Accelerator != CloudFront
- CloudFront : optimisé pour du contenu cacheable (statique/dynamique HTTP), via edge caching.
- Global Accelerator : optimisé pour le trafic TCP/UDP non-HTTP également, IP statiques, failover multi-région rapide pour des applications (pas de cache).

IP statique + failover multi-région rapide = Global Accelerator.

🔥⭐⭐⭐⭐

---

## AWS Network Firewall / WAF / Shield

- AWS WAF : protège les applications web (CloudFront, ALB, API Gateway, AppSync) au niveau 7 (HTTP) contre des attaques courantes (SQL injection, XSS), via des "Web ACL" et règles (rate-based, gérées AWS, custom).
- AWS Shield Standard : protection DDoS de base, gratuite, activée automatiquement pour tous les clients.
- AWS Shield Advanced : protection DDoS avancée (L3/L4/L7), équipe de réponse DDoS (DRT), remboursement des coûts liés aux pics DDoS, intégration avec WAF.
- AWS Network Firewall : pare-feu réseau managé (couches 3-7) pour un VPC entier, basé sur des règles Suricata, permet filtrage par domaine, IPS/IDS.

🔥⭐⭐⭐

---

## VPC Endpoints (Gateway vs Interface)

- Gateway Endpoint : pour S3 et DynamoDB uniquement, ajoute une route dans la table de routage du VPC, gratuit.
- Interface Endpoint (PrivateLink) : ENI avec IP privée dans le subnet, pour la plupart des autres services AWS (et services tiers), facturé à l'heure + au Go.

🔥⭐⭐⭐

---

## Elastic Load Balancing - ALB vs NLB vs GWLB

Définition: 3 types de load balancers managés, chacun pour un cas d'usage précis (piège classique de l'examen).

Points clés:
- Application Load Balancer (ALB) : couche 7 (HTTP/HTTPS/gRPC/WebSocket), routage avancé basé sur le path/host/header/query string, intégration native avec ECS/EKS (target type "ip"), Lambda comme cible, authentification intégrée (Cognito ou OIDC).
- Network Load Balancer (NLB) : couche 4 (TCP/UDP/TLS), latence ultra-faible, des millions de requêtes/seconde, IP statique/Elastic IP par AZ, préserve l'IP source du client. Seul ELB compatible avec PrivateLink (endpoint service) et avec les groupes cibles "Multi-Attach"/Auto Scaling à très grande échelle.
- Gateway Load Balancer (GWLB) : couche 3, déploie des appliances tierces transparentes (firewalls, IDS/IPS) en ligne devant le trafic via le protocole GENEVE, utilisé avec un GWLB Endpoint pour insérer une appliance dans le chemin réseau sans changer le routage applicatif.
- Cross-zone load balancing : ALB toujours activé ; NLB désactivé par défaut (active = répartition uniforme entre AZ mais trafic inter-AZ facturé).
- Sticky sessions : cookies d'application (ALB) ou basé sur la source IP (NLB).

Piège: Exposer un service à d'autres VPC/comptes via PrivateLink -> NLB obligatoire derrière le VPC Endpoint Service. Insérer un firewall tiers transparent -> GWLB.

🔥⭐⭐⭐⭐

---

## Conception de VPC - subnets, route tables, NACLs vs Security Groups

Définition: Notions de design réseau de base mais fréquemment testées dans des scénarios complexes (multi-AZ, multi-tier, hybrides).

Points clés:
- Security Groups : stateful (le trafic retour est automatiquement autorisé), s'appliquent au niveau ENI/instance, uniquement règles "allow".
- Network ACLs (NACL) : stateless (il faut autoriser explicitement le trafic retour), s'appliquent au niveau subnet, règles "allow" ET "deny" évaluées dans l'ordre des numéros de règle - utile pour bloquer explicitement une IP malveillante (impossible avec un Security Group).
- Subnets publics vs privés : un subnet est "public" si sa route table a une route vers un Internet Gateway (IGW) pour 0.0.0.0/0.
- NAT Gateway (managé, par AZ, haute disponibilité) vs NAT Instance (auto-géré, moins cher mais SPOF sauf HA scripté) : permettent à des ressources en subnet privé d'accéder à internet en sortie uniquement.
- VPC CIDR : planifier la taille (/16 typique) en anticipant le nombre de subnets/AZ et la croissance ; éviter les chevauchements de CIDR entre VPC qui devront être peerés.

Piège: Bloquer une IP spécifique au niveau réseau -> NACL (deny rule), pas Security Group.

🔥⭐⭐⭐

---

# Partie 3 - Migration & Modernisation

## Les 7 R de la migration

Définition: Stratégies de migration d'applications vers le cloud, à connaître par cœur:

1. Retire : décommissionner une application inutile.
2. Retain : garder on-premise (pas encore prêt à migrer).
3. Rehost ("lift and shift") : déplacer tel quel, sans modification (souvent via MGN).
4. Replatform ("lift, tinker and shift") : optimisations mineures sans changer l'architecture coeur (ex : RDS au lieu d'une DB auto-gérée).
5. Repurchase : remplacer par un SaaS (ex : CRM -> Salesforce).
6. Refactor / Re-architect : redesign complet pour exploiter le cloud-native (microservices, serverless).
7. Relocate : déplacer des VM/applications entières (ex : VMware Cloud on AWS) sans les modifier ni les racheter, changement d'hébergement uniquement.

🔥⭐⭐⭐⭐⭐

---

## AWS Application Discovery Service & Migration Hub

- Application Discovery Service : collecte des données sur les serveurs on-premise (config, utilisation, dépendances réseau) via un agent ou un connecteur agentless (vCenter), pour planifier une migration.
- Migration Hub : tableau de bord central qui suit l'avancement des migrations à travers plusieurs outils AWS (MGN, DMS, etc.).

Découvrir l'existant avant de migrer.

🔥⭐⭐

---

## AWS Application Migration Service (MGN)

Définition: Service de "rehost" (lift-and-shift) qui remplace AWS SMS (Server Migration Service, déprécié). Installe un agent léger sur les serveurs source (physiques, virtuels, autres clouds) qui réplique en continu les disques vers AWS ; au moment de la coupure, lance des instances EC2 prêtes à l'emploi.

Outil recommandé par AWS pour le "Rehost" à grande échelle.

🔥⭐⭐⭐⭐

---

## AWS Database Migration Service (DMS)

Définition: Migre des bases de données vers AWS avec un downtime minimal grâce à la réplication continue (CDC - Change Data Capture). Fonctionne en "homogène" (même moteur, ex: Oracle->Oracle) ou "hétérogène" (moteurs différents, ex: Oracle->Aurora, via AWS Schema Conversion Tool pour convertir le schéma).

Points clés:
- AWS SCT (Schema Conversion Tool) : convertit le schéma et le code (procédures stockées) lors d'une migration hétérogène.
- Réplication continue = downtime quasi nul (bascule finale après synchronisation).

🔥⭐⭐⭐⭐

---

## AWS DataSync vs Storage Gateway vs Snow Family

Piège classique - choisir le bon outil de transfert de données:

- AWS DataSync : transfert de fichiers automatisé et accéléré (jusqu'à 10x plus rapide que des outils open source) entre on-premise (NFS, SMB) et S3/EFS/FSx, ou entre services AWS. Idéal pour des transferts ponctuels ou récurrents/planifiés de gros volumes.
- AWS Storage Gateway : passerelle hybride qui donne un accès continu (pas juste un transfert ponctuel) depuis on-premise vers le stockage AWS, avec cache local. Trois types :
  - File Gateway : partage de fichiers NFS/SMB mappés sur S3.
  - Volume Gateway : volumes iSCSI mappés sur EBS (mode "cached" = données principalement dans le cloud, cache local ; mode "stored" = données principalement on-prem, sauvegardées async vers S3).
  - Tape Gateway : remplace les sauvegardes sur bande physique par un "virtual tape library" sur S3/Glacier.
- Snow Family (Snowcone, Snowball Edge, Snowmobile) : transfert physique de très gros volumes de données quand la bande passante réseau est insuffisante ("si le transfert prendrait plus d'une semaine via le réseau, pensez Snow"). Snowball Edge peut aussi faire du compute/edge (clusters EKS locaux).

Mnémo: DataSync = transfert (un coup ou récurrent) ; Storage Gateway = accès permanent hybride ; Snow = pas assez de bande passante, transport physique.

🔥⭐⭐⭐⭐⭐

---

# Partie 4 - Stockage avancé

## Amazon S3 - fonctionnalités avancées

- Storage Classes : Standard, Intelligent-Tiering (déplace automatiquement entre tiers selon le pattern d'accès), Standard-IA, One Zone-IA, Glacier Instant/Flexible/Deep Archive.
- Lifecycle Policies : transitions automatiques entre classes de stockage et expiration des objets.
- Replication : 
  - CRR (Cross-Region Replication) : DR, conformité (réglementations exigeant une copie dans une autre région), latence (rapprocher les données des utilisateurs).
  - SRR (Same-Region Replication) : agrégation de logs, conformité (copie dans un compte différent), environnements de prod/test séparés.
  - Réplication non rétroactive par défaut (objets existants non copiés sauf S3 Batch Replication).
- S3 Object Lambda : applique une fonction Lambda à la volée sur les objets retournés par une requête GET (ex : rédaction de PII, redimensionnement d'image) sans dupliquer les données.
- Multi-Region Access Points : point d'accès unique qui route les requêtes S3 vers le bucket le plus proche/disponible parmi plusieurs régions, avec réplication bidirectionnelle.
- S3 Access Points : noms d'accès simplifiés avec leurs propres policies, pour gérer l'accès à grande échelle (multi-application).
- S3 Object Lock : conformité WORM (Write Once Read Many), modes Governance/Compliance.

🔥⭐⭐⭐⭐⭐

---

## EFS vs FSx (famille FSx)

Piège classique - bon système de fichiers selon l'OS/use case:

- Amazon EFS : NFS managé, multi-AZ natif, pour workloads Linux.
- Amazon FSx for Windows File Server : SMB managé, pour workloads Windows (intégration Active Directory).
- Amazon FSx for Lustre : système de fichiers haute performance pour HPC, machine learning, traitement de données massives, peut être lié à S3 (lazy loading des données depuis/vers S3).
- Amazon FSx for NetApp ONTAP : fonctionnalités NetApp (snapshots, clones, déduplication), pour migrer des charges NetApp on-premise, supporte NFS/SMB/iSCSI.
- Amazon FSx for OpenZFS : système de fichiers ZFS managé, hautes performances et faible latence, snapshots.

🔥⭐⭐⭐

---

## EBS - points avancés

- Types de volumes : gp3/gp2 (SSD usage général), io1/io2 Block Express (IOPS élevées, bases de données critiques), st1 (HDD throughput-optimisé, big data), sc1 (HDD cold, archives).
- Snapshots : incrémentaux, stockés sur S3, peuvent être copiés entre régions (DR) et chiffrés (chiffrement à la copie même si snapshot source non chiffré).
- Fast Snapshot Restore (FSR) : élimine la latence d'initialisation ("lazy loading") lors de la création d'un volume depuis un snapshot.
- Multi-Attach (io1/io2) : attache un volume à plusieurs instances Nitro simultanément (nécessite un système de fichiers cluster-aware).

🔥⭐⭐

---

# Partie 5 - Bases de données

## Amazon Aurora - fonctionnalités avancées

- Aurora Global Database : une base primaire dans une région, jusqu'à 5 régions secondaires en lecture avec une latence de réplication < 1s (réplication au niveau du storage, pas du moteur). Permet un failover cross-région en cas de désastre régional (RTO < 1 min typiquement).
- Aurora Serverless v2 : scaling automatique et instantané de la capacité (ACUs) selon la charge, facturation à l'usage, utile pour des charges variables/imprévisibles ou du dev/test.
- Aurora Replicas vs Read Replicas MySQL/Postgres : les Aurora Replicas partagent le même stockage que le primaire (pas de lag de réplication au niveau storage) et peuvent devenir le nouveau writer en quelques secondes en cas de failover.
- Backtrack (Aurora MySQL) : "rembobiner" la base à un instant T sans restaurer un backup complet.

🔥⭐⭐⭐⭐⭐

---

## Amazon DynamoDB - fonctionnalités avancées

- Global Tables : tables multi-région actives-actives, réplication automatique, résolution de conflits "last writer wins". Utilisé pour DR et latence faible globale.
- DynamoDB Accelerator (DAX) : cache en mémoire compatible API DynamoDB, réduit la latence de millisecondes à microsecondes pour les lectures.
- DynamoDB Streams : flux ordonné des modifications, utilisé pour déclencher des Lambda (ex : réplication, agrégation, audit).
- Capacité : On-Demand (paiement à l'usage, scaling automatique) vs Provisioned (avec Auto Scaling configurable).
- TTL (Time To Live) : suppression automatique d'items expirés.

🔥⭐⭐⭐⭐

---

## RDS Multi-AZ vs Read Replicas

Piège classique:
- Multi-AZ : haute disponibilité (DR), réplication SYNCHRONE vers un standby dans une autre AZ, failover automatique en cas de panne, le standby n'est PAS utilisable pour des lectures (sauf Multi-AZ avec 2 réplicas lisibles, dispo pour certains moteurs).
- Read Replicas : scalabilité en lecture, réplication ASYNCHRONE, peuvent être dans une autre région (cross-region read replica = DR + latence), peuvent être promues en instance autonome.

Mnémo: Multi-AZ = disponibilité (sync, pas de lecture), Read Replica = performance lecture (async, lecture possible).

🔥⭐⭐⭐⭐

---

## Amazon ElastiCache (Redis vs Memcached)

- Redis : persistance (snapshots/AOF), réplication multi-AZ avec failover automatique (cluster mode), structures de données avancées (sets, sorted sets), pub/sub, transactions.
- Memcached : multi-thread, simple, pas de persistance ni réplication, scaling horizontal par sharding simple, idéal pour un cache pur sans état à reconstruire.

Cache: Redis = "feature-rich + HA", Memcached = "simple + multi-thread".

🔥⭐⭐⭐

---

## Amazon Redshift - fonctionnalités avancées

- RA3 nodes : sépare le calcul du stockage (stockage géré sur S3, scaling indépendant).
- Redshift Spectrum : interroge directement des données dans S3 (data lake) sans les charger dans Redshift.
- Concurrency Scaling : ajoute automatiquement de la capacité de calcul transitoire pour absorber des pics de requêtes concurrentes.
- Redshift Serverless : exécution sans gestion de cluster, scaling automatique.

Data warehouse à l'échelle du pétaoctet.

🔥⭐⭐⭐

---

# Partie 6 - Sécurité & Chiffrement

## AWS KMS - points avancés

- Clés gérées par AWS vs clés gérées par le client (CMK) vs clés importées (BYOK - Bring Your Own Key).
- Multi-Region Keys : clés KMS répliquées entre régions avec le même key material, utile pour le chiffrement de données répliquées (ex : DynamoDB Global Tables, Aurora Global Database chiffrées).
- Envelope Encryption : KMS chiffre une "data key" qui chiffre elle-même les données (évite d'envoyer toutes les données à KMS).
- Quotas d'appels API KMS (peuvent nécessiter une stratégie de cache de data keys pour des charges à très haut débit).
- Key Policies vs IAM Policies : l'accès à une clé KMS nécessite à la fois une key policy l'autorisant ET (optionnellement) une IAM policy.

🔥⭐⭐⭐⭐

---

## Secrets Manager vs Systems Manager Parameter Store

- Secrets Manager : rotation automatique des secrets (intégration native RDS/Aurora/Redshift), chiffrement KMS natif, payant.
- Parameter Store (SSM) : stocke des configurations/secrets, tier standard gratuit, tier avancé payant (avec rotation possible via Lambda mais pas native), intégration KMS pour les "SecureString".

Piège: rotation automatique native d'identifiants de BDD -> Secrets Manager.

🔥⭐⭐⭐

---

## GuardDuty, Security Hub, Macie, Inspector, Detective

- Amazon GuardDuty : détection de menaces basée sur le ML, analyse les logs (CloudTrail, VPC Flow Logs, DNS logs, EKS audit logs, S3 data events) pour détecter des comportements anormaux (ex : instance compromise minant du crypto).
- AWS Security Hub : agrège et priorise les findings de sécurité de plusieurs services (GuardDuty, Inspector, Macie, Config) et vérifie la conformité à des standards (CIS, PCI DSS).
- Amazon Macie : utilise le ML pour découvrir et classifier automatiquement des données sensibles (PII) dans S3.
- Amazon Inspector : scan automatisé de vulnérabilités sur EC2, images de conteneurs (ECR) et fonctions Lambda.
- Amazon Detective : analyse approfondie et visualisation des causes racines d'un incident de sécurité (utilise les données de GuardDuty/CloudTrail/VPC Flow Logs).

Mnémo: GuardDuty détecte, Security Hub centralise/priorise, Macie cherche les PII, Inspector scanne les vulnérabilités, Detective enquête.

🔥⭐⭐⭐⭐

---

# Partie 7 - Résilience & Disaster Recovery

## Stratégies de Disaster Recovery (RTO/RPO croissants)

Définition: 4 stratégies classées de la moins chère/moins rapide à la plus chère/plus rapide:

1. Backup & Restore : sauvegardes régulières (vers S3/Glacier), restauration en cas de désastre. RTO/RPO élevés (heures), coût le plus faible.
2. Pilot Light : infrastructure minimale (souvent juste la base de données réplication active) déjà active dans la région DR ; le reste est provisionné (à partir d'AMI/templates) au moment du failover. RTO de l'ordre de dizaines de minutes.
3. Warm Standby : version réduite (scaled-down) mais pleinement fonctionnelle de l'environnement tourne en continu dans la région DR ; scale-up en cas de failover. RTO de l'ordre de minutes.
4. Multi-Site Active/Active (Hot Standby) : environnement complet actif dans plusieurs régions simultanément, répartition de charge entre régions. RTO quasi nul, coût le plus élevé.

RTO (Recovery Time Objective) = durée maximale d'interruption acceptable.
RPO (Recovery Point Objective) = quantité maximale de données perdues acceptable (mesurée en temps).

🔥⭐⭐⭐⭐⭐

---

## AWS Backup

Définition: Service centralisé de sauvegarde pour de multiples services AWS (EBS, RDS, DynamoDB, EFS, FSx, Storage Gateway, EC2...), permettant de définir des "Backup Plans" (planification, rétention, lifecycle vers cold storage) appliqués via des tags, et de répliquer des sauvegardes cross-region et cross-account (vault).

Centraliser et automatiser les sauvegardes multi-services/multi-comptes.

🔥⭐⭐⭐

---

# Partie 8 - Optimisation des coûts

## Outils de gestion des coûts

- AWS Cost Explorer : visualisation et analyse des coûts/usage historiques, prévisions.
- AWS Budgets : alertes basées sur des seuils de coût/usage/réservations (RI/SP) définis.
- AWS Compute Optimizer : recommandations basées sur le ML pour redimensionner (rightsizing) les ressources (EC2, EBS, Lambda, ASG) selon l'utilisation réelle.
- AWS Trusted Advisor : vérifications automatisées (coût, performance, sécurité, fault tolerance, service limits) avec recommandations ; le niveau de détail dépend du plan de support.
- Cost and Usage Report (CUR) : rapport le plus détaillé, exportable vers S3/Athena/QuickSight pour analyse fine.

🔥⭐⭐⭐

---

## Reserved Instances vs Savings Plans vs Spot

- Reserved Instances (RI) : engagement 1 ou 3 ans sur un type d'instance/région (Standard = peu flexible mais rabais max ; Convertible = peut changer de famille d'instance).
- Savings Plans (Compute SP / EC2 Instance SP) : engagement sur un montant d'usage ($/h) pendant 1 ou 3 ans, plus flexible que les RI (Compute SP s'applique à EC2, Fargate, Lambda, toutes régions/familles).
- Spot Instances : jusqu'à 90% de réduction, capacité pouvant être récupérée par AWS avec 2 min de préavis (Spot Interruption) - pour charges tolérantes aux interruptions (batch, CI/CD, stateless, ASG mixte).

Mnémo: RI/SP = engagement de durée pour charge stable et prévisible ; Spot = charge flexible/interruptible pour coût minimal.

🔥⭐⭐⭐⭐

---

# Partie 9 - Compute & Containers

## ECS vs EKS vs Fargate

- Amazon ECS : orchestrateur de conteneurs propriétaire AWS, plus simple à opérer, intégration native avec l'écosystème AWS (IAM par tâche, ALB, Service Discovery via Cloud Map).
- Amazon EKS : Kubernetes managé, pour des équipes avec expertise/besoin de portabilité Kubernetes (multi-cloud, outils K8s standards).
- AWS Fargate : moteur de calcul serverless pour ECS et EKS - pas de gestion d'instances EC2, on paie pour les ressources (vCPU/mémoire) des tâches/pods.

Piège: Fargate n'est pas un orchestrateur en soi, c'est un mode de lancement (compute) pour ECS ou EKS.

🔥⭐⭐⭐⭐

---

## EC2 - Placement Groups, options d'achat et Auto Scaling avancé

Définition: Notions de conception EC2/Auto Scaling fréquemment testées pour optimiser performance, résilience et coût.

Points clés:
- Placement Groups :
  - Cluster : instances regroupées sur un même rack/AZ pour latence réseau minimale (HPC, calcul distribué) - risque de panne corrélée.
  - Spread : instances réparties sur du matériel distinct (max 7 par AZ), pour des applications critiques nécessitant peu d'instances très résilientes.
  - Partition : instances regroupées en partitions (jusqu'à 7) sur du matériel distinct, chaque partition ne partage pas son hardware avec les autres - utilisé pour Hadoop/Kafka/Cassandra (résilience à grande échelle).
- Options d'achat EC2 : On-Demand, Reserved/Savings Plans (engagement), Spot (interruptible), Dedicated Hosts (serveur physique dédié, pour licences BYOL liées au socket/core ou compliance), Dedicated Instances (matériel dédié mais pas de contrôle du placement).
- Auto Scaling - politiques de scaling :
  - Target Tracking : maintient une métrique (ex : CPU 50%) - recommandé par défaut.
  - Step Scaling : ajuste la capacité par paliers selon l'ampleur de l'alarme.
  - Scheduled Scaling : anticipe des pics connus (ex : tous les lundis à 8h).
  - Predictive Scaling : utilise le ML pour anticiper la charge et pré-scaler.
- Warm Pools : instances pré-initialisées (stoppées ou en cours d'exécution) prêtes à rejoindre un Auto Scaling Group rapidement, réduit le temps de scale-out pour des applications à démarrage long.
- Lifecycle Hooks : mettent une instance en attente lors du lancement/arrêt pour exécuter des actions custom (installation, drainage de connexions) avant qu'elle entre/sorte du service.

Mnémo: Cluster = performance (latence), Spread = résilience max (peu d'instances), Partition = résilience à grande échelle (big data).

🔥⭐⭐⭐

---

## Stratégies de déploiement

- Rolling : remplace progressivement les instances/tâches anciennes par des nouvelles. Pas d'environnement supplémentaire, mais rollback plus lent.
- Blue/Green : nouvel environnement complet ("green") déployé en parallèle de l'existant ("blue"), bascule du trafic (DNS, ALB, CodeDeploy) une fois validé ; rollback quasi instantané.
- Canary : déploie la nouvelle version pour un petit pourcentage du trafic, augmente progressivement si pas d'erreur.
- Linear : augmentation par paliers réguliers (ex : 10% toutes les 10 min) - typique CodeDeploy/Lambda alias.

🔥⭐⭐⭐

---

# Partie 10 - Infrastructure as Code & Orchestration

## CloudFormation - fonctionnalités avancées

- StackSets : déploie une même stack CloudFormation sur plusieurs comptes et régions simultanément (intégration Organizations pour auto-déploiement sur nouveaux comptes).
- Nested Stacks : décompose un template complexe en sous-templates réutilisables.
- Drift Detection : détecte les changements manuels effectués hors CloudFormation sur des ressources gérées par une stack.
- Change Sets : prévisualise l'impact d'une mise à jour avant de l'appliquer.
- Custom Resources : permet à CloudFormation de provisionner des ressources non supportées nativement via une fonction Lambda.
- DeletionPolicy / UpdateReplacePolicy : contrôle ce qui arrive à une ressource (Retain, Snapshot, Delete) lors de la suppression/remplacement de la stack.

🔥⭐⭐⭐⭐

---

## AWS Step Functions

Définition: Orchestre des workflows (machines à états) composés de tâches Lambda, ECS, SQS, SNS, API SDK AWS, avec gestion native des retries, du parallélisme, des conditions et des erreurs.

Types: Standard (workflows longs, jusqu'à 1 an, exécution exactly-once) vs Express (haute fréquence/courte durée, exécution at-least-once, moins cher).

Orchestrer des workflows multi-services sans code de glue.

🔥⭐⭐⭐

---

# Partie 11 - Analytique & Découplage

## SQS vs SNS vs EventBridge vs Kinesis

- Amazon SQS : file d'attente (queue), un message consommé par UN seul consommateur (point-à-point), pour découpler producteurs/consommateurs (FIFO ou Standard).
- Amazon SNS : pub/sub, un message envoyé à PLUSIEURS abonnés (SQS, Lambda, HTTP, email, SMS) simultanément ("fan-out").
- Amazon EventBridge : bus d'événements avec routage basé sur des règles/patterns de contenu, intégration avec de nombreuses sources SaaS et services AWS, idéal pour des architectures événementielles découplées.
- Amazon Kinesis Data Streams : ingestion et traitement de flux de données en temps réel à très grande échelle, plusieurs consommateurs peuvent lire le même flux indépendamment (rétention configurable), contrairement à SQS.

Piège: "Fan-out" vers plusieurs systèmes -> SNS (ou EventBridge pour du routage conditionnel riche) ; plusieurs lecteurs indépendants du même flux avec replay -> Kinesis.

🔥⭐⭐⭐⭐

---

## AWS Lambda - points avancés pour l'examen

- Concurrency : Reserved Concurrency (limite/garantie un nombre d'exécutions simultanées pour une fonction) vs Provisioned Concurrency (pré-initialise des environnements d'exécution pour éliminer les cold starts).
- Lambda dans un VPC : ajoute une latence (attachement ENI), nécessite des subnets privés + NAT Gateway pour accéder à internet/services publics.
- Lambda Layers : partage de code/dépendances communes entre plusieurs fonctions.
- Destinations : envoi du résultat (succès/échec) d'une invocation asynchrone vers SQS/SNS/EventBridge/autre Lambda, sans code custom.
- Limites : timeout max 15 min, payload sync 6 Mo / async 256 Ko.

🔥⭐⭐⭐

---

## Amazon API Gateway

Définition: Service managé pour créer, publier et sécuriser des API (REST, HTTP, WebSocket) servant de point d'entrée vers des backends (Lambda, services AWS, endpoints HTTP privés via VPC Link).

Points clés:
- REST API (fonctionnalités complètes : caching, validation de requêtes, transformation) vs HTTP API (plus léger, moins cher, plus rapide - cas d'usage simple proxy vers Lambda/HTTP).
- WebSocket API : communication bidirectionnelle temps réel (chat, dashboards live).
- Autorisation : IAM, Cognito User Pools Authorizer, ou Lambda Authorizer (custom, ex : validation JWT/OAuth tiers).
- Throttling : limites de débit (rate/burst) au niveau compte, API, ou par clé API (usage plans).
- VPC Link : permet à API Gateway d'exposer des ressources privées (NLB) dans un VPC sans les rendre publiques.
- Stages + Canary release : déploiements progressifs avec bascule de pourcentage de trafic.

🔥⭐⭐⭐

---

## AWS AppSync & AWS AppFlow

- AWS AppSync : service GraphQL managé, agrège plusieurs sources de données (DynamoDB, Lambda, RDS via Aurora Data API, endpoints HTTP) derrière un schéma unique, supporte les abonnements temps réel (subscriptions) et la synchronisation offline pour apps mobiles.
- AWS AppFlow : transfère des données de façon sécurisée et managée entre applications SaaS (Salesforce, ServiceNow, SAP, Slack...) et des services AWS (S3, Redshift) sans écrire de code d'intégration, avec transformation/filtrage des données en transit.

🔥⭐⭐

---

# Partie 12 - Excellence opérationnelle & Observabilité

## Amazon CloudWatch - fonctionnalités avancées

Définition: Service de monitoring central (métriques, logs, alarmes, dashboards) pour les ressources AWS et applications custom.

Points clés:
- Composite Alarms : combinent plusieurs alarmes via des opérateurs logiques (AND/OR) pour réduire le bruit d'alertes (ex : alerter seulement si CPU élevé ET latence élevée simultanément).
- Anomaly Detection : utilise le ML pour établir une "bande" de valeurs attendues pour une métrique et alerter en cas de déviation, sans seuil fixe.
- CloudWatch Logs Insights : requêtes ad-hoc sur les logs pour analyse/debug rapide.
- Contributor Insights : identifie les "top contributeurs" (ex : IP, user-agent) générant le plus de trafic/erreurs à partir de logs.
- Metric Filters : extrait des métriques numériques depuis des logs (ex : compter les occurrences "ERROR").
- CloudWatch Synthetics : "canaries" (scripts) qui testent en continu la disponibilité/performance d'endpoints depuis l'extérieur.
- CloudWatch Alarms -> EventBridge/SNS/Auto Scaling : déclenchent des actions automatisées (remédiation, scaling, notification).

🔥⭐⭐⭐⭐

---

## AWS X-Ray

Définition: Service de traçage distribué qui permet de visualiser le parcours d'une requête à travers les différents composants d'une application (microservices, Lambda, API Gateway, ECS...), pour identifier les goulots d'étranglement et erreurs.

Points clés:
- Trace : enregistre le chemin complet d'une requête de bout en bout (service map visuelle).
- Segments / Subsegments : chaque composant ajoute un segment au trace.
- X-Ray SDK / Daemon : instrumente le code (SDK) et envoie les données vers le service X-Ray (via un daemon agent).
- Sampling rules : configure quel pourcentage de requêtes est tracé (pour limiter coût/volume).

Debug de performance dans une architecture microservices/serverless.

🔥⭐⭐⭐

---

## AWS CloudTrail

Définition: Enregistre l'historique de tous les appels API effectués sur le compte AWS (qui a fait quoi, quand, depuis où), essentiel pour l'audit, la sécurité et la conformité.

Points clés:
- Management Events (par défaut, actions de plan de contrôle) vs Data Events (actions sur les données, ex : GetObject S3, Lambda Invoke - volumineux, désactivés par défaut, coût supplémentaire).
- CloudTrail Insights : détecte automatiquement une activité API inhabituelle (ex : pic soudain d'appels `RunInstances`).
- Organization Trail : un seul trail enregistrant les événements de tous les comptes membres d'une Organization, livré dans un bucket S3 centralisé.
- Logs immuables : envoyer vers un bucket S3 avec Object Lock + un compte de log centralisé séparé (sécurité/conformité).
- CloudTrail vs Config : CloudTrail = QUI a fait QUOI (audit d'API) ; Config = QUEL ÉTAT/configuration une ressource avait à un instant T (conformité).

🔥⭐⭐⭐⭐

---

## AWS Systems Manager (SSM) - suite d'outils

Définition: Suite d'outils opérationnels pour gérer à grande échelle des instances EC2 et ressources on-premise (via SSM Agent).

Points clés:
- Session Manager : accès shell sécurisé aux instances sans SSH/RDP, sans bastion host, sans ouvrir de ports entrants (logs des sessions vers CloudWatch/S3 pour audit).
- Run Command : exécute des commandes/scripts à grande échelle sur un groupe d'instances sans SSH.
- Patch Manager : automatise le déploiement de patchs OS/applicatifs selon des "patch baselines" et fenêtres de maintenance.
- State Manager : maintient une configuration souhaitée (ex : agent antivirus installé) de façon continue.
- Automation (Runbooks) : exécute des workflows opérationnels (ex : créer un AMI, redémarrer une instance) - utilisé pour la remédiation automatique des Config Rules.
- Parameter Store : (déjà couvert en Partie 6) stockage de configuration/secrets.
- Fleet Manager : interface graphique pour gérer un parc d'instances.

Piège: Besoin d'un accès "SSH-like" sécurisé sans gérer de bastion/clés/ports ouverts -> Session Manager.

🔥⭐⭐⭐⭐

---

## AWS Well-Architected Tool & Framework

Définition: Outil gratuit qui guide la revue d'une charge de travail selon les 6 piliers du Well-Architected Framework, en posant des questions et en produisant un plan d'amélioration.

Points clés:
- 6 piliers : Excellence opérationnelle, Sécurité, Fiabilité, Performance, Optimisation des coûts, Durabilité (Sustainability).
- Lenses : ensembles de questions spécifiques à un domaine (ex : Serverless Lens, SaaS Lens, ML Lens).
- Génère un rapport de risques (High/Medium) avec recommandations priorisées.

Auto-évaluation structurée d'une architecture par rapport aux best practices AWS.

🔥⭐⭐
