---
title: Créer un point de restauration défini par l’utilisateur pour un pool SQL dédié
description: Découvrez comment utiliser le portail Azure pour créer un point de restauration défini par l’utilisateur pour un pool SQL dédié dans Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567925"
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

