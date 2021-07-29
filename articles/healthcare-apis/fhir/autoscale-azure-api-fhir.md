---
title: Mise à l’échelle automatique pour l’API Azure pour FHIR
description: Cet article décrit la fonctionnalité de mise à l’échelle automatique pour l’API Azure pour FHIR.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713377"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>Mise à l’échelle automatique pour l’API Azure pour FHIR 

L’API Azure pour FHIR en tant que service géré permet aux clients de conserver les données de santé conformes à FHIR et de les échanger en toute sécurité via l’API de service. Pour prendre en charge différentes charges de travail de transaction, les clients peuvent utiliser la mise à l’échelle manuelle ou la mise à l’échelle automatique.

## <a name="what-is-autoscale"></a>Qu’est-ce que la mise à l’échelle automatique ?

Par défaut, l’API Azure pour FHIR est définie sur la mise à l’échelle manuelle. Cette option fonctionne bien lorsque les charges de travail de transaction sont connues et cohérentes. Les clients peuvent ajuster le débit `RU/s` via le portail jusqu’à 10 000 et envoyer une demande d’augmentation de la limite. 

Avec la mise à l’échelle automatique, les clients peuvent exécuter diverses charges de travail et le débit `RU/s` est mis à l’échelle automatiquement, sans ajustement manuel.

## <a name="how-to-enable-autoscale"></a>Comment activer la mise à l’échelle automatique ?

Pour activer la fonctionnalité de mise à l’échelle automatique, vous pouvez créer un ticket de support à usage unique pour le demander. L’équipe du support technique de Microsoft activera la fonctionnalité de mise à l’échelle automatique en fonction de la priorité du support.

> [!NOTE]
> La fonctionnalité de mise à l’échelle automatique n’est pas disponible à partir de la Portail Azure.

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>Comment ajuster le débit maximal de RU/s ?

Lorsque la mise à l’échelle automatique est activée, le système calcule et définit la `Tmax` valeur initiale. L’extensibilité est régie par la valeur de débit maximale `RU/s` , ou `Tmax` , et s’exécute entre `0.1 *Tmax` (ou 10% `Tmax` ) et `Tmax RU/s` . 

Vous pouvez augmenter la `RU/s` valeur Max ou `Tmax` value et atteindre le niveau de charge maximal que le service prend en charge. Lorsque le service est occupé, le débit `RU/s` est mis à l’échelle jusqu’à la `Tmax` valeur. Lorsque le service est inactif, le débit est `RU/s` réduit à 10% de la `Tmax` valeur.
 
Vous pouvez également réduire la `RU/s` valeur Max ou `Tmax` . Lorsque vous réduisez le nombre maximal `RU/s` , la valeur minimale que vous pouvez lui affecter est : `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` , arrondie au 1000 le plus proche `RU/s` .

* **Exemple 1**: vous avez 1 Go de données et la plus élevée approvisionnée `RU/s` est 10 000. La valeur minimale est Max (**4000**, 10000/10, 1x400) = 4000. Le premier nombre, **4000**, est utilisé.
* **Exemple 2**: vous avez 20 Go de données et la plus élevée approvisionnée `RU/s` est 100 000. La valeur minimale est Max (4000, **100000/10**, 20x400) = 10 000. Le deuxième nombre, **100000/10 = 10 000**, est utilisé.
* **Exemple 3**: vous disposez de données de 80 Go et les ru/s approvisionnés les plus élevés sont 300 000. La valeur minimale est Max (4000, 300000/10, **80x400**) = 32 000. Le troisième nombre, **80x400 = 32000**, est utilisé.

Vous pouvez ajuster la `RU/s` valeur Max ou `Tmax` par le biais du portail s’il s’agit d’un nombre valide et qu’il n’est pas supérieur à 10 000 `RU/s` . Vous pouvez créer un ticket de support pour demander une `Tmax` valeur supérieure à 10 000.

## <a name="what-is-the-cost-impact-of-autoscale"></a>Quel est l’impact sur le coût de la mise à l’échelle automatique ?

La fonctionnalité de mise à l’échelle automatique entraîne des coûts en raison de la gestion automatique des unités de débit approvisionnées. Cette augmentation des coûts ne s’applique pas aux coûts de stockage et d’exécution. Pour plus d’informations sur la tarification, consultez [API Azure pour FHIR](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/).

>[!div class="nextstepaction"]
>[À propos de l’API Azure pour FHIR](overview.md)
