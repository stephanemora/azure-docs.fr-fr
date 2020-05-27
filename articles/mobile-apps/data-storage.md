---
title: Stocker, gérer et conserver des données d’application dans le cloud avec Visual Studio App Center et les services Azure
description: Découvrez des services tels que Visual Studio App Center qui vous permettent de stocker, de gérer et de conserver les données des applications mobiles dans le cloud.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: cb76151a3f952f932cc533b771283b5d45382366
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873446"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Stocker, synchroniser et interroger des données d’application mobile à partir du cloud
Quel que soit le type d'application que vous créez, vous êtes susceptible de générer et de traiter des données. Les attentes des utilisateurs de votre application sont élevées. Ils veulent que l'application fonctionne rapidement et avec fluidité, en toutes circonstances. La plupart des applications fonctionnent également sur plusieurs appareils. Vous pouvez accéder à votre application à partir d'un ordinateur de bureau ou d'un appareil mobile. Plusieurs utilisateurs peuvent utiliser l'application en même temps et partager des données en espérant disposer d'un accès immédiat et en temps réel aux données.

Les utilisateurs de votre application ne disposeront pas toujours d'une connexion Internet. Les applications sont conçues et prévues pour fonctionner avec ou sans connexion Internet. Les développeurs doivent choisir une solution adaptée pour le stockage et la synchronisation de leurs données dans le cloud, afin d'offrir une expérience exceptionnelle aux clients qui utilisent leur application.

Microsoft propose un large éventail de services qui éliminent le besoin de faire tourner des serveurs, de choisir votre base de données ou de vous soucier de la mise à l'échelle ou de la sécurité pour offrir l'expérience la plus riche possible. Ces services vous offrent une expérience de développement exceptionnelle qui vous permet de stocker les données d'application dans le cloud en utilisant des API SQL ou NoSQL. Vous pouvez également synchroniser les données sur tous les appareils et permettre aux applications de fonctionner avec ou sans connexion réseau pour créer des applications évolutives et robustes.

Utilisez les services suivants pour gérer et stocker les données d’application mobile dans le cloud.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est un service de base de données multimodèle distribué à l’échelle mondiale. Vous pouvez l'utiliser pour générer des applications à l'échelle de la planète. Azure Cosmos DB vous permet de faire évoluer en toute flexibilité et de façon indépendante le débit et le stockage dans un nombre illimité de régions Azure à travers le monde. Vous pouvez tirer parti d'un accès ultrarapide aux données en utilisant vos surfaces d'API préférées. Ces surfaces incluent SQL, MongoDB, Cassandra, Tables ou Gremlin. Azure Cosmos DB offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des contrats SLA complets.

**Fonctionnalités clés**
- Prise en charge d'un large éventail d'API, notamment l'API SQL (Core), l'API Cassandra, l'API MongoDB, l'API Gremlin et l'API Table.
- La distribution mondiale clé en main réplique vos données où que soient vos utilisateurs. Vos utilisateurs peuvent interagir avec un réplica des données proche d'eux.
- Pas de gestion de schéma ou d'index car le moteur de base de données est totalement indépendant du schéma.
- Présence régionale exhaustive car Azure Cosmos DB est disponible dans toutes les régions Azure du monde, dont 54 régions dans le cloud public.
- Choix de cohérence multiples et définis avec précision car le protocole de réplication multimaître d'Azure Cosmos DB a été soigneusement conçu pour offrir cinq choix de cohérence bien définis. Ces cinq choix sont les suivants : Forte, Obsolescence limitée, Session, Préfixe cohérent et Éventuelle.
- Disponibilité à 99,999 % pour les lectures et les écritures.
- Déclenchement par programmation (ou par le biais du portail Azure) du basculement régional de votre compte Azure Cosmos DB afin d'être sûr que votre application est conçue pour résister à un sinistre régional.
- Faible latence garantie, au 99e centile partout dans le monde.

**Informations de référence**
- [Azure portal](https://portal.azure.com) 
- [Documentation Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) est un service managé de base de données relationnelle à usage général. Vous pouvez l'utiliser afin de créer une couche de stockage de données hautement disponible et très performante pour les applications et les solutions dans le cloud Azure.

**Fonctionnalités clés**
- **Modèles et outils de base de données élastiques :** une base de données élastique permet aux développeurs de mettre des ressources en commun au sein d'un groupe de bases de données pour la mise à l'échelle. Pour la gestion administrative de ces ressources, vous soumettez un script en tant que travail. La base de données SQL exécute ensuite le script dans les bases de données.
- **Haute performance :** les applications à haut débit peuvent tirer parti de la dernière version. Elle offre 25 % de puissance de base de données Premium en plus.
- **Sauvegardes, réplication et haute disponibilité :** la réplication intégrée et un contrat SLA garanti par Microsoft au niveau de la base de données assurent la continuité des applications et la protection contre les événements catastrophiques. La géoréplication active vous permet de configurer le basculement et la restauration libre-service, qui assurent un contrôle total sur la « récupération d'urgence ». La restauration des données est disponible à partir des sauvegardes de données remontant à un maximum de 35 jours.
- **Maintenance presque nulle :** les logiciels automatiques font partie du service. Les réplicas système intégrés contribuent à assurer une protection des données inhérente, une disponibilité de la base de données et la stabilité du système. Les réplicas système sont automatiquement déplacés vers de nouveaux ordinateurs. Ils sont approvisionnés à la volée, car les anciens ne fonctionnent pas.
- **Sécurité :** Azure SQL Database fournit un portefeuille de fonctionnalités de sécurité afin de répondre aux stratégies de conformité de votre organisation ou secteur d'activité :
    - L’audit offre aux développeurs la possibilité d’effectuer des tâches liées à la conformité et d’obtenir des informations sur les activités.
    - Les développeurs et le personnel informatique peuvent également implémenter des stratégies au niveau de la base de données afin de limiter l’accès aux données sensibles à l’aide de la sécurité au niveau des lignes, du masquage des données dynamiques et du chiffrement transparent des données pour Azure SQL Database.
    - Azure SQL Database est vérifié par les principaux auditeurs de cloud dans le cadre des principales approbations et certifications de conformité Azure, comme HIPAA BAA, ISO/IEC 27001:2005, FedRAMP et les clauses contractuelles types de l'UE.

**Informations de référence**
- [Azure portal](https://portal.azure.com) 
- [Documentation Azure SQL Database](/azure/sql-database/) 
