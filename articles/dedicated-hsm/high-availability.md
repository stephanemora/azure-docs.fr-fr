---
title: Haute disponibilité - Azure Dedicated HSM | Microsoft Docs
description: Exemple de Haute disponibilité Azure Dedicated HSM et considérations de base
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: d8975827a17dbf5d5eda2b9eb90e99ea1c03d698
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111797"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Haute disponibilité Azure Dedicated HSM

Azure Dedicated HSM s’appuie sur les centres de données hautement disponibles de Microsoft. Toutefois, tous les centres de données hautement disponibles sont vulnérables aux défaillances localisées et, dans des circonstances extrêmes, aux défaillances au niveau régional. Microsoft déploie des appareils HSM dans différents centres de données d’une même région pour que le fait d’approvisionner plusieurs appareils ne les force pas à partager un seul rack. La haute disponibilité peut atteindre un niveau supérieur si ces modules de sécurité matériels sont couplés dans les différents centres de données d’une région. Il est également possible d’associer des appareils sur différentes régions afin d’effectuer un basculement régional dans une situation de récupération d’urgence. Avec cette configuration multicouche de la haute disponibilité, la moindre défaillance de l’appareil est automatiquement résolue afin de préserver le bon fonctionnement des applications. Tous les centres de données comportent également des appareils et composants de rechange sur site qui permettent de remplacer en temps voulu n’importe quel appareil en échec.

## <a name="high-availability-example"></a>Exemple de haute disponibilité

Pour plus d’informations sur la configuration de la haute disponibilité au niveau du logiciel sur les appareils HSM, voir « Guide d’administration HSM réseau Gemalto Luna ». Ce document est disponible sur le [Portail de support client Gemalto](https://supportportal.gemalto.com/csm/).

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

Pour plus d’informations sur la configuration de la haute disponibilité sur les appareils HSM en particulier, voir les guides d’administration du portail de support client Gemalto (section 6).
