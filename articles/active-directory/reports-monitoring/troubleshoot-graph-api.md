---
title: Résoudre les erreurs relatives à l’API de création de rapports Azure Active Directory | Microsoft Docs
description: Fournit des solutions aux erreurs rencontrées lors de l’appel des API de création de rapports Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d085b8043470ea16b4c2493760e4dc5875fe15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176439"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Résoudre les erreurs relatives à l’API de création de rapports Azure Active Directory

Cet article répertorie les messages d’erreur courants que vous pouvez rencontrer quand vous accédez aux rapports d’activité à l’aide de l’API MS Graph, et fournit des étapes de résolution pour chacun de ces messages.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Erreur interne du serveur HTTP 500 lors de l’accès au point de terminaison Microsoft Graph V2

Le point de terminaison Microsoft Graph v2 n’est pas pris en charge. Vous devez accéder aux journaux d’activité à l’aide du point de terminaison Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Error: Impossible d'obtenir les rôles d'utilisateur à partir d'AD Graph

Vous pouvez obtenir ce message d’erreur lorsque vous tentez d’accéder aux connexions à l’aide de l’Afficheur Graph. Vérifiez que vous êtes bien connecté à votre compte à l’aide des deux boutons de connexion de l’interface utilisateur de l’Afficheur Graph, comme illustré dans l’image suivante. 

![Afficheur Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Error: Impossible de vérifier la licence premium à partir d'AD Graph 

Si vous rencontrez ce message d’erreur lorsque vous tentez d’accéder aux connexions à l’aide de l’Afficheur Graph, choisissez **Modifier les autorisations** sous votre compte, dans la barre de navigation gauche, puis sélectionnez **Tasks.ReadWrite** et **Directory.Read.All**. 

![Modifier l’interface utilisateur des autorisations](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error: Aucun des locataires n'est B2C ou le locataire n'a pas de licence premium

L’accès aux rapports de connexion nécessite une licence Azure Active Directory Premium 1 (P1). Si vous voyez ce message d’erreur lorsque vous accédez aux connexions, vérifiez que votre locataire dispose bien d’une licence Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error: L'utilisateur ne dispose pas d'un rôle autorisé 

Si ce message d’erreur s’affiche quand vous tentez d’accéder aux journaux d’audit ou aux connexions à l’aide de l’API, vérifiez que votre compte est associé au rôle **Lecteur Sécurité** ou **Lecteur de rapport** dans votre locataire Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: L'application ne dispose pas de l'autorisation AAD « Lire les données de l'annuaire » 

Pour vérifier que votre application est exécutée avec le bon ensemble d’autorisations, suivez les étapes décrites dans les [Prérequis pour accéder à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Error: L'application ne dispose pas de l'autorisation de l'API MS Graph « Lire toutes les données du journal d'audit »

Pour vérifier que votre application est exécutée avec le bon ensemble d’autorisations, suivez les étapes décrites dans les [Prérequis pour accéder à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="next-steps"></a>Étapes suivantes

[Utiliser la documentation de référence de l’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[Utiliser la documentation de référence de l’API de rapport sur les activités de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
