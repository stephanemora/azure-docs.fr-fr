---
title: Échelle et hébergement dans Azure Functions | Microsoft Docs
description: Découvrez comment choisir entre le plan Consommation et le plan App Service d’Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, fonctions, plan consommation, plan app service, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 08/09/2018
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfdd0c647021c453095ec4e05c042992011389b9
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975888"
---
# <a name="azure-functions-scale-and-hosting"></a>Échelle et hébergement dans Azure Functions

Vous pouvez exécuter la solution Azure Functions dans deux modes : le plan Consommation et le plan Azure App Service. Le plan Consommation alloue automatiquement la puissance de calcul lors de l’exécution de votre code. La capacité de votre application est augmentée le cas échéant pour gérer la charge et réduite lorsque le code n’est pas en cours d’exécution. Vous n’avez pas à payer pour des machines virtuelles inactives ni à réserver de la capacité à l’avance.

> [!NOTE]  
> Pour l’instant, [l’hébergement Linux](functions-create-first-azure-function-azure-cli-linux.md) est uniquement disponible dans un plan App Service.

Si vous n’êtes pas familiarisé avec Azure Functions, consultez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Lorsque vous créez une application de fonction, vous devez choisir un plan d’hébergement pour les fonctions contenues dans l’application. Quel que soit le plan choisi, une instance de *l’hôte Azure Functions* exécute les fonctions. Le type de plan contrôle :

* la façon dont les instances d’hôte font l’objet d’une augmentation de taille ;
* les ressources disponibles pour chaque hôte.

> [!IMPORTANT]
> Vous devez choisir le type de plan d’hébergement durant la création de l’application de fonction. Vous ne pouvez pas en changer ultérieurement.

Dans un plan App Service, vous pouvez adapter les niveaux pour allouer différentes quantités de ressources. Dans le plan Consommation, Azure Functions gère automatiquement l’allocation de toutes les ressources. 

## <a name="consumption-plan"></a>Plan de consommation

Quand vous utilisez un plan Consommation, les instances de l’hôte Azure Functions sont ajoutées et supprimées de façon dynamique en fonction du nombre d’événements entrants. Ce plan serverless est mis à l’échelle automatiquement, et vous êtes facturé pour les ressources de calcul uniquement lorsque vos fonctions sont exécutées. Dans un plan Consommation, le délai d’attente de l’exécution d’une fonction arrive à expiration après une période configurable.

