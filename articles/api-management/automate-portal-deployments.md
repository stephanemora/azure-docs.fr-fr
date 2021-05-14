---
title: Automatiser les déploiements du portail des développeurs
titleSuffix: Azure API Management
description: Découvrez comment migrer automatiquement le contenu du portail des développeurs auto-hébergé entre deux services de gestion des API.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741228"
---
# <a name="automate-developer-portal-deployments"></a>Automatiser les déploiements du portail des développeurs

Le portail des développeurs Gestion des API prend en charge l’accès par programme au contenu. Elle vous permet d’importer ou d’exporter des données à partir d’un service Gestion des API via l'[API REST de gestion de contenu](/rest/api/apimanagement/). L’accès à l’API REST fonctionne pour les portails gérés et auto-hébergés.

## <a name="automated-migration-script"></a>Script de migration automatisé

Vous pouvez utiliser l’API pour automatiser la migration de contenu entre deux services Gestion des API (par exemple, un service dans l’environnement de test et un service dans l’environnement de production). Le script `scripts.v3/migrate.js` dans le [référentiel GitHub](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) du portail des développeurs de gestion des API simplifie ce processus d’automatisation.

> [!WARNING]
> Le script supprime le contenu du portail des développeurs dans votre service Gestion des API de destination. Si vous en avez besoin, veillez à effectuer une sauvegarde.

> [!NOTE]
> Si vous utilisez un portail auto-hébergé avec un compte de stockage personnalisé défini explicitement pour héberger des fichiers multimédias (c’est-à-dire que vous définissez le paramètre `blobStorageUrl` dans le fichier de configuration `config.design.json`), vous devez utiliser le [script](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js) `scripts/migrate.js` d’origine. Le script d’origine ne fonctionne pas pour les portails gérés ou auto-hébergés avec le compte de stockage multimédia géré par Gestion des API. Dans ce cas, utilisez plutôt le script à partir du dossier `/scripts.v3`.

Le script effectue les étapes suivantes :

1. Capturez le contenu et les médias du portail à partir du service Gestion des API source.
1. Supprimez le contenu du portail et le support du service Gestion des API de destination.
1. Chargez le contenu du portail et le support du service Gestion des API de destination.
1. Éventuellement et pour les portails gérés uniquement - publication automatique du portail.

Une fois l’exécution du script terminée, le service Gestion des API cible doit contenir le même contenu du portail que le service source et vous pouvez le voir en tant qu’administrateur.

* Si vous utilisez un portail géré, vous pouvez configurer le script pour qu’il publie automatiquement le portail de destination afin que la version migrée soit automatiquement disponible pour les visiteurs. 
* Si vous utilisez un portail auto-hébergé, vous devez publier le portail de destination manuellement. Suivez les instructions de publication et d’hébergement dans le didacticiel pour [configurer un portail de développement auto-hébergé](developer-portal-self-host.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)
- [Auto-héberger le portail des développeurs](developer-portal-self-host.md)