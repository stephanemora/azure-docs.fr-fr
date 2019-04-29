---
title: Mettre à niveau un registre de conteneurs Azure Classic
description: Tirez parti du jeu de fonctionnalités étendu des registres de conteneur managés De base, Standard et Premium en mettant à niveau votre registre de conteneurs classique non managé.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480342"
---
# <a name="upgrade-a-classic-container-registry"></a>Mettre à niveau un registre de conteneurs Classique

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, [appelés références SKU](container-registry-skus.md). La version initiale d’ACR proposait une référence unique Classique, à laquelle il manquait plusieurs fonctionnalités inhérentes aux références SKU Basic, Standard et Premium (collectivement appelées registres *managés*).

La référence classique est déconseillée et n’est pas disponible après avril 2019. Cet article explique comment migrer votre registre Classique non managé sur une des références SKU managées afin de pouvoir tirer parti de leur jeu de fonctionnalités avancées.

## <a name="why-upgrade"></a>Pourquoi procéder à une mise à niveau ?

La référence (SKU) est en cours de Registre classique **déconseillée**et ne sera pas disponible après **avril 2019**. Tous les registres classique existants doivent être mis à niveau avant avril 2019. Fonctionnalités de gestion via le portail des registres classique seront maintenues. La création de nouveaux registres classique ne sera désactivée après avril 2019.

En raison de l’obsolescence planifiée et les capacités limitées des registres classique non managés, tous vos registres classique doivent être mis à niveau pour les registres gérés (Basic, Standard ou Premium). Ces références (SKU) de niveau supérieur intègrent de manière plus étroite le registre avec les fonctionnalités Azure. Pour plus d’informations sur la tarification et les fonctionnalités des différents niveaux de service, consultez [références SKU de Registre de conteneur](container-registry-skus.md).

Le registre Classique dépend du compte de stockage qu’Azure provisionne automatiquement dans votre abonnement Azure quand vous avez créé le registre. En revanche, les références SKU de base, standard et Premium profitent des [fonctionnalités de stockage avancées](container-registry-storage.md) Azure par la gestion pour vous de manière transparente du stockage de vos images. Un compte de stockage distinct n’est pas créé dans votre propre abonnement.

Un stockage de registres managés offre les avantages suivants :

