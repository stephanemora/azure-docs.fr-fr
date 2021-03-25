---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187125"
---
## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour les registres de conteneurs** est facturé conformément aux indications de la [page des tarifs](../articles/security-center/security-center-pricing.md).|
|Registres et images pris en charge :|Images Linux dans des registres ACR accessibles depuis l’Internet public grâce à l’accès shell|
|Registres et images pris en charge :|Images Windows<br>Registres « privés »<br>Registres dont l’accès est limité par un pare-feu, à un point de terminaison de service ou à des points de terminaison privés tels qu’Azure Private Link<br>Images super-minimalistes, telles que les images de [base Docker](https://hub.docker.com/_/scratch/) ou « Distroless » qui contiennent uniquement une application et ses dépendances de runtime sans gestionnaire de packages, interpréteur de commandes ni système d’exploitation|
|Rôles et autorisations obligatoires :|**Lecteur de sécurité** et [autorisations et rôles Azure Container Registry](../articles/container-registry/container-registry-roles.md)|
|Clouds :|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Clouds commerciaux<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov et China Gov : seule la fonctionnalité d’analyse à l’envoi est actuellement prise en charge. En savoir plus dans [Quand les images sont-elles analysées ?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||