---
title: Sécurité de l’infrastructure Azure | Microsoft Docs
description: Cet article décrit comment Microsoft garantit la sécurité de nos centres de données Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586547"
---
# <a name="azure-infrastructure-security"></a>Sécurité de l’infrastructure Azure
Microsoft Azure s’exécute dans des centres de données gérés et exploités par Microsoft. Ces centres de données répartis à divers endroits sont conformes aux principales normes du secteur relatives à la sécurité et à la fiabilité, telles que ISO/IEC 27001:2013 et NIST SP 800-53. Les centres de données sont gérés, surveillés et administrés par le personnel d’exploitation de Microsoft. Le personnel d’exploitation a des années d’expérience dans les services en ligne les plus importants au monde avec une continuité des activités 24 heures sur 24, 7 jours sur 7.

Cette série d’articles fournit des informations sur ce que Microsoft fait pour sécuriser l’infrastructure Azure. Il aborde les sujets suivants :

- [Sécurité physique](azure-physical-security.md)
- [Disponibilité](azure-infrastructure-availability.md)
- [Composants et limites](azure-infrastructure-components.md)
- [Architecture réseau](azure-infrastructure-network.md)
- [Réseau de production](azure-production-network.md)
- [Base de données SQL](azure-infrastructure-sql.md)
- [Opérations](azure-infrastructure-operations.md)
- [Surveillance](azure-infrastructure-monitoring.md)
- [Intégrité](azure-infrastructure-integrity.md)
- [Protection des données](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modèle de responsabilité partagée
Il est important de comprendre la répartition des responsabilités entre vous et Microsoft. Localement, vous avez toute la responsabilité, mais, lorsque vous vous déplacez vers le cloud, certaines responsabilités sont transférées à Microsoft. Le graphique suivant illustre les domaines de responsabilité, en fonction du type de déploiement de votre pile (software as a service [SaaS], platform as a service [PaaS], infrastructure as a service [IaaS] ou local).

![Graphique illustrant les responsabilités][1]

Vous êtes toujours responsable des éléments suivants, quel que soit le type de déploiement :

- Données
- Points de terminaison
- Compte
- gestion de l’accès

Veillez à bien comprendre la répartition des responsabilités entre Microsoft et vous-même dans un déploiement SaaS, PaaS et IaaS. Pour plus d’informations, consultez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité d’Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données client Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
