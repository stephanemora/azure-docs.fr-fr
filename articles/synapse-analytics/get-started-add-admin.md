---
title: 'Tutoriel : Ajouter un administrateur'
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
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553496"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Ajouter un administrateur à votre espace de travail Synapse

Dans ce tutoriel, vous allez apprendre à ajouter un administrateur à votre espace de travail Synapse. Cet utilisateur aura un contrôle total sur l'espace de travail.

## <a name="overview"></a>Vue d’ensemble

Jusqu'à présent, dans le guide de démarrage, nous nous sommes concentrés sur les activités que *vous* effectuez dans l'espace de travail. Dans la mesure où vous avez créé l'espace de travail à l'ÉTAPE 1, vous êtes administrateur de l'espace de travail Synapse. Nous allons maintenant attribuer le rôle d'administrateur à un autre utilisateur, Ryan (`ryan@contoso.com`). Lorsque nous aurons terminé, Ryan sera en mesure d'effectuer les mêmes opérations que vous dans l'espace de travail.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC : Rôle de Propriétaire de l'espace de travail

Attribuez à `ryan@contoso.com` le rôle de **Propriétaire** Azure RBAC sur l'espace de travail.

1. Ouvrez le portail Azure, puis votre espace de travail.
1. Sur le côté gauche, sélectionnez **Contrôle d'accès (IAM)** .
1. Attribuez à `ryan@contoso.com` le rôle de **Propriétaire**. 
1. Cliquez sur **Enregistrer**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC : Rôle d'Administrateur Synapse de l'espace de travail

Attribuez à `ryan@contoso.com` le rôle d'**Administrateur Synapse** Synapse RBAC sur l’espace de travail.

1. Ouvrez votre espace de travail dans Synapse Studio.
1. Sur le côté gauche, cliquez sur **Gérer** pour ouvrir le hub Gérer.
1. Sous **Sécurité**, cliquez sur **Contrôle d'accès**.
1. Cliquez sur **Add**.
1. Laissez le champ **Étendue** défini sur **Espace de travail**.
1. Attribuez à `ryan@contoso.com` le rôle d'**Administrateur Synapse**. 
1. Cliquez ensuite sur **Appliquer**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>RBAC Azure : Attributions de rôle sur le compte de stockage principal

Attribuez à `ryan@contoso.com` le rôle de **Propriétaire** sur le compte de stockage principal de l'espace de travail.
Attribuez à `ryan@contoso.com` le rôle de **Contributeur aux données Azure Storage Blob** sur le compte de stockage principal de l'espace de travail.

1. Ouvrez le compte de stockage principal de l'espace de travail sur le portail Azure.
1. Sur le côté gauche, cliquez sur **Contrôle d'accès (IAM)** .
1. Attribuez à `ryan@contoso.com` le rôle de **Propriétaire**. 
1. Attribuez à `ryan@contoso.com` le rôle de **Contributeur aux données Azure Storage Blob**.

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
