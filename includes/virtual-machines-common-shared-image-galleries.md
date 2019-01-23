---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: c65fb1f0f635e79d594a7f080124827e3218f612
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193372"
---
La galerie d’images partagées est un service qui vous permet de structurer et d’organiser vos images de machine virtuelle managées personnalisées. Celle-ci vous permet de partager vos images avec différents utilisateurs, principaux de service ou groupes Active Directory au sein de votre organisation. Il est possible de répliquer des images partagées dans plusieurs régions, pour une mise à l’échelle plus rapide de vos déploiements.

Une image managée est une copie d’une machine virtuelle complète (y compris tous les disques de données attachés) ou simplement du disque de système d’exploitation, selon la façon dont vous créez l’image. Quand vous créez une machine virtuelle à partir de l’image, une copie des disques durs virtuels dans l’image est utilisée pour créer les disques de la nouvelle machine virtuelle. L’image managée reste dans le stockage et peut servir à créer des machines virtuelles à l’infini.

Si vous avez un grand nombre d’images managées à gérer et que vous voulez qu’elles soient disponibles pour toute l’entreprise, vous pouvez utiliser une galerie d’images partagées comme un dépôt qui facilite la mise à jour et le partage de vos images. Les frais d’utilisation d’une galerie d’images partagées correspondent simplement aux coûts de stockage des images, plus les coûts de sortie de réseau pour la réplication des images de la région source vers les régions publiées.

La fonctionnalité Galerie d’images partagées a plusieurs types de ressources :

| Ressource | Description|
|----------|------------|
| **Image managée** | Il s’agit d’une image de base pouvant être utilisée seule ou pour créer une **version de l’image**  dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définies dans une galerie et incluent des informations sur l’image et sur les exigences relatives à son utilisation en interne. Ces informations indiquent, par exemple, si l’image est Windows ou Linux, et comprennent les notes de publication et les exigences de mémoire maximale et minimale. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |

<br>


