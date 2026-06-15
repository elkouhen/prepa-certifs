# GCP Professional Cloud Architect - Ultimate Cheat Sheet

Légende: 🔥⭐ = fréquence/importance estimée à l'examen (1 à 5 étoiles).
Comme tu connais AWS, chaque section indique l'équivalence AWS approximative (≈) - utile pour mapper rapidement les concepts, mais attention aux différences de détail (souvent le piège de l'examen).

---

# Partie 1 - Hiérarchie des ressources & IAM

## Hiérarchie des ressources GCP

Définition: Organisation -> Folders (dossiers, optionnels, imbricables) -> Projects -> Resources. Les politiques IAM et Org Policies s'appliquent par héritage descendant (un projet hérite des policies de ses folders et de l'organisation).

Points clés:
- Project : unité de base de facturation, quotas et regroupement de ressources. Possède un Project ID (unique globalement, immuable), un Project Name et un Project Number.
- Folders : permettent de regrouper des projets par équipe/environnement/BU et d'appliquer des policies différenciées.
- Organization : nœud racine, lié à un domaine Google Workspace/Cloud Identity.

≈ AWS Organizations (Org -> OU -> Account), mais en GCP les "comptes" sont remplacés par des "projects" qui sont plus légers à créer (souvent 1 projet par environnement/service).

🔥⭐⭐⭐⭐⭐

---

## IAM - rôles et bindings

Définition: IAM GCP attribue des "rôles" (ensembles de permissions) à des "membres" (utilisateurs, groupes, service accounts, domaines) sur une ressource (politique = liste de bindings membre<->rôle).

Types de rôles:
- Basic roles (Owner, Editor, Viewer) : très larges, à éviter en prod (legacy).
- Predefined roles : granulaires, gérés par Google, alignés sur un service (ex: roles/storage.objectViewer).
- Custom roles : permissions sur mesure assemblées par l'utilisateur, au niveau projet ou organisation.

Piège: IAM est additif uniquement (pas de "deny" explicite par défaut) - pour bloquer explicitement, utiliser les IAM Deny Policies ou les Org Policies.

≈ AWS IAM Policies (mais sans policy "resource-based" généralisée comme S3 bucket policy - GCP utilise des bindings IAM directement sur la ressource).

🔥⭐⭐⭐⭐⭐

---

## Service Accounts

Définition: Identité non-humaine utilisée par les applications/VM pour s'authentifier aux API GCP. Chaque ressource (VM, fonction...) peut "agir en tant que" (actAs) un service account, avec les permissions associées.

Points clés:
- Compte de service par défaut (Compute Engine default SA) : a souvent le rôle Editor par défaut -> mauvaise pratique, créer des SA dédiés avec permissions minimales.
- Workload Identity Federation : permet à des identités externes (AWS, Azure, on-prem via OIDC) d'assumer un rôle GCP sans clé JSON statique - équivalent du AWS IAM Role assumable via OIDC.
- Impersonation : un utilisateur/SA peut "endosser" temporairement un autre SA (rôle iam.serviceAccountTokenCreator) sans partager de clés.
- Clés de SA (JSON) : à éviter au maximum (risque de fuite), préférer Workload Identity / Attached Service Accounts.

≈ AWS IAM Role (assumé par un service) + Instance Profile.

🔥⭐⭐⭐⭐⭐

---

## Organization Policies

