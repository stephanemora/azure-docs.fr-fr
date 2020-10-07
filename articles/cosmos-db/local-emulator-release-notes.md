---
title: Téléchargement de l’émulateur Cosmos Azure et notes de publication
description: Procurez-vous les notes de publication de l’émulateur Azure Cosmos pour connaître les différentes versions et les informations de téléchargement.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: ec034ac3cf6f4cbec2d373d9dade45a00f70ddbe
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566686"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Émulateur Azure Cosmos - Notes de publication et informations sur le téléchargement

Cet article présente les notes de publication de l’émulateur Azure Cosmos avec une liste de mises à jour de fonctionnalités qui ont été apportées à chaque version. Il répertorie également la dernière version de l’émulateur pour vous permettre de la télécharger et de l’utiliser.

## <a name="download"></a>Téléchargement

| | |
|---------|---------|
|**Téléchargement de MSI**|[Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Prise en main**|[Développer localement avec l’émulateur Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notes de publication

### <a name="2115-23-august-2020"></a>2.11.5 (23 août 2020)

Cette version ajoute deux nouvelles options de démarrage de l’émulateur Cosmos : 

* « /EnablePreview » active les fonctionnalités en préversion pour l’émulateur. Il s’agit des fonctionnalités de préversion qui sont encore en cours de développement ; elles sont accessibles via l’intégration continue et l’exemple d’écriture.
* « /EnableAadAuthentication » permet à l’émulateur d’accepter des jetons Azure Active Directory personnalisés comme alternative aux clés primaires Azure Cosmos. Cette fonctionnalité est toujours en cours de développement ; les affectations de rôles spécifiques et les autres paramètres liés aux autorisations ne sont pas pris en charge actuellement.

### <a name="2112-07-july-2020"></a>2.11.2 (07 juillet 2020)

- Cette version modifie la manière dont sont collectées les traces ETL qui sont nécessaires lors du dépannage de l’émulateur Cosmos. Les outils WPR (Windows Performance Runtime) sont désormais utilisés par défaut pour la capture des traces ETL. La capture basée sur LOGMAN est désormais dépréciée. Ce changement a été en partie nécessaire à cause des mises à jour de sécurité Windows récentes qui impactent le fonctionnement de LOGMAN lorsque celui-ci est exécuté via l’émulateur Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 juin 2020)

- Cette version corrige deux bogues liés à l’émulateur Data Explorer. Dans certains cas, quand vous utilisez l’émulateur Data Explorer par le biais d’un navigateur web, il ne parvient pas à se connecter au point de terminaison de l’émulateur Cosmos, et toutes les actions associées, telles que la création d’une base de données ou d’un conteneur, génèrent une erreur. Le deuxième problème résolu est lié à la création d’un élément à partir d’un fichier JSON à l’aide de l’action de chargement de Data Explorer.

### <a name="2110"></a>2.11.0

- Cette version introduit la prise en charge du débit provisionné par la mise à l’échelle automatique. Ces nouvelles fonctionnalités comprennent la possibilité de définir un niveau personnalisé du débit maximal provisionné dans les unités de requête (RU/s), d’activer la mise à l’échelle automatique sur les bases de données et conteneurs existants ainsi que la prise en charge programmatique au moyen des kits SDK Azure Cosmos DB.
- Correction d’un problème lors de l’interrogation d’un grand nombre de documents (plus de 1 Go) pendant lequel l’émulateur échoue avec le code d’état d’erreur interne 500.

### <a name="292"></a>2.9.2

- Cette version corrige un bogue pendant l’activation de la prise en charge du point de terminaison MongoDb version 3.2. Elle ajoute également la prise en charge de la génération de traces ETL à des fins de dépannage à l’aide de WPR au lieu de LOGMAN.

### <a name="291"></a>2.9.1

- Cette version corrige deux problèmes dans la prise en charge de l’API de requête, et restaure la compatibilité avec les anciens systèmes d’exploitation tels que Windows Server 2012.

### <a name="290"></a>2.9.0

- Cette version ajoute une option permettant de définir la cohérence avec un préfixe cohérent et d’augmenter les limites maximales pour les utilisateurs et les autorisations.

### <a name="272"></a>2.7.2

- Cette version ajoute la prise en charge du serveur MongoDB version 3.6 à l’émulateur Cosmos. Pour démarrer un point de terminaison MongoDB qui cible la version 3.6 du service, démarrez l’émulateur à partir d’une ligne de commande d’administrateur avec l’option « /EnableMongoDBEndpoint=3.6 ».

### <a name="270"></a>2.7.0

- Cette version corrige une régression qui empêchait les utilisateurs d’exécuter des requêtes sur le compte d’API SQL à partir de l’émulateur lors de l’utilisation de clients .NET Core ou .NET x86.

### <a name="246"></a>2.4.6

- Cette version offre une parité avec les fonctionnalités du service Azure Cosmos à partir de juillet 2019, avec les exceptions indiquées dans [Développer localement avec l’émulateur Azure Cosmos](local-emulator.md). Elle résout aussi plusieurs bogues liés à l’arrêt de l’émulateur quand il est appelé via la ligne de commande, et les remplacements d’adresses IP internes pour les clients SDK utilisant une connectivité en mode direct.

### <a name="243"></a>2.4.3

- Désactivation du démarrage du service de MongoDB par défaut. Seul le point de terminaison SQL est activé par défaut. L’utilisateur doit démarrer le point de terminaison manuellement à l’aide de l’option de ligne de commande /EnableMongoDbEndpoint. C’est maintenant similaire aux autres points de terminaison de service, tels que Gremlin, Cassandra et Table.
- Correction d’un bogue dans l’émulateur lors du démarrage avec « /AllowNetworkAccess », où les points de terminaison Gremlin, Cassandra et Table ne géraient pas correctement les requêtes des clients externes.
- Ajoutez des ports de connexion directe aux paramètres de règles de pare-feu.

### <a name="240"></a>2.4.0

- Correction d’un problème avec l’émulateur, qui ne parvenait pas à démarrer lorsque des applications de surveillance réseau, comme le client Pulse, sont présentes sur l’ordinateur hôte.
