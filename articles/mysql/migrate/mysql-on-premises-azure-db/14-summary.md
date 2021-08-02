---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Résumé
description: Ce document a présenté plusieurs sujets liés à la migration d’une application de MySQL local vers Azure Database pour MySQL.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 06885636d299fa406550f9bd332724e2e01d775f
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082819"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-summary"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Résumé

## <a name="prerequisites"></a>Prérequis

[Sécurité](13-security.md)

## <a name="overview"></a>Vue d’ensemble

Ce document a présenté plusieurs sujets liés à la migration d’une application de MySQL local vers Azure Database pour MySQL. Nous avons abordé la manière de commencer et d’évaluer le projet jusqu’au basculement des applications.

L’équipe chargée de la migration doit examiner les sujets avec soin, car les choix effectués peuvent avoir des répercussions sur la chronologie du projet. Le coût total de possession est attrayant en raison des nombreuses fonctionnalités d’entreprise prêtes à l’emploi.

L’approche du projet de migration est importante. L’équipe doit évaluer la complexité de l’application et de la base de données pour déterminer la durée de la conversion. Les outils de conversion facilitent la transition, mais il y a toujours eu une part de révision et de mise à jour manuelles. Il est important de prévoir des tâches de prémigration et des tests de post-migration.

L’architecture et la conception de l’application peuvent fournir de solides indicateurs quant au niveau d’effort requis. Par exemple, les applications qui utilisent des infrastructures ORM peuvent être d’excellents candidats, en particulier si la logique métier est contenue dans l’application plutôt que dans les objets de la base de données.

En fin de compte, plusieurs outils existent sur le marché, qu’ils soient gratuits ou payants. Ce document couvre les étapes nécessaires si l’équipe prévoit la migration d’une base de données à l’aide de l’un des outils open source les plus populaires. Quelle que soit la voie choisie, Microsoft et la communauté MySQL disposent des outils et de l’expertise nécessaires pour réussir la migration de la base de données.

## <a name="questions-and-feedback"></a>Questions et commentaires

Pour toute question ou suggestion sur l’utilisation d’Azure Database pour MySQL, envoyez un e-mail à l’équipe Azure Database pour MySQL (AskAzureDBforMySQL@service.microsoft.com). 

> [!Important]
> L’adresse de l’équipe Azure Database pour MySQL est réservée aux questions générales et non aux tickets de support.

De plus, tenez compte des points de contact suivants le cas échéant :

  - Pour contacter Support Azure ou résoudre un problème lié à votre compte, [créez un ticket à partir du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

  - Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
fwill
## <a name="find-a-partner-to-help-migrate"></a>Rechercher un partenaire pour aider à la migration

Ce guide peut être impressionnant, mais ne vous inquiétez pas \! Il existe de nombreux experts dans la communauté qui ont fait leurs preuves en matière de migration. [Recherchez un partenaire](https://www.microsoft.com/solution-providers/home) ou un [MVP](https://mvp.microsoft.com/MvpSearch) pour vous aider à trouver la stratégie de migration la plus appropriée. Vous n’êtes pas seul \!

Vous pouvez également parcourir les forums techniques et les groupes sociaux pour obtenir des informations concrètes plus détaillées :

  - [Forum de la communauté Microsoft](/answers/topics/azure-database-mysql.html)

  - [Communauté technique autour d’Azure Database pour MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/bg-p/ADforMySQL)

  - [StackOverflow pour Azure MySQL](https://stackoverflow.com/questions/tagged/azure-database-mysql)

  - [Groupe Facebook Azure ](https://www.facebook.com/groups/MsftAzure)

  - [Groupe LinkedIn Azure ](https://www.linkedin.com/groups/2733961/)

  - [Groupe LinkedIn de développeurs Azure ](https://www.linkedin.com/groups/1731317/)

## <a name="next-steps"></a>Étapes suivantes

Pour déployer un exemple d’application avec un guide de migration MySQL de bout en bout et pour vérifier les paramètres de serveur disponibles, consultez l’[annexe](15-appendix.md).