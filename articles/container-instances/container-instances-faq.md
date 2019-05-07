---
title: Azure Container Instances - Forum aux questions
description: Réponses aux questions fréquemment posées relatives au service Azure Container Instances
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079014"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Forum aux questions sur Azure Container Instances

Cet article traite des questions fréquemment posées sur Azure Container Instances.

## <a name="deployment"></a>Déploiement

### <a name="how-large-can-my-container-image-be"></a>La taille peut être mon image de conteneur ?

La taille maximale d’une image de conteneur pouvant être déployées sur Azure Container Instances est de 15 Go. Vous pouvez être en mesure de déployer des images de grande taille en fonction de la disponibilité exacte au moment où vous déployez, mais cela n’est pas garanti.

La taille de votre image de conteneur a un impact sur la durée nécessaire pour déployer, généralement que vous souhaitez conserver vos images de conteneur aussi réduite que possible.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Comment puis-je accélérer le déploiement de mon conteneur ?

Étant donné qu’un des principaux facteurs déterminant de temps de déploiement est la taille de l’image, recherchez des moyens de réduire la taille. Supprimer les couches, vous n’avez besoin, ou réduire la taille des couches dans l’image (en choisissant une image de système d’exploitation de base plus claire). Par exemple, si vous exécutez des conteneurs Linux, envisagez d’utiliser Alpine comme votre image de base au lieu d’un serveur Ubuntu complet. De même, pour les conteneurs Windows, utilisez une image de base Nano Server si possible. 

Vous devez également vérifier la liste des images préalablement mis en cache dans les Images de conteneur Azure, disponible via le [liste mise en cache des Images](/rest/api/container-instances/listcachedimages) API. Vous pouvez être en mesure d’extraire un calque d’image pour l’une des images préalablement mis en cache. 

En savoir plus [conseils détaillés](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) à la réduction des temps de démarrage de conteneur.

### <a name="what-windows-base-os-images-are-supported"></a>Les images de base du système d’exploitation Windows sont prises en charge ?

#### <a name="windows-server-2016-base-images"></a>Images de base Windows Server 2016

* [NANO Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Les images de Windows basées sur canal semi-annuel version 1709 ou 1803 ne sont pas prises en charge.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 et le client les images de base (version préliminaire)

* [NANO Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Les couches d’image .NET ou .NET Core dois-je utiliser dans mon conteneur ? 

Utiliser l’image plus petite qui répond à vos besoins. Pour Linux, vous pouvez utiliser un *runtime-alpine* image .NET Core, ce qui a été pris en charge depuis la version de .NET Core 2.1. Pour Windows, si vous utilisez le .NET Framework complet, puis vous devez utiliser une image Windows Server Core (image runtime, tels que *4.7.2-windowsservercore-ltsc2016*). Runtime uniquement des images sont plus petits, mais ne prennent pas en charge les charges de travail qui nécessitent le SDK .NET.

## <a name="availability-and-quotas"></a>Quotas et disponibilité

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Le nombre de cœurs et de mémoire dois-je allouer à mes conteneurs ou le groupe de conteneurs ?

Cela dépend vraiment de votre charge de travail. Commencez petit et tester les performances pour voir comment vos conteneurs. [Surveiller l’utilisation des ressources processeur et mémoire](container-instances-monitor.md), puis ajoutez la mémoire en fonction du type de processus que vous déployez dans le conteneur ou des cœurs. 

Veillez également à vérifier le [la disponibilité des ressources](container-instances-region-availability.md#availability---general) pour la région que vous déployez pour les limites supérieures sur les cœurs de processeur et mémoire disponible par groupe de conteneurs. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Quelle infrastructure sous-jacente ACI exécute-t-elle ?

Azure Container Instances vise à être un service de conteneurs-on-demand sans serveur, afin de pouvoir se concentrer sur le développement de vos conteneurs et nous ne vous inquiétez pas sur l’infrastructure ! Pour ceux qui vous intéresse, ou si vous pour effectuer des comparaisons sur les performances, ACI s’exécute sur des ensembles de machines virtuelles Azure de différentes UGS, principalement à partir de F et de la série D. Nous pensons que cette option pour changer à l’avenir que nous continuons à développer et optimiser le service. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Je souhaite déployer des milliers de cœurs sur ACI : puis-je augmenter le quota ?
 
Oui (parfois). Consultez le [quotas et limites](container-instances-quotas.md) article pour les quotas actuels et les limites peuvent être augmentées par demande.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Puis-je déployer avec plus de 4 cœurs et 16 Go de RAM ?

Pas encore. Actuellement, ce sont les valeurs maximales pour un groupe de conteneurs. Contactez le Support Azure avec des exigences spécifiques ou des demandes. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quand sera-t-il ACI dans une région spécifique ?

Disponibilité de région actuelle est publiée [ici](container-instances-region-availability.md#availability---general), et des informations à jour soient disponibles via le [les fonctionnalités de liste](/rest/api/container-instances/listcapabilities) API. Si vous avez besoin pour une région spécifique, contactez le Support Azure.

## <a name="features-and-scenarios"></a>Scénarios et fonctionnalités

### <a name="how-do-i-scale-a-container-group"></a>Comment pour modifier un groupe de conteneurs ?

Actuellement, la mise à l’échelle n’est pas disponible pour les conteneurs ou des groupes de conteneurs. Si vous avez besoin d’exécuter plusieurs instances, utilisez notre API pour automatiser et de créer plus de demandes de création d’un groupe conteneur pour le service. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quelles sont les fonctionnalités disponibles pour les instances en cours d’exécution dans un réseau virtuel personnalisé ?

Vous pouvez déployer des groupes de conteneurs dans un réseau virtuel Azure de votre choix et déléguer des adresses IP privées pour les groupes de conteneurs pour acheminer le trafic au sein du réseau virtuel pour vos ressources Azure. Déploiement d’un groupe de conteneurs dans un réseau virtuel est actuellement en version préliminaire, et certains aspects de cette fonctionnalité peuvent changer avant la disponibilité générale (GA). Consultez [afficher un aperçu des limitations](container-instances-vnet.md#preview-limitations) pour les informations mises à jour.

## <a name="pricing"></a>Tarifs

### <a name="when-does-the-meter-start-running"></a>Quand le compteur commence en cours d’exécution ?

Durée du groupe conteneur est calculée à partir de l’heure à laquelle nous commencerons à extraire l’image de votre premier conteneur (pour un nouveau déploiement) ou de votre groupe de conteneurs est redémarré (s’il est déjà déployé), jusqu'à ce que le groupe de conteneurs est arrêté. D’informations, consultez [tarification Container Instances](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Arrêter facturé lorsque mes conteneurs sont arrêtés ?

Compteurs arrêter l’exécution une fois que votre groupe de l’intégralité du conteneur est arrêté. Tant qu’un conteneur dans votre groupe de conteneurs est en cours d’exécution, nous organisons les ressources au cas où vous souhaitez démarrer à nouveau les conteneurs. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus](container-instances-overview.md) sur Azure Container Instances.
* [Résoudre les problèmes courants](container-instances-troubleshooting.md) dans Azure Container Instances.