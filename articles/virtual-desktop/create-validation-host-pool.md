---
title: Mises à jour du service de pool d'hôtes Windows Virtual Desktop - Azure
description: Comment créer un pool d’hôtes de validation pour surveiller les mises à jour de service avant de déployer les mises à jour en production.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 177763e7939de082faa0c83d2ab661292f0758b2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292649"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutoriel : Créer un pool d’hôtes pour valider les mises à jour de service

>[!IMPORTANT]
>Ce contenu s’applique à Windows Virtual Desktop avec des objets Windows Virtual Desktop Azure Resource Manager. Si vous utilisez la version Windows Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Les pools d'hôtes sont des ensembles d'une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop. Avant de déployer des pools d’hôtes dans votre environnement de production, nous vous recommandons vivement de créer un pool d’hôtes de validation. Les mises à jour sont d'abord appliquées aux pools d’hôtes de validation, ce qui vous permet de surveiller les mises à jour de service avant de les déployer dans votre environnement de production. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui introduisent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement de production.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement de production. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu'au pool d’hôtes de production. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes dans le pool d’hôtes de validation à l'aide de [la fonctionnalité de diagnostic](diagnostics-role-service.md) ou des [articles de résolution des problèmes Windows Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

>[!IMPORTANT]
>Windows Virtual Desktop avec l’intégration Azure Resource Management a actuellement des difficultés à activer et désactiver les environnements de validation. Nous mettrons à jour cet article quand nous aurons résolu le problème.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, suivez les instructions fournies dans [Configurer le module PowerShell Windows Virtual Desktop](powershell-module.md) pour configurer votre module PowerShell et vous connecter à Azure.

## <a name="create-your-host-pool"></a>Créer votre pool d'hôtes

Vous pouvez créer un pool d'hôtes en suivant les instructions de ces articles :
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace.md)
- [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Définir votre pool d’hôtes en tant que pool d’hôtes de validation

Exécutez les cmdlets PowerShell suivantes pour définir le nouveau pool d’hôtes en tant que pool d’hôtes de validation. Remplacez les valeurs entre crochets par les valeurs correspondant à votre session :

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Exécutez la cmdlet PowerShell suivante pour vérifier que la propriété de validation a été définie. Remplacez les valeurs entre crochets par les valeurs correspondant à votre session.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Les résultats de la cmdlet doivent se présenter comme suit :

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Mettre à jour la planification

Les mises à jour de service sont mensuelles. S’il existe des problèmes majeurs, les mises à jour critiques sont fournies à un rythme plus fréquent.

S’il existe des mises à jour de service, vérifiez que vous avez au moins un petit groupe d’utilisateurs qui se connectent chaque jour pour valider l’environnement. Nous vous recommandons de consulter régulièrement notre site [TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) et de suivre les publications avec WVDUPdate pour rester informé des mises à jour de service.

## <a name="next-steps"></a>Étapes suivantes

Un pool d’hôtes de validation étant créé, vous pouvez apprendre à utiliser Azure Service Health pour superviser votre déploiement de Windows Virtual Desktop. 

> [!div class="nextstepaction"]
> [Configurer des alertes de service](./set-up-service-alerts.md)
