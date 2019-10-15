---
title: Téléchargement de l’émulateur Cosmos Azure et notes de publication
description: Lisez les notes de publication de l’émulateur Azure Cosmos et téléchargez-le.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973715"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Émulateur Azure Cosmos - Notes de publication et informations sur le téléchargement

Cet article présente les notes de publication de l’émulateur Azure Cosmos avec une liste de mises à jour de fonctionnalités qui ont été apportées à chaque version. Il répertorie également la dernière version de l’émulateur pour vous permettre de la télécharger et de l’utiliser.

## <a name="download"></a>Téléchargement

| | |
|---------|---------|
|**Téléchargement de MSI**|[Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Prise en main**|[Développer localement avec l’émulateur Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notes de publication

### <a name="246"></a>2.4.6

- Cette version offre une parité avec les fonctionnalités du service Azure Cosmos à partir de juillet 2019, avec les exceptions indiquées dans [Développer localement avec l’émulateur Azure Cosmos](local-emulator.md). Elle résout aussi plusieurs bogues liés à l’arrêt de l’émulateur quand il est appelé via la ligne de commande et les remplacements d’adresses IP internes pour les clients SDK utilisant une connectivité en mode direct.

### <a name="243"></a>2.4.3

- Désactivation du démarrage du service de MongoDB par défaut. Seul le point de terminaison SQL est activé par défaut. L’utilisateur doit démarrer le point de terminaison manuellement à l’aide de l’option de ligne de commande /EnableMongoDbEndpoint. C’est maintenant similaire aux autres points de terminaison de service, tels que Gremlin, Cassandra et Table.
- Correction d’un bogue dans l’émulateur lors du démarrage avec « /AllowNetworkAccess », où les points de terminaison Gremlin, Cassandra et Table ne géraient pas correctement les requêtes des clients externes.
- Ajoutez des ports de connexion directe aux paramètres de règles de pare-feu.

### <a name="240"></a>2.4.0

- Correction d’un problème avec l’émulateur, qui ne parvenait pas à démarrer lorsque des applications de surveillance réseau, comme le client Pulse, sont présentes sur l’ordinateur hôte.
