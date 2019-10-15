---
title: Vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL
description: Fournit une vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177963"
---
# <a name="what-is-azure-database-for-postgresql"></a>Qu’est-ce qu’Azure Database pour PostgreSQL ?
Azure Database pour PostgreSQL est un service de base de données relationnelle dans le cloud de Microsoft conçu pour les développeurs. Il est basé sur la version communauté du moteur de base de données [PostgreSQL](https://www.postgresql.org/) open source et est disponible dans deux options de déploiement : Serveur unique et Hyperscale (Citus) (préversion).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database pour PostgreSQL - Serveur unique
L’option de déploiement Serveur unique offre :

- Une haute disponibilité intégrée sans coût supplémentaire (SLA 99,99 %)
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation
- Une mise à l’échelle verticale en fonction des besoins en quelques secondes
- La supervision et la génération d’alertes pour évaluer rapidement l’impact de la mise à l’échelle
- La protection des données sensibles au repos et en mouvement
- Des sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours
- Une sécurité et une conformité de classe Entreprise

Toutes ces fonctionnalités ne nécessitent presque aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à gérer des machines virtuelles et une infrastructure. Vous pouvez également continuer à développer votre application avec les outils open source et la plateforme de votre choix sans avoir besoin d’acquérir de nouvelles compétences.

L’option de déploiement Serveur unique offre trois niveaux tarifaires : De base, Usage général et À mémoire optimisée. Chaque niveau offre des fonctionnalités de ressources différentes pour prendre en charge vos charges de travail de base de données. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez  [Niveaux tarifaires](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion)
L’option Hyperscale (Citus) met horizontalement à l’échelle les requêtes sur plusieurs machines à l’aide du partitionnement. Son moteur de requête parallélise les requêtes SQL entrantes sur ces serveurs afin de générer des réponses plus rapides sur les jeux de données volumineux. Elle s’adresse aux applications qui nécessitent des performances et une mise à l’échelle plus grandes, généralement des charges de travail qui approchent, ou dépassent déjà, 100 Go de données.

L’option de déploiement Hyperscale (Citus) offre les fonctionnalités suivantes :

- Mise à l’échelle horizontale sur plusieurs machines à l’aide du partitionnement
- Parallélisation des requêtes sur ces serveurs pour générer des réponses plus rapides sur les jeux de données volumineux
- Excellente prise en charge des applications multilocataires, analytique opérationnelle en temps réel et charges de travail transactionnelles à débit élevé

Les applications générées pour PostgreSQL peuvent exécuter des requêtes distribuées sur Hyperscale (Citus) avec des [bibliothèques de connexions](./concepts-connection-libraries.md) standard et des modifications minimes.

Disponible en préversion publique, Hyperscale (Citus) n’offre pas de contrat SLA.

## <a name="data-security"></a>Sécurité des données
Azure Database pour PostgreSQL respecte la tradition de sécurité des données des services de base de données Azure. Il offre des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement et vous aident à superviser l’activité. Visitez le [Centre de confidentialité Azure](https://azure.microsoft.com/overview/trusted-cloud/) pour plus d’informations sur la sécurité de la plateforme Azure.

Le service Azure Database pour PostgreSQL utilise le module de chiffrement conforme à la norme FIPS 140-2 pour chiffrer le stockage des données au repos. À l’exception des fichiers temporaires créés durant l’exécution des requêtes, toutes les données, y compris les sauvegardes, sont chiffrées sur le disque. Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé. Par défaut, le service Azure Database pour PostgreSQL requiert des connexions sécurisées pour les données en mouvement à la fois sur le réseau et entre la base de données et l’application cliente.

## <a name="contacts"></a>Contacts
Pour toute question ou suggestion au sujet d’Azure Database pour PostgreSQL, envoyez un e-mail à l’équipe Azure Database pour PostgreSQL ([@Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Utilisez cette adresse pour les questions générales, plutôt que pour des tickets de support.

De plus, tenez compte des points de contact suivants le cas échéant :
- Pour contacter le support technique Azure ou résoudre un problème lié à votre compte, [émettez un ticket à partir du portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Étapes suivantes
- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/postgresql/) pour des comparaisons de coûts et des calculatrices.
- Commencez par créer votre première instance de [serveur unique](./quickstart-create-server-database-portal.md) or [Hyperscale (Citus) (préversion)](./quickstart-create-hyperscale-portal.md) d’Azure Database pour PostgreSQL.
- Créez votre première application Python, PHP, Ruby, C\#, Java, Node.js : [Bibliothèques de connexions](./concepts-connection-libraries.md)
