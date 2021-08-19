---
title: 'Démarrage rapide : Ajouter un administrateur'
description: Dans ce tutoriel, vous allez apprendre à ajouter un autre utilisateur administratif à votre espace de travail.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7a03715a89b5319e341d1704719b020e1b61ef1d
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219417"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Ajouter un administrateur à votre espace de travail Synapse

Dans ce tutoriel, vous allez apprendre à ajouter un administrateur à votre espace de travail Synapse. Cet utilisateur aura un contrôle total sur l'espace de travail.

## <a name="overview"></a>Vue d’ensemble

Jusqu'à présent, dans le guide de démarrage, nous nous sommes concentrés sur les activités que *vous* effectuez dans l'espace de travail. Dans la mesure où vous avez créé l'espace de travail à l'ÉTAPE 1, vous êtes administrateur de l'espace de travail Synapse. Nous allons maintenant attribuer le rôle d'administrateur à un autre utilisateur, Ryan (`ryan@contoso.com`). Lorsque nous aurons terminé, Ryan sera en mesure d'effectuer les mêmes opérations que vous dans l'espace de travail.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC : Rôle de Propriétaire de l'espace de travail

1. Ouvrez le portail Azure, puis votre espace de travail Synapse.
1. Sur le côté gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.
1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Propriétaire |
    | Attribuer l’accès à | Utilisateur |
    | Membre | ryan@contoso.com |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. Sélectionnez **Enregistrer**. 
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC : Rôle d'Administrateur Synapse de l'espace de travail

Attribuez à `ryan@contoso.com` le rôle d'**Administrateur Synapse** Synapse RBAC sur l’espace de travail.

1. Ouvrez votre espace de travail dans Synapse Studio.
1. Sur le côté gauche, sélectionnez **Gérer** pour ouvrir le hub Gérer.
1. Sous **Sécurité**, sélectionnez **Contrôle d’accès**.
1. Sélectionnez **Ajouter**.
1. Laissez le champ **Étendue** défini sur **Espace de travail**.
1. Attribuez à `ryan@contoso.com` le rôle d'**Administrateur Synapse**. 
1. Ensuite, sélectionnez **Appliquer**.
 
## <a name="azure-rbac-role-assignments-on-the-workspaces-primary-storage-account"></a>RBAC Azure : Attributions de rôle sur le compte de stockage principal de l’espace de travail

1. Ouvrez le compte de stockage principal de l'espace de travail sur le portail Azure.
1. Sur le côté gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.
1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Rôle 1 | Propriétaire |
    | Rôle 2| Contributeur aux données Azure Storage Blob|
    | Attribuer l’accès à | Utilisateur |
    | Membre | ryan@contoso.com |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="dedicated-sql-pools-db_owner-role"></a>Pools SQL dédiés : Rôle db_owner

Attribuez à `ryan@contoso.com` le rôle de **db_owner** sur chaque pool SQL dédié de l'espace de travail.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
