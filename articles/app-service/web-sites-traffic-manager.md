---
title: Contrôle du trafic avec Traffic Manager - Azure App Service
description: Cet article fournit des informations récapitulatives sur Azure Traffic Manager et son rapport à Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677440"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Contrôle du trafic Azure App Service avec Azure Traffic Manager
> [!NOTE]
> Cet article présente des informations résumées sur Microsoft Azure Traffic Manager et son rapport à Azure App Service. Vous trouverez d'autres informations sur Azure Traffic Manager en suivant les liens fournis à la fin de cet article.
> 
> 

## <a name="introduction"></a>Présentation
Vous pouvez utiliser Azure Traffic Manager pour contrôler la façon dont les requêtes des clients web sont distribuées aux applications dans Azure App Service. Lorsque des points de terminaison App Service sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos applications App Service (en cours d’exécution, arrêtées ou supprimées) pour pouvoir décider du point de terminaison qui doit recevoir le trafic.

## <a name="routing-methods"></a>Méthodes de routage
Azure Traffic Manager utilise quatre méthodes de routage. Ces méthodes sont décrites dans la liste suivante, car elles se rapportent à Azure App Service.

* **[Priorité](../traffic-manager/traffic-manager-routing-methods.md#priority) :** utilisez une application principale pour tout le trafic, et fournissez des sauvegardes au cas où celle-ci ou les applications de sauvegarde ne seraient pas disponibles.
* **[Pondération](../traffic-manager/traffic-manager-routing-methods.md#weighted) :** répartissez le trafic sur un ensemble d’applications, uniformément ou en fonction du poids que vous définissez.
* **[Performances](../traffic-manager/traffic-manager-routing-methods.md#performance) :** lorsque vous avez des applications à différents emplacements géographiques, utilisez l’application « la plus proche » en termes de latence réseau faible.
* **[Géographique](../traffic-manager/traffic-manager-routing-methods.md#geographic) :** orientez les utilisateurs vers des applications spécifiques en fonction de l’emplacement géographique d’où provient leur requête DNS. 

Pour plus d’informations, voir [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service et profils dans Traffic Manager
Pour configurer le contrôle du trafic d’application App Service, créer un profil dans Azure Traffic Manager qui utilise l’une des quatre charge équilibrage des méthodes décrites précédemment et puis ajoutez les points de terminaison (dans ce cas, App Service) pour lequel vous souhaitez contrôler le trafic vers le profil. Le statut de votre application (en cours d’exécution, arrêtée ou supprimée) est régulièrement communiqué au profil, ce qui permet à Azure Traffic Manager d’orienter le trafic en conséquence.

Lorsque vous utilisez Azure Traffic Manager avec Azure, tenez compte des points suivants :

* Pour les déploiements des applications dans une même région uniquement, App Service fournit déjà les fonctionnalités de basculement et de tourniquet (round-robin) et ce, quel que soit le mode des applications.
* Pour les déploiements dans une même région qui utilisent App Service conjointement avec un autre service cloud Azure, vous pouvez combiner les deux types de point de terminaison pour autoriser les scénarios hybrides.
* Vous pouvez spécifier un seul point de terminaison App Service par région dans un profil. Lorsque vous sélectionnez une application comme point de terminaison pour une région, les autres applications de cette région ne peuvent plus être sélectionnées pour ce profil.
* Le point de terminaison App Service, que vous spécifiez dans un profil Azure Traffic Manager, s’affiche à la section **Noms de domaine** de la page Configurer pour l’application dans le profil, mais il n’est pas configurable ici.
* Lorsque vous avez ajouté une application à un profil, l’**URL du site** dans le tableau de bord de l’application sur le portail affiche l’URL du domaine personnalisé de l’application (s’il est défini). Sinon, l’URL du profil Traffic Manager (par exemple, `contoso.trafficmanager.net`) s’affiche. Le nom de domaine direct de l’application et l’URL de Traffic Manager sont tous deux affichés dans la page Configurer de l’application, à la section **Noms de domaine**.
* Vos noms de domaines personnalisés fonctionnent comme prévu, mais en plus de les ajouter à vos applications, vous devez configurer votre carte DNS pour qu’elle pointe vers l’URL de Traffic Manager. Pour plus d’informations sur la façon de configurer un domaine personnalisé pour une application App Service, consultez [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md).
* Vous ne pouvez ajouter que des applications en mode standard ou Premium à un profil Azure Traffic Manager.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d'ensemble conceptuelle et technique d'Azure Traffic Manager, consultez la rubrique [Vue d'ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Pour plus d’informations sur l’utilisation de Traffic Manager avec App Service, consultez les billets de blog [Utilisation de Microsoft Azure Traffic Manager avec WAWS](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) et [Azure Traffic Manager peut désormais s’intégrer dans les sites web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