> [!NOTE]
> Le délai d’expiration par défaut pour les fonctions dans un plan Consommation est de 5 minutes. Pour l’application de fonction, vous pouvez augmenter cette valeur jusqu’à un maximum de 10 minutes en changeant la propriété `functionTimeout` dans le fichier projet [host.json](functions-host-json.md#functiontimeout).

La facturation est basée sur le nombre d’exécutions, la durée d’exécution et la mémoire utilisée. La facturation est unifiée pour toutes les fonctions d’une même application de fonction. Pour plus d’informations, consultez la page [Tarification d’Azure Functions].

Le plan d’hébergement par défaut (le plan Consommation) présente les avantages suivants :

* Paiement uniquement à l’exécution de vos fonctions
* Augmentation automatique de la taille des instances même pendant les périodes de charge élevée

## <a name="app-service-plan"></a>Plan App Service

Dans le plan App Service dédié, vos applications de fonction sont exécutées sur des machines virtuelles dédiées sur des références SKU de base, Standard, Premium et Isolé, comme c’est le cas pour les autres applications App Service. Les machines virtuelles dédiées sont allouées à votre application de fonction, ce qui signifie que l’hôte des fonctions peut toujours être [en cours d’exécution](#always-on). Les plans App Service prennent en charge Linux.

Pensez à un plan App Service dans les cas suivants :

* Vous disposez de machines virtuelles existantes, sous-utilisées qui exécutent déjà d’autres instances App Service.
* Vos applications de fonction s’exécutent en continu ou presque. Dans ce cas, un plan App Service peut être plus économique.
* Vous avez besoin de plus d’options de mémoire ou de processeur que celles qui sont proposées dans le plan Consommation.
* Votre code doit s’exécuter pendant une durée supérieure à celle autorisée dans le plan Consommation, qui est de 10 minutes maximum.
* Vous avez besoin de fonctionnalités qui sont disponibles uniquement dans un plan App Service, telles que la prise en charge d’App Service Environment, la connectivité des réseaux virtuels/VPN et la configuration de machines virtuelles volumineuses.
* Vous souhaitez exécuter votre application de fonction sur Linux, ou voulez fournir une image personnalisée sur laquelle exécuter vos fonctions.

L’utilisation d’une machine virtuelle dissocie le coût du nombre d’exécutions, de la durée d’exécution et de la mémoire utilisée. Vous ne payez donc pas plus que le coût de l’instance de machine virtuelle que vous allouez. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Avec un plan App Service, vous pouvez augmenter manuellement la taille des instances en ajoutant des instances de machine virtuelle, ou vous pouvez activer la mise à l’échelle automatique. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../monitoring-and-diagnostics/monitoring-autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Vous pouvez également effectuer une montée en puissance en choisissant un autre plan App Service. Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](../app-service/web-sites-scale.md). 

Lorsque vous exécutez des fonctions JavaScript dans un plan App Service, vous devez choisir un plan qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans App Service à cœur unique](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### <a name="always-on"></a>Always On

Si vous utilisez un plan App Service, vous devez activer le paramètre **Always On** afin que l’application de fonction s’exécute correctement. Dans un plan App Service, comme le runtime des fonctions devient inactif après quelques minutes d’inactivité, seuls des déclencheurs HTTP peuvent « relancer » vos fonctions. Le paramètre Always On est disponible uniquement dans un plan App Service. Dans un plan Consommation, la plateforme active automatiquement les applications de fonction.

## <a name="what-is-my-hosting-plan"></a>Quel est mon plan d’hébergement

Pour identifier le plan d’hébergement utilisé par votre application de fonction, consultez la section **Plan App Service / niveau tarifaire** dans l’onglet **Vue d’ensemble** correspondant à l’application de fonction dans le [portail Azure](https://portal.azure.com). Pour les plans App Service, la section affiche également le niveau tarifaire. 

![Afficher le plan de mise à l’échelle dans le portail](./media/functions-scale/function-app-overview-portal.png)

Vous pouvez également utiliser l’interface de ligne de commande Azure pour identifier le plan utilisé tel que suit :

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Lorsque la sortie de cette commande est `dynamic`, votre application de fonction utilise le plan Consommation. Toute autre valeur représente un niveau de plan App Service.

Même lorsque le paramètre Always On est activé, le délai d’exécution des fonctions individuelles est contrôlé par le paramètre `functionTimeout` dans le fichier projet [host.json](functions-host-json.md#functiontimeout).

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage

Dans un plan Consommation ou un plan App Service, une application de fonction nécessite un compte de stockage Azure général prenant en charge les stockages Blob, File d’attente, Fichiers et Table Azure. Cela s’explique par le fait qu’Azure Functions utilise le stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Certains comptes de stockage ne prennent cependant pas en charge les files d’attente et les tables. Ces comptes, qui incluent les comptes de stockage Blob uniquement (notamment le stockage Premium) et les comptes de stockage à usage général avec la réplication de stockage redondant interzone (ZRS), ne sont pas inclus dans la liste de **comptes de stockage** existants que vous pouvez sélectionner lorsque vous créez une application de fonction.

<!-- JH: Does using a Premium Storage account improve perf? -->

Pour en savoir plus sur les types de compte de stockage, consultez [Présentation des services Stockage Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Fonctionnement du plan de consommation

Dans le plan Consommation, le contrôleur de mise à l’échelle met automatiquement à l’échelle les ressources processeur et mémoire en ajoutant des instances de l’hôte Functions en fonction du nombre d’événements en fonction desquels ses fonctions sont déclenchées. Chaque instance de l’hôte Functions est limitée à 1,5 Go de mémoire.  Une instance de l’hôte est l’application de fonction, ce qui signifie que toutes les fonctions dans une application de fonction partagent des ressources au sein d’une instance et sont mises à l’échelle en même temps. Les applications de fonction qui partagent le même plan Consommation sont mises à l’échelle indépendamment.  

Quand vous utilisez le plan d’hébergement Consommation, les fichiers de code de fonction sont stockés dans des partages de fichiers Azure du compte de stockage principal de la fonction. Lorsque vous supprimez le compte de stockage principal de l’application de fonction, les fichiers de code de fonction sont supprimés et ne peuvent pas être récupérés.

> [!NOTE]
> Quand vous utilisez un déclencheur d’objet blob dans un plan Consommation, il peut y avoir jusqu’à 10 minutes de délai dans le traitement des nouveaux objets blob. Ce délai intervient lorsqu’une application de fonction est devenue inactive. Une fois l’application de fonction en cours d’exécution, les objets blob sont traités immédiatement. Pour éviter ce délai de démarrage à froid, utilisez un plan App Service avec le paramètre **Always On** activé ou le déclencheur Event Grid. Pour plus d’informations, consultez [l’article de référence sur la liaison de déclencheur blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Mise à l’échelle du runtime

Azure Functions utilise un composant appelé *contrôleur de mise à l’échelle* pour surveiller la fréquence des événements et déterminer s’il convient de monter en puissance ou de diminuer la taille des instances. Le contrôleur de mise à l’échelle utilise une méthode heuristique pour chaque type de déclencheur. Par exemple, si vous utilisez un déclencheur de stockage File d’attente Azure, il est mis à l’échelle en fonction de la longueur de la file d’attente et de l’âge du plus ancien message en file d’attente.

L’unité de mise à l’échelle est l’application de fonction. Quand les instances de l’application de fonction font l’objet d’une augmentation de taille, des ressources supplémentaires sont allouées pour exécuter plusieurs instances de l’hôte Azure Functions. À l’inverse, quand la demande de calcul est réduite, le contrôleur de mise à l’échelle supprime des instances de l’hôte de fonction. Le nombre d’instances est finalement réduit à zéro si aucune fonction n’est exécutée dans une application de fonction.

![Contrôleur de mise à l’échelle surveillant les événements et créant des instances](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Présentation des comportements de mise à l’échelle

La mise à l’échelle peut varier en fonction de certains facteurs et selon le déclencheur et le langage sélectionnés. Toutefois, quelques aspects de la mise à l’échelle sont notables dans le système aujourd’hui :

* Une application de fonction peut évoluer jusqu’à 200 instances maximum. Une seule instance, par contre, peut traiter plusieurs messages ou requêtes à la fois, ainsi il n’y a pas de limite définie sur le nombre d’exécutions simultanées.
* Les nouvelles instances ne sont tout au plus allouées qu’une fois toutes les 10 secondes.

Différents déclencheurs peuvent également avoir des limites de mise à l’échelle différentes, comme documentées ci-dessous :

* [Concentrateur d’événements](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bonnes pratiques et modèles pour les applications scalables

Nombreux sont les aspects d’une application de fonction qui impactent sa bonne mise à l’échelle, notamment la configuration de l’hôte, l’encombrement du runtime et l’efficacité des ressources.  Pour plus d’informations, consultez la [section sur l’extensibilité dans l’article Considérations relatives aux performances](functions-best-practices.md#scalability-best-practices). Vous devez également savoir ce qu’il se passe au niveau des connexions lors de la mise à l’échelle de votre application de fonction. Pour plus d’informations, consultez [How to manage connections in Azure Functions](manage-connections.md) (Comment gérer des connexions dans Azure Functions).

### <a name="billing-model"></a>Modèle de facturation

La facturation du plan de consommation est décrite en détail dans la page [Tarification d’Azure Functions]. L’utilisation est agrégée au niveau de l’application de fonction et compte uniquement la durée d’exécution du code de fonction. Les unités de facturation sont les suivantes :

* **Consommation des ressources en gigaoctet/seconde (Go/s)**. Calcul effectué d’après une combinaison de la taille de la mémoire et de la durée d’exécution pour toutes les fonctions d’une application de fonction. 
* **Exécutions**. Comptées chaque fois qu’une fonction est exécutée en réponse à un déclencheur d’événements.

[Tarification d’Azure Functions]: https://azure.microsoft.com/pricing/details/functions
