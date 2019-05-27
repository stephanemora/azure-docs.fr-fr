---
title: Échelle et hébergement dans Azure Functions | Microsoft Docs
description: Découvrez comment choisir entre Premium et le plan de consommation Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, fonctions, plan de consommation, plan premium, le traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05fb277564f72e62a4cdacc12a16cce229b2befc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872740"
---
# <a name="azure-functions-scale-and-hosting"></a>Échelle et hébergement dans Azure Functions

Azure Functions s’exécute dans deux modes différents : La consommation et le plan Premium (version préliminaire publique). Le plan de consommation ajoute automatiquement la puissance de calcul lors de l’exécution de votre code. Votre application est mis à l’échelle si nécessaire pour gérer la charge et mise à l’échelle vers le bas lorsque du code en cours d’exécution. Vous n’avez pas à payer pour des machines virtuelles inactives ni à réserver de la capacité à l’avance.  Le plan Premium sera automatiquement mise à l’échelle et ajoutez la puissance de calcul supplémentaires lors de l’exécution de votre code.  Le plan Premium est fourni avec des fonctionnalités supplémentaires telles que les instances de calcul premium, la possibilité de conserver les instances à chaud indéfiniment et la connectivité de réseau virtuel.  Si vous avez un Plan App Service existant, vous pouvez également exécuter vos applications de fonction au sein de celles-ci.

