---
title: Configuration du contrôle d’accès dans Azure Cosmos DB | Microsoft Docs
description: Découvrez comment configurer le contrôle d’accès dans Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 2dae2b6291aa7ce18cc6f612b25cd5bdcc1ba753
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611141"
---
# <a name="access-control-in-azure-cosmos-db"></a>Contrôle d’accès dans Azure Cosmos DB

Pour ajouter l’accès en lecture aux comptes Azure Cosmos DB à votre compte d’utilisateur, demandez à un propriétaire d’abonnement d’effectuer les étapes suivantes dans le portail Azure.

1. Ouvrez le portail Azure, puis sélectionnez votre compte Azure Cosmos DB.
2. Cliquez sur l’onglet **Contrôle d’accès (IAM)**, puis cliquez sur **+ Ajouter**.
3. Dans le volet **Ajouter des autorisations**, dans la zone **Rôle**, sélectionnez **Rôle de lecteur de compte Cosmos DB**.
4. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
5. Dans votre annuaire, sélectionnez l’utilisateur, le groupe ou l’application qui doit recevoir l’accès.  Dans l’annuaire, vous pouvez rechercher des noms d’affichage, des adresses e-mail et des identificateurs d’objet.
    L’utilisateur, le groupe ou l’application s’affiche alors dans la liste des membres sélectionnés.
6. Cliquez sur **Enregistrer**.

L’entité peut désormais lire les ressources Azure Cosmos DB.
