---
title: Créer des groupes IP dans Pare-feu Azure
description: Les groupes IP vous permettent de regrouper et de gérer des adresses IP pour les règles de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444854"
---
# <a name="create-ip-groups-preview"></a>Créer des groupes IP (préversion)

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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