---
title: Échelle et hébergement dans Azure Functions
description: Découvrez comment choisir entre le plan Consommation et le plan Premium d’Azure Functions.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6520f205d0a9c1a33d0cb4911a58a5e680bdadb7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929728"
---
# <a name="azure-functions-scale-and-hosting"></a>Échelle et hébergement dans Azure Functions

Lorsque vous créez une Function App dans Azure, vous devez choisir un plan d’hébergement pour votre application. Trois plans d’hébergement pour Azure Functions sont disponibles : le [plan Consommation](#consumption-plan), le [plan Premium](#premium-plan) et le [plan App Service](#app-service-plan).

Le plan d’hébergement que vous choisissez détermine les comportements suivants :

* La mise à l’échelle de votre Function App.
* Les ressources disponibles pour chaque instance de Function App.
* La prise en charge des fonctionnalités avancées, notamment la connectivité du réseau virtuel.

Les plans Consommation et Premium ajoutent automatiquement de la puissance de calcul lors de l’exécution de votre code. Le cas échéant, votre application bénéficie d’un scale-out afin de gérer la charge et d’un scale-down lorsque le code n’est plus en cours d’exécution. En outre, avec le plan Consommation, vous n’avez pas à payer pour des machines virtuelles inactives ni à réserver de la capacité à l’avance.  

Le plan Premium fournit des fonctionnalités supplémentaires, telles que des instances de calcul premium, la possibilité de conserver les instances chaudes indéfiniment et la connectivité de réseau virtuel.

Le plan App Service vous permet d’exploiter l’infrastructure dédiée que vous gérez. L’échelle de votre Function App ne change pas en fonction des événements, un scale-down jusqu’à zéro est donc impossible. ([Always On](#always-on) doit être activé.)

## <a name="hosting-plan-support"></a>Prise en charge du plan d’hébergement

La prise en charge des fonctionnalités se divise en deux catégories :

* _Disponibilité générale (GA)_ : entièrement pris en charge et approuvé pour la production.
* _Préversion_ : pas encore entièrement pris en charge et approuvé pour la production.

Le tableau suivant indique le niveau actuel de prise en charge pour les trois plans d’hébergement en cas d’exécution sous Windows ou Linux :

| | Plan de consommation | Plan Premium | Plan dédié |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plan de consommation

Quand vous utilisez le plan Consommation, les instances de l’hôte Azure Functions sont ajoutées et supprimées de façon dynamique en fonction du nombre d’événements entrants. Ce plan serverless est mis à l’échelle automatiquement, et vous êtes facturé pour les ressources de calcul uniquement lorsque vos fonctions sont exécutées. Dans un plan Consommation, le délai d’attente de l’exécution d’une fonction arrive à expiration après une période configurable.

La facturation est basée sur le nombre d’exécutions, la durée d’exécution et la mémoire utilisée. La facturation est unifiée pour toutes les fonctions d’une même application de fonction. Pour plus d’informations, consultez la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Le plan d’hébergement par défaut (le plan Consommation) présente les avantages suivants :

* Paiement uniquement à l’exécution de vos fonctions
* Augmentation automatique de la taille des instances même pendant les périodes de charge élevée

Les Function App d’une même région peuvent être affectées au même plan Consommation. Avoir plusieurs applications qui s’exécutent dans le même plan Consommation n’entraîne aucun inconvénient ou impact. Attribuer plusieurs applications au même plan Consommation n’affecte pas la résilience, l’extensibilité ou la fiabilité de chaque application.

Pour en savoir plus sur la façon d’estimer les coûts pour une exécution dans le cadre d’un plan Consommation, consultez [Compréhension des coûts d’un plan Consommation](functions-consumption-costs.md).

## <a name="premium-plan"></a>Plan Premium

Quand vous utilisez le plan Premium, les instances de l’hôte Azure Functions sont ajoutées et supprimées en fonction du nombre d’événements entrants, comme avec le plan Consommation.  Le plan Premium prend en charge les fonctionnalités suivantes :

* Les instances perpétuellement chaudes permettent d’éviter les démarrages à froid
* Connectivité des réseaux virtuels
* Durée d’exécution illimitée
* Tailles d’instance premium (instances à un, deux et quatre cœurs)
* Tarification plus prévisible
* Allocation d’application à haute densité pour les plans avec plusieurs Function App

Vous trouverez plus d’informations sur la configuration de ces options dans la [documentation sur le plan Premium d’Azure Functions](functions-premium-plan.md).

Au lieu d’une facturation en fonction des exécutions et de la mémoire consommée, la facturation du plan Premium se base sur le nombre de cœurs-seconde et la mémoire utilisée sur les instances nécessaires et préparées. Au moins une instance doit être chaude en permanence par plan. Cela signifie qu’il existe un coût mensuel minimum par plan actif, quel que soit le nombre d’exécutions. Gardez à l’esprit que toutes les applications de fonction d’un plan Premium partagent des instances actives et préparées.

Envisagez le plan Premium d’Azure Functions dans les situations suivantes :

* Vos applications de fonction s’exécutent en continu ou presque.
* Vous disposez d’un grand nombre d’exécutions de petite taille et avez une facture d’exécution élevée, mais également une facture de Go par seconde dans le plan Consommation.
* Vous avez besoin de plus d’options de mémoire ou de processeur que celles qui sont proposées dans le plan Consommation.
* Votre code exige une [durée d’exécution supérieure à celle qui est autorisée](#timeout) dans le plan Consommation.
* Vous avez besoin des fonctionnalités qui sont uniquement disponibles dans le plan Premium, notamment la connectivité des réseaux virtuels/VPN.

Lorsque vous exécutez des fonctions JavaScript dans un plan Premium, vous devez choisir une instance qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans Premium à cœur unique](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Plan (App Service) dédié

Vos Function App sont exécutées sur les mêmes machines virtuelles dédiées que d’autres applications App Service (références De base, Standard, Premium et Isolé).

Envisagez un plan App Service dans les situations suivantes :

* Vous disposez de machines virtuelles existantes, sous-utilisées qui exécutent déjà d’autres instances App Service.
* Vous souhaitez fournir une image personnalisée sur laquelle vos fonctions s’exécuteront.

Le coût des Function App dans un plan App Service est le même que pour d’autres ressources App Service, par exemple les applications web. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/overview-hosting-plans.md).

Avec un plan App Service, vous pouvez effectuer un scale-out manuel en ajoutant des instances de machine virtuelle supplémentaires. Vous pouvez également activer la mise à l’échelle automatique. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Vous pouvez également effectuer une montée en puissance en choisissant un autre plan App Service. Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](../app-service/manage-scale-up.md). 

Lorsque vous exécutez des fonctions JavaScript dans un plan App Service, vous devez choisir un plan qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans App Service à cœur unique](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Si vous utilisez un plan App Service, vous devez activer le paramètre **Always On** afin que l’application de fonction s’exécute correctement. Dans un plan App Service, comme le runtime des fonctions devient inactif après quelques minutes d’inactivité, seuls des déclencheurs HTTP peuvent « relancer » vos fonctions. Le paramètre Always On est disponible uniquement dans un plan App Service. Dans un plan Consommation, la plateforme active automatiquement les applications de fonction.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Même lorsque le paramètre Always On est activé, le délai d’exécution des fonctions individuelles est contrôlé par le paramètre `functionTimeout` dans le fichier projet [host.json](functions-host-json.md#functiontimeout).

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Déterminer le plan d’hébergement d’une application existante

Pour identifier le plan d’hébergement utilisé par votre application de fonction, consultez la section **Plan App Service / niveau tarifaire** dans l’onglet **Vue d’ensemble** correspondant à l’application de fonction dans le [portail Azure](https://portal.azure.com). Pour les plans App Service, la section affiche également le niveau tarifaire.

![Afficher le plan de mise à l’échelle dans le portail](./media/functions-scale/function-app-overview-portal.png)

Vous pouvez également utiliser l’interface de ligne de commande Azure pour identifier le plan utilisé tel que suit :

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Lorsque la sortie de cette commande est `dynamic`, votre application de fonction utilise le plan Consommation. Lorsque la sortie de cette commande est `ElasticPremium`, votre Function App utilise le plan Premium. Les autres valeurs représentent un plan App Service avec des niveaux différents.

## <a name="storage-account-requirements"></a>Conditions requises pour le compte de stockage

Dans n’importe quel plan, une Function App nécessite un compte de stockage Azure général prenant en charge les stockages Blob, File d’attente, Fichiers et Table Azure. Cela s’explique par le fait qu’Azure Functions utilise le stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Certains comptes de stockage ne prennent cependant pas en charge les files d’attente et les tables. Ces comptes, qui incluent les comptes de stockage Blob uniquement (notamment le stockage Premium) et les comptes de stockage à usage général avec la réplication de stockage redondant interzone (ZRS), ne sont pas inclus dans la liste de **comptes de stockage** existants que vous pouvez sélectionner lorsque vous créez une application de fonction.

Le même compte de stockage que celui utilisé par votre application de fonction peut également être utilisé par vos déclencheurs et liaisons pour stocker vos données d’application. Toutefois, pour les opérations gourmandes en stockage, il est préférable d’utiliser un compte de stockage distinct.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Pour en savoir plus sur les types de compte de stockage, consultez [Présentation des services Stockage Azure](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Fonctionnement des plans Consommation et Premium

Dans les plans Consommation et Premium, l’infrastructure d’Azure Functions met à l’échelle les ressources processeur et mémoire en ajoutant des instances de l’hôte Functions selon le nombre d’événements sur lesquels ses fonctions sont déclenchées. Chaque instance de l’hôte Functions dans le plan Consommation est limitée à 1,5 Go de mémoire et un seul UC.  Une instance de l’hôte constitue l’intégralité de la Function App, ce qui signifie que toutes les fonctions dans une Function App partagent des ressources au sein d’une instance et sont mises à l’échelle simultanément. Les Function App qui partagent le même plan Consommation sont mises à l’échelle indépendamment.  Dans le plan Premium, la taille de votre plan détermine la mémoire et les processeurs disponibles pour toutes les applications de ce plan, sur cette instance.  

Les fichiers de code de fonction sont stockés dans des partages Azure Files du compte de stockage principal de la fonction. Lorsque vous supprimez le compte de stockage principal de l’application de fonction, les fichiers de code de fonction sont supprimés et ne peuvent pas être récupérés.

### <a name="runtime-scaling"></a>Mise à l’échelle du runtime

Azure Functions utilise un composant appelé *contrôleur de mise à l’échelle* pour surveiller la fréquence des événements et déterminer s’il convient de monter en puissance ou de diminuer la taille des instances. Le contrôleur de mise à l’échelle utilise une méthode heuristique pour chaque type de déclencheur. Par exemple, si vous utilisez un déclencheur de stockage File d’attente Azure, il est mis à l’échelle en fonction de la longueur de la file d’attente et de l’âge du plus ancien message en file d’attente.

L’unité d’échelle pour Azure Functions est la Function App. Quand les instances de l’application de fonction font l’objet d’une augmentation de taille, des ressources supplémentaires sont allouées pour exécuter plusieurs instances de l’hôte Azure Functions. À l’inverse, quand la demande de calcul est réduite, le contrôleur de mise à l’échelle supprime des instances de l’hôte de fonction. Le nombre d’instances est finalement *réduit à zéro* si aucune fonction n’est exécutée dans une application de fonction.

![Contrôleur de mise à l’échelle surveillant les événements et créant des instances](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Présentation des comportements de mise à l’échelle

La mise à l’échelle peut varier en fonction de certains facteurs et selon le déclencheur et le langage sélectionnés. Il est nécessaire de connaître certaines subtilités relatives aux comportements de mise à l’échelle :

* Une application de fonction peut évoluer jusqu’à 200 instances maximum. Une seule instance, par contre, peut traiter plusieurs messages ou requêtes à la fois, ainsi il n’y a pas de limite définie sur le nombre d’exécutions simultanées.
* Pour les déclencheurs HTTP, les nouvelles instances sont allouées une fois par seconde au maximum.
* Pour les déclencheurs non HTTP, les nouvelles instances sont allouées une fois toutes les 30 secondes au maximum.

Différents déclencheurs peuvent également avoir des limites de mise à l’échelle différentes, comme documentées ci-dessous :

* [Concentrateur d’événements](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bonnes pratiques et modèles pour les applications scalables

Nombreux sont les aspects d’une application de fonction qui impactent sa bonne mise à l’échelle, notamment la configuration de l’hôte, l’encombrement du runtime et l’efficacité des ressources.  Pour plus d’informations, consultez la [section sur l’extensibilité dans l’article Considérations relatives aux performances](functions-best-practices.md#scalability-best-practices). Vous devez également savoir ce qu’il se passe au niveau des connexions lors de la mise à l’échelle de votre application de fonction. Pour plus d’informations, consultez [How to manage connections in Azure Functions](manage-connections.md) (Comment gérer des connexions dans Azure Functions).

### <a name="billing-model"></a>Modèle de facturation

La facturation des différents plans est décrite en détail dans la [page Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/). L’utilisation est agrégée au niveau de l’application de fonction et compte uniquement la durée d’exécution du code de fonction. Les unités de facturation sont les suivantes :

* **Consommation des ressources en gigaoctet/seconde (Go/s)** . Calcul effectué d’après une combinaison de la taille de la mémoire et de la durée d’exécution pour toutes les fonctions d’une application de fonction. 
* **Exécutions**. Comptées chaque fois qu’une fonction est exécutée en réponse à un déclencheur d’événements.

Vous trouverez des requêtes et des informations utiles pour vous aider à comprendre votre facture de consommation [dans la FAQ sur la facturation](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Limites du service

Le tableau suivant indique les limites qui s’appliquent aux Function App lorsqu’elles sont exécutées dans les différents plans d’hébergement :

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
