---
title: Bonnes pratiques concernant les registres
description: Découvrez comment utiliser votre instance Azure Container Registry de manière efficace en suivant ces meilleures pratiques.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578132"
---
# <a name="best-practices-for-azure-container-registry"></a>Meilleures pratiques pour Azure Container Registry

En suivant ces bonnes pratiques, vous pouvez optimiser les performances et l’efficacité des coûts d’utilisation de votre registre privé dans Azure, et déployer des images conteneur et d’autres artefacts.

Pour plus d’informations sur les concepts des registres, consultez [À propos des registres, des référentiels et des images](container-registry-concepts.md). Consultez également les [Suggestions pour la création de balises et de versions pour les images de conteneurs](container-registry-image-tag-version.md) pour des stratégies de création de balises et de versions pour les images dans votre registre. 

## <a name="network-close-deployment"></a>Déploiement proche du réseau

Créez votre registre de conteneurs dans la même région Azure que celle où vous déployez des conteneurs. En plaçant votre registre dans une région dont le réseau est proche des hôtes de vos conteneurs, vous pouvez en réduire la latence et le coût.

Un déploiement proche du réseau est l’une des principales raisons qui justifient l’utilisation d’un registre de conteneurs privé. Les images Docker ont une [construction en couches](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) efficace qui permet un déploiement incrémentiel. Les nouveaux nœuds doivent cependant extraire toutes les couches requises pour une image donnée. Ce `docker pull` initial peut rapidement atteindre plusieurs gigaoctets. Le fait d’avoir un registre privé proche de votre déploiement réduit la latence du réseau.
En outre, tous les clouds publics, y compris Azure, appliquent des frais de sortie du réseau. L’extraction d’images d’un centre de données à un autre augmente non seulement la latence, mais également les frais de sortie du réseau.

## <a name="geo-replicate-multi-region-deployments"></a>Géo-réplication de déploiements dans plusieurs régions

Utilisez la fonction de [géo-réplication](container-registry-geo-replication.md) d’Azure Container Registry si vous déployez des conteneurs dans plusieurs régions. Si vous intervenez auprès de clients du monde entier à partir de centres de données locaux ou si votre équipe de développement se trouve sur différents sites, vous pouvez géo-répliquer votre registre pour en simplifier la gestion et réduire la latence. Vous pouvez également configurer des [webhooks](container-registry-webhook.md) régionaux pour vous avertir des événements dans des réplicas spécifiques, par exemple quand des images sont envoyées.

