---
title: Présentation du guide de migration de MySQL local vers Azure Database pour MySQL
description: Guide de migration de MySQL local vers Azure Database pour MySQL
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: 1a65469b38a82b4dabeeb2be1f2ea8db269b760d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958629"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-introduction"></a>Présentation du guide de migration de MySQL local vers Azure Database pour MySQL

Ce guide de migration est conçu pour fournir des informations empilables et actionnables aux intégrateurs de logiciels et aux clients MySQL cherchant à migrer les charges de travail MySQL vers [Azure Database pour MySQL.](../overview.md) Ce guide fournit des informations applicables à la plupart des cas et donne des conseils pour réussir la planification et l’exécution d’une migration MySQL vers Azure.

Le processus de déplacement des bases de données et charges de travail MySQL existantes dans le cloud peut présenter des difficultés au niveau de la fonctionnalité de charge de travail et de la connectivité des applications existantes. Les informations présentées dans ce guide offrent des liens utiles et des recommandations axés sur une migration réussie et garantissent que les charges de travail et les applications continuent de fonctionner comme prévu initialement.

Les informations fournies sont centrées sur le parcours d’un client utilisant le [Cloud Adoption Framework](/azure/cloud-adoption-framework/get-started/) de Microsoft pour effectuer des activités d’évaluation, de migration et de post-optimisation pour un environnement Azure Database pour MySQL.

## <a name="mysql"></a>MySQL

MySQL a un historique riche dans la communauté open source et est devenu populaire dans les entreprises du monde entier pour les sites web et autres applications critiques. Ce guide aide les administrateurs qui sont chargés de délimiter, planifier et exécuter la migration. Les administrateurs qui débutent avec MySQL peuvent également consulter la [documentation MySQL](https://dev.mysql.com/doc/) pour obtenir des informations plus approfondies sur les fonctionnements internes de MySQL. Ce guide contient également des liens vers plusieurs articles de référence dans chacune des sections, qui vous orienteront vers des informations et des tutoriels utiles.

## <a name="azure-database-for-mysql"></a>Azure Database pour MySQL

[Azure Database pour MySQL](../overview.md) est une offre PaaS (Platform as a Service) de Microsoft, où l’environnement MySQL est complètement managé. Dans cet environnement complètement managé, les mises à jour du système d’exploitation et des logiciels sont automatiquement appliquées, ainsi que l’implémentation de la haute disponibilité et de la protection des données.

En plus de l’offre PaaS, il est toujours possible d’exécuter MySQL sur des machines virtuelles Azure. Consultez l’article [Choisir l’option MySQL Server appropriée dans Azure](../select-right-deployment-type.md) pour déterminer le type de déploiement le mieux adapté à la charge de travail de données cible.

![Comparaison des environnements MySQL](./media/image3.jpg)

**Comparaison des environnements MySQL.**

Ce guide porte entièrement sur la migration des charges de travail MySQL locales vers l’offre de plateforme en tant que service Azure Database pour MySQL, en raison de ses différents avantages par rapport à l’infrastructure en tant que service (IaaS), tels que les fonctionnalités de scale-up/scale-out, de paiement à l’utilisation, de haute disponibilité, de sécurité et de gestion.  

## <a name="summary"></a>Récapitulatif

Une fois que vous avez terminé ce guide, vous pouvez essayer de [déployer un exemple d’application et de passer en revue les paramètres serveur](summary.md#next-steps) par vous-même.

> [!div class="nextstepaction"]
> [Cas d’usage représentatif](./representative-use-case.md)