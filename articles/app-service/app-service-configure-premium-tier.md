---
title: Configurer le niveau PremiumV2
description: Découvrez comment améliorer les performances de vos applications web, mobiles et API dans Azure App Service en les passant au nouveau niveau tarifaire PremiumV2.
keywords: app service, azure app service, mise à l'échelle, évolutif, plan app service, coût d'app service
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672209"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurer le niveau PremiumV2 pour Azure App Service

Le nouveau niveau tarifaire **PremiumV2** offre des processeurs plus rapides, un stockage SSD et le double de ratio mémoire-cœur des niveaux tarifaires précédents. Avec cet avantage de performances, vous pouvez économiser de l’argent en exécutant vos applications sur moins d’instances. Dans cet article, vous allez apprendre à créer une application dans le niveau **PremiumV2** ou à monter en puissance une application en la passant au niveau **PremiumV2**.

## <a name="prerequisites"></a>Prérequis

Pour effectuer un scale-up d’une application en la passant au niveau **PremiumV2**, vous devez disposer d’une application Azure App Service qui s’exécute dans un niveau tarifaire inférieur à **PremiumV2** et dans un déploiement App Service prenant en charge PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilité de PremiumV2

Le niveau **PremiumV2** est disponible pour App Service sous _Windows_ et sous _Linux_.

**PremiumV2** est disponible dans la plupart des régions Azure. Pour savoir si ce niveau est disponible dans votre région, exécutez la commande Azure CLI suivante dans [Azure Cloud Shell](../cloud-shell/overview.md) :

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Créer une application dans le niveau PremiumV2

Le niveau tarifaire d’une application App Service est défini dans le [plan App Service](overview-hosting-plans.md) dans lequel elle s’exécute. Vous pouvez créer un plan App Service de façon séparée ou dans le cadre de la création d’une application.

Quand vous configurez le plan App Service dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, sélectionnez **Niveau tarifaire**. 

Sélectionnez **Production**, puis sélectionnez **P1V2**, **P2V2** ou **P3V2**, puis cliquez sur **Appliquer**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Si les options **P1V2**, **P2V2** et **P3V2** n’apparaissent pas ou sont grisées, il est probable que **PremiumV2** ne soit pas disponible dans le déploiement App Service sous-jacent qui contient le plan App Service. Pour plus d’informations, voir [Monter en puissance à partir d’une combinaison groupe de ressources/région non prise en charge](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Monter en puissance une application existante en la passant au niveau PremiumV2

Avant de mettre à l’échelle une application existante en la passant au niveau **PremiumV2**, vérifiez que **PremiumV2** est disponible. Pour plus d’informations, consultez [Disponibilité de PremiumV2](#availability). S’il n’est pas disponible, voir [Monter en puissance à partir d’une combinaison groupe de ressources/région non prise en charge](#unsupported).

Selon votre environnement d’hébergement, la montée en puissance peut nécessiter des étapes supplémentaires. 

Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez la page de votre application App Service.

Dans le volet de navigation de gauche dans la page de votre application App Service, sélectionnez **Monter en puissance (plan App Service)** .

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Sélectionnez **Production**, puis sélectionnez **P1V2**, **P2V2** ou **P3V2**, puis cliquez sur **Appliquer**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Si l’opération se termine sans erreur, la page de présentation de votre application montre que l’application s’exécute maintenant dans le niveau **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>En cas d’erreur

Certains plans App Service ne peuvent pas passer au niveau PremiumV2 si le déploiement App Service sous-jacent ne le prend pas en charge.  Pour plus d’informations, voir [Monter en puissance à partir d’une combinaison groupe de ressources/région non prise en charge](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Monter en puissance à partir d’une combinaison groupe de ressources/région non prise en charge

Si votre application s’exécute dans un déploiement App Service où **PremiumV2** n’est pas disponible, ou dans une région qui ne prend pas encore en charge **PremiumV2**, il vous faudra la redéployer pour tirer parti de **PremiumV2**.  Deux options s'offrent à vous :

- Créez un **nouveau** groupe de ressources, puis une **nouvelle** application et un nouveau plan App Service dans le **nouveau** groupe de ressources, en choisissant la région Azure souhaitée lors du processus de création.  Il est **impératif** de sélectionner le plan **PremiumV2** lors de la création du plan App Service.  C’est ce qui garantira que la combinaison du groupe de ressources, du plan App Service et de la région Azure entraînera la création du plan App Service dans un déploiement App Service prenant en charge **PremiumV2**.  Redéployez ensuite le code de votre application dans l’application et le plan App Service nouvellement créées. Si vous le souhaitez, vous pourrez par la suite descendre en puissance le plan App Service à partir du niveau **PremiumV2** pour diminuer les coûts ; il sera toujours possible de monter en puissance à nouveau au niveau **PremiumV2**.
- Si votre application s’exécute déjà dans un niveau **Premium** existant, vous pouvez la cloner avec tous ses paramètres, ses chaînes de connexion et sa configuration de déploiement dans un nouveau plan App Service qui utilise **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Sur la page **Cloner l’application**, vous pouvez créer un plan App Service avec **PremiumV2** dans la région de votre choix, puis spécifier les paramètres d’application et la configuration à cloner.

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la création d’application dans le niveau **PremiumV2** à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>D’Azure CLI

La commande suivante crée un plan App Service dans _P1V2_. Vous pouvez l’exécuter dans Cloud Shell. Les options possibles pour `--sku` sont P1V2, _P2V2_ et _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La commande suivante crée un plan App Service dans _P1V2_. Les options possibles pour `-WorkerSize` sont _Small_, _Medium_ et _Large_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Autres ressources

[Montée en puissance d’une application dans Azure](manage-scale-up.md)  
[Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md)
