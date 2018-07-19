---
title: Opérations et administration du réseau de production Azure
description: Cet article fournit une description générale de l’administration et du fonctionnement du réseau de production Azure.
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
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101599"
---
# <a name="azure-production-operations-and-management"></a>Opérations et administration du réseau de production Azure    
Pour administrer et faire fonctionner le réseau de production Azure, les équipes des opérations Azure et Azure SQL Database doivent coordonner leurs efforts. Plusieurs outils de monitoring des performances système et d’application sont utilisés dans l’environnement. Les appareils réseau, les serveurs, les services et les processus d’application sont supervisés avec les outils appropriés.

Plusieurs niveaux de monitoring, de journalisation et de création de rapports sont implémentés pour garantir une exécution sécurisée des services qui s’exécutent dans l’environnement Microsoft Azure, notamment les actions suivantes :

- Avant tout, Microsoft Azure Monitoring Agent (MA) collecte des informations de monitoring et de journal de diagnostic à de nombreux emplacements, y compris le contrôleur de structure et le système d’exploitation racine, et les écrit dans des fichiers journaux. Enfin, il pousse une partie digérée des informations sur un compte de stockage Azure préconfiguré. Par ailleurs, le service MDS (Monitoring and Diagnostic Service) est un service indépendant qui lit différentes données de journal de diagnostic et de monitoring et synthétise les informations. MDS écrit les informations dans un journal intégré. Azure utilise l’extension ASM (Azure Security Monitoring) intégrée, qui est une extension du système de monitoring Azure. Elle présente des composants qui observent, analysent et reportent des événements de sécurité pertinents de différents points de la plateforme.
- La plateforme Microsoft Azure SQL Database WinFabric offre des services d’administration, de déploiement, de développement et de supervision d’opérations pour Microsoft Azure SQL Database. Elle offre des services distribués de déploiement à plusieurs étapes, une supervision de l’intégrité, des réparations automatiques et une conformité aux versions des services. Elle propose les services suivants :

   - Fonctionnalités de modélisation de service avec un environnement de développement haute fidélité (les clusters de centre de données sont chers et rares).
   - Déploiement en un clic et workflows de mise à niveau pour l’amorçage et la maintenance des services.
   - Rapports sur l’intégrité avec des workflows de réparation automatique pour permettre une réparation spontanée.
   - Monitoring, alertes et débogage en temps réel des installations sur les nœuds d’un système distribué.
   - Un ensemble centralisé de données et de métriques de fonctionnement de la racine distribuée permet d’obtenir des insights d’analyse et de service.
   - Outils de fonctionnement du déploiement, de la gestion des changements et du monitoring.
   - La plateforme Microsoft Azure SQL Database WinFabric et des scripts de surveillance s’exécutent en continu et supervisent en temps réel.

Si une anomalie se produit, le processus de réponse aux incidents suivi par l’équipe de tri des incidents Azure est activé. Le personnel compétent du support Azure est notifié pour répondre à l’incident. Le suivi et la résolution des problèmes sont documentés et gérés dans un système centralisé de gestion des tickets. Les métriques de la disponibilité système sont disponibles sous l’accord de confidentialité et sur demande.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Réseau d’entreprise et accès multifacteur en production
La base d’utilisateurs du réseau d’entreprise inclut le personnel du support de Microsoft Azure. Le réseau d’entreprise prend en charge les fonctions internes de l’entreprise et comprend l’accès aux applications internes qui sont utilisées pour le support Azure. Le réseau d’entreprise est à la fois logiquement et physiquement séparé du réseau de production Azure. Le personnel Microsoft Azure a accès au réseau d’entreprise sur ses ordinateurs portables et ses stations de travail Microsoft Azure. Tous les utilisateurs doivent avoir un compte Active Directory (AD), avec un nom d’utilisateur et un mot de passe, pour accéder aux ressources du réseau d’entreprise. L’accès au réseau d’entreprise utilise les comptes AD, qui sont émis pour l’ensemble du personnel Microsoft, les sous-traitants et les fournisseurs, et gérés par MSIT. Les identificateurs d’utilisateur uniques distinguent le personnel en fonction de leur poste chez Microsoft.

L’accès aux applications Azure internes est contrôlé par une authentification avec ADFS (Active Directory Federation Services). ADFS est un service hébergé par MSIT qui permet d’authentifier les utilisateurs du réseau d’entreprise en appliquant un jeton sécurisé et des revendications utilisateur. ADFS permet aux applications internes de Microsoft Azure d’authentifier les utilisateurs sur le domaine AD d’entreprise Microsoft. Pour accéder au réseau de production à partir de l’environnement du réseau d’entreprise, l’utilisateur doit s’authentifier à l’aide de l’authentification multifacteur.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Monitoring de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données des clients dans Azure](azure-protection-of-customer-data.md)
