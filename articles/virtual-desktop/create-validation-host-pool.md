---
title: Mises à jour du service de pool d’hôtes Azure Virtual Desktop – Azure
description: Comment créer un pool d’hôtes de validation pour surveiller les mises à jour de service avant de déployer les mises à jour en production.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 29a86a476737df6f5a2787748c5551953984f6b6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261592"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutoriel : Créer un pool d’hôtes pour valider les mises à jour de service

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (Classic) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Les pools d’hôtes consistent en une collection d’une ou plusieurs machines virtuelles identiques situées dans un environnement Azure Virtual Desktop. Nous vous recommandons vivement de créer un pool d’hôtes de validation dans lequel les mises à jour de service seront appliquées en premier. Les pools d’hôtes de validation vous permettent de superviser les mises à jour de service avant que celui-ci ne les applique à votre environnement standard ou de non-validation. Sans pool d’hôtes de validation, vous risquez de ne pas détecter les modifications qui génèrent des erreurs, ce qui peut entraîner des temps d’arrêt pour les utilisateurs de votre environnement standard.

Pour veiller à ce que vos applications fonctionnent avec les dernières mises à jour, le pool d’hôtes de validation doit également être aussi semblable que possible aux pools d’hôtes de votre environnement non validé. Les utilisateurs sont invités à se connecter aussi fréquemment au pool d’hôtes de validation qu’au pool d’hôtes standard. Si vous disposez de tests automatisés sur votre pool d’hôtes, vous devez inclure ces tests au pool d’hôtes de validation.

Vous pouvez déboguer les problèmes du pool d’hôtes de validation avec la [fonctionnalité de diagnostic](./troubleshoot-set-up-overview.md) ou les [articles de résolution des problèmes Azure Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> Nous vous recommandons d'utiliser le pool d’hôtes de validation pour tester toutes les mises à jour à venir.

>[!IMPORTANT]
>Des problèmes se posent actuellement pour activer et désactiver les environnements de validation avec l’intégration entre Azure Virtual Desktop et Azure Resource Management. Nous mettrons à jour cet article quand nous aurons résolu le problème.

## <a name="create-your-host-pool"></a>Créer votre pool d'hôtes

Vous pouvez configurer n’importe quel pool d’hôtes personnel ou groupé existant en tant que pool d’hôtes de validation. Vous pouvez également créer un pool d’hôtes à utiliser pour la validation en suivant les instructions de l’un des articles suivants :
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure ou Azure CLI](create-host-pools-azure-marketplace.md)
- [Créer un pool d’hôtes avec PowerShell ou Azure CLI](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Définir votre pool d’hôtes en tant que pool d’hôtes de validation

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour utiliser le portail Azure pour configurer votre pool d’hôtes de validation :

1. Connectez-vous au portail Azure sur <https://portal.azure.com>.
2. Recherchez et sélectionnez **Azure Virtual Desktop**.
3. Sur la page Azure Virtual Desktop, sélectionnez **Pools d’hôtes**.
4. Sélectionnez le nom du pool d’hôtes que vous souhaitez modifier.
5. Sélectionner **Propriétés**.
6. Dans le champ d’environnement de validation, sélectionnez **Oui** pour activer l’environnement de validation.
7. Sélectionnez **Enregistrer** pour appliquer les nouveaux paramètres.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Si vous ne l’avez pas déjà fait, suivez les instructions fournies dans [Configurer le module PowerShell Azure Virtual Desktop](powershell-module.md) pour configurer votre module PowerShell et vous connecter à Azure.

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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si vous ne l’avez pas déjà fait, préparez votre environnement pour Azure CLI et connectez-vous.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pour définir le nouveau pool d’hôtes comme pool d’hôtes de validation, utilisez la commande [az desktopvirtualization hostpool update](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_update) :

```azurecli
az desktopvirtualization hostpool update --name "MyHostPool" \
    --resource-group "MyResourceGroup" \
    --validation-environment true
```

Utilisez la commande suivante pour vérifier que la propriété de validation a été définie.

```azurecli
az desktopvirtualization hostpool show --name "MyHostPool" \
    --resource-group "MyResourceGroup" 
```
---

## <a name="update-schedule"></a>Mettre à jour la planification

Les mises à jour de service sont mensuelles. S’il existe des problèmes majeurs, les mises à jour critiques sont fournies à un rythme plus fréquent.

S’il existe des mises à jour de service, vérifiez qu’au moins quelques utilisateurs se connectent chaque jour pour valider l’environnement. Nous vous recommandons de consulter régulièrement notre site [TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) et de suivre les publications avec WVDUPdate pour rester informé des mises à jour de service.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un pool d’hôtes de validation, vous pouvez apprendre à utiliser Azure Service Health pour superviser votre déploiement Azure Virtual Desktop.

> [!div class="nextstepaction"]
> [Configurer des alertes de service](./set-up-service-alerts.md)