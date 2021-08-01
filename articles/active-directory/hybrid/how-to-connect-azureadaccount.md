---
title: Changer le mot de passe du compte de connecteur Azure AD | Microsoft Docs
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
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5a16e571478428ef1eb068236369b6e9af9f19e1
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853691"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Changer le mot de passe du compte de connecteur Azure AD
Le compte de connecteur Azure AD est censé être proposé en libre-service. Si vous devez réinitialiser les informations d’identification, cette rubrique vous concerne. Par exemple, un administrateur général peut avoir réinitialisé par erreur le mot de passe du compte à l’aide de PowerShell.

## <a name="reset-the-credentials"></a>Réinitialisation des informations d'identification
Si le compte de connecteur Azure AD ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure AD Connect et ouvrez PowerShell.
2. Pour fournir les informations d’identification d’administrateur général Azure AD, exécutez `$credential = Get-Credential`.
3. Exécutez le cmdlet `Add-ADSyncAADServiceAccount -AADCredential $credential`.

   Si le cmdlet est réussi, l’invite de commande PowerShell s’affiche. 
   
Ce cmdlet réinitialise le mot de passe du compte de service et l’actualise dans Azure AD et le moteur de synchronisation.

## <a name="known-issues-these-steps-can-solve"></a>Problèmes connus pouvant être résolus par les procédures indiquées ci-après
Cette section est une liste d’erreurs signalées par les clients qui ont été résolues par une réinitialisation des informations d’identification sur le compte de connecteur Azure AD.

---
Événement 6900 Le serveur a rencontré une erreur inattendue lors du traitement d’une notification de modification de mot de passe : AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

---
Événement 659 Erreur lors de la récupération de la configuration de la synchronisation de stratégie de mot de passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException : AADSTS70002 : Erreur de validation des informations d’identification. AADSTS50054 : Un ancien mot de passe est utilisé pour l’authentification.

## <a name="next-steps"></a>Étapes suivantes
**Rubriques de présentation**

* [Synchronisation Azure AD Connect : Comprendre et personnaliser la synchronisation](how-to-connect-sync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)
