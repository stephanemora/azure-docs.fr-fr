---
title: Supprimer des utilisateurs en bloc dans le portail Azure Active Directory | Microsoft Docs
description: Supprimer des utilisateurs en bloc dans le centre d’administration Azure dans Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdaa91a5ffed9e75666602490b4829a3a95e2782
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113597826"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Supprimer des utilisateurs en bloc dans Azure Active Directory.

À l'aide du portail Azure Active Directory (Azure AD), vous pouvez supprimer un grand nombre de membres d'un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour supprimer en bloc des utilisateurs.

## <a name="csv-template-structure"></a>Structure du modèle CSV

![Le fichier CSV contient les noms et les ID des utilisateurs à supprimer.](./media/users-bulk-delete/delete-csv-file.png)

Les lignes d’un modèle CSV téléchargé sont les suivantes :

- **Numéro de version** : La première ligne contenant le numéro de version doit être incluse dans le fichier CSV chargé.
- **En-têtes de colonne** : `User name [userPrincipalName] Required`. Les anciennes versions du modèle peuvent être différentes.
- **Exemples de lignes** : nous avons inclus dans le modèle un exemple de valeur acceptable. `Example: chris@contoso.com` Vous devez supprimer la ligne des exemples et la remplacer par vos propres entrées.

### <a name="additional-guidance"></a>Conseils supplémentaires

- Les deux premières lignes du modèle ne doivent pas être supprimées ou modifiées, sinon le modèle ne pourra pas être traité.
- Les colonnes obligatoires sont listées en premier.
- N'ajoutez pas de nouvelles colonnes au modèle. Les colonnes que vous ajouterez seront ignorées et ne seront pas traitées.
- Téléchargez la dernière version du modèle CSV avant d'apporter de nouvelles modifications.

## <a name="to-bulk-delete-users"></a>Pour supprimer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Opérations en bloc** > **Supprimer en bloc**.
1. Sur la page **Supprimer des utilisateurs en bloc**, sélectionnez **Télécharger** pour télécharger la dernière version du modèle CSV.
1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque utilisateur à supprimer. La seule valeur obligatoire est **Nom d’utilisateur principal**. Enregistrez le fichier .
1. Dans la page **Supprimer des utilisateurs en bloc**, sous **Charger votre fichier csv**, accédez au fichier. Quand vous sélectionnez le fichier et cliquez sur Envoyer, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Lorsque votre fichier réussit la validation, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui supprime les utilisateurs.
1. Une fois l’opération de suppression terminée, vous recevez une notification indiquant que l’opération en bloc a réussi.

Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc**.

   [![Check delete status in the Bulk Operations Results page.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

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
