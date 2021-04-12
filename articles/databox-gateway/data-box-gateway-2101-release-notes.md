---
title: Notes de publication d’Azure Data Box Gateway 2101 | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions pour Azure Data Box Gateway exécutant la version 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072484"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Notes de publication d’Azure Data Box Gateway 2101

Les notes de publication suivantes identifient les problèmes majeurs existants et les problèmes résolus dans la version 2101 d’Azure Data Box Gateway.

Les notes de publication sont constamment mises à jour. Les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez attentivement les informations contenues dans les notes de publication avant de déployer votre instance d’Azure Data Box Gateway.  

Cette version correspond aux versions logicielles :

- **Data Box Gateway 2101 (1.6.1475.2528)** – KB 4603462

> [!NOTE]
> La mise à jour 2101 ne peut être appliquée qu’aux appareils disposant de versions du logiciel en disponibilité générale ou ultérieures.

## <a name="whats-new"></a>Nouveautés

Cette version contient le correctif de bogue suivant :

- **Charger le problème** : cette version résout un problème de chargement où les redémarrages du chargement causés par une défaillance ralentissaient l’exécution du chargement. Ce problème était susceptible de se produire lors du chargement d’un jeu de données constitué principalement de fichiers de taille importante par rapport à la bande passante disponible, en particulier lorsque la limitation de bande passante était active. Cette modification donne au chargement la possibilité de se terminer avant qu’il soit redémarré pour un fichier donné.

Cette version contient également les mises à jour suivantes :

- Toutes les mises à jour cumulatives de Windows et de .NET Framework publiées jusqu’en octobre 2020.
- L’adresse IP statique pour Azure Data Box Gateway est conservée entre les mises à jour de logiciel.

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version

Aucun nouveau problème n’est indiqué dans les notes de publication de cette version. Tous les problèmes mentionnés dans les notes de publication proviennent des versions précédentes. Pour afficher la liste des problèmes connus, accédez à [Problèmes connus dans la version mise à la disposition générale](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
