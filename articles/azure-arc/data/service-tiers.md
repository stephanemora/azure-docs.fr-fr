---
title: Niveaux de service SQL Managed Instance avec Azure Arc
description: Décrit les niveaux de service disponibles pour les déploiements Azure SQL Managed Instance.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783996"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>Niveaux de service SQL Managed Instance avec Azure Arc

Faisant partie de la famille des produits Azure SQL, SQL Managed Instance avec Azure Arc est disponible en deux niveaux de service [vCore](../../azure-sql/database/service-tiers-vcore.md).

- Le niveau **Usage général**, le plus économique, est conçu pour la plupart des charges de travail présentant des caractéristiques communes en matière de performances et de disponibilité.
- Le niveau **Critique pour l’entreprise** est conçu pour les charges de travail sensibles aux performances qui ont des caractéristiques plus élevées en matière de disponibilité.

Le niveau de service Critique pour l’entreprise est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Le niveau de service Usage général est mis à la disposition générale. 

Dans Azure, le stockage et le calcul sont fournis par Microsoft avec des contrats de niveau de service (SLA) garantis pour les performances, le débit, la disponibilité, etc. sur chacun des niveaux de service. Avec les services de données Azure Arc, les clients fournissent le stockage et le calcul. Par conséquent, il n’existe aucun contrat SLA garanti fourni aux clients avec les services de données Azure Arc. Toutefois, les clients bénéficient de la flexibilité nécessaire pour apporter leur propre matériel performant, quel que soit le niveau de service. 

## <a name="service-tier-comparison"></a>Comparaison des niveaux de service

Vous trouverez ci-dessous une description des différentes capacités disponibles à partir des services de données Azure Arc sur les deux niveaux de service :


Domaine | Critique pour l’entreprise (préversion)* | Usage général
----------|-----------------|------------------
Ensemble des fonctionnalités | Identique à SQL Server Édition Entreprise | Identique à SQL Server Édition Standard
Limite d’UC/instance | Illimité  | 24 cœurs
Limite de mémoire/instance | Illimité | 128 Go
Haute disponibilité | Groupe de disponibilité | Une seule instance avec un redéploiement Kubernetes et un stockage partagé.
Lecture du scale-out | Groupe de disponibilité | Aucune
Taux de change AHB pour le composant IP du prix | 1:1 Enterprise Edition <br> 4:1 Standard Edition | 1:4 Enterprise Edition <br> 1:1 Standard Edition 
Tarifs Dev/Test | Aucun coût | Aucun coût

\* Actuellement, le niveau de service Critique pour l’entreprise est en préversion et n’entraîne pas de frais d’utilisation pendant cette phase. Certaines fonctionnalités peuvent changer à mesure que nous approcherons de la disponibilité générale.

## <a name="how-to-choose-between-the-service-tiers"></a>Comment choisir entre les niveaux de service

Étant donné que les clients apportent leur propre matériel avec des exigences en matière de performances et de disponibilité basées sur les besoins de leur entreprise, les principaux facteurs de différenciation entre les niveaux de service sont les éléments fournis au niveau du logiciel. 

### <a name="choose-general-purpose-if"></a>Choisissez Usage général si

- La configuration requise du processeur/de la mémoire satisfait ou est comprise dans les limites du niveau de service usage général
- Les options de haute disponibilité fournies par Kubernetes, comme les redéploiements de pod, sont suffisantes pour la charge de travail
- L’application n’a pas besoin de mise à l’échelle en lecture
- L’application ne nécessite aucune des fonctionnalités du niveau de service Critique pour l’entreprise (identique à SQL Server Édition Entreprise)

### <a name="choose-business-critical-if"></a>Choisissez Critique pour l’entreprise si

- La configuration requise du processeur/de la mémoire dépasse les limites du niveau de service usage général
- L’application nécessite un niveau supérieur de haute disponibilité, comme les groupes de disponibilité intégrés, pour gérer les basculements d’application par rapport à ce qui est proposé par Kubernetes. 
- L’application peut tirer parti de la mise à l’échelle en lecture pour décharger les charges de travail de lecture sur les réplicas secondaires
- L’application nécessite des fonctionnalités uniquement disponibles dans le niveau de service Critique pour l’entreprise (identique à SQL Server Édition Entreprise)
