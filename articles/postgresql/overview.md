---
title: Vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL
description: Fournit une vue d’ensemble du service de base de données relationnelle d’Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/14/2018
ms.openlocfilehash: 775c9990c85feb3e9e180af6470e7c9a1aa124f3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808976"
---
# <a name="what-is-azure-database-for-postgresql"></a>Qu’est-ce qu’Azure Database pour PostgreSQL ?

Azure Database pour PostgreSQL est un service de base de données relationnelle dans le cloud Microsoft conçu pour les développeurs basés sur la version Community du moteur de base de données [PostgreSQL](https://www.postgresql.org/) open source,versions 9.5, 9.6 et 10. Azure Database pour PostgreSQL offre :

- Une haute disponibilité intégrée sans coût supplémentaire
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation
- Mise à l’échelle en fonction des besoins en quelques secondes
- La protection des données sensibles au repos et en mouvement
- Des sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours
- Une sécurité et une conformité de classe Entreprise

Toutes ces fonctionnalités ne nécessitent presque aucune administration, et toutes sont fournies sans coût supplémentaire. Ces fonctionnalités vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à la gestion des machines virtuelles et de leur infrastructure. En outre, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix et fournir avec la vitesse et l’efficacité que vos activités professionnelles exigent sans avoir à acquérir de nouvelles compétences. 

Cet article présente les principaux concepts et fonctionnalités d’Azure Database pour PostgreSQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Consultez ces démarrages rapides pour bien commencer :

- [Création d’une instance d’Azure Database pour PostgreSQL à l’aide du portail Azure](quickstart-create-server-database-portal.md)
- [Création d’une instance d’Azure Database pour PostgreSQL à l’aide de la CLI Azure](quickstart-create-server-database-azure-cli.md)

Pour accéder à des exemples Azure CLI, consultez :

- [Exemples Azure CLI pour base de données pour PostgreSQL](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes
Le service Azure Database pour PostgreSQL propose trois niveaux tarifaires : De base, Usage général et À mémoire optimisée. Chaque niveau offre des fonctionnalités de ressources différentes pour prendre en charge vos charges de travail de base de données. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez  [Niveaux tarifaires](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Comment savoir quand augmenter ou diminuer la taille des instances ? Vous utilisez la surveillance Azure intégrée et les fonctionnalités d’alerte. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle (montées ou descentes en charge) en fonction de vos besoins en stockage ou en performances actuels ou pour un projet. Pour plus d’informations, consultez [Alertes](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service (SLA) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database pour PostgreSQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec Azure Database pour PostgreSQL, chaque niveau tarifaire offre un ensemble complet de fonctionnalités et d’options assurant la continuité des activités, que vous pouvez utiliser pour démarrer votre base de données et vous assurer qu’elle fonctionne de manière continue. Vous pouvez utiliser la [limite de restauration dans le temps](howto-restore-server-portal.md) pour renvoyer une base de données à un état antérieur, jusqu’à 35 jours. En outre, si le centre de données hébergeant vos bases de données connaît une panne, vous pouvez restaurer les bases de données à partir de copies géo-redondantes des sauvegardes récentes.

## <a name="secure-your-data"></a>Sécurisez vos données
Les services de base de données Azure ont une tradition de sécurité des données qu’Azure Database pour PostgreSQL respecte avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement, et vous aident à surveiller l’activité. Visitez le [Centre de confidentialité Azure](https://azure.microsoft.com/overview/trusted-cloud/) pour plus d’informations sur la sécurité de la plateforme Azure.

Le service Base de données Azure pour PostgreSQL utilise le chiffrement de stockage pour les données au repos. Les données incluant les sauvegardes sont chiffrées sur le disque (à l’exception des fichiers temporaires créés par le moteur lors de l’exécution des requêtes). Le service utilise le chiffrement AES 256 bits qui est inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.

Par défaut, le service Base de données Azure pour PostgreSQL est configuré afin de requérir la [sécurité de connexion SSL](./concepts-ssl-connection-security.md) pour les données en mouvement sur le réseau. L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application. Vous avez la possibilité de désactiver le recours obligatoire au protocole SSL pour la connexion à votre service de base de données si votre application cliente ne prend pas en charge la connectivité SSL.

## <a name="contacts"></a>Contacts
Pour toute question ou suggestion au sujet d’Azure Database pour PostgreSQL, envoyez un e-mail à l’équipe Azure Database pour PostgreSQL ([@Ask Azure DB pour PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Remarque : il ne s’agit pas d’un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :
- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Étapes suivantes
- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/postgresql/) pour des comparaisons de coûts et des calculatrices.
- Commencez par [créer votre première instance d’Azure Database pour PostgreSQL](./quickstart-create-server-database-portal.md).
- Créez votre première application Python, PHP, Ruby, C\#, Java, Node.js : [Bibliothèques de connexions](./concepts-connection-libraries.md)
