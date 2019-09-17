---
title: Supprimer des utilisateurs en bloc (préversion) dans le portail Azure Active Directory | Microsoft Docs
description: Supprimer des utilisateurs en bloc dans le centre d’administration Azure dans Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901377"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Supprimer des utilisateurs en bloc (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de création, de suppression et d’invitation en bloc d’utilisateurs, ainsi que le téléchargement de listes d’utilisateurs, de groupes et de membres de groupes.

## <a name="to-bulk-delete-users"></a>Pour supprimer des utilisateurs en bloc

1. Connectez-vous à votre organisation Azure AD avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Supprimer en bloc**.
1. Dans la page **Supprimer des utilisateurs en bloc**, sélectionnez **Télécharger** pour recevoir un fichier de valeurs séparées par des virgules (CSV) valide contenant des propriétés utilisateur, puis ajoutez-y les utilisateurs à supprimer.
1. Lorsque vous avez fini de modifier le fichier CSV ou si vous avez un fichier prêt pour chargement, sélectionnez **Charger votre fichier CSV** pour valider l’opération.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à supprimer](./media/users-bulk-delete/bulk-delete.png)

1. Une fois le contenu du fichier validé, vous devez corriger les erreurs éventuelles avant d’envoyer le travail.
1. Lorsque votre fichier réussit la validation, sélectionnez **Envoyer** pour démarrer le programme de traitement par lots Azure qui supprime les utilisateurs. Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats sur la page Résultats de l’opération en bloc. Le fichier contient la raison de chaque erreur.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![Vérifier l’état du chargement dans la page Résultats de l’opération en bloc](./media/users-bulk-delete/bulk-center.png)

Ensuite, vous pouvez vérifier si les utilisateurs que vous avez supprimés n’existent plus au sein de l’organisation Azure AD via le portail Azure ou à l’aide de PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Vérifier la suppression d’utilisateurs via le portail Azure

1. Connectez-vous au portail Azure en utilisant un compte d’administrateur d’utilisateurs de l’organisation.
1. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sous **Afficher**, sélectionnez **Tous les utilisateurs** et vérifiez que les utilisateurs que vous avez supprimés ne sont plus répertoriés.

### <a name="verify-deleted-users-with-powershell"></a>Vérifier la suppression d’utilisateurs à l’aide de PowerShell

Exécutez la commande suivante :

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Vérifiez que les utilisateurs que vous avez supprimés ne sont plus répertoriés.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs en bloc](users-bulk-add.md)
- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
