---
title: Vue d’ensemble des modèles courants de mise à l’échelle automatique
description: Découvrez certains des modèles courants de mise à l’échelle automatique de vos ressources dans Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: c7084a10aceafcdd1039893b810fcbd8b74b874b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967402"
---
# <a name="overview-of-common-autoscale-patterns"></a>Vue d’ensemble des modèles courants de mise à l’échelle automatique
Cet article décrit certains des modèles courants de mise à l’échelle de vos ressources dans Azure.

La mise à l’échelle automatique Azure Monitor s’applique uniquement à [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aux [services cloud](https://azure.microsoft.com/services/cloud-services/), à [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) et aux [services Gestion des API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

# <a name="lets-get-started"></a>Prise en main

Cet article suppose que vous êtes familiarisé avec la mise à l’échelle automatique. Vous pouvez [commencer ici à mettre à l’échelle votre ressource][1]. Voici quelques modèles de mise à l’échelle courants.

## <a name="scale-based-on-cpu"></a>Mise à l’échelle en fonction du processeur

Vous avez une application web (/VMSS/rôle de service cloud) et

- Vous souhaitez faire augmenter/diminuer les tailles d’instance sur la base du processeur.
- Vous souhaitez en outre vous assurer qu’il existe un nombre minimal d’instances.
- Vous souhaitez aussi vous assurer que vous définissez une limite maximale pour le nombre d’instances, vers lequel vous pouvez faire évoluer.

![Mise à l’échelle en fonction du processeur][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Mettre à l’échelle différemment durant les week-ends et jours de la semaine

Vous avez une application web (/VMSS/rôle de service cloud) et

- Vous voulez 3 instances par défaut (pour les jours de la semaine)
- Vous ne prévoyez le trafic lors des week-ends et, par conséquent, vous souhaitez réduire de 1 instance les week-ends.

![Mettre à l’échelle différemment durant les week-ends et jours de la semaine][3]

## <a name="scale-differently-during-holidays"></a>Mettre à l’échelle différemment pendant les jours fériés

Vous avez une application web (/VMSS/rôle de service cloud) et

- Vous souhaitez augmenter/diminuer la taille des instances en fonction de l’utilisation du processeur par défaut
- Toutefois, pendant les vacances (ou des jours spécifiques qui sont importants pour votre entreprise), vous souhaitez remplacer les valeurs par défaut et avoir plus de capacité à votre disposition.

![Mettre à l’échelle différemment lors des jours fériés][4]

## <a name="scale-based-on-custom-metric"></a>Mise à l’échelle en fonction de métriques personnalisées

Vous avez un site web frontal et un niveau d’API qui communique avec le serveur principal.

- Vous souhaitez mettre à l’échelle le niveau API sur la base d’événements personnalisés sur le serveur frontal (exemple : vous souhaitez mettre à l’échelle de votre processus de validation en fonction du nombre d’éléments dans le panier d’achat)

![Mise à l’échelle en fonction de métriques personnalisées][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
