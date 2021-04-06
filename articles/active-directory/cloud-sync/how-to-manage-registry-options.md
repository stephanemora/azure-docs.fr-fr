---
title: 'Agent de provisionnement cloud Azure AD Connect : Gestion des options du Registre | Microsoft Docs'
description: Cet article explique comment gérer les options du Registre dans l’agent de provisionnement cloud Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678219"
---
# <a name="manage-agent-registry-options"></a>Gestion des options du Registre de l’agent

Cette section décrit les options du Registre que vous pouvez définir pour contrôler le comportement de traitement du runtime de l’agent de provisionnement Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configuration du délai de connexion LDAP
Par défaut, lorsque vous effectuez des opérations LDAP sur des contrôleurs de domaine Active Directory configurés, l’agent de provisionnement utilise la valeur par défaut du délai de connexion (30 secondes). Si votre contrôleur de domaine met plus de temps à répondre, le message d’erreur suivant est susceptible de s’afficher dans le fichier journal de l’agent : 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Les opérations de recherche LDAP risquent de durer plus longtemps si l’attribut de recherche n’est pas indexé. Dans un premier temps, si vous recevez l’erreur ci-dessus, commencez par vérifier si l’attribut de recherche est [indexé](/windows/win32/ad/indexed-attributes). Si c’est le cas et que l’erreur persiste, vous pouvez augmenter le délai de connexion LDAP en procédant comme suit : 

1. Connectez-vous en tant qu’administrateur sur le serveur Windows sur lequel s’exécute l’agent de provisionnement Azure AD Connect.
1. Utilisez l’élément de menu *Exécuter* pour ouvrir l’Éditeur du Registre (regedit.exe). 
1. Recherchez le dossier de clé **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**.
1. Cliquez avec le bouton droit, puis sélectionnez « Créer -> Valeur de chaîne ».
1. Indiquez le nom `LdapConnectionTimeoutInMilliseconds`.
1. Double-cliquez sur le **Nom de la valeur** et entrez comme données de valeur `60000` millisecondes.
    > [!div class="mx-imgBorder"]
    > ![Délai de connexion LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Redémarrez le service de provisionnement Azure AD Connect à partir de la console *Services*.
1. Si vous avez déployé plusieurs agents de provisionnement, appliquez cette modification du Registre à la totalité d’entre eux dans un souci de cohérence. 

## <a name="configure-referral-chasing"></a>Configuration du repérage de références
Par défaut, l’agent de provisionnement Azure AD Connect ne repère pas les [références](/windows/win32/ad/referrals). Vous pouvez activer le repérage de références pour prendre en charge certains scénarios de provisionnement entrant RH : 
* Vérification de l’unicité de l’UPN dans plusieurs domaines
* Résolution de références inter-domaines à des gestionnaires

Procédez comme suit pour activer le repérage de références :

1. Connectez-vous en tant qu’administrateur sur le serveur Windows sur lequel s’exécute l’agent de provisionnement Azure AD Connect.
1. Utilisez l’élément de menu *Exécuter* pour ouvrir l’Éditeur du Registre (regedit.exe). 
1. Recherchez le dossier de clé **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**.
1. Cliquez avec le bouton droit, puis sélectionnez « Créer -> Valeur de chaîne ».
1. Indiquez le nom `ReferralChasingOptions`.
1. Double-cliquez sur le **Nom de la valeur** et entrez comme données de valeur `96`. Cette valeur, qui correspond à la valeur de constante de `ReferralChasingOptions.All`, spécifie que les références de la sous-arborescence et celles du niveau de base seront suivies par l’agent. 
    > [!div class="mx-imgBorder"]
    > ![Repérage de références](media/how-to-manage-registry-options/referral-chasing.png)
1. Redémarrez le service de provisionnement Azure AD Connect à partir de la console *Services*.
1. Si vous avez déployé plusieurs agents de provisionnement, appliquez cette modification du Registre à la totalité d’entre eux dans un souci de cohérence.

## <a name="skip-gmsa-configuration"></a>Ignorer la configuration GMSA
Avec la version de l’agent 1.1.281.0 +, par défaut, lorsque vous exécutez l’assistant de configuration de l’agent, vous êtes invité à configurer [le compte de service administré de groupe (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). Le programme d’installation de gMSA par l’assistant est utilisé lors de l’exécution pour toutes les opérations de synchronisation et d’approvisionnement. 

Si vous effectuez une mise à niveau à partir d’une version antérieure de l’agent et que vous avez configuré un compte de service personnalisé avec des autorisations déléguées au niveau de l’unité d’organisation spécifiques à votre topologie d’Active Directory, vous pouvez ignorer/reporter la configuration gMSA et planifier cette modification. 

> [!NOTE]
> Cette aide s’applique spécifiquement aux clients qui ont configuré l’approvisionnement entrant RH (jour ouvrable/SuccessFactors) avec des versions d’agent antérieures à 1.1.281.0 et qui ont configuré un compte de service personnalisé pour les opérations de l’agent. À long terme, nous vous recommandons de passer à gMSA en tant que meilleure pratique.  

Dans ce scénario, vous pouvez toujours mettre à niveau les fichiers binaires de l’agent et ignorer la configuration gMSA en procédant comme suit : 

1. Connectez-vous en tant qu’administrateur sur le serveur Windows sur lequel s’exécute l’agent de provisionnement Azure AD Connect.
1. Exécutez le programme d’installation de l’agent pour installer les nouveaux fichiers binaires de l’agent. Fermez l’assistant de configuration de l’agent qui s’ouvre automatiquement une fois l’installation terminée. 
1. Utilisez l’élément de menu *Exécuter* pour ouvrir l’Éditeur du Registre (regedit.exe). 
1. Recherchez le dossier de clé **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**.
1. Cliquez avec le bouton droit, puis sélectionnez « Créer -> Valeur de chaîne »
1. Indiquez le nom `UseCredentials`.
1. Double-cliquez sur le **Nom de la valeur** et entrez comme données de valeur `1`.  
    > [!div class="mx-imgBorder"]
    > ![Utiliser des informations d'identification](media/how-to-manage-registry-options/use-credentials.png)
1. Redémarrez le service de provisionnement Azure AD Connect à partir de la console *Services*.
1. Si vous avez déployé plusieurs agents de provisionnement, appliquez cette modification du Registre à la totalité d’entre eux dans un souci de cohérence.
1. À partir du raccourci sur le bureau, exécutez l’assistant de configuration de l’agent. L’assistant va ignorer la configuration de gMSA. 


> [!NOTE]
> Vous pouvez vérifier que les options du Registre ont été définies en activant la [journalisation détaillée](how-to-troubleshoot.md#log-files). Les journaux émis pendant le démarrage de l’agent affichent les valeurs de configuration choisies à partir du Registre. 

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)