Définition: Contraintes (restrictions) appliquées au niveau organisation/folder/projet, qui limitent ce qui PEUT être configuré (ex: interdire la création d'IP externes, restreindre les régions autorisées, désactiver la création de clés de service account).

≈ AWS SCP (Service Control Policies) - même logique de "garde-fou" descendant dans la hiérarchie.

🔥⭐⭐⭐⭐

---

# Partie 2 - Networking

## VPC GCP - particularités

Définition: Contrairement à AWS, un VPC GCP est une ressource GLOBALE (pas liée à une région) ; les subnets, eux, sont régionaux (et s'étendent sur toutes les zones de la région).

Points clés:
- Auto mode VPC : crée automatiquement un subnet par région (déconseillé en prod, peu de contrôle).
- Custom mode VPC : l'utilisateur définit ses subnets région par région (best practice).
- Firewall rules : appliquées au niveau du VPC (pas du subnet), basées sur des tags réseau ou service accounts, avec priorité et direction (ingress/egress). Règle implicite "deny all ingress" et "allow all egress" par défaut.
- Routes : également globales au VPC.

Piège: un VPC GCP peut couvrir plusieurs régions nativement sans peering ni Transit Gateway (contrairement à AWS où un VPC est régional).

≈ AWS VPC, mais GCP VPC = global, AWS VPC = régional.

🔥⭐⭐⭐⭐⭐

---

## Shared VPC vs VPC Peering vs Network Connectivity Center

- Shared VPC : un projet "host" possède le VPC et partage des subnets avec des projets "service" (les ressources des projets service utilisent le réseau du host, mais restent facturées/gérées séparément). Permet une gestion réseau centralisée tout en gardant l'isolation des projets applicatifs.
- VPC Network Peering : connexion privée non-transitive entre 2 VPC (potentiellement de projets/organisations différents), pas de chevauchement de CIDR autorisé.
- Network Connectivity Center : hub central pour interconnecter des réseaux (VPC, on-prem via VPN/Interconnect) de façon transitive.

≈ Shared VPC = AWS RAM (partage de subnets) ; Peering = AWS VPC Peering (même limitation de non-transitivité) ; Network Connectivity Center ≈ AWS Transit Gateway.

🔥⭐⭐⭐⭐⭐

---

## Cloud Interconnect & Cloud VPN

- Dedicated Interconnect : connexion physique directe entre le réseau on-prem et le réseau GCP (≈ AWS Direct Connect dédié), nécessite une présence dans un point de colocation Google.
- Partner Interconnect : connexion via un fournisseur partenaire, pour des débits plus faibles ou sans présence en colocation (≈ AWS DX via partenaire).
- Cloud VPN : tunnel IPSec chiffré via internet. HA VPN (2 interfaces, SLA 99.99%) recommandé vs Classic VPN (déprécié).
- Cross-Cloud Interconnect : connectivité dédiée directe entre GCP et d'autres clouds (AWS, Azure...).

≈ AWS Direct Connect (Dedicated/Partner Interconnect) + Site-to-Site VPN (Cloud VPN).

🔥⭐⭐⭐⭐

---

## Cloud Load Balancing

Définition: Service de load balancing GLOBAL ou régional, basé sur l'Andromeda/Maglev (réseau logiciel défini de Google), avec une seule IP anycast pour distribuer le trafic mondialement.

Types principaux:
- Global external Application Load Balancer (HTTP/S, L7) : distribue le trafic mondialement vers le backend le plus proche, intégré à Cloud CDN et Cloud Armor.
- Global external proxy Network Load Balancer (TCP/SSL, L4) : pour du trafic non-HTTP nécessitant une portée globale.
- Regional external/internal Application Load Balancer : L7, régional.
- Internal passthrough Network Load Balancer : L4, interne, conserve l'IP source (passthrough = pas de proxy).

Piège: 
- "Global" = une IP anycast unique servant plusieurs régions (≈ proche de AWS Global Accelerator + ALB combinés).
- "Passthrough" (Network LB) vs "Proxy" (Application LB / proxy Network LB) - passthrough préserve l'adresse IP source du client jusqu'au backend.

≈ ALB/NLB AWS, mais le LB "Global HTTP(S)" de GCP n'a pas d'équivalent direct unique côté AWS (combine ALB multi-région + Global Accelerator + CloudFront pour le edge).

🔥⭐⭐⭐⭐⭐

---

## Cloud CDN, Cloud DNS, Cloud NAT, Cloud Armor

- Cloud CDN : cache de contenu à la périphérie, s'active simplement sur un backend de Global external Application LB (≈ CloudFront, mais couplé au LB plutôt qu'indépendant).
- Cloud DNS : service DNS managé, supporte les zones publiques et privées (≈ Route 53, sans toutes les routing policies avancées - pas de "latency-based" ou "geoproximity" natifs identiques).
- Cloud NAT : NAT managé régional pour permettre à des VM sans IP externe d'accéder à internet en sortie uniquement (≈ AWS NAT Gateway).
- Cloud Armor : WAF + protection DDoS au niveau des Global Load Balancers, règles basées sur IP/géolocalisation/L7 (≈ AWS WAF + Shield).

🔥⭐⭐⭐

---

## Private Google Access & VPC Service Controls

- Private Google Access : permet à des VM sans IP externe d'atteindre les API Google (Cloud Storage, BigQuery...) via leur IP interne/Private Service Connect.
- Private Service Connect : connectivité privée vers des services gérés (Google ou tiers) sans passer par internet (≈ AWS PrivateLink).
- VPC Service Controls : crée un "périmètre de sécurité" autour de ressources (projets) pour empêcher l'exfiltration de données même avec des credentials valides (ex: empêcher qu'un bucket GCS soit accessible depuis l'extérieur du périmètre, même par un compte autorisé).

Piège: VPC Service Controls n'a pas d'équivalent direct simple en AWS - c'est une couche de défense contre l'exfiltration de données indépendante d'IAM.

🔥⭐⭐⭐⭐

---

## Private Services Access (PSA)

Définition: Mécanisme qui permet à des services managés Google (Cloud SQL, Memorystore, AlloyDB, Vertex AI...) hébergés dans un VPC propre à Google ("tenant project") d'être joints en IP privée depuis votre VPC, via du VPC Peering automatisé.

Fonctionnement:
1. Vous réservez une plage d'adresses IP internes ("allocated range") dans votre VPC, dédiée aux services Google.
2. Une connexion de service privé (`gcloud services vpc-peerings connect`) établit un peering automatique entre votre VPC et le VPC du service managé.
3. L'instance Cloud SQL (par ex.) reçoit une IP privée dans cette plage, joignable depuis votre VPC sans IP publique ni Cloud SQL Auth Proxy.

Points clés / pièges:
- PSA repose sur du VPC Peering -> donc NON transitif : si votre VPC est connecté à un autre VPC (peering ou Shared VPC), ce 2e VPC NE PEUT PAS atteindre le service via cette connexion PSA (sauf "Export/Import custom routes" activé sur le peering, avec prudence).
- La plage IP réservée doit être suffisamment grande et ne pas chevaucher vos subnets existants.
- Une seule connexion PSA par VPC, mais elle peut servir plusieurs services/instances (toutes partagent la même plage allouée).
- À ne pas confondre avec Private Service Connect (PSC) : PSA = peering automatisé pour les services Google "first-party" (Cloud SQL, etc.), PSC = endpoints privés (type PrivateLink) pour exposer/consommer des services (y compris tiers/cross-organisation) sans peering ni problème de chevauchement de CIDR.

Mnémo: PSA = "peering pour parler à un service managé Google en IP privée" ; PSC = "PrivateLink-like, plus flexible, pas de peering".

≈ AWS RDS dans un VPC géré par AWS mais joignable en privé... en réalité côté AWS, RDS est directement DANS votre VPC (pas de peering nécessaire) - PSA est donc une spécificité GCP liée au fait que les services managés vivent dans un VPC séparé appartenant à Google.

🔥⭐⭐⭐⭐⭐

---

## Serverless VPC Access

Définition: Connecteur qui permet à des environnements serverless (Cloud Run, Cloud Functions, App Engine Standard) d'envoyer du trafic vers des ressources internes d'un VPC (ex: Cloud SQL via IP privée, VM internes, Memorystore) sans passer par une IP publique.

Piège: sans ce connecteur, un service serverless ne peut atteindre une ressource qu'en IP publique (ou via PSA/PSC pour les services managés qui le supportent directement).

≈ Lambda déployé "dans un VPC" (attachement ENI) côté AWS - en GCP, c'est un connecteur dédié séparé plutôt qu'une config "in-VPC" du service lui-même.

🔥⭐⭐⭐

---

## Network Tiers (Premium vs Standard)

Définition: Niveau de réseau utilisé pour le trafic sortant vers internet et les IP externes:

- Premium Tier (par défaut) : trafic acheminé sur le réseau privé mondial de Google (backbone) le plus longtemps possible -> meilleure performance/latence, légèrement plus cher.
- Standard Tier : trafic acheminé comme un ISP classique (transite par l'internet public plus tôt) -> moins cher, performance/SLA moindres, et certaines fonctionnalités indisponibles (ex: pas de Global Load Balancing en Standard Tier - LB régional uniquement).

🔥⭐⭐

---

## Firewall : règles vs Hierarchical Firewall Policies vs Network Firewall Policies

- Règles de pare-feu VPC (legacy) : attachées directement à un VPC, basées sur tags réseau ou comptes de service, priorité numérique (0-65535, plus petit = priorité plus haute).
- Network Firewall Policies (régionales ou globales) : ressource de policy associable à un ou plusieurs VPC, règles organisées et réutilisables, supportent FQDN et Secure Tags.
- Hierarchical Firewall Policies : appliquées au niveau Organization ou Folder, héritées par tous les VPC des projets sous-jacents -> permettent d'imposer des règles de sécurité globales (ex: bloquer certains ports) que les équipes projet ne peuvent pas outrepasser.

≈ Hierarchical Firewall Policies n'a pas d'équivalent direct simple côté AWS (proche de l'usage de Network Firewall centralisé via Transit Gateway, mais appliqué au niveau organisationnel plutôt que réseau).

🔥⭐⭐⭐

---

## VPC Flow Logs & Firewall Rules Logging

- VPC Flow Logs : capture les informations sur le trafic IP entrant/sortant des VM (échantillonnable), exportable vers Cloud Logging/BigQuery/Pub-Sub pour analyse réseau, troubleshooting de connectivité, audit de sécurité. ≈ AWS VPC Flow Logs.
- Firewall Rules Logging : journalise les connexions évaluées par une règle de pare-feu spécifique (allow/deny), utile pour auditer quelles règles matchent réellement le trafic.

🔥⭐⭐

---

## Cloud DNS avancé : forwarding, peering, split-horizon

- DNS Forwarding : transfère la résolution de certains domaines vers des serveurs DNS on-prem (et inversement, via des "inbound/outbound server policies") - essentiel pour l'intégration hybride.
- DNS Peering : permet à un VPC de résoudre les enregistrements d'une zone privée appartenant à un AUTRE VPC/projet (ex: un Shared VPC host gère les zones, les projets service y font du peering DNS).
- Split-Horizon DNS : même nom de domaine résolu différemment selon que la requête vient d'une zone privée (interne) ou publique (internet), via deux zones Cloud DNS distinctes portant le même nom.

≈ Route 53 Resolver (forwarding rules + association VPC) et Route 53 Private Hosted Zones partagées entre VPC.

🔥⭐⭐⭐

---

# Partie 3 - Compute

## Compute Engine - points avancés

- Machine types : familles générales (E2, N2, N2D, C3), optimisées calcul (C2), mémoire (M-series), GPU (A2/G2).
- Preemptible VM / Spot VM : instances jusqu'à 91% moins chères, pouvant être arrêtées par Google (Spot = pas de limite de 24h contrairement aux anciennes preemptible). ≈ AWS Spot Instances.
- Managed Instance Groups (MIG) : groupe de VM identiques (depuis un template) avec autoscaling, auto-healing (health checks + recréation), mises à jour rolling/canary. ≈ AWS Auto Scaling Group.
- Sole-Tenant Nodes : matériel physique dédié pour des contraintes de licence/conformité (≈ AWS Dedicated Hosts).
- Custom Machine Types : choix indépendant du nombre de vCPU et de la quantité de RAM.
- Committed Use Discounts (CUD) : engagement 1 ou 3 ans sur de la capacité de calcul/mémoire pour réduction tarifaire (≈ AWS Savings Plans/RI).

🔥⭐⭐⭐⭐

---

## GKE (Google Kubernetes Engine)

Définition: Kubernetes managé. Pour l'examen, retenir surtout:

- Autopilot vs Standard : Autopilot = mode entièrement géré (Google gère les nodes, sécurité, scaling - facturation par pod), Standard = l'utilisateur gère les node pools.
- Node pools : groupes de nodes avec une config commune (machine type, taille...), un cluster peut avoir plusieurs node pools (ex: un pool GPU, un pool spot).
- Workload Identity : permet à un Pod d'utiliser un Service Account GCP via un Kubernetes Service Account (sans clé JSON) - équivalent IRSA AWS (IAM Roles for Service Accounts).
- Cluster régional vs zonal : un cluster régional réplique le control plane sur plusieurs zones pour la haute disponibilité.

≈ AWS EKS, avec Autopilot ≈ Fargate pour EKS (sans gestion de node).

🔥⭐⭐⭐⭐

---

## Cloud Run, App Engine, Cloud Functions

- Cloud Run : exécute des conteneurs serverless (scale-to-zero, scaling automatique selon les requêtes), facturé à l'usage réel (CPU/mémoire pendant le traitement de requêtes). ≈ AWS Fargate (serverless containers) + App Runner.
- App Engine : PaaS historique, Standard (runtimes managés, scale-to-zero rapide, sandboxé) vs Flexible (conteneurs Docker sur VM managées, scaling plus lent). ≈ AWS Elastic Beanstalk.
- Cloud Functions (2nd gen) : FaaS événementiel, basé sur Cloud Run en interne, déclenché par HTTP, Pub/Sub, Cloud Storage, Firestore... ≈ AWS Lambda.

Piège: Cloud Functions 2nd gen tourne en fait sur l'infrastructure Cloud Run (limites/timeouts alignés).

🔥⭐⭐⭐⭐

---

## GKE Networking (souvent un piège de scénario)

- VPC-native cluster (alias IP) vs routes-based (legacy) : VPC-native utilise des "alias IP ranges" pour les Pods et Services, ce qui les rend routables nativement dans le VPC (requis pour Private Service Connect, VPC Peering avec les Pods, etc.). À privilégier systématiquement (par défaut depuis un moment).
- Private clusters : les nodes n'ont que des IP internes. Le control plane peut être accessible publiquement ou en privé (private endpoint), avec des "control plane authorized networks" pour restreindre l'accès `kubectl`.
- GKE Ingress (Cloud Load Balancer) : un objet Ingress GKE provisionne automatiquement un Global external Application Load Balancer (≈ AWS Load Balancer Controller pour EKS, mais natif/automatique côté GKE).
- Network Policies : règles de filtrage L3/L4 entre Pods (basées sur Calico/eBPF selon le mode), pour micro-segmentation interne au cluster.
- Multi-cluster / Multi-Cluster Ingress (MCI) & Multi-Cluster Services (MCS) : exposent des services à travers plusieurs clusters/régions pour HA et déploiements globaux.

🔥⭐⭐⭐

---

## Compute Engine - sécurité & matériel spécialisé

- Shielded VM : protection contre le rootkit/bootkit (Secure Boot, vTPM, Integrity Monitoring) - activable par défaut sur la plupart des images publiques.
- Confidential VM : chiffre la mémoire en cours d'utilisation (memory encryption via AMD SEV), pour des données sensibles même pendant le traitement.
- GPUs & TPUs : GPU attachables à des VM standards (ML, rendu, HPC) ; TPU = matériel propriétaire Google optimisé pour l'entraînement/inférence de modèles ML à grande échelle (≈ pas d'équivalent direct AWS - proche d'Inferentia/Trainium mais écosystème TensorFlow/JAX natif).
- OS Login : gère l'accès SSH aux VM via IAM (au lieu de gérer des clés SSH par projet/instance) - active l'audit centralisé des connexions.

🔥⭐⭐

---

# Partie 4 - Stockage & Bases de données

## Cloud Storage - classes et fonctionnalités

- Classes : Standard, Nearline (accès <1x/mois), Coldline (<1x/trimestre), Archive (<1x/an, restitution la plus lente).
- Object Lifecycle Management : règles de transition/suppression automatique entre classes.
- Autoclass : transition automatique des objets entre classes selon le pattern d'accès, sans définir de règles (≈ S3 Intelligent-Tiering).
- Emplacement du bucket : Region, Dual-region, Multi-region (impact disponibilité/latence/coût/résidence des données).
- Object Versioning, retention policies (Bucket Lock = équivalent S3 Object Lock pour conformité WORM).
- Signed URLs : accès temporaire (≈ S3 Pre-signed URLs).

≈ Amazon S3, avec une grille tarifaire/nommage de classes différente (attention aux correspondances : Coldline ≈ Glacier Flexible, Archive ≈ Glacier Deep Archive).

🔥⭐⭐⭐⭐⭐

---

## Choisir la bonne base de données GCP

Piège majeur de l'examen - bien différencier:

- Cloud SQL : MySQL/PostgreSQL/SQL Server managés, relationnel, scaling vertical, réplicas en lecture régionaux/cross-région, HA via réplication synchrone (≈ Amazon RDS).
- Cloud Spanner : base relationnelle distribuée mondialement, scaling horizontal ILLIMITÉ avec cohérence forte (CP du théorème CAP) et SQL complet - idéal pour des charges transactionnelles globales nécessitant cohérence forte (≈ pas d'équivalent direct AWS ; un peu comme Aurora Global mais avec cohérence forte + scaling horizontal natif).
- Firestore : base NoSQL documentaire, mode "Native" (temps réel, mobile/web) - ≈ Amazon DynamoDB + sync temps réel type AppSync.
- Bigtable : base NoSQL grande échelle (pétaoctets), faible latence, idéale pour séries temporelles/IoT/analytique à très haut débit (≈ Amazon DynamoDB pour la volumétrie, mais modèle "wide-column" type HBase/Cassandra).
- BigQuery : data warehouse serverless, analytique SQL sur pétaoctets, séparation stockage/calcul, facturation par requête (scan) ou par capacité réservée (≈ Amazon Redshift Serverless / Redshift Spectrum).
- Memorystore : Redis/Memcached managés (≈ ElastiCache).
- AlloyDB : PostgreSQL haute performance compatible, optimisé pour charges analytiques+transactionnelles (HTAP) (≈ Aurora PostgreSQL).

Mnémo: "Spanner = SQL global + scale horizontal + cohérence forte", "Bigtable = NoSQL massif/IoT", "Firestore = NoSQL appli mobile/web", "BigQuery = analytique SQL serverless".

🔥⭐⭐⭐⭐⭐

---

## Cloud SQL - HA, réplicas et connectivité

- HA (regional) : instance primaire + standby synchrone dans une autre zone de la même région, failover automatique (≈ RDS Multi-AZ). Le standby n'est pas utilisable en lecture.
- Read Replicas : réplication asynchrone, même région ou cross-région (pour DR/latence de lecture), peuvent être promues en instance autonome (≈ RDS Read Replicas).
- Connectivité : Private IP (via PSA, recommandé), Public IP (avec autorisations réseau ou Cloud SQL Auth Proxy qui gère le chiffrement/IAM sans configurer de SSL manuellement).
- Cloud SQL Auth Proxy / Connectors : gèrent l'authentification IAM et le chiffrement TLS vers Cloud SQL sans gérer de certificats manuellement, utile depuis Cloud Run/GKE/Compute.
- Point-in-time recovery (PITR) : restauration à un instant T via les logs binaires (nécessite leur activation).

🔥⭐⭐⭐

---

## Cloud Spanner - configuration & scaling

- Instance configuration : Regional (1 région, 3 réplicas dans des zones différentes) vs Multi-region (réplicas répartis entre plusieurs régions pour une dispo encore plus forte et lecture à faible latence partout, avec un coût plus élevé).
- Capacité : exprimée en nodes (1000 processing units) ou en granular processing units (100 PU minimum) - scaling horizontal en ajoutant des nodes/PU, sans downtime.
- Interleaved tables : modélisation de relations parent-enfant colocalisées physiquement pour optimiser les jointures à grande échelle.
- Piège: Spanner garantit une cohérence forte ET un scaling horizontal quasi illimité avec SQL - une combinaison qu'aucun service AWS natif n'offre directement (Aurora = pas de scale horizontal illimité en écriture ; DynamoDB = pas de SQL/cohérence forte multi-item par défaut).

🔥⭐⭐⭐⭐

---

## BigQuery - architecture & optimisation

- Séparation stockage/calcul : les données sont stockées dans Colossus (stockage distribué Google), le calcul (slots) est alloué dynamiquement par requête.
- Pricing : On-demand (facturé au volume de données scannées par requête) vs Capacity-based / Editions (slots réservés, coût prévisible pour charge constante/élevée).
- Partitioning & Clustering : le partitionnement (par date/entier) réduit le volume scanné en éliminant des partitions non pertinentes ; le clustering trie physiquement les données dans chaque partition pour accélérer les filtres/agrégations - les deux réduisent fortement coût ET latence.
- Materialized Views : vues pré-calculées et rafraîchies automatiquement, accélèrent les requêtes répétitives sans re-scanner toute la table.
- BI Engine : couche d'accélération en mémoire pour des dashboards (Looker Studio, etc.) avec une latence sub-seconde.
- Authorized Views / Datasets : permet de partager le résultat d'une requête (vue) sans donner accès aux tables sous-jacentes - utile pour le partage de données inter-équipes/projets en respectant le moindre privilège.
- BigQuery Omni : exécute des requêtes BigQuery sur des données stockées dans S3/Azure Blob sans les déplacer (analytique multi-cloud).

Piège: pour réduire les coûts d'une charge BigQuery imprévisible avec beaucoup de petites requêtes -> on-demand + partitioning/clustering ; pour une charge stable et prévisible à fort volume -> capacity-based (slots réservés/Editions).

🔥⭐⭐⭐⭐

---

## Filestore & Persistent Disk

- Persistent Disk : stockage bloc attaché aux VM (zonal ou régional pour la réplication synchrone entre 2 zones). ≈ AWS EBS (régional ≈ EBS multi-AZ qui n'existe pas nativement côté AWS - PD régional est en fait une spécificité GCP).
- Filestore : système de fichiers NFS managé (≈ Amazon EFS).
- Hyperdisk : nouvelle génération de disques avec IOPS/débit configurables indépendamment de la taille (≈ EBS io2 Block Express / gp3).

🔥⭐⭐

---

# Partie 5 - Migration & transfert de données

## Outils de migration GCP

- Migrate to Virtual Machines (anciennement Migrate for Compute Engine) : migre des VM (VMware, AWS EC2, Azure) vers Compute Engine avec une réplication continue, similaire à un "lift and shift" automatisé. ≈ AWS Application Migration Service (MGN).
- Database Migration Service (DMS) : migration de MySQL/PostgreSQL/SQL Server vers Cloud SQL/AlloyDB avec réplication continue (CDC). ≈ AWS DMS.
- Storage Transfer Service : transfert/synchronisation de données depuis S3, Azure Blob, HTTP/HTTPS ou un autre bucket GCS vers Cloud Storage, sur planning récurrent. ≈ AWS DataSync.
- Transfer Appliance : appliance physique pour transférer de très gros volumes de données quand la bande passante est insuffisante. ≈ AWS Snowball.
- BigQuery Data Transfer Service : automatise l'ingestion récurrente de données depuis des SaaS (Google Ads, YouTube...) ou d'autres entrepôts vers BigQuery.

🔥⭐⭐⭐

---

# Partie 6 - Sécurité

## Cloud KMS & Secret Manager

- Cloud KMS : gestion de clés de chiffrement (symétriques/asymétriques), CMEK (Customer-Managed Encryption Keys) pour chiffrer les données stockées (GCS, BigQuery, disques) avec ses propres clés, CSEK (Customer-Supplied Encryption Keys) pour fournir directement le matériel de clé. ≈ AWS KMS.
- Secret Manager : stockage versionné de secrets (API keys, mots de passe) avec contrôle d'accès IAM et rotation (via Cloud Functions/Scheduler, pas nativement automatique comme AWS Secrets Manager pour certaines bases). ≈ AWS Secrets Manager / Parameter Store.

🔥⭐⭐⭐

---

## Identity-Aware Proxy (IAP) & Security Command Center

- Identity-Aware Proxy (IAP) : permet d'accéder à des applications/VM (SSH/RDP via IAP TCP forwarding, ou apps web) sans VPN, en authentifiant chaque requête via IAM/Identity (BeyondCorp / Zero Trust). ≈ pas d'équivalent direct AWS unique - combine SSM Session Manager (accès sans VPN) + un reverse-proxy authentifiant type ALB+Cognito.
- Security Command Center (SCC) : plateforme centrale de gestion de la posture de sécurité (détection de menaces, vulnérabilités, conformité), niveaux Standard/Premium. ≈ AWS Security Hub + GuardDuty combinés.

🔥⭐⭐⭐

---

# Partie 7 - Fiabilité, observabilité & coûts

## Régions, zones et haute disponibilité

Définition: Une région GCP contient plusieurs zones (généralement 3+), chaque zone = un ou plusieurs datacenters avec alimentation/réseau indépendants. Pour la HA, répartir les ressources sur plusieurs zones (MIG multi-zone, Persistent Disk régional, Cloud SQL HA cross-zone) ; pour le DR, répartir sur plusieurs régions.

≈ Région AWS = Région GCP, Zone GCP = AZ AWS (mais le mapping de nommage n'est pas garanti identique entre comptes/projets).

🔥⭐⭐⭐

---

## Cloud Monitoring, Cloud Logging (anciennement Stackdriver)

- Cloud Monitoring : métriques, dashboards, alerting sur les ressources GCP et applications (via OpenTelemetry/agent Ops). ≈ Amazon CloudWatch (métriques + alarms).
- Cloud Logging : collecte centralisée de logs (audit logs, logs applicatifs, logs de plateforme), avec des "sinks" pour exporter vers BigQuery/GCS/Pub-Sub. ≈ CloudWatch Logs + CloudTrail (les Cloud Audit Logs incluent Admin Activity, Data Access, System Event, Policy Denied).

🔥⭐⭐⭐

---

## Gestion des coûts

- Billing Account : peut être lié à plusieurs projects, avec des Budgets & Alerts configurables par projet/dossier.
- Recommender : recommandations automatiques de rightsizing (VM sous-utilisées, disques inutilisés, droits IAM trop larges). ≈ AWS Compute Optimizer + Trusted Advisor.
- Committed Use Discounts (CUD) : remise sur engagement de ressources (spend-based ou resource-based), 1 ou 3 ans. ≈ Savings Plans / RI.
- Sustained Use Discounts : remise automatique (sans engagement) pour les VM qui tournent une grande partie du mois (spécificité GCP, pas d'équivalent AWS direct).

🔥⭐⭐⭐

---

# Partie 8 - Big Data & découplage (souvent dans les cas d'usage de l'examen)

## Pub/Sub, Dataflow, Dataproc

- Pub/Sub : messagerie asynchrone pub/sub globale et durable, "push" ou "pull", garantit at-least-once delivery. ≈ Amazon SNS (fan-out) + SQS combinés, à l'échelle globale.
- Dataflow : traitement de données serverless (batch et streaming) basé sur Apache Beam, autoscaling. ≈ pas d'équivalent direct unique AWS (proche de Kinesis Data Analytics / Glue pour Spark, mais unifié batch+streaming).
- Dataproc : clusters Hadoop/Spark managés, éphémères. ≈ Amazon EMR.

Mnémo: Pub/Sub = ingestion/découplage événementiel global, Dataflow = traitement (ETL) batch+streaming unifié, Dataproc = pour migrer des jobs Hadoop/Spark existants.

🔥⭐⭐⭐

---

## Cloud Composer & orchestration

- Cloud Composer : Apache Airflow managé, pour orchestrer des workflows complexes multi-services (ETL, ML pipelines) avec dépendances. ≈ Amazon MWAA (Managed Workflows for Apache Airflow).
- Cloud Tasks : exécution asynchrone et différée de tâches (avec contrôle de débit/retry) appelant typiquement un endpoint HTTP ou Cloud Function. ≈ Amazon SQS + Lambda (avec gestion fine du throttling).
- Cloud Scheduler : cron managé déclenchant des HTTP endpoints, Pub/Sub ou App Engine. ≈ Amazon EventBridge Scheduler.

🔥⭐⭐

---

# Partie 9 - CI/CD & Infrastructure as Code

## Cloud Build, Artifact Registry, Cloud Deploy

- Cloud Build : service CI/CD serverless qui exécute des étapes de build définies dans un fichier `cloudbuild.yaml` (build d'images Docker, tests, déploiement), déclenchable par des triggers (push GitHub/Cloud Source Repositories). ≈ AWS CodeBuild.
- Artifact Registry : registre managé pour images de conteneurs, packages (Maven, npm, Python...) - remplace Container Registry (déprécié). ≈ Amazon ECR + CodeArtifact.
- Cloud Deploy : service de delivery continue managé pour déployer vers GKE/Cloud Run avec des pipelines de promotion (dev -> staging -> prod) et des approbations manuelles. ≈ AWS CodeDeploy / CodePipeline.

🔥⭐⭐

---

## Infrastructure as Code

- Terraform : outil IaC multi-cloud le plus utilisé avec GCP (provider `google`), souvent la réponse attendue à l'examen pour "reproductible, versionné, multi-environnement".
- Deployment Manager : outil IaC natif GCP historique (YAML/Python), largement remplacé par Terraform dans les nouvelles architectures.
- Config Connector : permet de gérer des ressources GCP comme des objets Kubernetes (CRDs) via GKE - pour les équipes "GitOps" qui pilotent déjà leur infra via Kubernetes.

🔥⭐⭐

---

# Partie 10 - Hybride & Multi-cloud

## Anthos / GKE Enterprise

Définition: Plateforme permettant de gérer des clusters Kubernetes de façon cohérente sur GCP (GKE), on-premise (GKE on Bare Metal/VMware) et sur d'autres clouds (GKE on AWS/Azure), avec une gestion centralisée de la configuration (Config Sync/GitOps), de la sécurité (Policy Controller basé sur OPA/Gatekeeper) et du service mesh (Anthos Service Mesh / Istio).

Cas d'usage typique de l'examen: "l'entreprise veut une plateforme Kubernetes cohérente entre son datacenter et GCP, avec des policies de sécurité appliquées partout" -> Anthos / GKE Enterprise.

≈ pas d'équivalent direct unique côté AWS (proche d'EKS Anywhere + Systems Manager + Config combinés, mais Anthos est plus intégré et orienté Kubernetes/Istio).

🔥⭐⭐

---

# Partie 11 - Conformité, identité avancée & gestion organisationnelle

## Assured Workloads, Access Transparency, Access Approval

- Assured Workloads : applique automatiquement des contrôles de conformité (résidence des données, restrictions de personnel support, chiffrement) pour des régimes réglementaires spécifiques (FedRAMP, IL4, etc.) sur un dossier de projets.
- Access Transparency : logs montrant les actions effectuées par le personnel Google sur vos ressources/données (quand l'accès support est nécessaire).
- Access Approval : exige une approbation explicite du client avant que le personnel Google n'accède à ses données/configurations.

≈ Pas d'équivalent direct grand public côté AWS (proche d'AWS Artifact pour la conformité documentaire, mais ces contrôles GCP sont plus opérationnels/temps réel).

🔥⭐⭐

---

## Cloud Identity, Workforce Identity Federation vs Workload Identity Federation

- Cloud Identity : gère les identités des utilisateurs/groupes (annuaire) indépendamment de Google Workspace, base de l'IAM GCP pour les humains.
- Workforce Identity Federation : permet à des UTILISATEURS authentifiés via un IdP externe (Azure AD, Okta, SAML/OIDC) d'accéder à la console/API GCP sans compte Cloud Identity dédié. ≈ AWS IAM Identity Center fédéré avec un IdP externe.
- Workload Identity Federation : permet à des CHARGES DE TRAVAIL externes (autre cloud, on-prem, CI/CD comme GitHub Actions) d'obtenir des credentials GCP temporaires via OIDC, sans clé de service account statique. ≈ AWS IAM Role assumable via OIDC (ex: GitHub Actions -> AWS).
- Workload Identity (GKE) : cas particulier de Workload Identity Federation pour les Pods GKE (≈ IRSA AWS).

Piège: bien distinguer "Workforce" (humains, accès console) de "Workload" (charges de travail/applications, accès API).

🔥⭐⭐⭐

---

## Resource Manager : tags, labels et quotas

- Labels : paires clé/valeur sur les ressources pour le filtrage de coûts/inventaire (purement informatif).
- Tags (secure tags) : paires clé/valeur qui peuvent être référencées dans des Organization Policies et des règles de pare-feu pour appliquer des conditions (≈ AWS Resource Tags utilisés en conditions IAM/SCP).
- Quotas : limites par projet/région (ex: nombre d'IP, vCPU) - certaines ajustables via demande à Google, d'autres "system limits" non ajustables.
- Resource Hierarchy "liens" (asset.googleapis.com) / Cloud Asset Inventory : inventaire et historique de toutes les ressources et politiques IAM de l'organisation, exportable pour audit.

🔥⭐⭐

---

# Partie 12 - API Management

## Apigee & API Gateway

- Apigee : plateforme complète de gestion d'API (API management) pour exposer, sécuriser, monitorer et monétiser des API à grande échelle (quotas, analytics, transformation de payloads, developer portal). ≈ Amazon API Gateway + une partie de CloudFront/WAF, mais avec des fonctionnalités de gouvernance/monétisation bien plus poussées.
- API Gateway (Cloud Endpoints / API Gateway managé) : solution plus légère pour exposer des API serverless (Cloud Functions/Cloud Run) avec authentification et limitation de débit basique. ≈ Amazon API Gateway (cas d'usage simple).

Mnémo: petit besoin (exposer une API serverless avec clé API/auth) -> API Gateway ; besoin entreprise (monétisation, portail développeur, gouvernance multi-équipes) -> Apigee.

🔥⭐⭐

---

# Tableau récapitulatif - équivalences rapides AWS <-> GCP

| AWS | GCP |
|---|---|
| Organizations / OU / Account | Organization / Folder / Project |
| SCP | Organization Policies |
| IAM Role + Instance Profile | Service Account |
| VPC (régional) | VPC (global) + Subnets (régionaux) |
| Transit Gateway | Network Connectivity Center |
| PrivateLink | Private Service Connect |
| Direct Connect | Cloud Interconnect (Dedicated/Partner) |
| Site-to-Site VPN | Cloud VPN (HA VPN) |
| ALB / NLB + Global Accelerator + CloudFront | Cloud Load Balancing (Global external HTTP(S)) + Cloud CDN |
| NAT Gateway | Cloud NAT |
| WAF + Shield | Cloud Armor |
| EC2 ASG | Managed Instance Group (MIG) |
| EKS / Fargate | GKE Standard / GKE Autopilot |
| ECS Fargate / App Runner | Cloud Run |
| Elastic Beanstalk | App Engine |
| Lambda | Cloud Functions (2nd gen, sur Cloud Run) |
| S3 | Cloud Storage |
| EBS / EFS | Persistent Disk / Filestore |
| RDS | Cloud SQL |
| Aurora Global (proche) | Cloud Spanner (cohérence forte + scale horizontal) |
| DynamoDB | Firestore (appli) / Bigtable (volumétrie/IoT) |
| Redshift | BigQuery |
| ElastiCache | Memorystore |
| KMS | Cloud KMS |
| Secrets Manager / Parameter Store | Secret Manager |
| GuardDuty + Security Hub | Security Command Center |
| CloudWatch | Cloud Monitoring |
| CloudTrail | Cloud Audit Logs |
| MGN | Migrate to Virtual Machines |
| DMS | Database Migration Service |
| DataSync | Storage Transfer Service |
| Snowball | Transfer Appliance |
| SNS + SQS | Pub/Sub |
| Glue / Kinesis Data Analytics / EMR | Dataflow / Dataproc |
| Savings Plans / RI | Committed Use Discounts |
| Compute Optimizer / Trusted Advisor | Recommender |
| MWAA (Airflow) | Cloud Composer |
| EventBridge Scheduler | Cloud Scheduler |
| SQS + Lambda (throttling) | Cloud Tasks |
| CodeBuild | Cloud Build |
| ECR / CodeArtifact | Artifact Registry |
| CodeDeploy / CodePipeline | Cloud Deploy |
| RDS-in-VPC (direct) | Cloud SQL via Private Services Access (PSA) |
| Lambda in VPC (ENI) | Serverless VPC Access connector |
| IAM Role via OIDC (ex: GitHub Actions) | Workload Identity Federation |
| IAM Identity Center fédéré | Workforce Identity Federation |
| Resource Tags en condition IAM/SCP | Secure Tags |
| API Gateway | API Gateway (Cloud Endpoints) / Apigee (entreprise) |
| EKS Anywhere + Config + SSM (combiné) | Anthos / GKE Enterprise |
