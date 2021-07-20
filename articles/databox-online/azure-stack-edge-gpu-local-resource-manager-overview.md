---
title: Présentation d’Azure Resource Manager local sur un appareil Azure Stack Edge Pro avec GPU
description: Offre une vue d’ensemble d’Azure Resource Manager local sur votre appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 8d200195540f92c88ae3dc93737ea09a461e6c26
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114486"
---
# <a name="what-is-local-azure-resource-manager-on-azure-stack-edge"></a>Présentation d’Azure Resource Manager local sur Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager fournit une couche de gestion qui vous permet de créer, mettre à jour et supprimer des ressources dans votre abonnement Azure. Les appareils Azure Stack Edge prennent en charge les mêmes API Azure Resource Manager pour créer, mettre à jour et supprimer des machines virtuelles dans un abonnement local. Cette prise en charge vous permet de gérer l’appareil d’une manière cohérente avec le cloud. 

Cet article fournit une vue d’ensemble d’Azure Resource Manager local qui permet de se connecter aux API locales sur vos appareils Azure Stack Edge.

## <a name="about-local-azure-resource-manager"></a>À propos d’Azure Resource Manager local

Azure Resource Manager local fournit une couche de gestion cohérente pour tous les appels à destination de l’appareil Azure Stack Edge via l’utilisation de modèles Resource Manager. Les avantages d’Azure Resource Manager local sont décrits dans les sections suivantes.

#### <a name="consistent-management-layer"></a>Couche de gestion cohérente

Azure Resource Manager local fournit une couche de gestion cohérente pour appeler les API d’appareil Azure Stack Edge et effectuer des opérations telles que la création, la mise à jour et la suppression de machines virtuelles. 

1. Lorsque vous envoyez une requête à partir d’API REST ou de SDK, Azure Resource Manager local sur l’appareil reçoit la demande. 
1. Azure Resource Manager local utilise le service d’émission de jeton de sécurité (STS) pour authentifier et autoriser la requête. STS est responsable de la création, de la validation, du renouvellement et de l’annulation des jetons de sécurité. STS crée les deux types de jetons de sécurité : les jetons d’accès et les jetons d’actualisation. Ces jetons sont utilisés à des fins de communication continue entre l’appareil et les clients qui accèdent à l’appareil via Azure Resource Manager local.
1. Resource Manager envoie ensuite la requête aux fournisseurs de ressources qui effectue l’action demandée.   

    Les fournisseurs de ressources pré-inscrits auprès d’Azure Stack Edge sont les suivants :

    - **Calcul** : `Microsoft.Compute` ou le fournisseur de ressources de calcul vous permet de déployer des machines virtuelles sur votre appareil Azure Stack Edge. Le fournisseur de ressources de calcul offre la possibilité de créer des machines virtuelles ainsi que des extensions de machines virtuelles. 

    - **Fournisseur de ressources de mise en réseau** : `Microsoft.Network` ou le fournisseur de ressources de mise en réseau vous permet de créer des ressources telles que des interfaces réseau et des réseaux virtuels.

    - **Fournisseur de ressources de stockage** : `Microsoft.Storage` ou le fournisseur de ressources de stockage propose un service de stockage cohérent et la gestion de comptes Key Vault à des fins de gestion et d’audit des secrets, comme les mots de passe et les certificats.  
    
    - **Fournisseur de ressources de disque** : `Microsoft.Disks` ou le fournisseur de ressources de disque vous permet de créer des disques managés qui peuvent être utilisés pour créer des machines virtuelles.

    Les ressources sont des éléments gérables qui disponibles via Azure Stack Edge, et les fournisseurs de ressources sont chargés de fournir des ressources. Par exemple, les machines virtuelles, les comptes de stockage et les réseaux virtuels sont des exemples de ressources. Le fournisseur de ressources de calcul, quant à lui, fournit la ressource de machine virtuelle.    

Comme toutes les demandes sont gérées via la même API, vous voyez des résultats cohérents et des capacités cohérentes dans tous les différents outils.

L’image suivante montre le mécanisme de gestion de toutes les requêtes d’API et le rôle joué par Azure Resource Manager local pour fournir une couche de gestion cohérente afin de gérer ces requêtes.

![Diagramme pour Azure Resource Manager.](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


#### <a name="use-of-resource-manager-templates"></a>Utilisation des modèles Resource Manager

Autre avantage clé d’Azure Resource Manager, il vous permet d’utiliser des modèles Resource Manager. Il s’agit de fichiers JSON (JavaScript Object Notation) dans une syntaxe déclarative qui peuvent être utilisés pour déployer des ressources de manière cohérente et répétée. La syntaxe déclarative vous permet de déclarer « Voici ce que je souhaite créer » sans devoir écrire la séquence de commandes de programmation pour le créer. Par exemple, vous pouvez utiliser ces modèles de syntaxe déclarative pour déployer des machines virtuelles sur vos appareils Azure Stack Edge. Pour plus d’informations, consultez [Déployer des machines virtuelles sur votre appareil Azure Stack Edge via des modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

## <a name="connect-to-the-local-azure-resource-manager"></a>Se connecter à Azure Resource Manager local

Pour créer des machines virtuelles, des partages ou des comptes de stockage sur votre appareil Azure Stack Edge, vous devez créer les ressources correspondantes. Par exemple, pour les machines virtuelles, vous devez disposer de ressources, telles que l’interface réseau, le système d’exploitation et les disques de données sur la machine virtuelle, à partir des fournisseurs de ressources de mise en réseau, de disque et de stockage. 

Pour demander la création de ressources à partir des fournisseurs de ressources, vous devez d’abord vous connecter à Azure Resource Manager local. Pour obtenir des instructions détaillées, consultez [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-connect-resource-manager.md).

La première fois que vous vous connectez à Azure Resource Manager, vous devez également réinitialiser votre mot de passe. Pour obtenir des instructions détaillées, consultez [Réinitialiser votre mot de passe Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md).


## <a name="azure-resource-manager-endpoints"></a>Points de terminaison Azure Resource Manager

Azure Resource Manager local et les services STS s’exécutent sur votre appareil et sont accessibles à des points de terminaison spécifiques. Le tableau suivant récapitule les différents points de terminaison exposés sur votre appareil par ces services, les protocoles pris en charge et les ports pour accéder à ces points de terminaison. 

| # | Point de terminaison | Protocoles pris en charge | Port utilisé | Utilisé pour |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Pour se connecter à Azure Resource Manager pour l’automatisation |
| 2. | Service d’émission de jeton de sécurité | https | 443 | Pour s’authentifier via des jetons d’accès et d’actualisation |


## <a name="next-steps"></a>Étapes suivantes

[Se connecter à Azure Resource Manager local sur votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