> [!NOTE]  
> Les deux [plan Premium](https://aka.ms/functions-premiumplan) et [plan de consommation pour Linux](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/) sont actuellement en version préliminaire.

Si vous n’êtes pas familiarisé avec Azure Functions, consultez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Lorsque vous créez une application de fonction, vous devez choisir un plan d’hébergement pour les fonctions contenues dans l’application. Quel que soit le plan choisi, une instance de *l’hôte Azure Functions* exécute les fonctions. Le type de plan contrôle :

* la façon dont les instances d’hôte font l’objet d’une augmentation de taille ;
* les ressources disponibles pour chaque hôte.
* Fonctionnalités de l’instance, telles que la connectivité de réseau virtuel.

> [!NOTE]
> Vous pouvez basculer entre les plans consommation et Premium en modifiant la propriété de plan de la ressource function app.

## <a name="consumption-plan"></a>Plan de consommation

Lorsque vous utilisez le plan de consommation, les instances de l’hôte Azure Functions sont ajoutés et supprimés dynamiquement en fonction du nombre d’événements entrants. Ce plan serverless est mis à l’échelle automatiquement, et vous êtes facturé pour les ressources de calcul uniquement lorsque vos fonctions sont exécutées. Dans un plan Consommation, le délai d’attente de l’exécution d’une fonction arrive à expiration après une période configurable.

La facturation est basée sur le nombre d’exécutions, la durée d’exécution et la mémoire utilisée. La facturation est unifiée pour toutes les fonctions d’une même application de fonction. Pour plus d’informations, consultez la page [Tarification d’Azure Functions].

Le plan d’hébergement par défaut (le plan Consommation) présente les avantages suivants :

* Paiement uniquement à l’exécution de vos fonctions
* Augmentation automatique de la taille des instances même pendant les périodes de charge élevée

## <a name="premium-plan-public-preview"></a>Plan Premium (version préliminaire publique)

Lorsque vous utilisez le plan Premium, les instances de l’hôte Azure Functions sont rapidement ajoutés et supprimés en fonction du nombre d’événements entrants comme le plan de consommation.  Toutefois, le plan Premium offre également :

* Instances toujours à chaud afin d’éviter tout démarrage à froid.
* Connectivité de réseau virtuel.
* Durée d’exécution illimitée.
* Tailles d’instance Premium (un seul cœur, core deux et quatre instances core).
* Options de tarification de prédictible.
* Allocation d’une application à haute densité pour les plans avec plusieurs applications de fonction.

Vous trouverez plus d’informations sur la façon dont vous pouvez configurer ces options dans le [plan premium d’Azure Functions](functions-premium-plan.md).

Au lieu de facturation par l’exécution et de mémoire consommée, la facturation est basée sur le nombre de cœurs-secondes et Go (en secondes) entre les instances réservées et nécessaires.  Au moins une instance est nécessaire pour arriver à chaud du tout, il existe un coût mensuel fixe par plan est actif (quel que soit le nombre d’exécutions).

Étudiez le plan de premium d’Azure Functions dans les cas suivants :
* Vos applications de fonction s’exécutent en continu ou presque. Dans ce cas, un plan App Service peut être plus économique.
* Vous avez besoin de plus d’options de mémoire ou de processeur que celles qui sont proposées dans le plan Consommation.
* Votre code doit s’exécuter plus de temps que le [durée d’exécution maximale autorisée](#timeout) sur le plan de consommation.
* Vous avez besoin de fonctionnalités qui sont disponibles uniquement dans un plan App Service, telles que la prise en charge d’App Service Environment, la connectivité des réseaux virtuels/VPN et la configuration de machines virtuelles volumineuses.

> [!NOTE]
> La version préliminaire du plan premium prend actuellement en charge les fonctions exécutées dans .NET, nœud ou Java via l’infrastructure de Windows.

Lors de l’exécution des fonctions JavaScript sur un plan Premium, vous devez choisir une instance qui possède moins de processeurs virtuels. Pour plus d’informations, consultez le [choisir des plans de Premium à cœur unique](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>plan App Service

Vos applications de fonction peuvent également exécuter sur les même des machines virtuelles dédiées en tant que d’autres applications App Service (Basic, Standard, Premium et isolé références (SKU)). Les plans App Service prennent en charge Linux.

Pensez à un plan App Service dans les cas suivants :

* Vous disposez de machines virtuelles existantes, sous-utilisées qui exécutent déjà d’autres instances App Service.
* Vous souhaitez exécuter votre application de fonction sur Linux, ou voulez fournir une image personnalisée sur laquelle exécuter vos fonctions.

Vous payez le même pour les applications de fonction dans un Plan App Service comme vous le feriez pour d’autres ressources App Service, comme les applications web. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/overview-hosting-plans.md). 

Avec un plan App Service, vous pouvez augmenter manuellement la taille des instances en ajoutant des instances de machine virtuelle, ou vous pouvez activer la mise à l’échelle automatique. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Vous pouvez également effectuer une montée en puissance en choisissant un autre plan App Service. Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](../app-service/web-sites-scale.md). 

Lorsque vous exécutez des fonctions JavaScript dans un plan App Service, vous devez choisir un plan qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [choisir des plans App Service à cœur unique](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Si vous utilisez un plan App Service, vous devez activer le paramètre **Always On** afin que l’application de fonction s’exécute correctement. Dans un plan App Service, comme le runtime des fonctions devient inactif après quelques minutes d’inactivité, seuls des déclencheurs HTTP peuvent « relancer » vos fonctions. Le paramètre Always On est disponible uniquement dans un plan App Service. Dans un plan Consommation, la plateforme active automatiquement les applications de fonction.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>Quel est mon plan d’hébergement

Pour identifier le plan d’hébergement utilisé par votre application de fonction, consultez la section **Plan App Service / niveau tarifaire** dans l’onglet **Vue d’ensemble** correspondant à l’application de fonction dans le [portail Azure](https://portal.azure.com). Pour les plans App Service, la section affiche également le niveau tarifaire. 

![Afficher le plan de mise à l’échelle dans le portail](./media/functions-scale/function-app-overview-portal.png)

Vous pouvez également utiliser l’interface de ligne de commande Azure pour identifier le plan utilisé tel que suit :

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Lorsque la sortie de cette commande est `dynamic`, votre application de fonction utilise le plan Consommation. Lorsque la sortie de cette commande est `ElasticPremium`, votre application de fonction se trouve dans le plan Premium.  Toute autre valeur représente un niveau de plan App Service.

Même lorsque le paramètre Always On est activé, le délai d’exécution des fonctions individuelles est contrôlé par le paramètre `functionTimeout` dans le fichier projet [host.json](functions-host-json.md#functiontimeout).

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage

Sur n’importe quel plan, une application de fonction nécessite un compte de stockage Azure général qui prend en charge le stockage Blob, file d’attente, fichiers et Table Azure. Il s’agit, car les fonctions s’appuient sur le stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonction, mais certains comptes de stockage ne prennent pas en charge les files d’attente et tables. Ces comptes, qui incluent les comptes de stockage Blob uniquement (notamment le stockage Premium) et les comptes de stockage à usage général avec la réplication de stockage redondant interzone (ZRS), ne sont pas inclus dans la liste de **comptes de stockage** existants que vous pouvez sélectionner lorsque vous créez une application de fonction.

<!-- JH: Does using a Premium Storage account improve perf? -->

Pour en savoir plus sur les types de compte de stockage, consultez [Présentation des services Stockage Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Fonctionnement des plans de consommation et premium

Dans la consommation et les plans premium, le contrôleur de mise à l’échelle s’ajuste automatiquement les ressources processeur et mémoire en ajoutant des instances de l’hôte Functions, en fonction du nombre d’événements desquels ses fonctions sont déclenchées. Chaque instance de l’hôte Functions dans le plan de consommation est limitée à 1,5 Go de mémoire et de 1 processeur.  Une instance de l’hôte est l’application de fonction entière, ce qui signifie que de toutes les fonctions d’une ressource de partage de function app au sein d’une instance et la mise à l’échelle en même temps. Les applications de fonction qui partagent le même plan de consommation sont mis à l’échelle indépendamment.  Dans le plan premium, la taille de votre plan détermine la mémoire disponible et le processeur pour toutes les applications dans ce plan sur cette instance.  

Fichiers de code de fonction sont stockés sur des partages de fichiers Azure sur le compte de stockage principal de la fonction. Lorsque vous supprimez le compte de stockage principal de l’application de fonction, les fichiers de code de fonction sont supprimés et ne peuvent pas être récupérés.

> [!NOTE]
> Quand vous utilisez un déclencheur d’objet blob dans un plan Consommation, il peut y avoir jusqu’à 10 minutes de délai dans le traitement des nouveaux objets blob. Ce délai intervient lorsqu’une application de fonction est devenue inactive. Une fois l’application de fonction en cours d’exécution, les objets blob sont traités immédiatement. Pour éviter ce délai de démarrage à froid, utilisez le plan Premium, ou le [déclencheur Event Grid](functions-bindings-event-grid.md). Pour plus d’informations, consultez [l’article de référence sur la liaison de déclencheur blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Mise à l’échelle du runtime

Azure Functions utilise un composant appelé *contrôleur de mise à l’échelle* pour surveiller la fréquence des événements et déterminer s’il convient de monter en puissance ou de diminuer la taille des instances. Le contrôleur de mise à l’échelle utilise une méthode heuristique pour chaque type de déclencheur. Par exemple, si vous utilisez un déclencheur de stockage File d’attente Azure, il est mis à l’échelle en fonction de la longueur de la file d’attente et de l’âge du plus ancien message en file d’attente.

L’unité de mise à l’échelle est l’application de fonction. Quand les instances de l’application de fonction font l’objet d’une augmentation de taille, des ressources supplémentaires sont allouées pour exécuter plusieurs instances de l’hôte Azure Functions. À l’inverse, quand la demande de calcul est réduite, le contrôleur de mise à l’échelle supprime des instances de l’hôte de fonction. Le nombre d’instances est finalement réduit à zéro si aucune fonction n’est exécutée dans une application de fonction.

![Contrôleur de mise à l’échelle surveillant les événements et créant des instances](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Présentation des comportements de mise à l’échelle

La mise à l’échelle peut varier en fonction de certains facteurs et selon le déclencheur et le langage sélectionnés. Toutefois, quelques aspects de la mise à l’échelle sont notables dans le système aujourd’hui :

* Une application de fonction peut évoluer jusqu’à 200 instances maximum. Une seule instance, par contre, peut traiter plusieurs messages ou requêtes à la fois, ainsi il n’y a pas de limite définie sur le nombre d’exécutions simultanées.
* Pour les déclencheurs HTTP, les nouvelles instances sont uniquement affectés au maximum une fois toutes les secondes.
* Pour les déclencheurs non-HTTP, nouvelles instances sont uniquement affectés au maximum une fois toutes les 30 secondes.

Différents déclencheurs peuvent également avoir des limites de mise à l’échelle différentes, comme documentées ci-dessous :

* [Concentrateur d’événements](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bonnes pratiques et modèles pour les applications scalables

Nombreux sont les aspects d’une application de fonction qui impactent sa bonne mise à l’échelle, notamment la configuration de l’hôte, l’encombrement du runtime et l’efficacité des ressources.  Pour plus d’informations, consultez la [section sur l’extensibilité dans l’article Considérations relatives aux performances](functions-best-practices.md#scalability-best-practices). Vous devez également savoir ce qu’il se passe au niveau des connexions lors de la mise à l’échelle de votre application de fonction. Pour plus d’informations, consultez [How to manage connections in Azure Functions](manage-connections.md) (Comment gérer des connexions dans Azure Functions).

### <a name="billing-model"></a>Modèle de facturation

La facturation du plan de consommation est décrite en détail dans la page [Tarification d’Azure Functions]. L’utilisation est agrégée au niveau de l’application de fonction et compte uniquement la durée d’exécution du code de fonction. Les unités de facturation sont les suivantes :

* **Consommation des ressources en gigaoctet/seconde (Go/s)**. Calcul effectué d’après une combinaison de la taille de la mémoire et de la durée d’exécution pour toutes les fonctions d’une application de fonction. 
* **Exécutions**. Comptées chaque fois qu’une fonction est exécutée en réponse à un déclencheur d’événements.

Requêtes utiles et des informations sur la façon de comprendre votre facture de consommation, vous pouvez trouver [sur le Forum aux questions de facturation](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Tarification d’Azure Functions]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites du service

Le tableau suivant indique les limites qui s’appliquent aux applications de fonction lors de l’exécution dans les plans d’hébergement différents :

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
