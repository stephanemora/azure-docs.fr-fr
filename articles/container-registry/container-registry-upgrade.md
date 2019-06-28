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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480342"
---
# <a name="upgrade-a-classic-container-registry"></a>Mettre à niveau un registre de conteneurs Classique

Azure Container Registry (ACR) est disponible dans plusieurs niveaux de service, [appelés références SKU](container-registry-skus.md). La version initiale d’ACR proposait une référence unique Classique, à laquelle il manquait plusieurs fonctionnalités inhérentes aux références SKU Basic, Standard et Premium (collectivement appelées registres *managés*).

La référence SKU Classique est dépréciée et ne sera plus disponible après avril 2019. Cet article explique comment migrer votre registre Classique non managé sur une des références SKU managées afin de pouvoir tirer parti de leur jeu de fonctionnalités avancées.

## <a name="why-upgrade"></a>Pourquoi procéder à une mise à niveau ?

La référence SKU du registre Classique est **dépréciée** et ne sera plus disponible à partir d'**avril 2019**. Tous les registres Classiques existants doivent être mis à niveau avant avril 2019. Les fonctionnalités de gestion de portail des registres Classiques vont être progressivement supprimées. La fonctionnalité de création de registres Classiques sera désactivée après avril 2019.

En raison de la dépréciation planifiée et des capacités limitées des registres Classiques non managés, tous vos registres Classiques doivent être mis à niveau vers des registres managés (De base, Standard ou Premium). Ces références (SKU) de niveau supérieur intègrent de manière plus étroite le registre avec les fonctionnalités Azure. Pour plus d'informations sur les prix et fonctionnalités des différents niveaux de service, consultez [Références SKU des registres de conteneurs](container-registry-skus.md).

Le registre Classique dépend du compte de stockage qu’Azure provisionne automatiquement dans votre abonnement Azure quand vous avez créé le registre. En revanche, les références SKU de base, standard et Premium profitent des [fonctionnalités de stockage avancées](container-registry-storage.md) Azure par la gestion pour vous de manière transparente du stockage de vos images. Un compte de stockage distinct n’est pas créé dans votre propre abonnement.

Un stockage de registres managés offre les avantages suivants :

* Les images de conteneurs sont [chiffrées au repos](container-registry-storage.md#encryption-at-rest).
* Les images sont stockées à l'aide du [stockage géoredondant](container-registry-storage.md#geo-redundant-storage), ce qui garantit la sauvegarde de vos images avec une réplication multirégion (référence SKU Premium uniquement).
* Possibilité de [basculer d’une référence à une autre](container-registry-skus.md#changing-skus) librement, ce qui permet de bénéficier d’un débit plus élevé quand vous choisissez une référence de niveau supérieur. Avec chaque référence, ACR peut répondre à vos besoins en matière de débit à mesure qu’ils augmentent.
* Le modèle de sécurité unifiée pour le registre et son stockage offrent une gestion simplifiée des droits. Vous gérez les autorisations uniquement pour le registre de conteneurs, sans avoir à gérer également les autorisations d’un compte de stockage distinct.

Pour plus d’informations sur le stockage des images dans l’ACR, consultez [stockage des images conteneur dans Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Considérations relatives à la migration

Lorsque vous procédez à la mise à niveau d'un registre Classique vers un registre managé, Azure doit copier toutes les images de conteneurs existantes du compte de stockage créé par ACR dans votre abonnement vers un compte de stockage géré par Azure. Selon la taille du registre, ce processus peut durer quelques minutes, voire plusieurs heures. À titre indicatif, prévoyez une cadence de migration d'environ 0,5 Gio par minute.

Lors du processus de conversion, les opérations `docker push` sont désactivées pendant les 10% restants de la migration. `docker pull` continue de fonctionner normalement.

Ne supprimez , ni ne modifiez le contenu du compte de stockage en sauvegardant votre registre Classique au cours du processus de conversion du compte de stockage. Vous pourriez corrompre vos images de conteneur.

Au terme de la migration, le compte de stockage de l'abonnement dans lequel le registre Classique était sauvegardé n'est plus utilisé par Azure Container Registry. Après avoir vérifié la réussite de la migration, envisagez de supprimer le compte de stockage afin de réduire les coûts.

>[!IMPORTANT]
> La mise à niveau de Classique vers une des références SKU managées est un **processus unidirectionnel**. Une fois que vous avez converti un registre Classique en registre De base, Standard ou Premium, vous ne pouvez plus revenir en arrière. Vous pouvez, toutefois, librement passer d’une référence SKU à une autre disposant de la capacité suffisante pour votre registre.

## <a name="how-to-upgrade"></a>Mise à niveau

Vous pouvez mettre à niveau un registre Classique non managé vers une des références SKU managées de plusieurs façons. Dans les sections suivantes, nous décrivons le processus d’utilisation d’[Azure CLI][azure-cli] et du [portail Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Mise à niveau dans Azure CLI

Pour mettre à niveau un registre Classique dans Azure CLI, exécutez la commande [az acr update][az-acr-update] et spécifiez la nouvelle référence (SKU) pour le registre. Dans l’exemple suivant, un registre Classique nommé *myclassicregistry* est mis à niveau vers la référence (SKU) Premium :

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Une fois la migration terminée, vous devez obtenir un résultat similaire à ce qui suit. Notez que la référence `sku` est « Premium » et que le `storageAccount` est `null`, ce qui indique qu'Azure gère désormais le stockage des images pour ce registre.

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

Lorsque vous mettez à niveau un registre Classique à l'aide du portail Azure, Azure sélectionne automatiquement la référence SKU Standard ou Premium, en fonction de la référence SKU qui peut s'adapter à vos images. Par exemple, si votre registre contient moins de 100 Gio d'images, Azure sélectionne le registre Classique et le convertit automatiquement en registre Standard (100 Gio maximum).

Pour mettre à niveau votre registre Classique à l’aide du portail Azure, accédez à la **vue d’ensemble** du registre de conteneurs et sélectionnez **Upgrade to managed registry** (Mettre à niveau vers le registre managé).

![Bouton de mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-01-upgrade]

Sélectionnez **OK** pour confirmer que vous souhaitez effectuer une mise à niveau vers un registre managé.

Lors de la migration, le portail indique que l’**état Approvisionnement** du registre est *Mise à jour*. Comme mentionné précédemment, les opérations `docker push` sont désactivées pendant les 10% restants de la migration. Vous ne devez pas supprimer ou mettre à jour le compte de stockage utilisé par le registre Classique tant que la migration est en cours, car cela risquerait de corrompre les images.

![Progression de la mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-03-updating]

Au terme de la migration, l'**état d'approvisionnement** indique *Opération réussie* et vous pouvez reprendre des opérations normales avec votre registre.

![État d’achèvement de la mise à niveau du registre Classique dans l’interface utilisateur du portail Azure][update-classic-04-updated]

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez mis à niveau un registre Classique vers un registre managé, Azure n'utilise plus le compte de stockage dans lequel le registre Classique était sauvegardé. Pour réduire les coûts, envisagez de supprimer le compte de stockage ou le conteneur d’objets Blob du compte qui contient les images de l’ancien conteneur.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com