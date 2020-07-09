---
title: Créer des groupes IP dans Pare-feu Azure
description: Les groupes IP vous permettent de regrouper et de gérer des adresses IP pour les règles de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602531"
---
# <a name="create-ip-groups"></a>Créer des groupes IP

Les groupes IP vous permettent de regrouper et de gérer des adresses IP pour les règles de Pare-feu Azure. Ils peuvent avoir une seule adresse IP, plusieurs adresses IP ou une ou plusieurs plages d’adresses IP.

## <a name="create-an-ip-group"></a>Créer un groupe IP

1. Dans la page d’accueil du portail Azure, sélectionnez **Créer une ressource**.
2. Saisissez **IP Groups** (Groupes IP) dans la zone de recherche, puis sélectionnez **IP Groups** (Groupes IP).
3. Sélectionnez **Create** (Créer).
4. Sélectionnez votre abonnement.
5. Sélectionnez un groupe de ressources ou créez-en un.
6. Saisissez un nom unique pour votre groupe d’adresses IP, puis sélectionnez une région.

6. Sélectionnez **Suivant : adresses IP**.
7. Saisissez une adresse IP, plusieurs adresses IP ou des plages d’adresses IP.

   Il existe deux façons d’entrer des adresses IP :
   - Vous pouvez les entrer manuellement
   - Vous pouvez les importer à partir d’un fichier

   Pour les importer à partir d’un fichier, sélectionnez **Import from a file** (Importer à partir d’un fichier). Vous pouvez faire glisser votre fichier dans la zone ou sélectionner **Browse for files** (Parcourir les fichiers). Si nécessaire, vous pouvez passer en revue et modifier vos adresses IP chargées.

   Lorsque vous saisissez une adresse IP, le portail la valide pour vérifier les problèmes de chevauchement, de doublons et de mise en forme.

5. Quand vous avez terminé, sélectionnez **Review + Create** (Vérifier + créer).
6. Sélectionnez **Create** (Créer).


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les groupes IP](ip-groups.md)