---
title: Haute disponibilité - Azure Dedicated HSM | Microsoft Docs
description: Un exemple de Haute disponibilité Azure Dedicated HSM et des considérations de base. Cet article contient un exemple.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c46ccbda35936ed94079a21e3e9c72405875b961
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608865"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Haute disponibilité Azure Dedicated HSM

Azure Dedicated HSM s’appuie sur les centres de données hautement disponibles de Microsoft. Toutefois, tous les centres de données hautement disponibles sont vulnérables aux défaillances localisées et, dans des circonstances extrêmes, aux défaillances au niveau régional. Microsoft déploie des appareils HSM dans différents centres de données d’une même région pour que le fait d’approvisionner plusieurs appareils ne les force pas à partager un seul rack. La haute disponibilité peut atteindre un niveau supérieur si ces modules de sécurité matériels sont couplés dans les différents centres de données d’une région à l’aide de la fonctionnalité de groupe HA Thales. Il est également possible d’associer des appareils sur différentes régions afin d’effectuer un basculement régional dans une situation de récupération d’urgence. Avec cette configuration multicouche de la haute disponibilité, la moindre défaillance de l’appareil est automatiquement résolue afin de préserver le bon fonctionnement des applications. Tous les centres de données comportent également des appareils et composants de rechange sur site qui permettent de remplacer en temps voulu n’importe quel appareil en échec.

## <a name="high-availability-example"></a>Exemple de haute disponibilité

Pour plus d’informations sur la configuration de la haute disponibilité au niveau du logiciel sur les appareils HSM, consultez « Thales Luna 7 HSM Administration Guide » (Guide d’administration HSM Thales Luna 7). Ce document est disponible sur la [page HSM Thales](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).

Le diagramme suivant illustre une architecture hautement disponible. Elle comporte plusieurs appareils dans une région et plusieurs appareils couplés dans une autre. Elle utilise au minimum quatre appareils HSM et composants de réseau virtuel.

![Diagramme de haute disponibilité](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Étapes suivantes

Il est recommandé de bien comprendre tous les concepts clés du service, comme la haute disponibilité et la sécurité, avant le provisionnement des appareils et la conception ou le déploiement des applications.
Quelques rubriques conceptuelles supplémentaires :

* [Architecture de déploiement](deployment-architecture.md)
* [Sécurité physique](physical-security.md)
* [Mise en réseau](networking.md)
* [Prise en charge](supportability.md)
* [Surveillance](monitoring.md)

Pour des informations spécifiques sur la configuration de la haute disponibilité sur les appareils HSM, consultez les guides d’administration du [portail de support client Thales](https://supportportal.thalesgroup.com/csm) (section 6).
