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
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 842e4d8413544a303b656a8e4a05bdf58a7c8164
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223839"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Prise en main de l’API de création de rapports Azure Active Directory

Azure Active Directory vous fournit plusieurs [rapports](active-directory-reporting-azure-portal.md). Les données de ces rapports peuvent être très utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. 

En utilisant les API de création de rapports Azure AD, vous pouvez obtenir un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Cet article vous fournit une feuille de route pour l’accès aux données de rapports à l’aide de l’API associée.

Si vous rencontrez des problèmes, consultez [Comment obtenir une assistance pour Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Prérequis

Pour accéder à l’API de création de rapports, même si vous envisagez d’accéder à l’API à l’aide d’un script, vous devez :

1. Affecter des rôles (lecteur Sécurité, administrateur de la sécurité, administrateur général)
2. Inscrire une application
3. Accorder des autorisations
4. Rassembler les paramètres de configuration


 
Pour plus d’instructions, consultez les [prérequis à l’accès à l’API de création de rapports Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md) sont satisfaits.


## <a name="recommendation"></a>Recommandation 

Si vous envisagez de récupérer des données de rapport sans intervention de l’utilisateur, vous devez envisager d’utiliser l’API de création de rapports Azure AD avec des certificats.

Pour plus d’instructions, consultez [Obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Explorer

Faites-vous une première impression des API de création de rapports :
   
   - [Utilisation des exemples pour l’API d’audit](active-directory-reporting-api-audit-samples.md) 
 
   - [Utilisation des exemples pour l’API de création de rapports sur l’activité de connexion](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Personnaliser  

Créez votre propre solution : 
   
   - [Utilisation de la référence d’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Utilisation de la référence d’API de création de rapports sur l’activité de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



