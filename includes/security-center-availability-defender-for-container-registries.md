---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 05/19/2021
ms.topic: include
ms.openlocfilehash: 103a23a478a3629eb913c3d1672b3665bc9cba9c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559472"
---
## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour les registres de conteneurs** est facturé conformément aux indications de la [page des tarifs](../articles/security-center/security-center-pricing.md).|
|Registres et images pris en charge :|Images Linux dans des registres ACR accessibles depuis l’Internet public grâce à l’accès shell|
|Registres et images pris en charge :|Images Windows<br>Registres « privés »<br>Images super-minimalistes, telles que les images de [base Docker](https://hub.docker.com/_/scratch/) ou « Distroless » qui contiennent uniquement une application et ses dépendances de runtime sans gestionnaire de packages, interpréteur de commandes ni système d’exploitation<br>Images avec [Spécification du format d’image Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md)|
|Rôles et autorisations obligatoires :|**Lecteur de sécurité** et [autorisations et rôles Azure Container Registry](../articles/container-registry/container-registry-roles.md)|
|Clouds :|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Clouds commerciaux<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov et China Gov : seule la fonctionnalité d’analyse à l’envoi est actuellement prise en charge. En savoir plus dans [Quand les images sont-elles analysées ?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||
