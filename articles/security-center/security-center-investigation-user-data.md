---
title: Gérer les données utilisateur trouvées dans une enquête Azure Security Center | Microsoft Docs
description: " Découvrez comment gérer les données utilisateur trouvées dans la fonctionnalité d’enquête d’Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 6685db4eeda72928753c74c64b4b26539ccb1eb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659616"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gérer les données utilisateur trouvées dans une enquête Azure Security Center
Cet article explique comment gérer les données utilisateur trouvées dans la fonctionnalité d’enquête d’Azure Security Center. Les données d’enquête sont stockées dans [Azure Log Analytics](../log-analytics/log-analytics-overview.md) et exposées dans Security Center. La gestion des données utilisateur inclut la possibilité de supprimer ou d’exporter des données.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Recherche et identification des données personnelles
Dans le Portail Azure, vous pouvez utiliser la [fonctionnalité d’enquête](../security-center/security-center-investigation.md) de Security Center pour rechercher des données personnelles. La fonctionnalité d’enquête est disponible sous **Alertes de sécurité**.

La fonctionnalité d’enquête montre toutes les entités, informations utilisateur et données sous l’onglet **Entités**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Sécurisation et contrôle de l’accès aux informations personnelles
Un utilisateur de Security Center disposant du rôle Lecteur, Propriétaire, Contributeur ou Administrateur de compte peut accéder aux données client dans l’outil.

Pour en savoir plus sur les rôles Lecteur, Propriétaire et Contributeur, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/built-in-roles.md). Consultez [Ajout ou modification des administrateurs d’abonnements Azure](../billing/billing-add-change-azure-subscription-administrator.md) pour en savoir plus sur le rôle Administrateur de compte.

## <a name="deleting-personal-data"></a>Suppression des données personnelles
Un utilisateur de Security Center disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut supprimer les informations d’enquête.

Pour supprimer une enquête, vous pouvez envoyer une requête `DELETE` à l’API REST Azure Resource Manager :

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

L’entrée `incidentName` peut être trouvée en répertoriant tous les incidents à l’aide d’une requête `GET` :

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportation des données personnelles
Un utilisateur de Security Center disposant du rôle Propriétaire, Contributeur ou Administrateur de compte peut exporter les informations d’enquête. Pour exporter les informations d’enquête, accédez à l’onglet **Entités** pour copier et coller les informations pertinentes.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la gestion des données utilisateur, consultez [Manage user data in Azure Security Center](security-center-privacy.md) (Gérer les données utilisateur dans Azure Security Center).
