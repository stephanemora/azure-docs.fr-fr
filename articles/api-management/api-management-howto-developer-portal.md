---
title: Présentation du portail des développeurs dans Gestion des API Azure
titleSuffix: Azure API Management
description: 'En savoir plus sur le portail des développeurs dans Gestion des API : un site web personnalisable, où les consommateurs d’API peuvent explorer vos API.'
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: aaf02affce924232deb56bf7694771c838b0b323
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739599"
---
# <a name="overview-of-the-developer-portal"></a>Présentation du portail des développeurs

Le portail des développeurs est un site web généré automatiquement et entièrement personnalisable avec la documentation de vos API. C’est là que les consommateurs d’API peuvent découvrir vos API, apprendre à les utiliser, y demander l’accès et les essayer.

Comme nous l’avons vu dans cet article, vous pouvez personnaliser et étendre le portail des développeurs pour vos scénarios spécifiques. 

![Portail des développeurs Gestion des API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migration à partir du portail hérité

> [!IMPORTANT]
> Le portail des développeurs hérité est désormais déconseillé et recevra uniquement des mises à jour de sécurité. Vous pouvez continuer à l’utiliser normalement jusqu’à sa suppression en octobre 2023, lorsqu’il sera supprimé de tous les services de gestion des API.

La migration vers le nouveau portail des développeurs est décrite dans l’[article de documentation dédié](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Personnalisation et styles

Le portail des développeurs peut être personnalisé et mis en forme à l’aide de l’éditeur visuel intégré, par glisser-déplacer. Consultez ce [didacticiel](api-management-howto-developer-portal-customize.md) pour plus d’informations.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Extensibilité

Votre service de gestion des API intègre un portail des développeurs **géré**, toujours à jour. Vous pouvez y accéder à partir de l’interface du portail Azure.

Si vous avez besoin de l’étendre avec une logique personnalisée, ce qui n’est pas pris en charge par défaut, vous pouvez modifier sa base de code. La base de code du portail est [disponible dans un référentiel GitHub](https://github.com/Azure/api-management-developer-portal). Par exemple, vous pouvez implémenter un nouveau widget, qui s’intègre à un système de prise en charge tiers. Lorsque vous implémentez de nouvelles fonctionnalités, vous pouvez choisir l’une des options suivantes :

- **L’auto-hébergement** du portail résultant en dehors de votre service de gestion des API. Lorsque vous auto-hébergez le portail, vous êtes responsable de sa maintenance et de ses mises à niveau. L’assistance de Support Azure est limitée à la [configuration de base des portails auto-hébergés](developer-portal-self-host.md).
- Ouvrez une demande de tirage (pull request) pour l’équipe de gestion des API afin de fusionner les nouvelles fonctionnalités dans la base de code du portail **géré**.

Pour obtenir plus de détails et d’instructions sur l’extensibilité, reportez-vous au [référentiel GitHub](https://github.com/Azure/api-management-developer-portal) et au tutoriel sur l’[implémentation d’un widget](developer-portal-implement-widgets.md). Le tutoriel de [personnalisation du portail managé](api-management-howto-developer-portal-customize.md) décrit le panneau d’administration du portail, commun aux versions **auto-hébergée** et **managée**.


## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Accéder au portail managé des développeurs et le personnaliser](api-management-howto-developer-portal-customize.md)
- [Configurer la version auto-hébergée du portail](developer-portal-self-host.md)
- [Implémenter votre propre widget](developer-portal-implement-widgets.md)

Parcourez d’autres ressources :

- [Référentiel GitHub avec le code source](https://github.com/Azure/api-management-developer-portal)
- [FAQ relative au portail des développeurs](developer-portal-faq.md)
