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
ms.openlocfilehash: b74b7f0b79ad4064d7133a19316d6aec6bd5ba3a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611566"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutoriel : Créer un pool d’hôtes pour valider les mises à jour de service

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les pools d'hôtes sont des ensembles d'une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop. Avant de déployer des pools d’hôtes dans votre environnement de production, nous vous recommandons vivement de créer un pool d’hôtes de validation. Les mises à jour sont d'abord appliquées aux pools d’hôtes de validation, ce qui vous permet de surveiller les mises à jour de service avant de les déployer dans votre environnement de production. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui introduisent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement de production.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement de production. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu'au pool d’hôtes de production. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes dans le pool d’hôtes de validation à l'aide de [la fonctionnalité de diagnostic](diagnostics-role-service.md) ou des [articles de résolution des problèmes Windows Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

>[!IMPORTANT]
>La mise à jour Printemps 2020 de Windows Virtual Desktop a actuellement des difficultés à activer et désactiver l’environnement de validation. Nous mettrons à jour cet article quand nous aurons résolu le problème.

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
