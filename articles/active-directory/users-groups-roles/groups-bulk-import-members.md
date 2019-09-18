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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910689"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Importer en bloc les membres d’un groupe (préversion) dans Azure Active Directory

En utilisant le portail Azure Active Directory (Azure AD), vous pouvez ajouter un grand nombre de membres à un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour importer en bloc les membres du groupe.

> [!NOTE]
> Les opérations en bloc d’Azure AD sont une fonctionnalité en préversion publique d’Azure AD disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les conditions d’utilisation des préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-import-service-limits"></a>Limites du service d’importation en bloc

Chaque opération en bloc pour importer une liste des membres d’un groupe peut prendre jusqu’à une heure. Ceci permet l'importation d'une liste d'au moins 40 000 membres.

## <a name="to-bulk-import-group-members"></a>Pour importer en bloc les membres du groupe

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte Utilisateur administrateur dans l’organisation. Les propriétaires de groupes peuvent également importer en bloc les membres de groupes qu'ils possèdent.
1. Dans Azure AD, sélectionnez **Groupes** > **Tous les groupes**.
1. Ouvrez le groupe auquel vous ajoutez des membres, puis sélectionnez **Membres**.
1. Sur la page **Membres**, sélectionnez **Importer des membres** pour télécharger, mettre à jour et charger un fichier CSV répertoriant les membres que vous voulez importer dans le groupe.

   ![La commande Importer des membres se trouve sur la page de profil du groupe](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Vérifier l'état de l'importation

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l'opération en bloc (préversion)** .

   ![La page Résultats des opérations en bloc indique l’état de la demande en bloc](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer en bloc les membres d’un groupe](groups-bulk-remove-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
