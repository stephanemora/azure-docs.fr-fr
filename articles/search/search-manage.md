---
title: Administration des services sur le portail
titleSuffix: Azure Cognitive Search
description: Gérez Recherche cognitive Azure, un service de recherche cloud hébergé sur Microsoft Azure, à l'aide du portail Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: dbc82b67206f6453101b0d6f419718b928506f79
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003851"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administration des services pour Recherche cognitive Azure sur le portail Azure

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portail](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Recherche cognitive Azure est un service de recherche entièrement géré, basé sur le cloud, utilisé pour la création d’une expérience de recherche riche dans des applications personnalisées. Cet article aborde les tâches d’administration que vous pouvez effectuer dans le [portail Azure](https://portal.azure.com) pour un service de recherche que vous avez déjà créé. Le portail vous permet d’effectuer toutes les [tâches de gestion](#management-tasks) associées au service, ainsi que la gestion et l’exploration du contenu. Par conséquent, le portail offre un accès à large spectre à tous les aspects des opérations du service de recherche.

Chaque service de recherche est géré en tant que ressource autonome. L’image suivante montre les pages du portail pour un seul service de recherche gratuit appelé « demo-search-svc ». Bien que vous ayez l’habitude d’utiliser Azure PowerShell ou Azure CLI pour le management des services, il est judicieux de vous familiariser avec les outils et les capacités offerts par les pages du portail. Certaines tâches sont tout simplement plus faciles et plus rapides à effectuer dans le portail que dans le code. 

## <a name="overview-home-page"></a>Page de présentation (accueil)

La page de présentation désigne la page « Accueil » de chaque service. Ci-dessous, les zones de l’écran encadrées en rouge indiquent les tâches, les outils et les vignettes que vous risquez d’utiliser souvent, surtout si vous débutez avec le service.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Pages du portail pour un service de recherche" border="true":::

| Domaine | Description |
|------|-------------|
| 1  | La section **Informations essentielles** affiche les propriétés du service, y compris le point de terminaison utilisé lors de la configuration des connexions. Elle montre également le nombre de niveaux, de réplicas et de partitions d’un coup d’œil. |
| 2 | En haut de la page se trouve une série de commandes permettant d’appeler des outils interactifs ou de gérer le service. Les commandes [Import data](search-get-started-portal.md) et [Search explorer](search-explorer.md) sont couramment utilisées pour le prototypage et l’exploration. |
| 3 | Sous la section **Informations essentielles**, vous trouverez une série de sous-pages à onglets permettant d’accéder rapidement aux statistiques d’utilisation, aux métriques d’intégrité du service et à tous les index, indexeurs, sources de données et ensembles de compétences existants sur votre service. Si vous sélectionnez un index ou un autre objet, des pages supplémentaires sont disponibles pour afficher la composition, les paramètres et l’état de l’objet (le cas échéant). |
| 4 | À gauche, vous pouvez accéder à des liens qui ouvrent des pages supplémentaires pour accéder aux clés API utilisées sur les connexions, configurer le service, configurer la sécurité, surveiller les opérations, automatiser des tâches et obtenir de l’aide. |

### <a name="read-only-service-properties"></a>Propriétés du service en lecture seule

Plusieurs aspects d’un service de recherche sont déterminés lorsque le service est approvisionné et ne peuvent pas être modifiés :

* Nom du service (Vous ne pouvez pas renommer un service de recherche.)
* Emplacement du service (Vous ne pouvez pas facilement déplacer un service de recherche intact vers une autre région. Bien qu’il existe un modèle, le déplacement du contenu est un processus manuel.)
* Niveau de service (Vous ne pouvez pas passer de S1 à S2, par exemple, sans créer de nouveau service.)

## <a name="management-tasks"></a>Tâches de gestion

L’administration des services est légère de par sa conception et est souvent définie par les tâches que vous pouvez effectuer par rapport au service lui-même :

* [Ajuster la capacité](search-capacity-planning.md) en ajoutant ou en supprimant des réplicas et des partitions
* [Gérer les clés API](search-security-api-keys.md) utilisées pour les opérations d’administration et de requête
* [Contrôler l’accès aux opérations d’administration](search-security-rbac.md) via la sécurité en fonction du rôle
* [Configurer des règles de pare-feu IP](service-configure-firewall.md) pour limiter l’accès par adresse IP
* [Configurer un point de terminaison privé](service-create-private-endpoint.md) à l’aide d’Azure Private Link et d’un réseau virtuel privé
* [Surveiller l’intégrité du service](search-monitor-usage.md) : stockage, volumes de requêtes et latence

Vous pouvez également énumérer tous les objets créés sur le service : index, indexeurs, sources de données, ensembles de compétences, et ainsi de suite. La page de présentation du portail affiche tout le contenu existant sur votre service. La grande majorité des opérations effectuées sur un service de recherche sont liées au contenu.

Les mêmes tâches de gestion effectuées dans le portail peuvent également être traitées par programmation via les [API REST de gestion](/rest/api/searchmanagement/), le [module PowerShell Az.Search](search-manage-powershell.md), le [module Azure CLI az search](search-manage-azure-cli.md) et les Kits de développement logiciel (SDK) Azure pour .NET, Python, Java et JavaScript. Les tâches d’administration sont entièrement représentées sur le portail et toutes les interfaces de programmation. Aucune tâche d’administration spécifique n’est disponible que dans une seule modalité.

Recherche cognitive tire parti d’autres services Azure pour effectuer une surveillance et une gestion plus poussées. En soi, les seules données stockées dans le service de recherche sont le contenu des objets (définitions des index, indexeurs et sources de données, et autres objets). Les mesures exposées sur les pages du portail sont tirées de journaux internes sur un cycle de 30 jours continu. Pour la rétention des journaux et des événements supplémentaires contrôlés par l’utilisateur, vous devez disposer d’[Azure Monitor](../azure-monitor/index.yml). Pour plus d’informations sur la configuration de la journalisation des diagnostics pour un service de recherche, consultez [Collecter et analyser les données de journal](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Autorisations des administrateurs

Lorsque vous ouvrez la page de présentation du service de recherche, les autorisations attribuées à votre compte déterminent les pages qui sont à votre disposition. La page de présentation au début de l’article montre les pages du portail qu’un administrateur ou un contributeur verra.

Dans une ressource Azure, les droits d’administration sont accordés par le biais de l’attribution de rôles. Dans le cadre de Recherche cognitive Azure, les [attributions de rôles](search-security-rbac.md) déterminent les personnes qui peuvent allouer des réplicas et des partitions ou gérer des clés API, qu’elles utilisent le portail, [PowerShell](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md) ou les [API REST de gestion](/rest/api/searchmanagement) :

> [!TIP]
> L’approvisionnement ou le retrait du service lui-même peut être effectué par un administrateur d’abonnement Azure ou un coadministrateur. En utilisant des mécanismes à l’échelle d’Azure, vous pouvez verrouiller un abonnement ou une ressource pour empêcher la suppression accidentelle ou non autorisée de votre service de recherche par les utilisateurs disposant de droits d’administration. Pour plus d’informations, consultez [Verrouiller les ressources pour en empêcher la suppression](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Étapes suivantes

* Passer en revue les [capacités de surveillance](search-monitor-usage.md) disponibles dans le portail
* Automatiser avec [PowerShell](search-manage-powershell.md) ou [Azure CLI](search-manage-azure-cli.md)
* Réviser les [fonctionnalité de sécurité](search-security-overview.md) pour protéger le contenu et les opérations
* Activer la [journalisation des diagnostics](search-monitor-logs.md) pour surveiller les charges de travail de requête et d’indexation