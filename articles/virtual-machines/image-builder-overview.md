---
title: En savoir plus sur Azure Image Builder (préversion)
description: Découvrez Azure Image Builder pour les machines virtuelles Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 0e72c35af1f1990527b0154d2ba47a45d3f8b8c9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425620"
---
# <a name="preview-azure-image-builder-overview"></a>Aperçu : Vue d’ensemble du Générateur d’images Azure

Des images de machine virtuelle standardisées permettent aux organisations de migrer vers le cloud en veillant à la cohérence des déploiements. Les images incluent généralement des paramètres de sécurité et de configuration prédéfinis et les logiciels nécessaires. La définition de votre propre pipeline d’acquisition d’images nécessite du temps, une infrastructure et une configuration. Toutefois, avec Azure VM Image Builder, il vous suffit de fournir une configuration décrivant votre image et de l’envoyer au service, après quoi l’image est générée et distribuée.
 
Azure VM Image Builder (Azure Image Builder) vous permet de partir d’une image de la Place de marché Azure basée sur Windows ou Linux ou d’images personnalisées existantes, et de commencer à y ajouter vos propres personnalisations. Étant donné qu’Image Builder repose sur [HashiCorp Packer](https://packer.io/), vous verrez des similitudes, mais vous bénéficiez d’un service managé. Vous pouvez également spécifier où vous souhaitez que vos images soient hébergées dans la [Galerie d’images partagées Azure](shared-image-galleries.md), en tant qu’images managées ou disque dur virtuel.

> [!IMPORTANT]
> Le Générateur d’images Azure est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Fonctionnalités de préversion

La préversion prend en charge les fonctionnalités suivantes :

- Création d’images de référence, qui incluent vos configurations minimales de sécurité et d’entreprise, et qui permettent aux services d’affiner leur personnalisation.
- Intégration des applications de base, de sorte que les machines virtuelles peuvent accepter les charges de travail après leur création, ou ajouter des configurations pour prendre en charge les images Windows Virtual Desktop.
- En corrigeant des images existantes, le Générateur d’images vous permet de corriger en permanence des images personnalisées.
- Connectez Image Builder à vos réseaux virtuels existants, afin de pouvoir vous connecter à des serveurs de configuration existants (DSC, Chef, Puppet, etc.), à des partages de fichiers ou à tout autre serveur/service routable.
- L’intégration avec la Galerie d’images partagées Azure vous permet de distribuer, de contrôler les versions, et de mettre à l’échelle des images globalement, et offre un système de gestion des images.
- L’intégration avec des pipelines de génération d’images existants vous permet d’appeler simplement le Générateur d’images à partir de votre pipeline ou d’utiliser la simple tâche Azure DevOps de prévisualisation du Générateur d’images.
- Migrez un pipeline de personnalisation d’image existant vers Azure. Utilisez vos scripts, commandes et processus existants pour personnaliser des images.
- Création d’images au format VHD pour prendre en charge Azure Stack.
 

## <a name="regions"></a>Régions
Le service Générateur d’images Azure sera disponible en préversion dans les régions suivantes. Des images peuvent être distribuées en dehors de ces régions.
- USA Est
- USA Est 2
- Centre-USA Ouest
- USA Ouest
- USA Ouest 2
- Europe Nord
- Europe Ouest

## <a name="os-support"></a>Prise en charge du système d’exploitation
Le Générateur d’images Azure prendra en charge les images de système d’exploitation de base de la Place de marché Azure :
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisession/Professional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Fonctionnement

Azure VM Image Builder est un service Azure complètement managé qui est accessible à un fournisseur de ressources Azure. Fournissez au service une configuration qui spécifie l’image source, la personnalisation à effectuer et l’emplacement où la nouvelle image doit être distribuée. Le diagramme ci-dessous présente un workflow général :

![Tracé conceptuel du processus Azure Image Builder présentant les sources (Windows/Linux), les personnalisations (Shell, PowerShell, Redémarrage et mise à jour de Windows, ajout de fichiers) et la distribution mondiale avec Azure Shared Image Gallery](./media/image-builder-overview/image-builder-flow.png)

Les configurations de modèle peuvent être passées en utilisant PowerShell, Az CLI, des modèles ARM et la tâche DevOps d’Azure VM Image Builder. Quand vous la soumettrez au service, nous créerons une ressource de modèle d’image. Une fois la ressource de modèle d’image créée, vous voyez un groupe de ressources d’indexation créé dans votre abonnement, au format : IT_\<DestinationResourceGroup> _\<TemplateName>_ \(GUID). Le groupe de ressources d’indexation contient des fichiers et des scripts référencés dans la personnalisation des fichiers, de l’interpréteur de commandes et de PowerShell dans la propriété ScriptURI.

Pour exécuter la build, vous appelez `Run` sur la ressource de modèle d’image. Le service déploie alors des ressources supplémentaires pour la build, comme une machine virtuelle, un réseau, un disque, une carte réseau, etc. Si vous générez une image sans utiliser un réseau virtuel existant, Image Builder déploie également une IP publique et un groupe de sécurité réseau. Le service se connecte à la machine virtuelle de la build à l’aide de SSH de WinRM. Si vous sélectionnez un réseau virtuel existant, le service est déployé avec Azure Private Link et aucune adresse IP publique n’est nécessaire. Pour plus d’informations sur le réseau Image Builder, consultez ces [détails](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking).

Une fois la build terminée, toutes les ressources sont supprimées, à l’exception du groupe de ressources d’indexation et du compte de stockage. Pour les supprimer, vous devez supprimer la ressource de modèle d’image. Vous pouvez également les conserver en vue de réexécuter la build.

Cette documentation contient plusieurs exemples et des guides pas à pas qui font référence à des modèles de configuration et à des solutions dans le [dépôt GitHub d’Azure image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Prise en charge du déplacement
La ressource de modèle d’image est immuable et contient des liens vers les ressources et le groupe de ressources d’indexation. Par conséquent, le déplacement de ce type de ressource n’est pas pris en charge. Si vous voulez déplacer la ressource de modèle d’image, vérifiez que vous disposez d’une copie du modèle de configuration (extrayez la configuration existante de la ressource si vous ne l’avez pas), créez une ressource de modèle d’image dans le nouveau groupe de ressources sous un nouveau nom, puis supprimez la ressource de modèle d’image précédente. 

## <a name="permissions"></a>Autorisations
Lorsque vous vous inscrivez à AIB, cela permet au service AIB de créer, de gérer et de supprimer un groupe de ressources intermédiaire (IT_ *), et d’y ajouter des ressources requise pour la génération d’image. Cette opération est effectuée par un nom de principal du service AIB rendu disponible dans votre abonnement lors d’une inscription réussie.

Pour permettre à Azure VM Image Builder de distribuer des images aux images gérées ou à une galerie d’images partagées, vous devez créer une identité attribuée par l’utilisateur Azure qui dispose des autorisations nécessaires pour lire et écrire des images. Si vous accédez au Stockage Azure, des autorisations sont nécessaires pour lire des conteneurs privés et publics.

Les autorisations sont expliquées plus en détail pour [PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-powershell)et [AZ CLI](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-cli).

## <a name="costs"></a>Coûts
Nous vous facturons des coûts de calcul, de mise en réseau et de stockage lors de la création, de la génération et du stockage d’images avec le Générateur d’images Azure. Ces coûts sont similaires à ceux associés à la création manuelle d’images personnalisées. Pour les ressources, vous êtes facturé conformément aux tarifs Azure qui vous sont appliqués. 

Pendant le processus de création d’image, les fichiers sont téléchargés et stockés dans le groupe de ressources `IT_<DestinationResourceGroup>_<TemplateName>`, ce qui implique des coûts de stockage modiques. Si vous ne souhaitez pas conserver ces ressources, supprimez le **modèle d’image** une fois l’image générée.
 
Le Générateur d’images crée une machine virtuelle de taille D1v2, ainsi que le stockage et la mise en réseau nécessaires pour la machine virtuelle. Ces ressources durent pendant le temps d’exécution du processus de génération, puis sont supprimées une fois l’image créée par le Générateur d’images. 
 
Le Générateur d’images Azure distribue l’image aux régions choisies, ce qui peut occasionner des frais de sortie de réseau.

## <a name="hyper-v-generation"></a>Hyper-V Génération
Le générateur Image Builder prend en charge uniquement en mode natif la création d’images Hyper-V de génération 1 (Gen1) dans Azure Shared Image Gallery (SIG) ou Managed Image. 
 
## <a name="next-steps"></a>Étapes suivantes 
 
Pour essayer le Générateur d’images Azure, voir les articles sur la génération d’images [Linux](./linux/image-builder.md) ou [Windows](./windows/image-builder.md).

