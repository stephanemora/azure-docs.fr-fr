---
title: Charger une importation en bloc pour ajouter des membres à un groupe - Azure Active Directory | Microsoft Docs
description: Ajoutez en bloc les membres d’un groupe dans le centre d’administration Azure Active Directory.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517130"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Importer en bloc les membres d’un groupe (préversion) dans Azure Active Directory

En utilisant le portail Azure Active Directory (Azure AD), vous pouvez ajouter un grand nombre de membres à un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour importer en bloc les membres du groupe.

## <a name="to-bulk-import-group-members"></a>Pour importer en bloc les membres du groupe

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte Utilisateur administrateur dans l’organisation. Les propriétaires de groupes peuvent également importer en bloc les membres de groupes qu'ils possèdent.
1. Dans Azure AD, sélectionnez **Groupes** > **Tous les groupes**.
1. Ouvrez le groupe auquel vous ajoutez des membres, puis sélectionnez **Membres**.
1. Dans la page **Membres**, sélectionnez **Importer des membres**.
1. Dans la page **Importer en bloc les membres du groupe (préversion)** , sélectionnez **Télécharger** pour obtenir le modèle de fichier CSV avec les propriétés des membres du groupe requises.

    ![La commande Importer des membres se trouve sur la page de profil du groupe](./media/groups-bulk-import-members/import-panel.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque membre du groupe que vous souhaitez importer dans le groupe (les valeurs requises sont **ID d’objet de membre** ou **Nom principal de l’utilisateur**). Puis enregistrez le fichier.

   ![Le fichier CSV contient les noms et les ID des membres à importer](./media/groups-bulk-import-members/csv-file.png)

1. Dans la page **Importer en bloc les membres du groupe (préversion)** , sous **Chargez votre fichier .csv**, accédez au fichier. Quand vous sélectionnez le fichier, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, la page d’importation en bloc affiche **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Une fois votre fichier validé, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui importe les membres du groupe dans le groupe.
1. Une fois l’opération d’importation terminée, vous recevez une notification indiquant que l’opération en bloc a réussi.

## <a name="check-import-status"></a>Vérifier l'état de l'importation

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![La page Résultats des opérations en bloc indique l’état de la demande en bloc](./media/groups-bulk-import-members/bulk-center.png)

Pour plus d’informations sur chaque élément de ligne au sein de l’opération en bloc, sélectionnez les valeurs sous les colonnes **Nombre de réussites**, **Nombre d’échecs** ou **Nombre total de requêtes**. Si des échecs se sont produits, les raisons sont affichées.

## <a name="bulk-import-service-limits"></a>Limites du service d’importation en bloc

Chaque opération en bloc pour importer une liste des membres d’un groupe peut prendre jusqu’à une heure. Ceci permet l'importation d'une liste d'au moins 40 000 membres.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer en bloc les membres d’un groupe](groups-bulk-remove-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
- [Télécharger une liste de tous les groupes](groups-bulk-download.md)
