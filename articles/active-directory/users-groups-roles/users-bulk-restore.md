---
title: Restaurer des utilisateurs en bloc (préversion) dans le portail Azure Active Directory | Microsoft Docs
description: Restaurer des utilisateurs supprimés en bloc dans le centre d’administration Azure AD dans Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174253"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Restaurer des utilisateurs supprimés en bloc (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de création, de suppression et d’invitation en bloc d’utilisateurs, ainsi que le téléchargement de listes d’utilisateurs, de groupes et de membres de groupes.

## <a name="to-bulk-restore-users"></a>Pour restaurer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Supprimés**.
1. Dans la page **Utilisateurs supprimés**, sélectionnez **Restaurer en bloc** pour charger un fichier CSV valide de propriétés des utilisateurs à restaurer.

   ![Sélectionner la commande Restaurer en bloc dans la page Utilisateurs supprimés](./media/users-bulk-restore/bulk-restore.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque utilisateur à restaurer. La seule valeur obligatoire est **ObjectID**. Puis enregistrez le fichier.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à ajouter](./media/users-bulk-restore/upload-button.png)

1. Dans la page **Restauration en bloc (préversion)** , sous **Charger votre fichier csv**, accédez au fichier. Quand vous sélectionnez le fichier et cliquez sur **Envoyer**, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Lorsque votre fichier réussit la validation, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui restaure les utilisateurs.
1. Une fois l’opération de restauration terminée, vous recevez une notification indiquant que l’opération en bloc a réussi.

Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![Vérifier l’état du chargement dans la page Résultats de l’opération en bloc](./media/users-bulk-restore/bulk-center.png)

Ensuite, vous pouvez vérifier que les utilisateurs que vous avez restaurés existent bien au sein de l’organisation Azure AD via le portail Azure ou à l’aide de PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Afficher les utilisateurs restaurés sur le portail Azure

1. [Connectez-vous au centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de votre organisation.
1. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sous **Afficher**, sélectionnez **Tous les utilisateurs**, puis vérifiez que les utilisateurs que vous avez restaurés sont répertoriés.

### <a name="view-users-with-powershell"></a>Afficher les utilisateurs avec PowerShell

Exécutez la commande suivante :

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Les utilisateurs que vous avez restaurés devraient être répertoriés.

## <a name="next-steps"></a>Étapes suivantes

- [Importer des utilisateurs en bloc](users-bulk-add.md)
- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
