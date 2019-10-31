---
title: Stocker, gérer et conserver des données d’application dans le cloud avec Visual Studio App Center et les services Azure
description: Découvrez les services tels qu’App Center qui vous permettent de stocker, de gérer et de conserver des données d’application mobile dans le cloud.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795109"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Stocker, synchroniser et interroger des données d’application mobile à partir du cloud
Quel que soit le type d’application que vous créez, vous générerez et traiterez probablement des données. Les attentes des utilisateurs sont élevées ; ils souhaitent que l’application fonctionne rapidement et avec fluidité, dans toutes les circonstances. La plupart des applications fonctionnent sur plusieurs appareils. Par conséquent, quand vous accédez à votre application, il est possible que vous y accédiez à partir d’un ordinateur de bureau ou d’un appareil mobile. Plusieurs utilisateurs peuvent utiliser l’application en même temps et partager des données en s’attendant à disposer d’un accès immédiat et en temps réel aux données.

En plus de tout cela, les utilisateurs de votre application ne disposent pas toujours d’une connexion Internet. Les applications sont conçues et censées fonctionner avec ou sans connexion Internet. Avec toutes ces complexités croissantes, il est très important pour les développeurs de choisir une solution adaptée pour le stockage et la synchronisation de leurs données dans le cloud, afin d’offrir à leurs clients une expérience exceptionnelle pour leur application.

Microsoft propose un large éventail de services qui éliminent le besoin de faire tourner les serveurs, de choisir votre base de données ou de se soucier de la mise à l’échelle ou de la sécurité pour offrir une expérience la plus riche possible. Ces services offrent une expérience de développement exceptionnelle qui vous permet de stocker des données d’application dans le cloud à l’aide d’API SQL ou NoSQL, de synchroniser des données sur tous les appareils, et de permettre à l’application de fonctionner avec ou sans connexion réseau, tout ceci pour vous aider à créer une solution scalable et des applications robustes.

Utilisez les services suivants pour gérer et stocker les données d’application mobile dans le cloud.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) est un service de gestion des données qui permet aux applications de gérer, de conserver et de synchroniser des données d’application dans le cloud sur différents appareils et plateformes dans des scénarios en ligne et hors connexion. Il s’agit d’un **service basé sur Cosmos DB** qui évolue à mesure que votre base d’utilisateurs et le nombre d’applications augmente. Ce service garantit une faible latence, une haute disponibilité et une scalabilité élevée pour toutes vos données.

**Fonctionnalités clés**
- **Provisionnez une nouvelle base de données Cosmos DB**  ou **connectez-vous à une base de données Cosmos DB existante** en toute simplicité à partir du portail App Center.
- **Prise en charge des bases de données NoSQL** pour stocker, synchroniser et interroger facilement les données d’application.
- **Reposant sur Cosmos DB**, ce service hérite de la plupart des principales fonctionnalités offertes par Azure Cosmos DB et peut **être mis à l’échelle globalement** pour répondre aux besoins de votre entreprise.
- Fonctionnalités **de synchronisation en ligne et hors connexion** permettant de synchroniser les données entre les appareils.
- **Kits de développement logiciel (SDK) client** mobiles qui vous permettent de gérer facilement les données d’application privées.
- **Prise en charge de plateformes** : iOS, Android, Xamarin, React Native.

**Informations de référence**
- [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Bien démarrer avec les données App Center](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est le service de base de données multimodèle de Microsoft distribué qui vous aide à créer des applications à l’échelle mondiale. D’un simple clic, Cosmos DB vous permet de mettre à l’échelle de façon élastique et indépendante le débit et le stockage dans autant de régions Azure que nécessaire à l’échelon mondial. Vous pouvez mettre à l’échelle le débit et le stockage de façon élastique et tirer parti d’un accès rapide aux données (moins de 10 millisecondes) à l’aide de vos surfaces d’API favorites parmi SQL, MongoDB, Cassandra, Tables ou Gremlin. Cosmos DB offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des contrats SLA complets.

**Fonctionnalités clés**
- Prise en charge d’un **large éventail d’API**, notamment l’API SQL (Core), l’API Cassandra, l’API MongoDB, l’API Gremlin et l’API Table.
- La **distribution globale clés en main** réplique vos données où que soient les utilisateurs, afin que ces derniers puissent interagir avec le réplica des données le plus proche.
- **Aucun schéma ou gestion d’index**, car le moteur de base de données est entièrement indépendant du schéma.
- **Présence régionale exhaustive**, car Cosmos DB est disponible dans toutes les régions Azure dans le monde entier, y compris 54 régions dans le cloud public.
- **Choix de cohérence multiple bien définis**, car le protocole de réplication multimaître de Cosmos DB a été soigneusement conçu pour offrir cinq choix de cohérence bien définis (fort, obsolescence limitée, session, préfixe cohérent et éventuel).
- **Disponibilité à 99,999 %** pour les lectures et les écritures.
- **Déclenchement par programmation (ou par le biais du portail) du basculement régional** de votre compte COSMOS afin d’être sûr que votre application est conçue pour résister à un sinistre régional.
- **Faible latence garantie, au 99e centile**, partout dans le monde.

**Informations de référence**
- [Portail Azure](https://portal.azure.com) 
- [Documentation](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) est un service managé de bases de données relationnelles à usage général qui vous permet de créer une couche de stockage de données hautement disponible et performante pour les applications et solutions dans le cloud Azure.

**Fonctionnalités clés**
- **Modèles et outils de base de données élastique** : une base de données élastique offre aux développeurs la possibilité de regrouper des ressources devant être utilisées par un groupe de bases de données pour la mise à l’échelle, et qui peuvent ensuite être gérées de manière administrative en soumettant un script en tant que travail, ce script étant alors exécuté par SQL Database parmi les bases de données.
- **Performances élevées** : les applications à débit élevé peuvent tirer parti de la dernière version, qui augmente la puissance des bases de données Premium de 25 %.
- **Sauvegardes, réplication et haute disponibilité** : la réplication intégrée et un contrat SLA garanti par Microsoft au niveau de la base de données assurent la continuité des applications et la protection contre les événements catastrophiques. La géoréplication active vous permet de configurer le basculement et la restauration en libre-service, qui assurent un contrôle total sur la « récupération d’urgence » (restauration de données à partir de sauvegardes de données disponibles allant jusqu’à 35 jours).
- **Maintenance presque nulle** : les logiciels automatiques font partie du service, et les réplicas système intégrés permettent de fournir une protection des données, une disponibilité de base de données et une stabilité du système inhérentes. Les réplicas système sont déplacés automatiquement vers de nouveaux ordinateurs rapidement provisionnés quand les anciens tombent en panne.
- **Sécurité** : SQL Database fournit un portefeuille de fonctionnalités de sécurité afin de répondre aux stratégies de conformité de votre organisation ou secteur d’activité.
    - L’audit offre aux développeurs la possibilité d’effectuer des tâches liées à la conformité et d’obtenir des informations sur les activités.
    - Les développeurs et le personnel informatique peuvent également implémenter des stratégies au niveau de la base de données afin de limiter l’accès aux données sensibles à l’aide de la sécurité au niveau des lignes, du masquage des données dynamiques et du chiffrement transparent des données pour Azure SQL Database.
    - SQL Database est vérifié par les principaux auditeurs du cloud dans le cadre du périmètre des principales approbations et certifications de conformité Azure comme HIPAA BAA, ISO/IEC 27001:2005, FedRAMP et les clauses contractuelles types de l’UE.

**Informations de référence**
- [Portail Azure](https://portal.azure.com) 
- [Documentation](/azure/sql-database/)
   