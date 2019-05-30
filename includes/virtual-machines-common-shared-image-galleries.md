---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 7a0e628eed861767d1eeb50b0ded7bb3d8807328
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271571"
---
Galerie d’images partagé est un service qui vous permet de créer la structure et l’organisation autour de vos images gérés. Fournissent des galeries d’images partagé :

- Réplication globale gérée d’images.
- Le contrôle de version et le regroupement des images pour faciliter la gestion.
- Images hautement disponibles avec des comptes de stockage de redondant Zone (ZRS) dans les régions qui prennent en charge les Zones de disponibilité. Le stockage ZRS offre une meilleure résilience contre les défaillances zonales.
- Partage dans des abonnements et même entre les locataires Active Directory (AD), à l’aide de RBAC.
- Mise à l’échelle vos déploiements avec des réplicas d’image dans chaque région.

Celle-ci vous permet de partager vos images avec différents utilisateurs, principaux de service ou groupes Active Directory au sein de votre organisation. Il est possible de répliquer des images partagées dans plusieurs régions, pour une mise à l’échelle plus rapide de vos déploiements.

Une image managée est une copie d’une machine virtuelle complète (y compris tous les disques de données attachés) ou simplement du disque de système d’exploitation, selon la façon dont vous créez l’image. Quand vous créez une machine virtuelle à partir de l’image, une copie des disques durs virtuels dans l’image est utilisée pour créer les disques de la nouvelle machine virtuelle. L’image managée reste dans le stockage et peut servir à créer des machines virtuelles à l’infini.

Si vous avez un grand nombre d’images gérées que vous avez besoin maintenir et que vous souhaitez rendre disponibles tout au long de votre entreprise, vous pouvez utiliser une galerie d’images partagé en tant que référentiel qui rend plus facile de partager vos images. 

La fonctionnalité Galerie d’images partagées a plusieurs types de ressources :

| Ressource | Description|
|----------|------------|
| **Image managée** | Une image de base qui peut être utilisée seul ou utilisée pour créer un **version de l’image** dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définis dans une galerie et acheminent des informations sur l’image et la configuration requise pour l’utilisation au sein de votre organisation. Vous pouvez inclure des informations telles que si l’image est Windows ou Linux, les besoins en mémoire minimale et maximale et notes de publication. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |

<br>


