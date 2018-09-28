---
title: Vue d’ensemble du service de base de données relationnelle Azure Database for MariaDB
description: Vue d’ensemble du service de base de données relationnelle Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 1ba4a9bd597473b71ac7561c41b81783b899766a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971601"
---
# <a name="what-is-azure-database-for-mariadb"></a>Qu’est-ce qu’Azure Database for MariaDB ?
Azure Database for MariaDB est un service de base de données relationnelle dans le cloud Microsoft qui repose sur le moteur de base de données [MariaDB Community Edition](https://mariadb.org/download/). Ce service est en version préliminaire publique. Azure Database for MariaDB offre :

- Une haute disponibilité intégrée sans coût supplémentaire ;
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation ;
- Mise à l’échelle en fonction des besoins en quelques secondes ;
- La protection des données sensibles au repos et en mouvement ;
- Des sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours ;
- Une sécurité et une conformité de classe Entreprise.

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à gérer des machines virtuelles et une infrastructure. Par ailleurs, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix pour travailler avec la rapidité et l’efficacité exigées par votre activité, et ce sans avoir à acquérir de nouvelles compétences.

Cet article présente les principaux concepts et fonctionnalités d’Azure Database for MariaDB en matière de performances, d’extensibilité et de facilité de gestion. Il propose également des liens pour en explorer les détails. Consultez ces démarrages rapides pour bien commencer :
- [Créer un serveur Azure Database for MariaDB à l’aide du Portail Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Créer un serveur Azure Database for MariaDB à l’aide d’Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes
En préversion, le service Azure Database for MariaDB offre plusieurs niveaux de service : De base, Usage général et À mémoire optimisée. Chaque niveau offre différentes performances et fonctionnalités pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez [Niveaux tarifaires](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Comment savoir quand augmenter ou diminuer la taille des instances ? Vous utilisez les fonctionnalités intégrées de surveillance et d’alerte de performances, combinées avec les évaluations de performance basées sur des vCores. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle des vCores (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service (SLA) d’Azure (non proposé dans la préversion publique), soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database for MariaDB, vous profitez de la sécurité intégrée, de la tolérance en cas de panne et de la protection des données que vous devriez sinon acheter ou concevoir, créer et gérer. Avec Azure Database for MariaDB, vous pouvez utiliser la limite de restauration dans le temps pour restaurer un serveur à un état antérieur pouvant remonter jusqu’à 35 jours.

## <a name="secure-your-data"></a>Sécurisez vos données
Les services de base de données Azure ont une tradition de sécurité des données qui est perpétuée par Azure Database for MariaDB avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement et vous aident à superviser l’activité. Visitez le [Centre de confidentialité Azure](https://www.microsoft.com/en-us/trustcenter/security) pour plus d’informations sur la sécurité de la plateforme Azure.

Le service Azure Database for MariaDB utilise le chiffrement de stockage pour les données au repos. Les données, notamment les sauvegardes, sont chiffrées sur le disque (à l’exception des fichiers temporaires créés par le moteur pendant l’exécution de requêtes). Le service utilise le chiffrement AES 256 bits qui est inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.

Par défaut, le service Azure Database for MariaDB est configuré de manière à exiger la sécurité de la connexion SSL<!--[SSL connection security](./concepts-ssl-connection-security.md)--> pour les données en mouvement sur le réseau. L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application. Vous avez la possibilité de désactiver le recours obligatoire au protocole SSL pour la connexion à votre service de base de données si votre application cliente ne prend pas en charge la connectivité SSL.

## <a name="contacts"></a>Contacts
Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database for MariaDB, envoyez un e-mail à l’équipe Azure Database for MariaDB ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Remarque : il ne s’agit pas d’un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :
- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu la présentation d’Azure Database for MariaDB et répondu à la question « Qu’est-ce qu’Azure Database for MariaDB ? », vous êtes prêt à :
- Consultez la page de tarification pour des comparaisons de coûts et des calculatrices. [Tarification](https://azure.microsoft.com/pricing/details/mariadb/)
- Commencez par créer votre premier serveur. [Créer un serveur Azure Database for MariaDB à l’aide du Portail Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
