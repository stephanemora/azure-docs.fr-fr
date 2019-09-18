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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910704"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Supprimer en bloc les membres d’un groupe (préversion) dans Azure Active Directory

En utilisant le portail Azure Active Directory (Azure AD), vous pouvez supprimer un grand nombre de membres d’un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour supprimer en bloc les membres du groupe.

> [!NOTE]
> Les opérations en bloc d’Azure AD sont une fonctionnalité en préversion publique d’Azure AD disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les conditions d’utilisation des préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-removal-service-limits"></a>Limites du service de suppression en bloc

Chaque opération en bloc de suppression d’une liste d’utilisateurs peut prendre jusqu’à une heure. Ceci permet la suppression d'une liste d'au moins 40 000 membres.

## <a name="to-bulk-remove-group-members"></a>Supprimer en bloc les membres d’un groupe

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte Utilisateur administrateur dans l’organisation. Les propriétaires de groupes peuvent également supprimer en bloc les membres de groupes qu'ils possèdent.
1. Dans Azure AD, sélectionnez **Groupes** > **Tous les groupes**.
1. Ouvrez le groupe dont vous supprimez des membres, puis sélectionnez **Membres**.
1. Sur la page **Membres**, sélectionnez **Supprimer des membres** pour télécharger, mettre à jour et charger un fichier CSV répertoriant les membres que vous voulez supprimer du groupe.

   ![La commande Supprimer des membres se trouve sur la page de profil du groupe](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Vérifier l’état de la suppression

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l'opération en bloc (préversion)** .

   ![La page Résultats des opérations en bloc indique l’état de la demande en bloc](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Étapes suivantes

- [Importer en bloc les membres du groupe](groups-bulk-import-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
