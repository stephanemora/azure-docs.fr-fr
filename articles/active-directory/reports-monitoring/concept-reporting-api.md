---
title: Prise en main de l’API de création de rapports Azure AD | Microsoft Docs
description: Prise en main de l'API de création de rapports Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e6ad35702b15090202278cfdead62b245040302
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309618"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Prise en main de l’API de création de rapports Azure Active Directory

Azure Active Directory vous fournit plusieurs [rapports](overview-reports.md). Les données de ces rapports peuvent être très utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. 

En utilisant les API de création de rapports Azure AD, vous pouvez obtenir un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Cet article vous fournit une feuille de route pour l’accès aux données de rapports à l’aide de l’API associée.

Si vous rencontrez des problèmes, consultez [Comment obtenir une assistance pour Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

Cet article s’applique à l’API Graph Azure AD.  Pour des informations similaires sur l’API Microsoft Graph, consultez [Type de ressource directoryAudit](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/directoryaudit).

## <a name="prerequisites"></a>Prérequis

Pour accéder à l’API de création de rapports, même si vous envisagez d’accéder à l’API à l’aide d’un script, vous devez :

1. Affecter des rôles (lecteur Sécurité, administrateur de la sécurité, administrateur général)
2. Inscrire une application
3. Accorder des autorisations
4. Rassembler les paramètres de configuration

Pour plus d’instructions, consultez les [prérequis à l’accès à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) sont satisfaits.

## <a name="apis-with-graph-explorer"></a>API avec l’Afficheur Graph

Vous pouvez utiliser l’[Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) pour vérifier vos données de connexion et d’API d’audit. Veillez à vous connecter à votre compte à l’aide des deux boutons de connexion disponibles dans l’interface utilisateur de l’Explorateur Graph, puis à définir les autorisations **AuditLog.Read.All** et **Directory.ReadAll** pour votre abonné, comme indiqué.   

![Afficheur Graph](./media/concept-reporting-api/graph-explorer.png)

![Modifier l’interface utilisateur des autorisations](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Utilisez les certificats pour accéder à l’API de création de rapports Azure AD 

Utilisez l’API de création de rapports Azure AD avec des certificats si vous envisagez de récupérer des données de rapports sans intervention de l’utilisateur.

Pour plus d’instructions, consultez [Obtenir des données à l’aide de l’API de création de rapports Azure AD avec des certificats](tutorial-access-api-with-certificates.md).


## <a name="next-steps"></a>Étapes suivantes

 * [Informations de référence sur l’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Informations de référence sur l’API de création de rapports relatifs à l’activité de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Résoudre les erreurs dans l’API de création de rapports Azure AD](troubleshoot-graph-api.md)


