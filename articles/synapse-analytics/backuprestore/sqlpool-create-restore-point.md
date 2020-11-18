---
title: Créer un point de restauration défini par l’utilisateur pour un pool SQL dédié
description: Guide pratique pour créer un point de restauration pour un pool SQL dédié.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331994"
---
# <a name="user-defined-restore-points"></a>Points de restauration définis par l’utilisateur

Dans cet article, vous allez apprendre à créer un point de restauration défini par l’utilisateur pour un pool SQL dédié dans Azure Synapse Analytics à l’aide du portail Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Créer des points de restauration définis par l’utilisateur via le portail Azure

Les points de restauration définis par l’utilisateur peuvent également être créés via le portail Azure.

1. Connectez-vous à votre compte [Portail Azure](https://portal.azure.com/).

2. Accédez au pool SQL dédié pour lequel vous souhaitez créer un point de restauration.

3. Sélectionnez **Vue d’ensemble** dans le volet gauche, puis sélectionnez **+ nouveau point de restauration**. Si le bouton Nouveau point de restauration n’est pas activé, vérifiez que le pool SQL dédié n’est pas suspendu.

    ![Nouveau point de restauration](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Spécifiez un nom pour votre point de restauration défini par l'utilisateur, puis cliquez sur **Appliquer**. Les points de restauration définis par l’utilisateur ont une période de rétention par défaut de sept jours.

    ![Nom du point de restauration](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un pool SQL dédié existant](restore-sql-pool.md)

