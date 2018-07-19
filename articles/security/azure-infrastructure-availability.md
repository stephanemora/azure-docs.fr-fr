---
title: Disponibilité de l’infrastructure Azure
description: Cet article décrit les niveaux de redondance pour garantir une disponibilité optimale des données des clients.
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
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902575"
---
# <a name="azure-infrastructure-availability"></a>Disponibilité de l’infrastructure Azure
Azure fournit une disponibilité robuste basée sur une redondance étendue obtenue grâce à la technologie de virtualisation. Azure fournit de nombreux niveaux de redondance pour garantir une disponibilité optimale des données des clients.

## <a name="temporary-outages-and-natural-disaster"></a>Pannes temporaires et catastrophe naturelle
L’équipe Microsoft Cloud Infrastructure and Operations conçoit, génère, exploite et sécurise l’infrastructure cloud. Cette équipe garantit que l’infrastructure Azure offre une disponibilité et une fiabilité élevées, une haute efficacité, une scalabilité intelligente, et un cloud sécurisé, privé et de confiance.

Des onduleurs et d’énormes banques de batteries garantissent la continuité de la fourniture en électricité en cas de brève interruption d’alimentation.

Des groupes électrogènes d’urgence fournissent une alimentation de secours en cas de panne prolongée et de maintenance planifiée. Le centre de données dispose de réserves locales de carburant en cas de catastrophe naturelle.

Des réseaux à fibre optique robustes et à grande vitesse connectent les centres de données avec d’autres hubs majeurs et avec les utilisateurs d’Internet. Des nœuds de calcul hébergent les charges de travail plus près des utilisateurs finaux afin de réduire la latence, de fournir une géoredondance et d’accroître la résilience globale du service. Une équipe d’ingénieurs travaille 24 heures sur 24 afin de garantir la disponibilité permanente des services pour les clients.

Microsoft garantit une haute disponibilité grâce à une surveillance avancée et une réponse aux incidents, à la prise en charge des services et à une capacité de basculement de secours. Les centres d’opérations Microsoft géographiquement distribués fonctionnent 24 heures sur 24, 7 jours sur 7, 365 jours par an. Le réseau Azure est l’un des plus grands au monde. Le réseau de distribution de contenu à fibre optique connecte les centres de données et les nœuds de périphérie afin de garantir la fiabilité et de hautes performances.

## <a name="disaster-recovery"></a>Reprise d’activité
Azure conserve les données des clients dans deux emplacements, le client ayant la possibilité de choisir l’emplacement du site de sauvegarde. Aux deux emplacements, Azure tient constamment à jour plusieurs (3) réplicas sains des données des clients.

## <a name="database-availability"></a>Disponibilité des bases de données
Azure garantit qu’une base de données est accessible sur Internet par le biais d’une passerelle Internet avec une disponibilité de base de données soutenue. La surveillance évalue l’intégrité et l’état des bases de données actives toutes les cinq minutes.

## <a name="storage-availability"></a>Disponibilité du stockage
Azure offre un stockage par le biais d’un service de stockage hautement scalable et durable qui fournit des points de terminaison de connectivité, ce qui lui permet d’être accessible directement par une application consommatrice. Grâce à ce service de stockage, les requêtes de stockage entrantes sont traitées efficacement avec une intégrité transactionnelle.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données des clients dans Azure](azure-protection-of-customer-data.md)