![Graphique montrant comment vous pouvez avoir plusieurs versions d’une image dans la galerie](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Prise en charge régionale

La prise en charge régionale pour les galeries d’images partagées est en préversion, mais elle doit se développer au fil du temps. Pour la préversion limitée, voici la liste des régions dans lesquelles vous pouvez créer des galeries et la liste des régions dans lesquelles vous pouvez répliquer n’importe quelle image de galerie : 

| Créer la galerie dans  | Répliquer la version vers |
|--------------------|----------------------|
| USA Centre-Ouest    |Toutes les régions publiques &#42;|
| USA Est 2          ||
| USA Centre Sud   ||
| Asie Sud-Est     ||
| Europe Ouest        ||
| USA Ouest            ||
| USA Est            ||
| Centre du Canada     ||
|                    ||



&#42; Pour répliquer dans les régions Australie Centre et Australie Centre 2, votre abonnement doit figurer en liste verte. Pour demander la mise en liste verte, accédez à : https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Mise à l'échelle
La galerie d’images partagées vous permet de spécifier le nombre de réplicas qu’Azure doit conserver pour les images. De cette façon, dans les scénarios de déploiement multimachines virtuelles, les déploiements de machines virtuelles peuvent être répartis sur différents réplicas pour réduire le risque de limitation du traitement de création d’instances liée à la surcharge d’un seul réplica.

![Graphique montrant comment mettre à l’échelle des images](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Réplication
La galerie d’images partagées vous permet aussi de répliquer vos images sur d’autres régions Azure automatiquement. Chaque version de l’image partagée peut être répliquée sur différentes régions en fonction des besoins de votre organisation. Par exemple, vous pouvez répliquer la dernière image dans plusieurs régions tout en gardant les versions plus anciennes disponibles dans une seule région. De cette façon vous économisez sur les coûts de stockage des versions d’image partagée. 

Les régions sur lesquelles est répliquée une version d’image partagée peuvent être mises à jour après la création. La durée de réplication sur différentes régions dépend de la quantité de données copiées et du nombre de régions concernées. La réplication peut prendre plusieurs heures dans certains cas. Pendant le processus, vous pouvez voir l’état de la réplication par région. Une fois la réplication de l’image effectuée dans une région, vous pouvez ensuite déployer une machine virtuelle ou un groupe de machines virtuelles identiques à partir de cette version d’image dans la région.

![Graphique montrant comment répliquer des images](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
Tout comme la Galerie d’images partagées, l’Image partagée et la version d’Image partagée sont des ressources qui peuvent être partagées à l’aide des contrôles RBAC d’Azure natifs intégrés. RBAC vous permet de partager ces ressources avec d’autres utilisateurs, principaux de service et groupes dans votre organisation. L’étendue de partage de ces ressources est limitée au même locataire Azure AD. Dès qu’un utilisateur a accès à la version d’image partagée, il peut déployer une machine virtuelle ou un groupe de machines virtuelles identiques dans un des abonnements auxquels il a accès, dans le même locataire Azure AD que celui de la version d’image partagée.  La matrice de partage suivante vous aide à comprendre les éléments auxquels l’utilisateur a accès :

| Partagé avec l’utilisateur     | Galerie d’images partagées | Image partagée | Version d’image partagée |
|----------------------|----------------------|--------------|----------------------|
| Galerie d’images partagées | Oui                  | OUI          | Oui                  |
| Image partagée         | Non                    | OUI          | Oui                  |
| Version d’image partagée | Non                    | Non            | Oui                  |



## <a name="billing"></a>Facturation
L’utilisation du service Galerie d’images partagées n’engendre aucuns frais supplémentaires. Vous êtes facturé pour les ressources suivantes :
- Coûts de stockage des versions d’Image partagée. Ils dépendent du nombre de réplicas de la version et du nombre de régions sur lesquelles la version est répliquée.
- Coûts de sortie de réseau pour la réplication de la version de la région source vers les régions répliquées.

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

**Q.** Comment s’inscrire à la préversion publique de la galerie d’images partagées ?
 
 R. Pour vous inscrire à la préversion publique de la galerie d’images partagées, vous devez exécuter les commandes suivantes à partir de chaque abonnement dans lequel vous voulez créer des ressources de galerie d’images partagées, de définition d’image ou de version d’image, et dans lequel vous prévoyez de déployer des machines virtuelles à partir des versions d’image.

**CLI** : 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell** : 

```powershell
Register-AzureRmProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**Q.** Comment partager mes images entre abonnements ?
 
 R. Vous pouvez partager des images entre abonnements à l’aide du contrôle d’accès en fonction du rôle (RBAC). Tout utilisateur avec des autorisations de lecture sur une version d’image, même dans plusieurs abonnements, peut déployer une machine virtuelle à partir de la version d’image.


**Q.** Puis-je déplacer mon image existante vers la galerie d’images partagées ?
 
 R. Oui. Il existe 3 scénarios basés sur les types d’images.

 Scénario 1 : Si vous avez une image managée, vous pouvez créer une définition et une version de cette image.

 Scénario 2 : Si vous avez une image généralisée non managée, vous pouvez créer une image managée de l’image, puis créer une définition et une version de cette image. 

 Scénario 3 : Si vous avez un VHD dans votre système de fichiers local, vous devez le charger, puis créer une image managée pour pouvoir ensuite créer une définition et une version de cette image. 
    - Si le VHD vient d’une machine virtuelle Windows, consultez [Charger un disque dur virtuel généralisé](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
    - Si le VHD est pour une machine virtuelle Linux, consultez [Charger un disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**Q.** Puis-je créer une version d’image à partir d’un disque spécialisé ?

 R. Non, nous ne prenons pas encore en charge les disques spécialisés sous forme d’images. Si vous avez un disque spécialisé, vous devez [créer une machine virtuelle à partir du disque dur virtuel](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) en attachant le disque spécialisé à une nouvelle machine virtuelle. Une fois la machine virtuelle en cours d’exécution, suivez les instructions pour créer une image managée à partir de la [machine virtuelle Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Dès que vous avez une image managée généralisée, vous pouvez démarrer le processus de création d’une description et d’une version de l’image partagée.


**Q.** Puis-je créer une galerie d’images partagées, une définition d’image et une version d’image dans le portail Azure ?

 R. Non, nous ne prenons pas en charge pour l’instant la création d’une ressource de galerie d’images partagées dans le portail Azure. Toutefois, nous prenons en charge la création des ressources de galerie d’images partagées dans l’interface CLI, les modèles et les SDK. PowerShell sera également bientôt pris en charge.

 
**Q.** Puis-je mettre à jour la définition ou la version de l’image que je viens de créer ? Quelles sont les informations que je peux modifier ?

 R. Les informations qui peuvent être mises à jour sur chacune des ressources sont indiquées ci-dessous :
 
Galerie d’images partagées :
- Description

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire
- Description
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclusion du dernier
- Date de fin de vie


**Q.** Une fois la ressource de galerie d’images partagées créée, puis-je la déplacer vers un autre abonnement ?

 R. Non, vous ne pouvez pas déplacer la ressource de galerie d’images partagées vers un autre abonnement. Toutefois, vous pouvez répliquer les versions d’image dans la galerie vers d’autres régions selon vos besoins.

**Q.** Puis-je répliquer mes versions d’image entre clouds (Azure China 21Vianet, Azure Allemagne et Azure Government Cloud) ? 

 R. Non, vous ne pouvez pas répliquer les versions d’image entre clouds.

**Q.** Puis-je répliquer mes versions d’image entre abonnements ? 

 R. Non, vous pouvez répliquer les versions d’image entre régions dans un abonnement et les utiliser dans d’autres abonnements au moyen de RBAC.

**Q.** Puis-je partager des versions d’image entre locataires Azure AD ? 

 R. Non, la galerie d’images partagées ne prend pas en charge pour l’instant le partage de versions d’image entre locataires Azure AD. Toutefois, vous pouvez utiliser la fonctionnalité Offres privées sur la Place de marché Azure pour le faire.


**Q.** Combien de temps faut-il pour répliquer des versions d’image sur les régions cibles ?

 R. Le temps de réplication des versions d’image dépend entièrement de la taille de l’image et du nombre de régions cibles. Toutefois, une bonne pratique est de limiter la taille de l’image et de rapprocher les régions source et cible pour obtenir de meilleurs résultats. Vous pouvez vérifier l’état de la réplication à l’aide de l’indicateur -ReplicationStatus.


**Q.** Combien de galeries d’images partagées puis-je créer dans un abonnement ?

 R. Le quota par défaut est le suivant : 
- 10 galeries d’images partagées par abonnement, par région
- 200 définitions d’image par abonnement, par région
- 2 000 versions d’image par abonnement, par région


**Q.** Quelle est la différence entre la région source et la région cible ?

 R. La région source est la région dans laquelle votre version d’image est créée et les régions cibles sont les régions dans lesquelles vous stockez une copie de votre version d’image. Pour chaque version d’image, vous pouvez avoir seulement une région source. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier la région source pendant la création de la version d’image ?

 R. Quand vous créez une version d’image, vous pouvez utiliser l’étiquette **--location** dans l’interface CLI et **-Location** dans PowerShell pour spécifier la région source. Vérifiez que l’image managée que vous utilisez comme image de base pour créer la version d’image est dans le même emplacement que celui dans lequel vous voulez créer la version d’image. Par ailleurs, vérifiez que vous passez l’emplacement de la région source comme l’une des régions cibles quand vous créez une version d’image.  


**Q.** Comment spécifier le nombre de réplicas de version d’image à créer dans chaque région ?

 R. Deux méthodes vous permettent de spécifier le nombre de réplicas de version d’image à créer dans chaque région :
 
1. Le nombre de réplicas régionaux, qui spécifie le nombre de réplicas que vous voulez créer par région. 
2. Le nombre de réplicas communs, qui est le nombre par défaut par région si le nombre de réplicas régionaux n’est pas spécifié. 

Pour spécifier le nombre de réplicas régionaux, passez l’emplacement avec le nombre de réplicas à créer dans cette région de la manière suivante : « USA Centre Sud=2». 

Si le nombre de réplicas régionaux n’est pas spécifié avec chaque emplacement, le nombre de réplicas par défaut est le nombre de réplicas communs que vous avez spécifié. 

Pour spécifier le nombre de réplicas communs dans l’interface CLI, utilisez l’argument **--replica-count** dans la commande `az sig image-version create`.


**Q.** Puis-je créer la galerie d’images partagées dans un autre emplacement que celui où je veux créer la définition d’image et la version d’image ?

 R. Oui, vous pouvez. Mais nous vous encourageons à conserver au même endroit le groupe de ressources, la galerie d’images partagées, la définition d’image et la version d’image.


**Q.** Quel est le coût d’utilisation de la galerie d’images partagées ?

 R. L’utilisation du service Galerie d’images partagées n’engendre aucun coût, à l’exception des coûts de stockage des versions d’image et des coûts de sortie de réseau pour la réplication des versions d’image de la région source vers les régions cibles.

**Q.** Quelle version d’API utiliser pour créer une galerie d’images partagées, une définition d’image, une version d’image et une machine virtuelle/un groupe de machines virtuelles identiques à partir de la version d’image ?

 R. Pour déployer une machine virtuelle ou un groupe de machines virtuelles identiques à partir d’une version d’image, nous vous recommandons d’utiliser la version d’API 2018-04-01 ou une version ultérieure. Pour utiliser des galeries d’images partagées, des définitions d’image et des versions d’image, nous vous recommandons d’utiliser la version d’API 2018-06-01. 
