---
title: Solution Azure VMware de CloudSimple - Quota de nœuds CloudSimple
description: Décrit les limites de quota pour les nœuds de CloudSimple et comment demander une augmentation du quota
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816672"
---
# <a name="cloudsimple-node-quota-limits"></a>Limites de quota de nœuds CloudSimple

Quatre nœuds est la quantité par défaut disponible à l’approvisionnement lorsque votre abonnement est activé pour le service CloudSimple.  Vous pouvez approvisionner tout [type de nœud](cloudsimple-node.md) à partir du portail Azure.  Un minimum de trois nœuds de la même référence SKU est requis pour créer un cloud privé.  Si vous avez approvisionné les nœuds, vous pourriez voir une erreur lorsque vous tentez d’approvisionner des nœuds supplémentaires.

## <a name="quota-increase"></a>Augmentation de quota

Vous pouvez augmenter le quota de nœuds en envoyant une demande de prise en charge. L’équipe des opérations de service évaluera la requête et travaillera avec vous pour augmenter le quota de nœuds.  Sélectionnez les options suivantes lorsque vous ouvrez un nouveau ticket :

* Type de problème : **Technique**
* Abonnement : **Votre ID d’abonnement**
* Type de service : **Solution VMware de CloudSimple**
* Type de problème : **Quota de nœuds dédiés**
* Sous-type du problème : **Augmenter le quota de nœuds dédiés**
* Objet : **Augmentation de quota**

Dans les détails du ticket de support, indiquez le nombre requis de nœuds et la référence SKU de nœud.

Vous pouvez également contacter votre responsable de compte Microsoft à [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) pour augmenter le quota de nœuds de votre abonnement.  Vous devrez fournir les éléments suivants :

* Identifiant d’abonnement
* Référence SKU de nœud
* Nombre de nœuds supplémentaires pour lesquels vous demandez l’augmentation du quota

## <a name="next-steps"></a>Étapes suivantes

* [Approvisionner les nœuds](create-nodes.md)
* [Vue d’ensemble des nœuds CloudSimple](cloudsimple-node.md)