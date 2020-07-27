---
title: Mises à jour du service de pool d’hôtes Windows Virtual Desktop (automne 2019) - Azure
description: Découvrez comment créer un pool d’hôtes de validation pour superviser les mises à jour de service avant de déployer les mises à jour en production.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 806c3396d9188ea6abc5f779a26d99247d802ebe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527580"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-fall-2019-release"></a>Tutoriel : Créer un pool d’hôtes pour valider les mises à jour de service (version d’automne 2019)

>[!IMPORTANT]
>Ce contenu s’applique à la version Automne 2019 qui ne prend pas en charge les objets Azure Resource Manager Windows Virtual Desktop. Si vous essayez de gérer les objets Azure Resource Manager Windows Virtual Desktop introduits dans la mise à jour Printemps 2020, consultez [cet article](../create-validation-host-pool.md).

Les pools d'hôtes sont des ensembles d'une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop. Avant de déployer des pools d’hôtes dans votre environnement de production, nous vous recommandons vivement de créer un pool d’hôtes de validation. Les mises à jour sont d'abord appliquées aux pools d’hôtes de validation, ce qui vous permet de surveiller les mises à jour de service avant de les déployer dans votre environnement de production. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui introduisent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement de production.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement de production. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu'au pool d’hôtes de production. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes dans le pool d’hôtes de validation à l'aide de [la fonctionnalité de diagnostic](diagnostics-role-service-2019.md) ou des [articles de résolution des problèmes Windows Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/). Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Créer votre pool d'hôtes

Vous pouvez créer un pool d'hôtes en suivant les instructions de ces articles :
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace-2019.md)
- [Créer un pool d’hôtes avec le modèle Azure Resource Manager](create-host-pools-arm-template.md)
- [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Définir votre pool d’hôtes en tant que pool d’hôtes de validation

Exécutez les cmdlets PowerShell suivantes pour définir le nouveau pool d’hôtes en tant que pool d’hôtes de validation. Remplacez les valeurs entre guillemets par les valeurs correspondant à votre session :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Exécutez la cmdlet PowerShell suivante pour vérifier que la propriété de validation a été définie. Remplacez les valeurs entre guillemets par les valeurs correspondant à votre session.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Les résultats de la cmdlet doivent se présenter comme suit :

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Mettre à jour la planification

Les mises à jour de service sont mensuelles. S’il existe des problèmes majeurs, les mises à jour critiques sont fournies à un rythme plus fréquent.

## <a name="next-steps"></a>Étapes suivantes

Un pool d’hôtes de validation étant créé, vous pouvez apprendre à utiliser Azure Service Health pour superviser votre déploiement de Windows Virtual Desktop. 

> [!div class="nextstepaction"]
> [Configurer des alertes de service](set-up-service-alerts-2019.md)