![Graphique montrant comment vous pouvez avoir plusieurs versions d’une image dans la galerie](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Définitions de l’image

Définitions de l’image sont un regroupement logique des versions d’une image. La définition de l’image conserve plus d’informations sur la raison pour laquelle l’image a été créée, quel système d’exploitation pour et les informations sur l’utilisation de l’image. Une définition de l’image ressemble à un plan pour tous les détails relatifs à la création d’une image spécifique. Vous ne déployez pas une machine virtuelle à partir d’une définition de l’image, mais à partir de la version de l’image créée à partir de la définition.


Il existe trois paramètres pour chaque définition de l’image qui sont utilisées en combinaison - **Publisher**, **offrent** et **référence (SKU)** . Ceux-ci sont utilisés pour trouver une définition de l’image spécifique. Des versions d'image peuvent partager une ou deux de ces valeurs, mais pas les trois.  Par exemple, voici trois définitions d'image et leurs valeurs :

|Définition de l’image|Publisher|Offre|Sku|
|---|---|---|---|
|myImage1|Contoso|Finances|Backend|
|myImage2|Contoso|Finances|Serveur frontal|
|myImage3|Test|Finances|Serveur frontal|

Ces trois définitions présentent des ensembles de valeurs uniques. Le format est semblable à la façon dont vous pouvez spécifier actuellement de serveur de publication, l’offre et référence (SKU) pour [images Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) dans Azure PowerShell pour obtenir la dernière version d’une image de place de marché. Chaque définition de l’image doit posséder un ensemble unique de ces valeurs.

Autres paramètres qui peuvent être définies sur votre définition de l’image afin que vous pouvez suivre plus facilement vos ressources sont les suivantes :

* État de système d’exploitation - vous pouvez définir l’état du système d’exploitation généralisée ou spécialisée, mais uniquement généralisé est actuellement pris en charge. Les images doivent être créés à partir de machines virtuelles qui ont été généralisées à l’aide de Sysprep pour Windows ou `waagent -deprovision` pour Linux.
* Système d’exploitation - peut être Windows ou Linux.
* Description : description d’utilisation pour donner des informations plus détaillées sur la raison pour laquelle définir l’image existe. Par exemple, vous pouvez avoir une définition de l’image pour votre serveur frontal qui a l’application préinstallée.
* CLUF - peut être utilisé pour pointer vers un contrat de licence utilisateur final spécifique à la définition de l’image.
* Notes de publication et déclaration de confidentialité - stocker des notes de publication et les déclarations de confidentialité dans le stockage Azure et fournir un URI pour y accéder en tant que partie de la définition de l’image.
* Fin de vie date : attacher une date de fin de vie à votre définition de l’image afin de pouvoir utiliser l’automatisation pour supprimer les anciennes définitions de l’image.
* Balise : vous pouvez ajouter des balises lorsque vous créez votre définition de l’image. Pour plus d’informations sur les balises, consultez [à l’aide de balises pour organiser vos ressources](../articles/azure-resource-manager/resource-group-using-tags.md)
* Au minimum et des processeurs virtuels maximum et des recommandations de la mémoire - si votre image comporte des recommandations de processeurs virtuels et de la mémoire, vous pouvez attacher ces informations à votre définition de l’image.
* Types de disque - rejetés vous pouvez fournir des informations sur les besoins de stockage pour votre machine virtuelle. Par exemple, si l’image n’est pas adaptée pour les disques HDD standard, ajoutez-les à la liste d’interdiction.


## <a name="regional-support"></a>Prise en charge régionale

Régions de la source sont répertoriées dans le tableau ci-dessous. Toutes les régions publiques peuvent être des régions cibles, mais pour répliquer dans le centre de l’Australie et en Australie centrale 2 vous devez disposer d’autorisation de votre abonnement. Pour demander la mise en liste verte, accédez à : https://www.microsoft.com/en-au/central-regions-eligibility/


| Régions de code source |
|---------------------|-----------------|------------------|-----------------|
| Centre de l’Australie   | EUAP USA Centre | Centre de la Corée    | Sud du Royaume-Uni 2      |
| Centre de l’Australie 2 | Asie Est       | Corée du Sud      | Ouest du Royaume-Uni         |
| Australie Est      | USA Est         | USA Centre Nord | USA Centre-Ouest |
| Australie Sud-Est | USA Est 2       | Europe Nord     | Europe Ouest     |
| Brésil Sud        | (Euap) de l’est des États-Unis 2  | USA Centre Sud | Inde Ouest      |
| Centre du Canada      | France Centre  | Inde Sud      | USA Ouest         |
| Est du Canada         | France Sud    | Asie du Sud-Est   | USA Ouest         |
| Inde Centre       | Japon Est      | Nord du Royaume-Uni         | USA Ouest 2       |
| USA Centre          | Japon Ouest      | Sud du Royaume-Uni         |                 |



## <a name="limits"></a>limites 

Il existe des limites, par abonnement, pour le déploiement de ressources à l’aide des galeries d’images partagé :
- galeries d’images partagé 100, par abonnement, par région
- définitions d’image 1 000, par abonnement, par région
- versions d’image 10 000, par abonnement, par région

Pour plus d’informations, consultez [vérifier l’utilisation des ressources par rapport aux limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits) pour obtenir des exemples sur la façon de vérifier votre utilisation actuelle.
 

## <a name="scaling"></a>Mise à l'échelle
La galerie d’images partagées vous permet de spécifier le nombre de réplicas qu’Azure doit conserver pour les images. De cette façon, dans les scénarios de déploiement multimachines virtuelles, les déploiements de machines virtuelles peuvent être répartis sur différents réplicas pour réduire le risque de limitation du traitement de création d’instances liée à la surcharge d’un seul réplica.


Avec la galerie d’images partagé, vous pouvez désormais déployer jusqu'à un 1 000 instances de machine virtuelle dans un jeu de mise à l’échelle de machine virtuelle (en haut à partir de 600 avec des images managées). Fournissent des réplicas de l’image pour la cohérence, de fiabilité et de meilleures performances de déploiement.  Vous pouvez définir un nombre de réplicas différents dans chaque région cible, selon les besoins de mise à l’échelle pour la région. Étant donné que chaque réplica est une copie complète de votre image, cela permet de mettre à l’échelle vos déploiements de façon linéaire avec chaque réplica supplémentaire. Bien que nous ne comprenons aucuns deux images ou régions sont identiques, voici notre règle générale sur l’utilisation des réplicas dans une région :

- Pour chaque 20 machines virtuelles que vous créez simultanément, nous vous recommandons de que conserver un réplica. Par exemple, si vous créez des machines 120 virtuelles simultanément à l’aide de la même image dans une région, nous vous conseillons de que conserver au moins 6 réplicas de votre image. 
- Pour chaque déploiement identiques avec des instances jusqu'à 600, nous vous recommandons de que conserver au moins un réplica. Par exemple, si vous créez 5 identiques simultanément, chacune avec 600 instances de machine virtuelle à l’aide de la même image dans une seule région, nous vous conseillons de que conserver au moins 5 réplicas de votre image. 

Nous recommandons toujours à surprovisionner le nombre de réplicas en raison de facteurs tels que la taille de l’image, de contenus et de type de système d’exploitation.


![Graphique montrant comment mettre à l’échelle des images](./media/shared-image-galleries/scaling.png)



## <a name="make-your-images-highly-available"></a>Rendre vos images hautement disponible

[Azure Zone Redundant Storage (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fournit une résilience contre une défaillance de la Zone de disponibilité dans la région. Avec la disponibilité générale de la galerie d’images partagé, vous pouvez choisir de stocker vos images dans les comptes ZRS dans des régions avec des Zones de disponibilité. 

Vous pouvez également choisir le type de compte pour chacune des régions cible. Le type de compte de stockage par défaut est Standard_LRS, mais vous pouvez choisir Standard_ZRS pour les régions avec des Zones de disponibilité. Vérifiez la disponibilité régionale de ZRS [ici](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Graphique montrant ZRS](./media/shared-image-galleries/zrs.png)


## <a name="replication"></a>Réplication
La galerie d’images partagées vous permet aussi de répliquer vos images sur d’autres régions Azure automatiquement. Chaque version de l’image partagée peut être répliquée sur différentes régions en fonction des besoins de votre organisation. Par exemple, vous pouvez répliquer la dernière image dans plusieurs régions tout en gardant les versions plus anciennes disponibles dans une seule région. De cette façon vous économisez sur les coûts de stockage des versions d’image partagée. 

Les régions sur lesquelles est répliquée une version d’image partagée peuvent être mises à jour après la création. La durée de réplication sur différentes régions dépend de la quantité de données copiées et du nombre de régions concernées. La réplication peut prendre plusieurs heures dans certains cas. Pendant le processus, vous pouvez voir l’état de la réplication par région. Une fois la réplication de l’image complète dans une région, vous pouvez ensuite déployer une machine virtuelle ou un ensemble d’échelle à l’aide de cette version de l’image dans la région.

![Graphique montrant comment répliquer des images](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access

Galerie d’images partagé, de la définition de l’Image et de la version de l’Image sont toutes les ressources, elles peuvent être partagées à l’aide du contrôle natif Azure RBAC intégrés. À l’aide de RBAC, vous pouvez partager ces ressources pour les autres utilisateurs principaux de service et les groupes. Vous pouvez même partager l’accès aux personnes en dehors du locataire, dans qu'ils ont été créés. Une fois qu’un utilisateur a accès à la version d’Image partagées, ils peuvent déployer une machine virtuelle ou un jeu de mise à l’échelle de Machine virtuelle.  La matrice de partage suivante vous aide à comprendre les éléments auxquels l’utilisateur a accès :

| Partagé avec l’utilisateur     | Galerie d’images partagées | Définition de l’image | Version de l’image |
|----------------------|----------------------|--------------|----------------------|
| Galerie d’images partagées | Oui                  | OUI          | Oui                  |
| Définition de l’image     | Non                    | OUI          | Oui                  |

Nous vous recommandons de partage au niveau de la galerie pour une expérience optimale. Nous vous déconseillons de partage de versions des images individuelles. Pour plus d’informations sur RBAC, consultez [gérer l’accès aux ressources Azure à l’aide de RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Images peuvent également être partagées, à grande échelle, même entre les clients à l’aide d’une inscription d’une application multi-locataire. Pour plus d’informations sur le partage d’images entre les locataires, consultez [partager des images de machine virtuelle de la galerie sur les locataires Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Facturation
L’utilisation du service Galerie d’images partagées n’engendre aucuns frais supplémentaires. Vous êtes facturé pour les ressources suivantes :
- Coûts de stockage des versions d’Image partagée. Coût varie selon le nombre de réplicas de la version de l’image et le nombre de régions d’à que la version est répliquée. Par exemple, si vous avez 2 les images et les deux sont répliquées sur 3 régions, puis vous sera modifié pour 6 disques gérés en fonction de leur taille. Pour plus d’informations, consultez [Tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).
- Frais de sortie de réseau pour la réplication de la première version de l’image à partir de la région source pour les régions répliquées. Les réplicas suivants sont gérés dans la région, il y a pas de frais supplémentaires. 

## <a name="updating-resources"></a>La mise à jour des ressources

Une fois créé, vous pouvez modifier certains les ressources de galerie d’image. Ceux-ci sont limités à :
 
Galerie d’images partagées :
- Description

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire recommandée
- Description
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclure de la dernière version
- Date de fin de vie


## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Les SDK suivants prennent en charge la création de galeries d’images partagées :

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.JS](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Modèles

Vous pouvez créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

**Q.** Comment lister toutes les ressources de galerie d’images partagées de différents abonnements ? 
 
 R. Pour lister toutes les ressources de galerie d’images partagées de différents abonnements auxquels vous avez accès sur le portail Azure, suivez les étapes ci-dessous :

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accédez à **Toutes les ressources**.
1. Sélectionnez tous les abonnements pour lesquels vous voulez lister toutes les ressources.
1. Recherchez des ressources de type **Galerie privée**.
 
   Pour voir les définitions d’image et les versions d’image, vous devez aussi sélectionner **Afficher les types masqués**.
 
   Pour lister toutes les ressources de galerie d’images partagées des différents abonnements sur lesquels vous avez des autorisations, utilisez la commande suivante dans Azure CLI :

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**Q.** Puis-je déplacer mon image existante vers la galerie d’images partagées ?
 
 R. Oui. Il existe 3 scénarios basés sur les types d’images.

 Scénario 1 : Si vous avez une image managée, vous pouvez créer une définition et une version de cette image.

 Scénario 2 : Si vous avez une image généralisée non managée, vous pouvez créer une image managée de l’image, puis créer une définition et une version de cette image. 

 Scénario 3 : Si vous avez un VHD dans votre système de fichiers local, vous devez le charger, puis créer une image managée pour pouvoir ensuite créer une définition et une version de cette image.
- Si le VHD vient d’une machine virtuelle Windows, consultez [Charger un disque dur virtuel généralisé](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Si le VHD est pour une machine virtuelle Linux, consultez [Charger un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**Q.** Puis-je créer une version d’image à partir d’un disque spécialisé ?

 R. Non, nous ne prenons pas encore en charge les disques spécialisés sous forme d’images. Si vous avez un disque spécialisé, vous devez [créer une machine virtuelle à partir du disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) en attachant le disque spécialisé à une nouvelle machine virtuelle. Une fois la machine virtuelle en cours d’exécution, suivez les instructions pour créer une image managée à partir de la [machine virtuelle Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Dès que vous avez une image managée généralisée, vous pouvez démarrer le processus de création d’une description et d’une version de l’image partagée.

 
**Q.** Une fois la ressource de galerie d’images partagées créée, puis-je la déplacer vers un autre abonnement ?

 R. Non, vous ne pouvez pas déplacer la ressource de galerie d’images partagées vers un autre abonnement. Toutefois, vous pouvez répliquer les versions d’image dans la galerie vers d’autres régions selon vos besoins.

**Q.** Puis-je répliquer mes versions d’image entre clouds (Azure China 21Vianet, Azure Allemagne et Azure Government Cloud) ? 

 R. Non, vous ne pouvez pas répliquer les versions d’image entre clouds.

**Q.** Puis-je répliquer mes versions d’image entre abonnements ? 

 R. Non, vous pouvez répliquer les versions d’image entre régions dans un abonnement et les utiliser dans d’autres abonnements au moyen de RBAC.

**Q.** Puis-je partager des versions d’image entre locataires Azure AD ? 

 R. Oui, vous pouvez utiliser RBAC pour partager à des individus entre locataires. Mais, à partager à grande échelle, consultez « galerie de partage d’images entre les locataires Azure » à l’aide de [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**Q.** Combien de temps faut-il pour répliquer des versions d’image sur les régions cibles ?

 R. Le temps de réplication des versions d’image dépend entièrement de la taille de l’image et du nombre de régions cibles. Toutefois, une bonne pratique est de limiter la taille de l’image et de rapprocher les régions source et cible pour obtenir de meilleurs résultats. Vous pouvez vérifier l’état de la réplication à l’aide de l’indicateur -ReplicationStatus.


**Q.** Quelle est la différence entre la région source et la région cible ?

 R. La région source est la région dans laquelle votre version d’image est créée et les régions cibles sont les régions dans lesquelles vous stockez une copie de votre version d’image. Pour chaque version d’image, vous pouvez avoir seulement une région source. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier la région source pendant la création de la version d’image ?

 R. Quand vous créez une version d’image, vous pouvez utiliser l’étiquette **--location** dans l’interface CLI et **-Location** dans PowerShell pour spécifier la région source. Vérifiez que l’image managée que vous utilisez comme image de base pour créer la version d’image est dans le même emplacement que celui dans lequel vous voulez créer la version d’image. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier le nombre de réplicas de version d’image à créer dans chaque région ?

 R. Deux méthodes vous permettent de spécifier le nombre de réplicas de version d’image à créer dans chaque région :
 
1. Le nombre de réplicas régionaux, qui spécifie le nombre de réplicas que vous voulez créer par région. 
2. Le nombre de réplicas communs, qui est le nombre par défaut par région si le nombre de réplicas régionaux n’est pas spécifié. 

Pour spécifier le nombre de réplicas régional, passez à l’emplacement, ainsi que le nombre de réplicas que vous souhaitez créer dans cette région : « USA Centre Sud=2». 

Si le nombre de réplicas régionaux n’est pas spécifié avec chaque emplacement, le nombre de réplicas par défaut est le nombre de réplicas communs que vous avez spécifié. 

Pour spécifier le nombre de réplicas communs dans l’interface CLI, utilisez l’argument **--replica-count** dans la commande `az sig image-version create`.


**Q.** Puis-je créer la galerie d’images partagées dans un autre emplacement que celui où je veux créer la définition d’image et la version d’image ?

 R. Oui, vous pouvez. Mais, en tant que meilleure pratique, nous vous encourageons à conserver le groupe de ressources, Galerie d’images partagé, définition de l’image et version de l’image dans le même emplacement.


**Q.** Quel est le coût d’utilisation de la galerie d’images partagées ?

 R. L’utilisation du service Galerie d’images partagées n’engendre aucun coût, à l’exception des coûts de stockage des versions d’image et des coûts de sortie de réseau pour la réplication des versions d’image de la région source vers les régions cibles.

**Q.** Quelle version d’API utiliser pour créer une galerie d’images partagées, une définition d’image, une version d’image et une machine virtuelle/un groupe de machines virtuelles identiques à partir de la version d’image ?

 R. Pour déployer une machine virtuelle ou un groupe de machines virtuelles identiques à partir d’une version d’image, nous vous recommandons d’utiliser la version d’API 2018-04-01 ou une version ultérieure. Pour utiliser des galeries d’images partagées, des définitions d’image et des versions d’image, nous vous recommandons d’utiliser la version d’API 2018-06-01. Stockage redondant de zone (ZRS) requiert la version 2019-03-01 ou une version ultérieure.
