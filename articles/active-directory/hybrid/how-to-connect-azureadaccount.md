---
title: Modifier le mot de passe de compte de connecteur Azure AD | Microsoft Docs
description: Cette rubrique explique comment restaurer le compte de connecteur Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68c190b51b9bbb5faf21e8ea75b07d1a82005e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571405"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Modifier le mot de passe de compte de connecteur Azure AD
Le compte de connecteur Azure AD est censé pour être service gratuit. Si vous devez réinitialiser les informations d’identification, cette rubrique vous concerne. Par exemple, si un administrateur général a par erreur réinitialiser le mot de passe sur le compte à l’aide de PowerShell.

## <a name="reset-the-credentials"></a>Réinitialisation des informations d'identification
Si le compte de connecteur Azure AD ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure AD Connect et démarrez PowerShell.
2. Exécutez `Add-ADSyncAADServiceAccount`.  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournissez les informations d’identification de l’administrateur général Azure AD.

Cette applet de commande réinitialise le mot de passe du compte de service et l’actualise dans Azure AD et dans le moteur de synchronisation.

## <a name="known-issues-these-steps-can-solve"></a>Problèmes connus pouvant être résolus par les procédures indiquées ci-après
Cette section est une liste d’erreurs signalées par les clients qui ont été résolus par un informations d’identification réinitialiser sur le compte de connecteur Azure AD.

- - -
Événement 6900  
Le serveur a rencontré une erreur inattendue lors du traitement d’une notification de modification de mot de passe :  
AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

- - -
Événement 659  
Erreur lors de la récupération de la configuration de la synchronisation de stratégie de mot de passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException :  
AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

## <a name="next-steps"></a>Étapes suivantes
**Rubriques de présentation**

* [Synchronisation Azure AD Connect : Comprendre et personnaliser la synchronisation](how-to-connect-sync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)

