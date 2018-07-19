---
title: Sécurité de l’infrastructure Azure | Microsoft Docs
description: Cet article décrit comment Microsoft garantit la sécurité de ses centres de données.
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
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903854"
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
Il est important de comprendre la répartition des responsabilités entre vous et Microsoft. Localement, vous avez toute la responsabilité, mais, lorsque vous vous déplacez vers le cloud, certaines responsabilités sont transférées à Microsoft. La matrice de responsabilité suivante montre les zones de la pile dans un déploiement SaaS (Software as a Service), PaaS (Platform as a Service) et IaaS (Infrastructure as a Service) dont vous êtes responsable et celles dont Microsoft est responsable.

![Responsabilité partagée][1]

Les responsabilités qui sont toujours les vôtres quel que soit le type de déploiement sont les suivantes :

- Données
- Points de terminaison
- Compte
- Gestion de l’accès

Veillez à bien comprendre la répartition des responsabilités entre Microsoft et vous-même dans un déploiement SaaS, PaaS et IaaS. Pour plus d’informations, consultez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](azure-physical-security.md)
- [Disponibilité de l’infrastructure Azure](azure-infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](azure-infrastructure-components.md)
- [Architecture réseau Azure](azure-infrastructure-network.md)
- [Réseau de production Azure](azure-production-network.md)
- [Fonctionnalités de sécurité de Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Opérations de production et administration Azure](azure-infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](azure-infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](azure-infrastructure-integrity.md)
- [Protection des données des clients dans Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
