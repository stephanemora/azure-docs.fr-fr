---
title: Azure Database pour PostgreSQL - Notes de publication sur le serveur flexible
description: Notes de publication sur Azure Database pour PostgreSQL - Serveur flexible
author: sr-msft
ms.author: srranga
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: ad417bc44de7d13594f003f7aa1e39b2308204ed
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064860"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>Notes de publication - Azure Database pour PostgreSQL - Serveur flexible

Cette page fournit les toutes dernières informations concernant les ajouts de fonctionnalités, les versions de moteur prises en charge et les extensions. Elle inclut également toutes les annonces relatives au serveur flexible - PostgreSQL.

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

## <a name="release-april-26-2021"></a>Version : 26 avril 2021

* Prise en charge des [dernières versions mineures de PostgreSQL](./concepts-supported-versions.md) 12.6 et 11.11 avec les nouvelles créations de serveur
* Prise en charge de la [zone DNS privée](./concepts-networking.md#private-access-vnet-integration) du réseau virtuel
* Prise en charge du choix de la zone de disponibilité pendant l’opération de récupération jusqu’à une date et heure
* Prise en charge de nouvelles [régions](./overview.md#azure-regions), y compris Australie Est, Canada Centre et France Centre
* Prise en charge du pooler de connexions [PgBouncer intégré](./concepts-pgbouncer.md) 
* Prise en charge de la version 2.3.2 de l’extension [pglogical](https://github.com/2ndQuadrant/pglogical)
* [Performances intelligentes](concepts-query-store.md) en préversion
* Plusieurs corrections de bogues et amélioration de la stabilité et des performances

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet du serveur flexible Azure Database pour PostgreSQL, envoyez un e-mail à l’équipe Azure Database pour PostgreSQL ([@Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Notez que cette adresse e-mail n’est pas un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu l'introduction au mode de déploiement Azure Database pour PostgreSQL - Serveur flexible, vous êtes prêt à créer votre premier serveur : [Créer une instance Azure Database pour PostgreSQL - Serveur flexible à l’aide du portail Azure](./quickstart-create-server-portal.md)