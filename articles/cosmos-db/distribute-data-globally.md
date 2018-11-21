---
title: Distribution mondiale des données avec Azure Cosmos DB | Microsoft Docs
description: Découvrez plus d’informations sur la géoréplication à l’échelle de la planète, le multimaître, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, service de base de données multimodèle distribué mondialement.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c90450fa4cc35b460198f5a351a965aee4ea4f4b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636409"
---
# <a name="build-globally-distributed-applications-with-azure-cosmos-db"></a>Créer des applications distribuées mondialement avec Azure Cosmos DB

De nombreuses applications actuelles nécessitent d’être exploitées dans plusieurs centres de données. Ces applications sont appelées applications distribuées mondialement. Toujours actives, ces applications sont accessibles à des utilisateurs répartis aux quatre coins du monde. Il peut s’avérer délicat de gérer la distribution des données par ces applications au niveau mondial, tout en assurant une latence faible, une évolutivité élastique du débit et une haute disponibilité à travers le monde. Azure Cosmos DB est un service de base de données distribué au niveau mondial conçu pour offrir une faible latence, une évolutivité élastique du débit, une sémantique bien définie pour la cohérence des données et une haute disponibilité. En résumé, si votre application a besoin d’un temps de réponse rapide garanti partout dans le monde, si elle a besoin d’être toujours en ligne et a besoin d’une évolutivité illimitée et élastique du débit et du stockage, vous devez envisager de créer des applications en utilisant Azure Cosmos DB.

Il s’agit d’un service Azure fondamental, qui est disponible dans toutes les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/), par défaut. Microsoft exploite des centres de données Azure dans plus de 54 régions du monde, et continue à développer sa présence régionale pour répondre aux besoins croissants des clients. Lorsque vous créez un compte Azure Cosmos, vous choisissez la ou les régions dans lesquelles déployer ce service. Microsoft gère le service Cosmos Azure DB 24 heures sur 24, 7 jours sur 7, afin que vous puissiez vous concentrer sur vos applications.

Vous pouvez configurer vos bases de données afin qu’elles soient disponibles au niveau mondial et accessibles dans n’importe laquelle des régions Azure concernées. Pour réduire la latence, vous devez placer les données plus près de l’endroit où se trouvent vos utilisateurs. Le choix des régions requises dépend de la portée globale de votre application et de l'emplacement de vos utilisateurs. Azure Cosmos DB réplique de manière transparente les données de votre compte vers l’ensemble des régions configurées associées à votre compte. Il fournit une image unique des conteneurs et de la base de données Cosmos distribuée à l’échelle mondiale afin que votre application puisse lire et écrire les données au niveau local. Avec Azure Cosmos DB, vous pouvez à tout moment ajouter ou supprimer des régions associées à votre compte. Il n'est pas nécessaire de suspendre ou de redéployer votre application pour ajouter ou supprimer une région. L'application reste en permanence hautement disponible en raison des fonctionnalités d'hébergement multiple offertes par le service.

## <a name="key-benefits-of-global-distribution"></a>Principaux avantages de la distribution mondiale

**Créez des applications actives/actives mondiales** : avec la capacité multimaître, chaque région est une région d’écriture (en plus d'être lisible). La capacité multimaître garantit également une extensibilité illimitée en écriture, une disponibilité de lecture et d'écriture de 99,999 % partout dans le monde, ainsi que des lectures/écritures exécutées en moins de 10 millisecondes au 99e centile.  

Grâce aux API multihébergement d’Azure Cosmos DB, votre application identifie la région la plus proche et peut envoyer des requêtes à celle-ci. La région la plus proche est identifiée sans aucune modification de configuration. Lorsque vous ajoutez et supprimez des régions de votre compte Azure Cosmos DB, votre application n’a pas besoin d’être redéployée et continue d’être hautement disponible.

**Créez des applications hautement réactives** : votre application peut être facilement conçue pour exécuter des lectures et écritures en temps quasi réel, avec des latences en millisecondes à un seul chiffre, dans l’ensemble des régions que vous avez choisies pour votre base de données.  Azure Cosmos DB gère en interne la réplication des données entre les régions, de façon à garantir le niveau de cohérence choisi pour le compte Azure Cosmos.

De nombreuses applications bénéficieront des améliorations apportées aux performances grâce à la possibilité d’effectuer des écritures (locales) dans plusieurs régions. Certaines des applications qui requièrent une cohérence forte préfèrent rediriger toutes les écritures vers une seule région. Pour assurer la prise en charge de ces applications, Azure Cosmos DB prend en charge les configurations dans une région ou dans plusieurs régions.

**Créez des applications hautement disponibles** : l’exécution d’une base de données dans plusieurs régions permet d’accroître sa disponibilité. Si une région n’est pas disponible, les autres régions gèrent automatiquement les requêtes des applications. Pour les bases de données dans plusieurs régions, Azure Cosmos DB offre la disponibilité des lectures et écritures, à 99,999 %.

**Continuité d’activité pendant les pannes de courant régionales** : Azure Cosmos DB prend en charge le [basculement automatique](how-to-manage-database-account.md#automatic-failover) pendant une panne régionale. En outre, durant les pannes, Azure Cosmos DB continue à assurer ses SLA en matière de débit, de disponibilité, de cohérence et de latence. Pour vous aider à garantir la haute disponibilité de votre application dans son ensemble, Azure Cosmos DB propose des API de basculement manuel permettant de simuler une panne régionale. Cette API vous permet de tester régulièrement la continuité des activités.

**Extensibilité des lectures et écritures au niveau mondial** : grâce à la fonctionnalité multimaître, vous pouvez faire évoluer le débit des lectures et écritures de manière élastique, au niveau mondial. La fonctionnalité multimaître garantit que le débit que votre application configure sur une base de données Azure Cosmos DB ou un conteneur est assuré dans toutes les régions et protégé par des [SLA soutenus financièrement](https://aka.ms/acdbsla).

**Plusieurs modèles de cohérence correctement définis** : le protocole de réplication d’Azure Cosmos DB est conçu pour offrir cinq modèles de cohérence bien définis, pratiques et intuitifs. Chaque modèle de cohérence offre un équilibre pertinent entre cohérence et performances. Ces modèles de cohérence vous permettent de créer très simplement des applications mondialement distribuées.

## <a id="Next Steps"></a>Étapes suivantes

Découvrez plus d’informations la diffusion mondiale dans les articles suivants :

* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Article relatif à la configuration des clients pour le multihébergement](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Article relatif à l’ajout/la suppression des régions de votre compte Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Article relatif à la création d’une stratégie de résolution de conflits personnalisée pour les comptes SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)