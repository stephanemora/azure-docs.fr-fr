---
title: Distribuer des données mondialement avec Azure Cosmos DB
description: Découvrez plus d’informations sur la géoréplication à l’échelle de la planète, le multimaître, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, service de base de données multimodèle distribué mondialement.
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 2c217a1a89d3b573bfe2297a263bf55849b5f6e1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843850"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribution de données mondiale avec Azure Cosmos DB

Les applications actuelles doivent être hautement réactives et toujours en ligne. Pour obtenir une faible latence et une haute disponibilité, les instances de ces applications doivent être déployées dans des centres de données qui sont proches des utilisateurs. Ces applications sont généralement déployées dans plusieurs centres de données et sont dénommées comme étant globalement distribuées. Les applications globalement distribuées ont besoin d’une base de données globalement distribuée qui peut répliquer en toute transparence les données n’importe où dans le monde pour permettre aux applications de fonctionner sur une copie des données qui est proche de ses utilisateurs. 

Azure Cosmos DB est un service de base de données distribué au niveau mondial conçu pour offrir une faible latence, une évolutivité élastique du débit, une sémantique bien définie pour la cohérence des données et une haute disponibilité. En résumé, si votre application a besoin d’un temps de réponse rapide garanti partout dans le monde, si elle a besoin d’être toujours en ligne et a besoin d’une évolutivité illimitée et élastique du débit et du stockage, envisagez de créer des applications en utilisant Azure Cosmos DB.

Vous pouvez configurer vos bases de données afin qu’elles soient disponibles au niveau mondial et accessibles dans n’importe laquelle des régions Azure concernées. Pour réduire la latence, placez les données plus près de l’endroit où se trouvent vos utilisateurs. Le choix des régions requises dépend de la portée globale de votre application et de l'emplacement de vos utilisateurs. Azure Cosmos DB réplique de manière transparente les données de votre compte vers l’ensemble des régions configurées associées à votre compte. Il fournit une image unique des conteneurs et de la base de données Cosmos distribuée à l’échelle mondiale afin que votre application puisse lire et écrire les données au niveau local. 

Avec Azure Cosmos DB, vous pouvez à tout moment ajouter ou supprimer des régions associées à votre compte. Il n’est pas nécessaire de suspendre ou de redéployer votre application pour ajouter ou supprimer une région. L’application reste en permanence hautement disponible en raison des fonctionnalités de multirésidence offertes par le service.

## <a name="key-benefits-of-global-distribution"></a>Principaux avantages de la distribution mondiale

**Créez des applications globales actif/actif.** Avec la fonctionnalité multimaître, chaque région est une région d’écriture. Chaque région est également accessible en lecture. La fonctionnalité multimaître garantit également :

- Extensibilité pour l’écriture élastique illimitée. 
- Disponibilité en lecture et en écriture de 99,999 % dans le monde entier.
- Lectures et écritures traitées en moins de 10 millisecondes au 99e centile.

Avec les API de multirésidence Azure Cosmos DB, votre application tient compte de la région la plus proche. Elle peut ensuite envoyer des demandes vers cette région. La région la plus proche est identifiée sans aucune modification de configuration. Lorsque vous ajoutez et supprimez des régions de votre compte Azure Cosmos DB, votre application n’a pas besoin d’être redéployée. Elle continue d’être hautement disponible.

**Créez des applications hautement réactives.** Votre application peut être facilement conçue pour traiter des lectures et des écritures en quasi temps réel. Elle peut utiliser des latences de moins de 10 millisecondes sur toutes les régions que vous avez choisies pour votre base de données. Azure Cosmos DB gère en interne la réplication des données entre les régions. Par conséquent, le niveau de cohérence sélectionné pour le compte Azure Cosmos DB est garanti.

De nombreuses applications bénéficient des améliorations apportées aux performances grâce à la possibilité d’effectuer des écritures (locales) dans plusieurs régions. Certaines des applications qui requièrent une cohérence forte préfèrent rediriger toutes les écritures vers une seule région. Pour ces applications, Azure Cosmos DB prend en charge les configurations dans une région ou dans plusieurs régions.

**Créez des applications hautement disponibles.** L’exécution d’une base de données dans plusieurs régions permet d’accroître sa disponibilité. Si une région n’est pas disponible, les autres régions gèrent automatiquement les requêtes des applications. Pour les bases de données dans plusieurs régions, Azure Cosmos DB offre la disponibilité des lectures et écritures, à 99,999 %.

**Maintenez la continuité des activités pendant les pannes régionales.** Azure Cosmos DB prend en charge le [basculement automatique](how-to-manage-database-account.md#automatic-failover) en cas de panne régionale. Durant les pannes, Azure Cosmos DB continue à assurer ses SLA en matière de débit, de disponibilité, de cohérence et de latence. Pour vous aider à garantir la haute disponibilité de votre application dans son ensemble, Azure Cosmos DB propose des API de basculement manuel permettant de simuler une panne régionale. Cette API vous permet de tester régulièrement la continuité des activités.

**Étendez le débit des lectures et des écritures dans le monde entier.** Grâce à la fonctionnalité multimaître, vous pouvez faire évoluer le débit des lectures et écritures de manière élastique, au niveau mondial. La fonctionnalité multimaître garantit que le débit que votre application configure sur une base de données Azure Cosmos DB ou un conteneur est assuré dans toutes les régions. Le débit est également protégé par des [SLA soutenus financièrement](https://aka.ms/acdbsla).

**Faites votre choix entre plusieurs modèles de cohérence bien définis.** Le protocole de réplication d’Azure Cosmos DB offre cinq modèles de cohérence bien définis, pratiques et intuitifs. Chaque modèle offre un équilibre pertinent entre cohérence et performances. Utilisez ces modèles de cohérence pour créer très simplement des applications mondialement distribuées.

## <a id="Next Steps"></a>Étapes suivantes

Découvrez plus d’informations la diffusion mondiale dans les articles suivants :

* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Configurer les clients pour la multirésidence](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Ajouter ou supprimer des régions de votre compte Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Créer une stratégie de résolution de conflits personnalisée pour les comptes d’API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)