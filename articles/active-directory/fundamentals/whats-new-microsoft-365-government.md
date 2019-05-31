---
title: Nouveautés d’Azure Active Directory dans Microsoft 365 Government - Azure Active Directory | Microsoft Docs
description: Découvrez certaines modifications à Azure Active Directory (Azure AD) dans l’instance de cloud Microsoft 365 Government, qui peut avoir un impact sur vous.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258893"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Nouveautés d’Azure Active Directory dans Microsoft 365 Government

Nous avons apporté des changements à Azure Active Directory (Azure AD) dans l’instance de cloud Microsoft 365 Government, qui s’applique aux clients en utilisant les services suivants :

- Microsoft Azure Government

- Microsoft 365 Government – GCC haute

- Microsoft 365 Government – DoD

Cet article ne s’applique pas à Microsoft 365 Government – les clients GCC.

## <a name="changes-to-the-initial-domain-name"></a>Modification du nom de domaine initial

Au cours inscription initiale de votre organisation à le pour un service en ligne Microsoft 365 Government, vous avez été invité à choisir le nom de domaine de votre organisation, `<your-domain-name>.onmicrosoft.com`. Si vous disposez déjà d’un nom de domaine avec le suffixe .com, rien ne change.

Toutefois, si vous avez souscrit un nouveau service de Microsoft 365 Government, vous devez choisir un nom de domaine à l’aide de la `.us` suffixe. Par conséquent, il sera `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Cette modification ne s’applique pas aux clients qui sont gérés par les fournisseurs de services cloud (CSP).

## <a name="changes-to-portal-access"></a>Modifications apportées à l’accès au portail

Nous avons mis à jour les points de terminaison de portail pour Microsoft Azure Government, Microsoft 365 Government – GCC High et Microsoft 365 Government – DoD, comme indiqué dans le [table de mappage de point de terminaison](#endpoint-mapping).

Précédemment, les clients peuvent se connecter à l’aide d’Azure dans le monde entier (portal.azure.com) et les portails Office 365 (portal.office.com). Avec cette mise à jour, les clients doivent désormais vous connecter à l’aide de la Microsoft Azure Government spécifiques, Microsoft 365 Government - GCC High et Microsoft 365 Government - portails du DoD.

## <a name="endpoint-mapping"></a>Mappage de point de terminaison

Le tableau suivant présente les points de terminaison pour tous les clients :

| Nom | Détails du point de terminaison |
|------|------------------|
| Portails |Microsoft Azure Government : https://portal.azure.us<p>Microsoft 365 Government – GCC élevé : https://portal.office365.us<p>Microsoft 365 Government – DoD : https://portal.apps.mil |
| Point de terminaison autorité Azure Active Directory | https://login.microsoftonline.us |
| API Graph Azure Active Directory | https://graph.windows.net |
| L’API Microsoft Graph pour Microsoft 365 Government - GCC haute | https://graph.microsoft.us |
| API Microsoft Graph pour Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Points de terminaison des services Azure Government | Pour plus d’informations, consultez [guide du développeur Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - points de terminaison GCC High | Pour plus d’informations, consultez [Office 365 US Points de terminaison GCC High Government](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Pour plus d’informations, consultez [Office 365 US Points de terminaison Government DoD](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles suivants :

- [Qu’est-ce qu’Azure Government ?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Mise à jour du point de terminaison autorité AAD Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Points de terminaison Microsoft Graph dans le cloud US Government](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High et DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)