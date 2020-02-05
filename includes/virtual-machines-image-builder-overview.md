---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: ec1b77118f94501363d950d72a65a67ece79ff77
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748985"
---
Des images de machine virtuelle standardisées permettent aux organisations de migrer vers le cloud en veillant à la cohérence des déploiements. Les images incluent généralement des paramètres de sécurité et de configuration prédéfinis et les logiciels nécessaires. La mise en place de votre propre pipeline d’acquisition d’image nécessite du temps, une infrastructure et une configuration. En revanche, le Générateur d’images de machine virtuelle Azure génère une simple configuration décrivant votre image et l’envoie au service qui la produit et la distribue.
 
Le Générateur d’images de machine virtuelle Azure vous permet de partir d’une image de la Place de marché Azure basée sur Windows ou Linux, d’images personnalisées existantes ou d’une image ISO Red Hat Enterprise Linux (RHEL), et d’y ajouter vos propres personnalisations. Le Générateur d’images s’appuyant sur [HashiCorp Packer](https://packer.io/), vous pouvez également importer vos scripts fournisseurs de shell Packer existants. Vous pouvez également spécifier où vous souhaitez que vos images soient hébergées dans la [Galerie d’images partagées Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), en tant qu’images managées ou disque dur virtuel.

> [!IMPORTANT]
> Le Générateur d’images Azure est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Fonctionnalités de préversion

La préversion prend en charge les fonctionnalités suivantes :

- Création d’images de base finales, qui incluent vos configurations minimales de sécurité et d’entreprise, et permettent aux services de les personnaliser en fonction de leurs besoins.
- En corrigeant des images existantes, le Générateur d’images vous permet de corriger en permanence des images personnalisées.
- L’intégration avec la Galerie d’images partagées Azure vous permet de distribuer, de contrôler les versions, et de mettre à l’échelle des images globalement, et offre un système de gestion des images.
- L’intégration avec des pipelines de génération d’images existants vous permet d’appeler simplement le Générateur d’images à partir de votre pipeline ou d’utiliser la simple tâche Azure DevOps de prévisualisation du Générateur d’images.
- Migrez un pipeline de personnalisation d’image existant vers Azure. Utilisez vos scripts, commandes et processus existants pour personnaliser des images.
- Utilisez le support Bring Your Own Subscription de Red Hat. Créez des images Red Hat Enterprise à utiliser avec vos abonnements Red Hat éligibles inutilisés.
- Création d’images au format VHD.
 

## <a name="regions"></a>Régions
Le service Générateur d’images Azure sera disponible en préversion dans les régions suivantes. Des images peuvent être distribuées en dehors de ces régions.
- USA Est
- USA Est 2
- Centre-USA Ouest
- USA Ouest
- USA Ouest 2

## <a name="os-support"></a>Prise en charge du système d’exploitation
Le Générateur d’images Azure prendra en charge les images de système d’exploitation de base de la Place de marché Azure :
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Entreprise/Professionnel/Entreprise pour le bureau virtuel (EVD) 
- Windows 2016
- Windows 2019

Le Générateur d’images Azure prendra en charge les images ISO Red Hat Enterprise Linux (RHEL) en tant que sources pour :
- RHEL 7.3
- RHEL 7.4
- RHEL 7.5

Les fichiers ISO RHEL 7.6 ne sont pas pris en charge, mais sont en cours de test.

## <a name="how-it-works"></a>Fonctionnement


![Schéma conceptuel du Générateur d’images Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Le Générateur d’images Azure est un service Azure entièrement managé accessible à un fournisseur de ressources Azure. Le processus du Générateur d’images Azure comporte trois parties principales, le sourçage, la personnalisation et la distribution, représentés dans un modèle. Le diagramme ci-dessous présente les composants avec certaines de leurs propriétés. 
 


**Processus du Générateur d’images** 

![Schéma conceptuel du processus du Générateur d’images Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Créez le modèle d’image en tant que fichier .json. Ce fichier .json contient des informations sur la source, les personnalisations et la distribution de l’image. Plusieurs exemples son disponibles dans le [référentiel GitHub du Générateur d’images Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Envoyez-le au service. Cela a pour effet de créer un artefact de modèle d’image dans le groupe de ressources que vous spécifiez. En arrière-plan, le Générateur d’images télécharge l’image ou l’ISO source et les scripts en fonction des besoins. Celles-ci sont stockées dans un groupe de ressources distinct créé automatiquement dans votre abonnement, au format suivant : IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Une fois le modèle d’image créé, vous pouvez générer l’image. Dans l’image d’arrière-plan, le générateur utilise le modèle et les fichiers sources pour créer une machine virtuelle (taille par défaut : Standard_D1_v2), le réseau, l’IP publique, le groupe de sécurité réseau et le stockage dans le groupe de ressources IT_\<Groupe_de_ressources_destination>_\<Nom_modèle>.
1. Dans le cadre de la création d’image, le Générateur d’images distribue l’image en fonction du modèle, puis supprime les ressources supplémentaires dans le groupe de ressources IT_\<DestinationResourceGroup>_\<TemplateName> créé pour le processus.


## <a name="permissions"></a>Autorisations

Pour permettre au Générateur d’images de machine virtuelle Azure de distribuer des images vers les images gérées ou une galerie d’images partagées, vous devrez fournir des autorisations « Contributeur » pour le service « Générateur d’images Azure Virtual Machine » (ID d’application : cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) sur les groupes de ressources. 

Si vous utilisez une image managée personnalisée ou une version d’image existantes, le Générateur d’images Azure a besoin au minimum d’un accès « Lecteur » à ces groupes de ressources.

Vous pouvez accorder l’accès à l’aide d’Azure CLI :

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Vous pouvez accorder l’accès à l’aide de PowerShell :

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Si le compte de service est introuvable, cela peut signifier que l’abonnement auquel vous ajoutez l’attribution de rôle n’est pas encore inscrit pour le fournisseur de ressources.


## <a name="costs"></a>Coûts
Nous vous facturons des coûts de calcul, de mise en réseau et de stockage lors de la création, de la génération et du stockage d’images avec le Générateur d’images Azure. Ces coûts sont similaires à ceux associés à la création manuelle d’images personnalisées. Pour les ressources, vous êtes facturé conformément aux tarifs Azure qui vous sont appliqués. 

Pendant le processus de création d’image, les fichiers sont téléchargés et stockés dans le groupe de ressources `IT_<DestinationResourceGroup>_<TemplateName>`, ce qui implique des coûts de stockage modiques. Si vous ne souhaitez pas conserver ces ressources, supprimez le **modèle d’image** une fois l’image générée.
 
Le Générateur d’images crée une machine virtuelle de taille D1v2, ainsi que le stockage et la mise en réseau nécessaires pour la machine virtuelle. Ces ressources durent pendant le temps d’exécution du processus de génération, puis sont supprimées une fois l’image créée par le Générateur d’images. 
 
Le Générateur d’images Azure distribue l’image aux régions choisies, ce qui peut occasionner des frais de sortie de réseau.
 
## <a name="next-steps"></a>Étapes suivantes 
 
Pour essayer le Générateur d’images Azure, voir les articles sur la génération d’images [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows](../articles/virtual-machines/windows/image-builder.md).
 
 
