---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: e1b3b5fe603072069cb3a19c7597fcc1872fefd7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111454"
---
Images de machine virtuelle standardisée (VM) permettent aux organisations de migrer vers le cloud et de garantir la cohérence dans les déploiements. Les images incluent généralement des paramètres de sécurité et de configuration prédéfinis et les logiciels nécessaires. Configurer votre propre pipeline de création d’images nécessite de temps, l’infrastructure et le programme d’installation, mais avec Azure VM Image Builder, simplement fournir une configuration simple décrivant votre image, envoyez-le au service et l’image est créé et distribué.
 
Azure VM Image Builder (Générateur d’images Azure) vous permet de commencer par un Windows ou image de place de marché Azure basés sur Linux, des images personnalisées existantes ou Red Hat Enterprise Linux (RHEL) ISO et commencer à ajouter vos propres personnalisations. Étant donné que le Générateur d’images s’appuie sur [HashiCorp Packer](https://packer.io/), vous pouvez également importer vos scripts de fournisseur Packer shell existants. Vous pouvez également spécifier où vous souhaitez que vos images hébergées dans la Azure Shared Galerie d’images (virtual-machines-common-shared-image-galleries.md), comme une image managée ou un disque dur virtuel.

> [!IMPORTANT]
> Générateur d’images Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Fonctionnalités préliminaires

La version préliminaire, ces fonctionnalités sont prises en charge :

- Création d’images de base finale, qui inclut votre sécurité minimale et les configurations d’entreprise et permettre aux services de personnaliser davantage pour leurs besoins.
- Mise à jour corrective d’images existantes, Générateur d’images vous permettra de correction en permanence des images personnalisées.
- Intégration avec la galerie d’images partagé Azure, vous permet de distribuer, version, et la mise à l’échelle d’images dans le monde entier, ainsi qu’un système de gestion d’image.
- Intégration avec l’image existante générer des pipelines, simplement appeler le Générateur d’images à partir de votre pipeline ou utilisez la tâche de DevOps Azure simple Preview Image Builder.
- Migrer un pipeline de personnalisation d’image existant vers Azure. Utiliser vos scripts existants, les commandes et les processus pour personnaliser des images.
- Utilisez la prise en charge de Red Hat Bring Your Own Subscription. Créer des images de Red Hat Enterprise pour une utilisation avec vos abonnements Red Hat éligibles et inutilisés.
- Création d’images au format VHD.
 

## <a name="regions"></a>Régions
Le Service de générateur d’images Azure sera disponible en version préliminaire dans ces régions. Les images peuvent être distribuées en dehors de ces régions.
- USA Est
- USA Est 2
- USA Centre-Ouest
- USA Ouest
- USA Ouest 2

## <a name="os-support"></a>Prise en charge du système d’exploitation
AIB prendra en charge des images de système d’exploitation de base de la place de marché Azure :
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Fonctionnement


![Schéma conceptuel de générateur d’images Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Le Générateur d’images Azure est un service Azure entièrement géré qui est accessible par un fournisseur de ressources Azure. Le processus de génération d’images Azure comporte trois parties principales : source, personnaliser et distribuer, ils sont représentés dans un modèle. Le diagramme ci-dessous illustre les composants, avec certaines de leurs propriétés. 
 


**Processus de générateur d’image** 

![Schéma conceptuel du processus de génération d’images Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Créer le modèle d’Image sous forme de fichier .json. Ce fichier .json contient des informations sur la source de l’image, les personnalisations et la distribution. Il existe plusieurs exemples dans le [référentiel GitHub de générateur d’Image Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Envoyer au service, cela créera un artefact de modèle d’Image dans le groupe de ressources que vous spécifiez. En arrière-plan, Générateur d’images télécharge l’image source ou ISO et les scripts en fonction des besoins. Ceux-ci sont stockés dans un groupe de ressources distinct est créé automatiquement dans votre abonnement, au format : IT_<DestinationResourceGroup>_<TemplateName>. 
1. Une fois le modèle d’Image est créé, vous pouvez ensuite générer l’image. Dans l’arrière-plan du Générateur d’images utilise les modèle et les fichiers sources pour créer une machine virtuelle, réseau et stockage dans le IT_<DestinationResourceGroup>_<TemplateName> groupe de ressources.
1. Dans le cadre de la création d’image, le Générateur d’images distribue l’image il en fonction du modèle, puis supprime les ressources supplémentaires dans le IT_<DestinationResourceGroup>_<TemplateName> groupe de ressources qui a été créé pour le processus.


## <a name="permissions"></a>Autorisations

Pour autoriser le Générateur d’images de machine virtuelle Azure distribuer des images aux images gérés ou à une galerie d’images partagé, vous devrez fournir les autorisations « Contributeur » pour le service « Générateur d’images Azure Virtual Machine » (ID d’application : cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) sur les groupes de ressources. 

Si vous utilisez une image managée personnalisée existante ou une version de l’image, le Générateur d’images Azure devez au minimum un accès « Lecteur » à ces groupes de ressources.

Vous pouvez affecter l’accès à l’aide de l’interface CLI :

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Si le compte de service est introuvable, cela peut signifier que l’abonnement où vous ajoutez l’attribution de rôle n’a pas encore inscrit pour le fournisseur de ressources.


## <a name="costs"></a>Coûts
Nous vous facturons certains calcul, de mise en réseau et les coûts de stockage lors de la création, de création et de stockage des images avec le Générateur d’images Azure. Ces coûts sont similaires aux coûts de création manuelle d’images personnalisées. Pour les ressources, vous êtes facturé au tarif de votre Azure. 

Pendant le processus de création d’image, les fichiers sont téléchargés et stockés dans le `IT_<DestinationResourceGroup>_<TemplateName>` groupe de ressources, ce qui implique des coûts de stockage petit. f, vous ne souhaitez pas conserver ces ressources, supprimer le modèle d’Image une fois la build d’image.
 
Générateur d’images crée une machine virtuelle à l’aide d’une taille de machine virtuelle D1v2 et le stockage et la mise en réseau nécessaires pour la machine virtuelle. Ces ressources sont valables pour la durée du processus de génération et seront supprimés une fois l’image de création d’Image Builder terminée. 
 
Générateur d’images Azure répartit l’image à votre régions choisies, ce qui peut entraîner des frais de sortie de réseau.
 
## <a name="next-steps"></a>Étapes suivantes 
 
Pour tester le Générateur d’images Azure, consultez les articles pour la création de [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows](../articles/virtual-machines/windows/image-builder.md) images.
 
 
