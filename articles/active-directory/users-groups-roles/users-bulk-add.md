---
title: Importer en bloc pour ajouter des utilisateurs (préversion) dans le portail Azure Active Directory | Microsoft Docs
description: Ajouter des utilisateurs en bloc dans le centre d’administration Azure AD dans Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22052117448cdb71bbc16e1df7899e6d3eb764e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901309"
---
# <a name="bulk-import-users-preview-in-azure-active-directory"></a>Importer des utilisateurs en bloc (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de création, de suppression et d’invitation en bloc d’utilisateurs, ainsi que le téléchargement de listes d’utilisateurs, de groupes et de membres de groupes.

## <a name="bulk-import-service-limits"></a>Limites du service d’importation en bloc

Chaque opération en bloc de création d’utilisateurs peut prendre jusqu’à une heure. Elle permet de créer en bloc au moins 50 000 utilisateurs.

## <a name="required-permissions"></a>Autorisations requises

Pour créer des utilisateurs en bloc dans le portail d’administration, vous devez être connecté en tant qu’administrateur d’entreprise ou administrateur d’utilisateurs.

## <a name="to-bulk-import-users"></a>Pour importer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Créer en bloc**.
1. Dans la page **Créer des utilisateurs en bloc**, sélectionnez **Télécharger** pour recevoir un fichier de valeurs séparées par des virgules (CSV) valide contenant des propriétés utilisateur, puis ajoutez-y vos nouveaux utilisateurs.
1. Lorsque vous avez fini de modifier le fichier CSV ou si vous avez un fichier prêt pour chargement, sélectionnez **Charger votre fichier CSV** pour valider l’opération.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à ajouter](./media/users-bulk-add/upload-button.png)

1. Une fois le contenu du fichier validé, vous devez corriger les erreurs éventuelles avant de démarrer le travail de chargement.
1. Lorsque votre fichier réussit la validation, sélectionnez **Envoyer** pour démarrer le programme de traitement par lots Azure qui ajoute de nouvelles informations utilisateur. Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats sur la page Résultats de l’opération en bloc. Le fichier contient la raison de chaque erreur.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![Vérifier l’état du chargement dans la page Résultats de l’opération en bloc](./media/users-bulk-add/bulk-center.png)

Ensuite, vous pouvez vérifier que les utilisateurs que vous avez créés existent bien au sein de l’organisation Azure AD via le portail Azure ou à l’aide de PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Vérifier la présence d’utilisateurs via le portail Azure

1. [Connectez-vous au centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de votre organisation.
1. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sous **Afficher**, sélectionnez **Tous les utilisateurs** et vérifiez que les utilisateurs que vous avez créés sont répertoriés.

### <a name="verify-users-with-powershell"></a>Vérifier la présence d’utilisateurs avec PowerShell

Exécutez la commande suivante :

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Les utilisateurs que vous avez créés devraient être répertoriés.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
