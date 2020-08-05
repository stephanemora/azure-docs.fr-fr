---
author: baanders
description: fichier include pour la spécification de rôle dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407484"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Configuration requise : Spécifications relatives aux autorisations

Pour pouvoir effectuer toutes les étapes de cet article, vous devez être classifié comme Propriétaire dans votre abonnement Azure. 

Vous pouvez vérifier votre niveau d’autorisation en exécutant cette commande dans Cloud Shell :

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Si vous êtes propriétaire, la valeur `roleDefinitionName` dans la sortie est *Propriétaire* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Fenêtre Cloud Shell qui affiche la sortie de la commande az role assignment list":::

Si vous constatez que la valeur est *Contributeur* ou autre chose que *Propriétaire*, vous pouvez procéder de l’une des façons suivantes :
* Contactez le propriétaire de votre abonnement et demandez-lui d’effectuer les étapes de cet article en votre nom.
* Contactez le propriétaire de votre abonnement ou une personne disposant du rôle Administration de l’accès utilisateur sur l’abonnement, et demandez-lui de vous élever au rang de Propriétaire de l’abonnement afin que vous disposiez des autorisations nécessaires pour continuer. Le fait que cela soit approprié dépend de votre organisation et de votre rôle au sein de celle-ci.