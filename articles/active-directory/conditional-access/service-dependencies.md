---
title: Quelles sont les dépendances de service dans l’accès conditionnel Azure Active Directory ? | Microsoft Docs
description: Découvrez comment les conditions sont utilisées dans l’accès conditionnel Azure Active Directory pour déclencher une stratégie.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f727fc7133ebc9ee124e63253e8a266862b0d908
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522028"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quelles sont les dépendances de service dans l’accès conditionnel Azure Active Directory ? 


Avec les stratégies d’accès conditionnel, vous pouvez spécifier des conditions d’accès aux sites Web et services. Par exemple, vos exigences d’accès peuvent inclure exigeant une authentification multifacteur (MFA) ou [appareils gérés](require-managed-devices.md). 


Lorsque vous accédez à un site ou service directement, l’impact d’une stratégie associée est généralement facile à évaluer. Par exemple, si vous avez une stratégie qui exige l’authentification Multifacteur pour SharePoint Online configuré, authentification Multifacteur est appliquée pour chaque connexion dans le portail web de SharePoint. Toutefois, il n’est pas toujours simple pour évaluer l’impact d’une stratégie, car il existe des applications de cloud avec des dépendances à d’autres applications cloud. Par exemple, Microsoft Teams s’appuie sur SharePoint online. Par conséquent, lorsque vous accédez à Microsoft Teams dans notre scénario actuel, vous êtes également soumis à la stratégie MFA de SharePoint.   


## <a name="policy-enforcement"></a>Application de stratégies 

Si vous avez une dépendance du service configurée, la stratégie peut être appliquée à l’aide de la mise en œuvre à liaison anticipée ou à liaison tardive. 

**Application de la stratégie de liaison anticipée** signifie qu’un utilisateur doit satisfaire à la stratégie de service dépendant avant d’accéder à l’application appelante. Par exemple, un utilisateur doit satisfaire la stratégie SharePoint avant de vous connecter MS Teams. 

**Application de la stratégie de liaison tardive** se produit après l’utilisateur se connecte à l’application appelante. Mise en œuvre est différé à lors de l’appel de demandes d’application, un jeton pour le service en aval. MS Teams l’accès à Planner et Office.com l’accès à SharePoint sont des exemples. 

Le diagramme ci-dessous illustre les dépendances du service MS Teams. Flèches pleines indiquent la mise en œuvre de liaison anticipée la flèche en pointillé pour Planner indique la mise en œuvre à liaison tardive. 



![Dépendances du service MS Teams](./media/service-dependencies/01.png)



  

Comme meilleure pratique, vous devez définir des stratégies courantes sur les applications associées et de services autant que possible. Avoir une méthode de sécurité cohérent vous offre la meilleure expérience utilisateur. Par exemple, définissant une stratégie commune entre Exchange Online, SharePoint Online, MS Teams et Skype pour entreprises réduit considérablement les invites inattendues qui peuvent survenir à partir de différentes stratégies appliquées aux services en aval. 

Le tableau ci-dessous répertorie les dépendances de service supplémentaires, où les applications clientes doivent satisfaire  

| Applications clientes         | Service en aval                          | Mise en œuvre |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portail et API) | Liaison anticipée |
| Microsoft Classroom | Microsoft Exchange                                    | Liaison anticipée |
|                     | SharePoint                                  | Liaison anticipée  |
| Microsoft Teams     | Microsoft Exchange                                    | Liaison anticipée |
|                     | MS Planner                                  | À liaison tardive  |
|                     | SharePoint                                  | Liaison anticipée |
|                     | Skype Entreprise Online                   | Liaison anticipée |
| Portail Office       | Microsoft Exchange                                    | À liaison tardive  |
|                     | SharePoint                                  | À liaison tardive  |
| Groupes Outlook      | Microsoft Exchange                                    | Liaison anticipée |
|                     | SharePoint                                  | Liaison anticipée |
| PowerApps           | Microsoft Azure Management (portail et API) | Liaison anticipée |
|                     | Microsoft Azure Active Directory              | Liaison anticipée |
| Projet             | Dynamics CRM                                | Liaison anticipée |
| Skype Entreprise  | Microsoft Exchange                                    | Liaison anticipée |
| Visual Studio       | Microsoft Azure Management (portail et API) | Liaison anticipée |



## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à implémenter l’accès conditionnel dans votre environnement, consultez [Planifier votre déploiement d’accès conditionnel dans Azure Active Directory](plan-conditional-access.md).
