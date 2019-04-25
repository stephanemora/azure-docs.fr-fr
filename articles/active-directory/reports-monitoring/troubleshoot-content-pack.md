---
title: Résolution des problèmes liés aux erreurs du pack de contenu des journaux d’activité Azure Active Directory | Microsoft Docs
description: Fournit la liste des messages d’erreur du pack de contenu d’activité Azure Active Directory et les étapes à suivre pour les corriger.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78676ac2f2dcff74a27e0260a5d83e924f7c246f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60284955"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Résolution des problèmes liés aux erreurs du pack de contenu des journaux d’activité Azure Active Directory 

|  |
|--|
|Actuellement, le pack de contenu Azure AD Power BI utilise les API Azure AD Graph pour récupérer des données à partir de votre locataire Azure AD. Par conséquent, vous pouvez voir certaines différences entre les données disponibles dans le pack de contenu et les données récupérées à l’aide des [API Microsoft Graph pour les rapports](concept-reporting-api.md). |
|  |

Lorsque vous utilisez le pack de contenu Power BI pour Azure Active Directory (Azure AD), vous pouvez rencontrer les erreurs suivantes : 

- [Échec de l’actualisation](troubleshoot-content-pack.md#refresh-failed) 
- [Échec de la mise à jour des informations d’identification de la source de données](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [L’importation de données prend trop de temps](#data-import-is-too-slow) 

Cet article traite des causes possibles de ces erreurs et explique comment les corriger.
 
## <a name="refresh-failed"></a>Échec de l’actualisation 
 
**Circonstances de l’apparition de l’erreur** : E-mail en provenance de Power BI ou état d’échec dans l’historique d’actualisation. 


| Cause : | Procédure de résolution |
| ---   | ---        |
| Les erreurs d’échec d’actualisation peuvent être générées quand les informations d’identification des utilisateurs se connectant au pack de contenu ont été réinitialisées, mais pas mises à jour dans les paramètres de connexion du pack de contenu. | Dans Power BI, recherchez le jeu de données correspondant au tableau de bord Journaux d’activité Azure AD (**journaux d’activité Azure Active Directory**), choisissez Planifier l’actualisation, puis entrez vos informations d’identification Azure AD. |
| Une actualisation peut échouer à cause de problèmes de données dans le pack de contenu sous-jacent. | [Soumettez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Échec de la mise à jour des informations d’identification de la source de données 
 
**Circonstances de l’apparition de l’erreur** : Dans Power BI, pendant que vous vous connectez au pack de contenu des journaux d’activité Azure AD. 

| Cause : | Procédure de résolution |
| ---   | ---        |
| L’utilisateur qui se connecte n’est ni administrateur général, ni lecteur sécurité, ni administrateur de sécurité. | Utilisez un compte ayant le rôle d’administrateur général, de lecteur sécurité ou d’administrateur de la sécurité pour accéder aux packs de contenu. |
| Votre locataire n’est pas de type Premium ou ne compte aucun utilisateur avec un fichier de licence Premium. | [Soumettez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>L’importation de données est trop lente 
 
**Circonstances de l’apparition de l’erreur** : Dans Power BI, quand vous avez connecté votre pack de contenu, le processus d’importation de données démarre pour préparer votre tableau de bord pour le journal d’activité Azure AD. Le message suivant s’affiche : **Importation de données...**, mais le processus n’évolue pas.  

| Cause : | Procédure de résolution |
| ---   | ---        |
| Selon la taille de votre locataire, cette étape peut prendre de quelques minutes à 30 minutes. | Si votre tableau de bord ne s’affiche pas à la place du message au bout d’une heure, [soumettez un ticket de support](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Étapes suivantes

* [Installer le pack de contenu Power BI pour les rapports d’Azure AD](quickstart-install-power-bi-content-pack.md).
* [Utiliser le pack de contenu Power BI pour les rapports Azure AD pour visualiser vos données](howto-power-bi-content-pack.md)
* [Obtenir une assistance pour Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
