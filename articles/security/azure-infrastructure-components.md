---
title: Composants et limites du système d’informations Azure
description: Cet article fournit une description générale de l’architecture de Microsoft Azure et des opérations de gestion associées.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101604"
---
# <a name="azure-information-system-components-and-boundaries"></a>Composants et limites du système d’informations Azure
Cet article fournit une description générale de l’architecture de Microsoft Azure et des opérations de gestion associées. L’environnement du système Azure se compose des réseaux suivants :

- Réseau de production Microsoft Azure (réseau Azure)
- Réseau d’entreprise Microsoft (réseau Corpnet)

Des équipes informatiques distinctes sont responsables des opérations et de la maintenance des réseaux Azure et CorpNet.

## <a name="azure-architecture"></a>Architecture Azure
Microsoft Azure est une plateforme et une infrastructure de cloud computing qui permet de générer, de déployer et de gérer des applications et des services à travers un réseau de centres de données gérés par Microsoft. Azure crée des machines virtuelles en fonction du nombre de ressources spécifiées par les clients. Ces machines virtuelles s’exécutent sur un hyperviseur Microsoft Azure conçu pour une utilisation dans le cloud et inaccessible au public.

Chaque nœud serveur physique Azure est doté d’un hyperviseur qui s’exécute directement sur le matériel. L’hyperviseur divise un nœud en un nombre variable de machines virtuelles invitées. Chaque nœud a également une machine virtuelle « racine » spéciale qui exécute le système d’exploitation hôte. Le Pare-feu Windows est activé sur chaque machine virtuelle. Les seuls ports ouverts et adressables, en interne ou en externe, sont ceux définis explicitement dans le fichier de définition de service configuré par le client. L’ensemble du trafic et des accès au disque et au réseau est régi par l’hyperviseur et le système d’exploitation racine.

Au niveau de la couche hôte, les machines virtuelles Azure exécutent la dernière version de Windows Server, personnalisée et renforcée. Microsoft Azure utilise une version simplifiée de Windows Server qui inclut uniquement les composants nécessaires à l’hébergement de machines virtuelles. Cette approche vise à améliorer les performances et à réduire la surface d’attaque. Les limites des machines sont appliquées par l’hyperviseur qui ne dépend pas de la sécurité du système d’exploitation.

**Gestion d’Azure par les contrôleurs de structure** : dans Azure, un « cluster » regroupe environ 1 000 machines virtuelles s’exécutant sur des serveurs physiques (panneaux/nœuds). Les machines virtuelles sont gérées indépendamment par un composant logiciel de plateforme, mis à l’échelle horizontalement et redondant, appelé « contrôleur de structure » (FC, Fabric Controller).

Chaque FC gère le cycle de vie des applications qui s’exécutent dans son cluster, et provisionne et surveille l’intégrité du matériel sous son contrôle. Il exécute ces deux opérations autonomes, ce qui lui permet par exemple de réincarner des instances de machine virtuelle sur des serveurs sains quand il détermine qu’un serveur a échoué. Le FC effectue également des opérations de gestion des applications comme le déploiement, la mise à jour et la mise à l’échelle horizontale d’applications.

Le centre de données est divisé en clusters. Les clusters isolent les erreurs au niveau du FC et empêchent certaines classes d’erreurs d’affecter des serveurs au-delà du cluster dans lequel elles se manifestent. Les FC qui servent un cluster Azure particulier sont regroupés dans un cluster FC.

**Inventaire du matériel** : un inventaire des périphériques matériels et réseau Azure est préparé durant le processus de configuration du démarrage et documenté dans le fichier de configuration datacenter.xml. Tout nouveau composant matériel et réseau entrant dans l’environnement de production Azure doivent suivre le processus de configuration du démarrage. Le FC est responsable de la gestion de tout l’inventaire répertorié dans le fichier de configuration datacenter.xml.

**Système d’exploitation géré par FC** : l’équipe du système d’exploitation fournit des images du système d’exploitation sous la forme de disques durs virtuels (VHD) qui sont déployés sur toutes les machines virtuelles hôtes et invitées dans l’environnement de production Azure. L’équipe du système d’exploitation construit ces « images de base » par le biais d’un processus de génération hors connexion automatisé. L’image de base est une version du système d’exploitation dans laquelle le noyau et d’autres composants de base ont été modifiés et optimisés pour prendre en charge l’environnement Azure.

Il existe trois types d’images de système d’exploitation gérées par Fabric :

