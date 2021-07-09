---
title: Mises à jour du service de pool d’hôtes Azure Virtual Desktop (classique) - Azure
description: Apprenez à créer un pool d’hôtes de validation dans Azure Virtual Desktop (classique) pour superviser les mises à jour de service avant de déployer les mises à jour en production.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: bca4d55d0c5abb4e133a8eef8b51a2a5ae57b5d5
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749828"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-azure-virtual-desktop-classic"></a>Tutoriel : Créer un pool d’hôtes pour valider les mises à jour du service dans Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop Azure Resource Manager, consultez [cet article](../create-validation-host-pool.md).

Les pools d’hôtes sont des ensembles d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Azure Virtual Desktop. Nous vous recommandons de créer un pool d’hôtes de validation dans lequel les mises à jour de service seront appliquées en premier. Cela vous permet de superviser les mises à jour de service avant que le service ne les applique à votre environnement standard ou non validé. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui introduisent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement de production.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement non validé. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu’au pool d’hôtes standard. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes du pool d’hôtes de validation avec la [fonctionnalité de diagnostic](diagnostics-role-service-2019.md) ou les [articles de résolution des problèmes Azure Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/). Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

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

Maintenant que vous avez créé un pool d’hôtes de validation, vous pouvez apprendre à utiliser Azure Service Health pour superviser votre déploiement Azure Virtual Desktop.

> [!div class="nextstepaction"]
> [Configurer des alertes de service](set-up-service-alerts-2019.md)
