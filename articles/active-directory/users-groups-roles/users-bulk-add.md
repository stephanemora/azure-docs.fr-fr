---
title: Créer des utilisateurs en bloc dans le portail Azure Active Directory | Microsoft Docs
description: Ajouter des utilisateurs en bloc dans le centre d’administration Azure AD dans Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a2c9500ecefed02b28c066de80137d8f0882fd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731502"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Créer des utilisateurs en bloc dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations de création et suppression en bloc d’utilisateurs, ainsi que le téléchargement de listes d’utilisateurs. Il vous suffit de remplir le modèle de valeurs séparées par des virgules (CSV) que vous pouvez télécharger à partir du portail Azure AD.

## <a name="required-permissions"></a>Autorisations requises

Pour créer des utilisateurs en bloc dans le portail d’administration, vous devez être connecté en tant qu’administrateur d’entreprise ou administrateur d’utilisateurs.

## <a name="understand-the-csv-template"></a>Comprendre le modèle CSV

Téléchargez et renseignez le modèle CSV de chargement en bloc pour vous aider à créer les utilisateurs Azure AD en bloc. Le modèle CSV que vous téléchargez peut se présenter comme dans l’exemple suivant :

![Feuille de calcul pour le chargement et les appels expliquant l’objectif et les valeurs de chaque ligne et colonne](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>Structure du modèle CSV

Les lignes d’un modèle CSV téléchargé sont les suivantes :

- **Numéro de version** : La première ligne contenant le numéro de version doit être incluse dans le fichier CSV chargé.
- **En-têtes de colonne** : Le format des en-têtes de colonne est &lt;*Nom d’élément*&gt; [PropertyName] &lt;*Obligatoire ou vide*&gt;. Par exemple : `Name [displayName] Required`. Certaines anciennes versions du modèle peuvent avoir de légères variations.
- **Exemples de lignes** : Nous avons inclus dans le modèle une ligne d’exemples de valeurs acceptables pour chaque colonne. Vous devez supprimer la ligne des exemples et la remplacer par vos propres entrées.

### <a name="additional-guidance"></a>Conseils supplémentaires

- Les deux premières lignes du modèle chargé ne doivent pas être supprimées ni modifiées, sinon, le chargement ne pourra pas être traité.
- Les colonnes obligatoires sont listées en premier.
- Nous vous déconseillons d’ajouter des colonnes au modèle. Les colonnes que vous ajouterez seront ignorées et ne seront pas traitées.
- Nous vous recommandons de télécharger la version la plus récente du modèle CSV aussi souvent que possible.

## <a name="to-create-users-in-bulk"></a>Pour créer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Créer en bloc**.
1. Dans la page **Créer des utilisateurs en bloc**, sélectionnez **Télécharger** pour recevoir un fichier de valeurs séparées par des virgules (CSV) valide contenant des propriétés utilisateur, puis ajoutez-y les utilisateurs que vous voulez créer.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à ajouter](./media/users-bulk-add/upload-button.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque utilisateur à créer. Les seules valeurs obligatoires sont **Nom**, **Nom d’utilisateur principal**, **Mot de passe initial** et **Bloquer la connexion (Oui/Non)** . Puis enregistrez le fichier.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. Dans la page **Créer des utilisateurs en bloc**, sous Charger votre fichier CSV, accédez au fichier. Quand vous sélectionnez le fichier et cliquez sur **Envoyer**, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Une fois votre fichier validé, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui importe les nouveaux utilisateurs.
1. Une fois l’opération d’importation terminée, vous recevez une notification de l’état de l’opération en bloc.

Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur. L’envoi du fichier doit correspondre au modèle fourni et inclure les noms de colonnes exacts.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc**.

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

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