- Système d’exploitation hôte : système d’exploitation personnalisé qui s’exécute sur les machines virtuelles hôtes
- Système d’exploitation natif : système d’exploitation qui s’exécute sur les locataires (par exemple, Stockage Azure) et qui ne dispose pas d’un hyperviseur
- Système d’exploitation invité : système d’exploitation qui s’exécute sur les machines virtuelles invitées

Les systèmes d’exploitation hôtes et natifs gérés par un contrôleur de structure sont conçus pour une utilisation dans le cloud et ne sont pas accessibles publiquement.

**Système d’exploitation hôte et natif** : les systèmes d’exploitation hôtes et natifs sont des images de système d’exploitation renforcées qui hébergent les agents Fabric (FA, Fabric Agent) et qui s’exécutent sur un nœud de calcul (comme première machine virtuelle sur le nœud) et les nœuds de stockage. L’utilisation d’images de base optimisée des systèmes d’exploitation hôtes et natifs permet non seulement de réduire la surface exposée par les API ou les composants inutilisés qui présentent des risques de sécurité élevés, mais aussi d’augmenter l’encombrement du système d’exploitation. Ces systèmes d’exploitation à encombrement réduit incluent uniquement les composants nécessaires à Azure. Ils améliorent les performances et réduisent la surface d’attaque.

**Système d’exploitation invité** : à la différence des clients externes, les composants Azure internes qui s’exécutent sur des machines virtuelles dotées d’un système d’exploitation invité ne peuvent pas exécuter le protocole RDP (Remote Desktop Protocol). Tout changement apporté aux paramètres de configuration de la ligne de base doit passer par le processus de gestion des changements et des versions.

## <a name="azure-datacenters"></a>Centres de données Azure
L’équipe MCIO (Microsoft Cloud Infrastructure and Operations) gère les installations physiques de Microsoft liées à l’infrastructure et au centre de données pour tous les services en ligne de Microsoft. MCIO est principalement responsable de la gestion des contrôles physiques et environnementaux dans les centres de données, ainsi que de la gestion et de la prise en charge des périphériques réseau du périmètre externe (routeurs de périphérie et routeurs de centre de données). MCIO est également en charge d’installer le matériel minimal nécessaire au fonctionnement des serveurs sur les racks du centre de données. Les clients n’ont aucune interaction directe avec Azure.

## <a name="service-management--service-teams"></a>Gestion des services et équipes de service
Le support du service Azure est assuré par plusieurs groupes d’ingénieurs appelés « équipes de service ». Chaque équipe de service est responsable d’un domaine de support pour Azure. Chaque équipe doit désigner un ingénieur, disponible 24h/24 et 7j/7, pour passer en revue et résoudre les défaillances du service. Par défaut, les équipes de service n’ont pas physiquement accès au matériel sur lequel fonctionne Azure.

Les équipes de service sont les suivantes :

- Application Platform
- Azure Active Directory
- Azure Compute
- Azure Net
- Cloud Engineering Services
- ISSD: Security
- Multifactor Authentication
- SQL Database
- Storage

## <a name="types-of-users"></a>Types d’utilisateurs
Les utilisateurs internes d’Azure ont tous un statut d’employé auquel est affecté un niveau de sensibilité qui définit leur accès ou non aux données des clients. Les employés (ou sous-traitants) de Microsoft sont considérés comme des utilisateurs internes. Tous les autres utilisateurs sont considérés comme des utilisateurs externes. Les privilèges dont disposent les utilisateurs dans Azure (autorisations accordées après l’authentification) sont décrits dans le tableau suivant :