* Les images de conteneurs sont [chiffrées au repos](container-registry-storage.md#encryption-at-rest).
* Les images sont stockées à l’aide de [stockage géo-redondant](container-registry-storage.md#geo-redundant-storage), ce qui garantit sauvegarde de vos images avec la réplication dans plusieurs régions (référence SKU Premium uniquement).
* Possibilité de [basculer d’une référence à une autre](container-registry-skus.md#changing-skus) librement, ce qui permet de bénéficier d’un débit plus élevé quand vous choisissez une référence de niveau supérieur. Avec chaque référence, ACR peut répondre à vos besoins en matière de débit à mesure qu’ils augmentent.
* Le modèle de sécurité unifiée pour le registre et son stockage offrent une gestion simplifiée des droits. Vous gérez les autorisations uniquement pour le registre de conteneurs, sans avoir à gérer également les autorisations d’un compte de stockage distinct.

Pour plus d’informations sur le stockage des images dans l’ACR, consultez [stockage des images conteneur dans Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Considérations relatives à la migration

Lorsque vous mettez à niveau un Registre classique vers un Registre managé, Azure doit copier toutes les images de conteneur existantes à partir du compte de stockage créés par l’ACR dans votre abonnement à un compte de stockage géré par Azure. Selon la taille du registre, ce processus peut durer quelques minutes, voire plusieurs heures. À titre d’estimation, prévoyez une durée de la migration d’environ 0,5 Go par minute.

Pendant le processus de conversion, `docker push` les opérations sont désactivées pendant au cours des 10 % de la migration. `docker pull` continue à fonctionner normalement.

Ne supprimez , ni ne modifiez le contenu du compte de stockage en sauvegardant votre registre Classique au cours du processus de conversion du compte de stockage. Vous pourriez corrompre vos images de conteneur.

Une fois la migration terminée, le compte de stockage dans votre abonnement que votre Registre classique était sauvegardé est n’est plus utilisé par Azure Container Registry. Après avoir vérifié la réussite de la migration, envisagez de supprimer le compte de stockage afin de réduire les coûts.

>[!IMPORTANT]
> La mise à niveau de Classique vers une des références SKU managées est un **processus unidirectionnel**. Une fois que vous avez converti un registre Classique en registre De base, Standard ou Premium, vous ne pouvez plus revenir en arrière. Vous pouvez, toutefois, librement passer d’une référence SKU à une autre disposant de la capacité suffisante pour votre registre.

## <a name="how-to-upgrade"></a>Mise à niveau

Vous pouvez mettre à niveau un registre Classique non managé vers une des références SKU managées de plusieurs façons. Dans les sections suivantes, nous décrivons le processus d’utilisation d’[Azure CLI][azure-cli] et du [portail Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Mise à niveau dans Azure CLI

Pour mettre à niveau un registre Classique dans Azure CLI, exécutez la commande [az acr update][az-acr-update] et spécifiez la nouvelle référence (SKU) pour le registre. Dans l’exemple suivant, un registre Classique nommé *myclassicregistry* est mis à niveau vers la référence (SKU) Premium :

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Une fois la migration terminée, vous devez obtenir un résultat similaire à ce qui suit. Notez que le `sku` est « Premium » et le `storageAccount` est `null`, indiquant qu’Azure gère désormais le stockage des images pour ce Registre.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Si vous spécifiez une référence SKU de registre managé dont la capacité maximale est inférieure à la taille de votre registre Classique, vous recevrez un message d’erreur semblable au suivant.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Si vous recevez une erreur similaire, réexécutez la commande [az acr update][az-acr-update] et spécifiez la référence SKU suggérée, qui est le niveau de référence SKU suivant le plus élevé capable de s’adapter à vos images.

## <a name="upgrade-in-azure-portal"></a>Mise à niveau dans le portail Azure

Lorsque vous mettez à niveau un Registre classique à l’aide du portail Azure, Azure sélectionne automatiquement le Standard ou une référence (SKU) Premium, selon ce qui les référence (SKU) peut prendre en charge vos images. Par exemple, si votre Registre contient moins de 100 Go dans les images, Azure automatiquement sélectionne et convertit le Registre classique Standard (100 Gio maximum).

Pour mettre à niveau votre registre Classique à l’aide du portail Azure, accédez à la **vue d’ensemble** du registre de conteneurs et sélectionnez **Upgrade to managed registry** (Mettre à niveau vers le registre managé).

![Bouton de mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-01-upgrade]

Sélectionnez **OK** pour confirmer que vous souhaitez effectuer une mise à niveau vers un registre managé.

Lors de la migration, le portail indique que l’**état Approvisionnement** du registre est *Mise à jour*. Comme mentionné précédemment, `docker push` les opérations sont désactivées pendant au cours des 10 % de la migration. Pas la suppression ou la mise à jour le compte de stockage utilisé par le Registre classique, tandis que la migration est en cours d’exécution, ainsi peut entraîner une corruption d’image.

![Progression de la mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-03-updating]

Une fois la migration terminée, le **état d’approvisionnement** indique *Succeeded*, et vous pouvez reprendre les opérations normales avec votre Registre.

![État d’achèvement de la mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-04-updated]

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez mis à niveau un Registre classique vers un Registre managé, Azure n’utilise plus le compte de stockage que le Registre classique était sauvegardé. Pour réduire les coûts, envisagez de supprimer le compte de stockage ou le conteneur d’objets Blob du compte qui contient les images de l’ancien conteneur.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com