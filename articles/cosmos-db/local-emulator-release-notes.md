---
title: Téléchargement de l’émulateur Cosmos Azure et notes de publication
description: Procurez-vous les notes de publication de l’émulateur Azure Cosmos pour connaître les différentes versions et les informations de téléchargement.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 3878f6d01a2adfff2adc6a22aad20a5f83992b8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719685"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Émulateur Azure Cosmos - Notes de publication et informations sur le téléchargement

Cet article présente les notes de publication de l’émulateur Azure Cosmos avec une liste de mises à jour de fonctionnalités qui ont été apportées à chaque version. Il répertorie également la dernière version de l’émulateur pour vous permettre de la télécharger et de l’utiliser.

## <a name="download"></a>Téléchargement

| | |
|---------|---------|
|**Téléchargement de MSI**|[Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Prise en main**|[Développer localement avec l’émulateur Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notes de publication

### <a name="290"></a>2.9.0

- Cette version ajoute une option permettant de définir la cohérence avec un préfixe cohérent et d’augmenter les limites maximales pour les utilisateurs et les autorisations.

### <a name="272"></a>2.7.2

- Cette version ajoute la prise en charge du serveur MongoDB version 3.6 à l’émulateur Cosmos. Pour démarrer un point de terminaison MongoDB qui cible la version 3.6 du service, démarrez l’émulateur à partir d’une ligne de commande d’administrateur avec l’option « /EnableMongoDBEndpoint=3.6 ».

### <a name="270"></a>2.7.0

- Cette version corrige une régression qui empêchait les utilisateurs d’exécuter des requêtes sur le compte d’API SQL à partir de l’émulateur lors de l’utilisation de clients .NET Core ou .NET x86.

### <a name="246"></a>2.4.6

- Cette version offre une parité avec les fonctionnalités du service Azure Cosmos à partir de juillet 2019, avec les exceptions indiquées dans [Développer localement avec l’émulateur Azure Cosmos](local-emulator.md). Elle résout aussi plusieurs bogues liés à l’arrêt de l’émulateur quand il est appelé via la ligne de commande et les remplacements d’adresses IP internes pour les clients SDK utilisant une connectivité en mode direct.

### <a name="243"></a>2.4.3

- Désactivation du démarrage du service de MongoDB par défaut. Seul le point de terminaison SQL est activé par défaut. L’utilisateur doit démarrer le point de terminaison manuellement à l’aide de l’option de ligne de commande /EnableMongoDbEndpoint. C’est maintenant similaire aux autres points de terminaison de service, tels que Gremlin, Cassandra et Table.
- Correction d’un bogue dans l’émulateur lors du démarrage avec « /AllowNetworkAccess », où les points de terminaison Gremlin, Cassandra et Table ne géraient pas correctement les requêtes des clients externes.
- Ajoutez des ports de connexion directe aux paramètres de règles de pare-feu.

### <a name="240"></a>2.4.0

- Correction d’un problème avec l’émulateur, qui ne parvenait pas à démarrer lorsque des applications de surveillance réseau, comme le client Pulse, sont présentes sur l’ordinateur hôte.