| Rôle | Interne ou externe | Niveau de sensibilité | Privilèges autorisés et fonctions exécutées | Type d’accès
| --- | --- | --- | --- | --- |
| Ingénieur de centre de données Azure | Interne | Pas d’accès aux données des clients | Gérer la sécurité physique des locaux ; effectuer des patrouilles dans et hors du centre de données et surveiller tous les points d’entrée ; effectuer des services d’escorte dans et hors du centre de données pour certains membres du personnel non autorisés qui fournissent des services généraux (restauration, nettoyage) ou des travaux informatiques dans le centre de données ; effectuer la surveillance et la maintenance de routine du matériel réseau ; gérer les incidents et réparer le matériel au moyen de divers outils ; effectuer la surveillance et la maintenance de routine du matériel physique dans les centres de données ; accéder à l’environnement à la demande des propriétaires. Être capable de réaliser des enquêtes scientifiques, de journaliser les rapports d’incidents et d’exiger des stratégies et des formations obligatoires en matière de sécurité ; prendre possession des opérations et effectuer la maintenance des outils de sécurité critiques, notamment les scanneurs et la collecte de journaux. | Accès persistant à l’environnement |
| Tri des incidents Microsoft Azure (ingénieurs chargés des interventions rapides) | Interne | Accès aux données des clients | Gérer les communications entre les opérations liées à l’infrastructure, le support et les équipes d’ingénieurs Azure ; trier les incidents de plateforme, les problèmes de déploiement et les demandes de service. | Accès juste à temps à l’environnement (avec accès persistant limité aux systèmes non clients) |
| Ingénieurs en charge du déploiement de Microsoft Azure | Interne | Accès aux données des clients | Assurer le déploiement/la mise à niveau des composants de plateforme, des logiciels et des changements de configuration planifiés pour prendre en charge Microsoft Azure. | Accès juste à temps à l’environnement (avec accès persistant limité aux systèmes non clients) |
| Support des pannes des clients Microsoft Azure (locataire) | Interne | Accès aux données des clients | Déboguer et diagnostiquer les pannes et les erreurs de la plateforme pour les locataires de calcul et les comptes Microsoft Azure ; analyser les erreurs et guider le développement de correctifs critiques au niveau de la plateforme ou du client, guider les améliorations techniques à l’échelle du support. | Accès juste à temps à l’environnement (avec accès persistant limité aux systèmes non clients) |
| Ingénieurs chargés du site actif Microsoft Azure (ingénieurs en monitoring) et des incidents | Interne | Accès aux données des clients | Prendre sous sa responsabilité le diagnostic de l’intégrité de la plateforme au moyen d’outils de diagnostic et l’atténuation des risques associés ; guider le développement de correctifs pour les pilotes de volume, réparer les éléments résultant de pannes et participer aux actions de restauration à la suite de pannes. | Accès juste à temps à l’environnement (avec accès persistant limité aux systèmes non clients) |
|Clients Microsoft Azure | Externe | N/A | N/A | N/A |

Azure utilise des identificateurs uniques pour authentifier les utilisateurs et clients organisationnels (ou les processus agissant au nom d’utilisateurs organisationnels) auprès de l’ensemble des ressources/périphériques qui font partie de l’environnement Azure.

**Authentification interne dans Microsoft Azure** : les communications entre les composants internes Azure sont protégées avec le chiffrement TLS. Dans la plupart des cas, les certificats X.509 sont auto-signés. Des exceptions sont faites pour les certificats avec des connexions accessibles de l’extérieur du réseau Azure et pour les FC. Ces derniers ont des certificats émis par une autorité de certification (AC) Microsoft soutenue par une AC racine de confiance. Les clés publiques des FC peuvent ainsi être facilement reconduites. Par ailleurs, les clés publiques des FC sont utilisées par les outils de développement Microsoft. Quand les développeurs soumettent de nouvelles images d’application, celles-ci sont donc chiffrées avec une clé publique de FC pour protéger les secrets incorporés.

**Authentification des périphériques matériels Microsoft Azure** : le FC gère un ensemble d’informations d’identification (clés et/ou mots de passe) permettant son authentification auprès de divers périphériques matériels sous son contrôle. Le système utilisé pour le transport, la persistance et l’utilisation de ces informations d’identification est conçu pour empêcher les développeurs Azure, administrateurs et services/personnels de remplacement d’avoir accès aux informations privées, confidentielles ou sensibles.

Le chiffrement basé sur la clé publique de l’identité maître du FC est utilisé dans les phases d’installation et de reconfiguration du FC pour transférer les informations d’identification permettant d’accéder aux périphériques matériels réseau. Les informations d’identification sont récupérées et déchiffrées par le FC quand celui-ci en a besoin.

**Périphériques réseau** : les comptes de service réseau sont configurés par l’équipe en charge de la mise en réseau d’Azure pour permettre à un client de Microsoft Azure de s’authentifier auprès de périphériques réseau (routeurs, commutateurs et équilibreurs de charge).

## <a name="secure-service-administration"></a>Administration sécurisée des services
Le personnel chargé des opérations Microsoft Azure est tenu d’utiliser des stations de travail d’administration sécurisées (SAW, Secure Admin Workstation). Il peut également implémenter des contrôles similaires à l’aide de stations de travail avec accès privilégié (PAW, Privileged Access Workstation). L’approche SAW est une extension de la pratique recommandée bien établie qui consiste à utiliser des comptes d’utilisateur et d’administrateur distincts pour le personnel d’administration. Cette pratique utilise un compte d’administration affecté à titre individuel et distinct du compte standard de l’utilisateur. SAW s’appuie sur cette pratique de séparation des comptes en fournissant une station de travail digne de confiance pour ces comptes sensibles.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Monitoring de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données de clients dans Azure](azure-protection-of-customer-data.md)
