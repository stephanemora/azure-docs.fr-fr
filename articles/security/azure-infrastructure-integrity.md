---
title: Intégrité de l’infrastructure Azure
description: Cet article traite de l’intégrité de l’infrastructure Azure.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901314"
---
# <a name="azure-infrastructure-integrity"></a>Intégrité de l’infrastructure Azure

## <a name="software-installation"></a>Installation de logiciels
Tous les composants dans la pile de logiciels qui sont installés dans l’environnement Azure sont conçus de manière personnalisé conformément au processus SDL (Security Development Lifecycle) de Microsoft. Tous les composants logiciels (notamment les images de système d’exploitation et SQL Database) sont déployés dans le cadre du processus de gestion des modifications et des versions. Le système d’exploitation qui s’exécute sur tous les nœuds est une version personnalisée de Windows Server 2008 ou Windows Server 2012. La version exacte est choisie par le contrôleur de structure en fonction du rôle qu’il a l’intention d’attribuer au système d’exploitation. En outre, le système d’exploitation hôte n’autorise pas l’installation de composants logiciels non autorisés.

Certains composants de Microsoft Azure (par exemple RDFE, le portail des développeurs, et ainsi de suite) sont déployés en tant que clients Azure sur une machine virtuelle invitée exécutée sur un système d’exploitation invité.

## <a name="virus-scans-on-builds"></a>Analyses antivirus sur les builds
Les builds de composants logiciels Azure (notamment le système d’exploitation) doivent subir une analyse antivirus à l’aide de l’outil de protection antivirus Microsoft Endpoint Protection (MEP). Chaque analyse antivirus crée un journal dans le répertoire de build associé, détaillant ce qui a été analysé et les résultats de l’analyse. L’analyse antivirus fait partie du code source de build pour chaque composant dans Azure. Le code ne doit pas passer en production sans avoir subi une analyse antivirus n’ayant révélé aucun problème. Si des problèmes sont signalés, la build est figée et transmise aux équipes de sécurité au sein de Microsoft Security afin d’identifier où le code « non autorisé » a pénêtré dans la build.

## <a name="closedlocked-environment"></a>Environnement fermé/verrouillé
Par défaut, aucun compte d’utilisateur n’est créé sur les nœuds d’infrastructure Azure et les machines virtuelles invitées. Les comptes d’administrateur Windows par défaut sont également désactivés. Les administrateurs de Microsoft Azure Live Support (WALS) peuvent, avec une authentification appropriée, se connecter à ces machines et administrer le réseau de production Azure à des fins de réparation d’urgence.

## <a name="microsoft-azure-sql-database-authentication"></a>Authentification Microsoft Azure SQL Database
Comme avec toute implémentation de SQL Server, la gestion des comptes utilisateur doit être contrôlée étroitement. Microsoft Azure SQL Database prend en charge uniquement l’authentification SQL Server. Des comptes d’utilisateur avec des mots de passe forts et configurés avec des droits spécifiques doivent aussi être utilisés afin de compléter le modèle de sécurité des données du client.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Pare-feu/listes ACL entre MSFT CorpNet et le cluster Microsoft Azure
Le pare-feu/les listes ACL entre la plateforme de service et le réseau d’entreprise MS protègent Microsoft Azure SQL Database contre tout accès interne non autorisé. En outre, seuls les utilisateurs compris dans les plages d’adresses IP de Microsoft CorpNet peuvent accéder au point de terminaison de gestion de plateforme WinFabric.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Pare-feu/listes ACL entre les nœuds dans un cluster Azure SQL DB
En guise de protection supplémentaire, dans le cadre de la stratégie de défense en profondeur, des listes ACL/pare-feu ont été implémentés entre les nœuds d’un cluster Microsoft Azure SQL DB. Toutes les communications au sein du cluster de plateforme WinFabric, ainsi que tout le code en cours d’exécution, sont approuvés.

## <a name="custom-mas-watchdogs"></a>Agents de gestion personnalisés (agents de surveillance)
Microsoft Azure SQL Database utilise des agents de gestion personnalisés appelés « agents de surveillance » afin de surveiller l’intégrité du cluster Microsoft Azure SQL DB.

## <a name="web-protocols"></a>Protocoles web

### <a name="role-instance-monitoring-and-restart"></a>Surveillance et redémarrage des instances de rôle
Azure garantit que tous les rôles en cours d’exécution (rôles de travail de traitement backend ou rôles web orientés vers Internet) déployés sont soumis à une surveillance de l’intégrité soutenue afin de garantir qu’ils délivrent efficacement les services dans lesquels ils ont été provisionnés. Dans le cas où un rôle perdrait son intégrité, suite à une défaillance critique de l’application hébergée ou à un problème de configuration sous-jacent au sein de l’instance de rôle proprement dite, le contrôleur de structure Microsoft Azure détectera le problème au sein de l’instance de rôle et lancera un état correctif.

### <a name="compute-connectivity"></a>Connectivité de calcul
Azure garantit que l’application/service déployé(e) est accessible par le biais de protocoles web standard. Les instances virtuelles de rôles web orientés vers Internet disposeront d’une connectivité Internet externe et seront accessibles directement par les utilisateurs web. Les instances virtuelles de rôles de travail de traitement backend disposent d’une connectivité Internet externe mais ne sont pas accessibles directement par un utilisateur web externe, afin de protéger la sensibilité et l’intégrité des opérations effectuées par les rôles de travail pour le compte des instances virtuelles de rôles web accessibles publiquement.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique d’Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Protection des données clientes dans Azure](azure-protection-of-customer-data.md)
