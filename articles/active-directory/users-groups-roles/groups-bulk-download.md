---
title: Télécharger une liste de groupes dans le portail Azure Active Directory | Microsoft Docs
description: Téléchargez des propriétés de groupe en bloc dans le centre d’administration Azure dans Azure Active Directory.
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
ms.openlocfilehash: 76aafa978732b3f4bae82f8ad3c09e67caf297ae
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146282"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Télécharger une liste de groupes par lot (préversion) dans Azure Active Directory

À l’aide du portail Azure Active Directory (Azure AD), vous pouvez télécharger en bloc la liste de tous les groupes de votre organisation dans un fichier de valeurs séparées par des virgules (CSV).

> [!NOTE]
> Les opérations par lot d’Azure AD sont une fonctionnalité en préversion publique d’Azure AD disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les conditions d’utilisation des préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-download-a-list-of-groups"></a>Pour télécharger une liste de groupes

1. Connectez-vous [au portail Azure](https://portal.azure.com) avec un compte administrateur dans l’organisation.
1. Dans Azure AD, sélectionnez **Groupes** > **Télécharger les groupes**.
1. Dans la page de **Téléchargement de groupes**, sélectionnez **Démarrer** pour recevoir un fichier CSV qui répertorie vos groupes.

   ![La commande Télécharger les groupes se trouve sur la page Tous les groupes](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Vérifier l’état du téléchargement

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![La page Résultats des opérations en bloc indique l’état de la demande en bloc](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites du service de téléchargement en bloc

Chaque opération en bloc de téléchargement de liste de groupes peut prendre jusqu’à une heure. Cela vous permet de télécharger une liste d’au moins 300 000 groupes.

## <a name="next-steps"></a>Étapes suivantes

- [Supprimer en bloc les membres d’un groupe](groups-bulk-remove-members.md)
- [Télécharger les membres d’un groupe](groups-bulk-download-members.md)