La géoréplication est disponible avec les registres [Premium](container-registry-skus.md). Pour savoir comment utiliser la géo-réplication, consultez le didacticiel en trois parties relatif à la [géoréplication dans Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Optimiser les performances des tirages

En plus du placement des images à proximité de vos déploiements, les caractéristiques de vos images elles-mêmes peuvent impacter les performances des tirages.

* **Taille de l’image** : réduisez la taille de vos images en supprimant les [couches](container-registry-concepts.md#manifest) inutiles ou en réduisant la taille des couches. Une façon de réduire la taille d’une image est d’utiliser l’approche de [génération Docker en plusieurs étapes](https://docs.docker.com/develop/develop-images/multistage-build/) pour inclure seulement les composants de runtime nécessaires. 

  Vérifiez également si votre image peut inclure une image du système d’exploitation de base plus légère. Et si vous utilisez un environnement de déploiement comme Azure Container Instances qui met en cache certaines images de base, vérifiez si vous pouvez échanger la couche d’une image avec une des images mises en cache. 
* **Nombre de couches** : équilibrer le nombre de couches utilisées. Si vous avez trop peu, vous ne bénéficiez pas de la réutilisation et de la mise en cache des couches sur l’hôte. Si vous en avez trop, votre environnement de déploiement consacre plus de temps aux tirages et à la décompression. Avoir 5 à 10 couches est optimal.

Choisissez également un [niveau de service](container-registry-skus.md) d’Azure Container Registry répondant à vos besoins en matière de performances. Le niveau Premium offre la bande passante la plus large et le débit le plus élevé d’opérations simultanées de lecture et d’écriture, utile quand vous avez des déploiements de grand volume.

## <a name="repository-namespaces"></a>Espaces de noms du référentiel

En utilisant des espaces de noms de référentiel, vous pouvez autoriser le partage d’un même registre entre plusieurs groupes au sein de votre organisation. Les registres peuvent être partagés entre plusieurs déploiements et équipes. Azure Container Registry prend en charge les espaces de noms imbriqués de manière à faciliter l’isolement de groupes. Toutefois, le registre gère tous les référentiels indépendamment, et non en tant que hiérarchie.

Prenons par exemple les balises d’image de conteneur suivantes : Les images qui sont utilisées dans l’ensemble de l’entreprise, telles que `aspnetcore`, sont placées dans l’espace de noms racine, tandis que les images de conteneur détenues par les groupes Produits et Marketing utilisent chacune leurs propres espaces de noms.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Groupe de ressources dédié

Étant donné que les registres de conteneurs sont des ressources utilisées sur plusieurs hôtes de conteneur, il est préférable qu'un registre réside dans son propre groupe de ressources.

Bien qu’il soit possible de tester un type d’hôte spécifique, par exemple [Azure Container Instances](../container-instances/container-instances-overview.md), vous voudrez probablement supprimer l’instance de conteneur une fois que vous en avez terminé. Mais vous trouverez peut-être également judicieux de conserver la collection d’images que vous avez transmise à Azure Container Registry. En plaçant votre registre dans son propre groupe de ressources, vous réduisez le risque de supprimer accidentellement la collection d’images dans le registre lorsque vous supprimez le groupe de ressources de l’instance de conteneur.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Lorsque vous vous authentifiez avec Azure Container Registry, vous pouvez vous trouver dans deux cas de figure : une authentification individuelle et une authentification de service (ou « sans affichage »). Le tableau suivant fournit une brève vue d’ensemble de ces scénarios et décrit la méthode d’authentification recommandée pour chacun.

| Type | Exemple de scénario | Méthode recommandée |
|---|---|---|
| Identité individuelle | Un développeur qui extrait des images ou transmet des images à partir de son ordinateur de développement. | [az acr login](/cli/azure/acr#az-acr-login) |
| Identité de service / sans affichage | Pipelines de génération et de déploiement dans lequel l’utilisateur n’intervient pas directement. | [Principal du service](container-registry-authentication.md#service-principal) |

Pour obtenir des informations détaillées sur ceux-ci et sur d’autres scénarios d’authentification auprès d’Azure Container Registry, consultez [S’authentifier auprès d’un registre de conteneurs Azure](container-registry-authentication.md).

Azure Container Registry prend en charge les pratiques de sécurité de votre organisation pour distribuer les responsabilités et les privilèges à différentes identités. En utilisant le [contrôle d’accès en fonction du rôle](container-registry-roles.md), affectez les autorisations appropriées à différents utilisateurs, principaux de service ou à d’autres identités qui effectuent différentes opérations sur le registre. Par exemple, affectez des autorisations d’envoi à un principal de service utilisé dans un pipeline de build et affectez des autorisations de tirage à une identité différente utilisée pour le déploiement. Créez des [jetons](container-registry-repository-scoped-permissions.md) pour un accès précis et limité dans le temps à des référentiels spécifiques.

## <a name="manage-registry-size"></a>Gérer la taille du registre      

Les contraintes de stockage de chaque [niveau de service du registre de conteneurs][container-registry-skus] sont conçues pour s’adapter à un scénario classique : **De base** pour démarrer, **Standard** pour la majorité des applications de production, et **Premium** pour des performances à très grande échelle et pour la [géoréplication][container-registry-geo-replication]. Pendant toute la durée de vie de votre registre, vous devez gérer sa taille en supprimant régulièrement le contenu inutilisé.

Utilisez la commande Azure CLI [az acr show-usage][az-acr-show-usage] pour afficher la taille actuelle de votre registre :

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Vous pouvez également retrouver le stockage actuel utilisé dans la **Vue d’ensemble** de votre registre dans le Portail Azure :

![Informations sur l’utilisation du registre dans le portail Azure][registry-overview-quotas]

### <a name="delete-image-data"></a>Supprimer les données d’image

Azure Container Registry prend en charge plusieurs méthodes permettant de supprimer des données image de votre registre de conteneurs. Vous pouvez supprimer des images par balise ou synthèse de manifeste, ou encore supprimer un référentiel dans son intégralité.

Pour en savoir plus sur la suppression des données image de votre registre, notamment des images sans balises (parfois appelées « non résolues » ou « orphelines ») des images, consultez la section [Supprimer des images conteneur dans Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Étapes suivantes

Azure Container Registry est disponible en plusieurs niveaux (également appelés « références SKU »), qui fournissent des fonctionnalités différentes. Pour plus d’informations sur les niveaux de service disponibles, consultez [Niveaux de service Azure Container Registry](container-registry-skus.md).

Pour obtenir des recommandations visant à améliorer la posture de sécurité de vos registres de conteneur, consultez [Base de référence de sécurité Azure pour Azure Container Registry](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md