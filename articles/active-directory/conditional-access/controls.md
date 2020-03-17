---
title: Contrôles personnalisés avec accès conditionnel Azure AD
description: Découvrez comment fonctionnent les contrôles personnalisés dans l’accès conditionnel Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671841"
---
# <a name="custom-controls-preview"></a>Contrôles personnalisés (préversion)

Les contrôles personnalisés sont une fonctionnalité de l’édition Premium P1 d’Azure Active Directory. Quand vous utilisez les contrôles personnalisés, les utilisateurs sont redirigés vers un service compatible pour satisfaire à d’autres exigences en dehors d’Azure Active Directory. Pour satisfaire à ce contrôle, le navigateur de l'utilisateur est redirigé vers le service externe, effectue les opérations d'authentification et de validation requises, puis est redirigé vers Azure Active Directory. Azure Active Directory vérifie la réponse. Si l’utilisateur a été correctement authentifié ou vérifié, il continue dans le flux d’accès conditionnel.

Ces contrôles permettent l’utilisation de certains services externes ou personnalisés comme contrôles d’accès conditionnel. Ils étendent généralement les fonctionnalités de l’accès conditionnel.

Les fournisseurs suivants offrent un service compatible :

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Identité Ping](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Pour plus d’informations sur ces services, contactez directement les fournisseurs.

## <a name="creating-custom-controls"></a>Création de contrôles personnalisés

Pour créer un contrôle personnalisé, commencez par contacter le fournisseur que vous souhaitez utiliser. Les fournisseurs autres que Microsoft ont chacun leurs propres processus et exigences que vous devez suivre pour vous inscrire, vous abonner ou devenir membre du service, et pour demander à intégrer l’accès conditionnel. Le fournisseur vous fournit alors un bloc de données au format JSON. Ces données permettent au fournisseur et à l’accès conditionnel de fonctionner ensemble pour votre locataire, créent le contrôle et définissent de quelle façon l’accès conditionnel indique si vos utilisateurs ont réussi la vérification auprès du fournisseur.

Les contrôles personnalisés ne peuvent pas être utilisés avec l’automatisation du service de protection des identités exigeant une authentification multifacteur ou pour élever des rôles dans PIM.

Copiez les données JSON et collez-les dans la zone de texte correspondante. Ne modifiez pas les données JSON, à moins d'être parfaitement sûr de la modification que vous apportez. Une modification inappropriée peut rompre la liaison entre le fournisseur et Microsoft, et potentiellement verrouiller l’accès à votre compte et aux comptes de vos utilisateurs.

L’option permettant de créer un contrôle personnalisé se trouve dans la section **Gérer** de la page **Accès conditionnel**.

![Control](./media/controls/82.png)

Cliquez sur **Nouveau contrôle personnalisé** pour ouvrir un panneau contenant une zone de texte réservée aux données JSON de votre contrôle.  

![Control](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Suppression de contrôles personnalisés

Pour supprimer un contrôle personnalisé, vous devez d'abord vous assurer qu'il n'est pas utilisé dans une stratégie d'accès conditionnel. Quand vous avez terminé :

1. Accédez à la liste de contrôles personnalisés
1. Cliquez sur...  
1. Sélectionnez **Supprimer**.

## <a name="editing-custom-controls"></a>Modification des contrôles personnalisés

Pour modifier un contrôle personnalisé, vous devez supprimer le contrôle existant et créer un autre contrôle avec les nouvelles informations.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

- [Mode Rapport uniquement](concept-conditional-access-report-only.md)

- [Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
