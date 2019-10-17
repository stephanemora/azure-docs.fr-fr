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
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433513"
---
# <a name="azure-infrastructure-security"></a>Sécurité de l’infrastructure Azure
Microsoft Azure s’exécute dans des centres de données gérés et exploités par Microsoft. Ces centres de données répartis à divers endroits sont conformes aux principales normes du secteur relatives à la sécurité et à la fiabilité, telles que ISO/IEC 27001:2013 et NIST SP 800-53. Les centres de données sont gérés, surveillés et administrés par le personnel d’exploitation de Microsoft. Le personnel d’exploitation a des années d’expérience dans les services en ligne les plus importants au monde avec une continuité des activités 24 heures sur 24, 7 jours sur 7.

Cette série d’articles fournit des informations sur ce que Microsoft fait pour sécuriser l’infrastructure Azure. Il aborde les sujets suivants :

- [Sécurité physique](physical-security.md)
- [Disponibilité](infrastructure-availability.md)
- [Composants et limites](infrastructure-components.md)
- [Architecture réseau](infrastructure-network.md)
- [Réseau de production](production-network.md)
- [Base de données SQL](infrastructure-sql.md)
- [Opérations](infrastructure-operations.md)
- [Surveillance](infrastructure-monitoring.md)
- [Intégrité](infrastructure-integrity.md)
- [Protection des données](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Modèle de responsabilité partagée
Il est important de comprendre la répartition des responsabilités entre vous et Microsoft. Localement, vous avez toute la responsabilité, mais, lorsque vous vous déplacez vers le cloud, certaines responsabilités sont transférées à Microsoft. Le graphique suivant illustre les domaines de responsabilité, en fonction du type de déploiement de votre pile (software as a service [SaaS], platform as a service [PaaS], infrastructure as a service [IaaS] ou local).

![Graphique illustrant les responsabilités](./media/infrastructure/responsibility-zones.png)

Vous êtes toujours responsable des éléments suivants, quel que soit le type de déploiement :

- Données
- Points de terminaison
- Compte
- gestion de l’accès

Veillez à bien comprendre la répartition des responsabilités entre Microsoft et vous-même dans un déploiement SaaS, PaaS et IaaS. Pour plus d’informations, consultez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur ce que Microsoft fait pour sécuriser l’infrastructure Azure, consultez :

- [Sécurité locale et physique des centres de données Azure](physical-security.md)
- [Disponibilité de l’infrastructure Azure](infrastructure-availability.md)
- [Composants et limites du système d’informations Azure](infrastructure-components.md)
- [Architecture réseau Azure](infrastructure-network.md)
- [Réseau de production Azure](production-network.md)
- [Fonctionnalités de sécurité d’Azure SQL Database](infrastructure-sql.md)
- [Opérations de production et administration Azure](infrastructure-operations.md)
- [Surveillance de l’infrastructure Azure](infrastructure-monitoring.md)
- [Intégrité de l’infrastructure Azure](infrastructure-integrity.md)
- [Protection des données client Azure](protection-customer-data.md)


