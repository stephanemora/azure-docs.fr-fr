---
title: Supprimer en bloc les membres d’un groupe en chargeant un fichier csv - Azure Active Directory | Microsoft Docs
description: Ajoutez en bloc des utilisateurs dans le centre d'administration Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08708c23f9c8f4c4a8fc9f2f0aa5cd20d8333a42
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146379"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Supprimer en bloc les membres d’un groupe (préversion) dans Azure Active Directory

En utilisant le portail Azure Active Directory (Azure AD), vous pouvez supprimer un grand nombre de membres d’un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour supprimer en bloc les membres du groupe.

> [!NOTE]
> Les opérations en bloc d’Azure AD sont une fonctionnalité en préversion publique d’Azure AD disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les conditions d’utilisation des préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-bulk-remove-group-members"></a>Supprimer en bloc les membres d’un groupe

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte Utilisateur administrateur dans l’organisation. Les propriétaires de groupes peuvent également supprimer en bloc les membres de groupes qu'ils possèdent.
1. Dans Azure AD, sélectionnez **Groupes** > **Tous les groupes**.
1. Ouvrez le groupe dont vous supprimez des membres, puis sélectionnez **Membres**.
1. Dans la page **Membres**, sélectionnez **Supprimer des membres**.
1. Dans la page **Supprimer en bloc les membres du groupe (préversion)** , sélectionnez **Télécharger** pour obtenir le modèle de fichier CSV avec les propriétés des membres du groupe requises.

   ![La commande Supprimer des membres se trouve sur la page de profil du groupe](./media/groups-bulk-remove-members/remove-panel.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque membre du groupe que vous souhaitez supprimer du groupe (les valeurs requises sont ID d’objet de membre ou Nom principal de l’utilisateur). Puis enregistrez le fichier.

   ![Le fichier CSV contient les noms et les ID des membres à supprimer](./media/groups-bulk-remove-members/csv-file.png)

1. Dans la page **Supprimer en bloc les membres du groupe (préversion)** , sous **Chargez votre fichier .csv**, accédez au fichier. Quand vous sélectionnez le fichier, la validation du fichier .csv démarre.
1. Quand le contenu du fichier est validé, la page d’importation en bloc affiche **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Une fois votre fichier validé, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui supprime les membres du groupe.
1. Une fois l’opération de suppression terminée, vous recevez une notification indiquant que l’opération en bloc a réussi.

## <a name="check-removal-status"></a>Vérifier l’état de la suppression

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l'opération en bloc (préversion)** .

   ![La page Résultats des opérations en bloc indique l’état de la demande en bloc](./media/groups-bulk-remove-members/bulk-center.png)

Pour plus d’informations sur chaque élément de ligne au sein de l’opération en bloc, sélectionnez les valeurs sous les colonnes **Nombre de réussites**, **Nombre d’échecs** ou **Nombre total de requêtes**. Si des échecs se sont produits, les raisons sont affichées.

## <a name="bulk-removal-service-limits"></a>Limites du service de suppression en bloc

Chaque opération en bloc de suppression d’une liste d’utilisateurs peut prendre jusqu’à une heure. Ceci permet la suppression d'une liste d'au moins 40 000 membres.

## <a name="next-steps"></a>Étapes suivantes

- [Importer en bloc les membres du groupe](groups-bulk-import-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
- [Télécharger une liste de tous les groupes](groups-bulk-download.md)
