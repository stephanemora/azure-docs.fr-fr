---
title: Créer des utilisateurs en bloc (préversion) dans le portail Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174304"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Créer des utilisateurs en bloc (préversion) dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de création, de suppression et d’invitation en bloc d’utilisateurs, ainsi que le téléchargement de listes d’utilisateurs, de groupes et de membres de groupes.

## <a name="required-permissions"></a>Autorisations requises

Pour créer des utilisateurs en bloc dans le portail d’administration, vous devez être connecté en tant qu’administrateur d’entreprise ou administrateur d’utilisateurs.

## <a name="to-create-users-in-bulk"></a>Pour créer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Créer en bloc**.
1. Dans la page **Créer des utilisateurs en bloc**, sélectionnez **Télécharger** pour recevoir un fichier de valeurs séparées par des virgules (CSV) valide contenant des propriétés utilisateur, puis ajoutez-y les utilisateurs que vous voulez créer.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à ajouter](./media/users-bulk-add/upload-button.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque utilisateur à créer. Les seules valeurs obligatoires sont **Nom**, **Nom d’utilisateur principal**, **Mot de passe initial** et **Bloquer la connexion (Oui/Non)** . Puis enregistrez le fichier.

   ![Le fichier CSV contient les noms et les ID des utilisateurs à créer](./media/users-bulk-add/add-csv-file.png)

1. Dans la page **Créer des utilisateurs en bloc (préversion)** , sous Charger votre fichier CSV, accédez au fichier. Quand vous sélectionnez le fichier et cliquez sur **Envoyer**, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Une fois votre fichier validé, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui importe les nouveaux utilisateurs.
1. Une fois l’opération d’importation terminée, vous recevez une notification de l’état de l’opération en bloc.

Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur.

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

## <a name="bulk-import-service-limits"></a>Limites du service d’importation en bloc

Chaque opération en bloc de création d’utilisateurs peut prendre jusqu’à une heure. Elle permet de créer en bloc au moins 50 000 utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
