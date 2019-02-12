---
title: 'Tutoriel :  Configurer PHS comme sauvegarde pour AD FS dans Azure AD Connect | Microsoft Docs'
description: Explique comment activer la synchronisation de hachage de mot de passe en tant que sauvegarde et pour AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 5219241de9b18008032a3eb78c6aab25a2713370
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660406"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Tutoriel :  Configurer PHS en tant que sauvegarde pour AD FS dans Azure AD Connect

Ce tutoriel explique comment configurer la synchronisation de hachage de mot de passe en tant que sauvegarde et basculement pour AD FS.  Ce document explique également comment activer la synchronisation de hachage de mot de passe en tant que méthode d’authentification principale, si AD FS a échoué ou n'est plus disponible.

## <a name="prerequisites"></a>Prérequis
Ce tutoriel s'appuie sur le tutoriel [ : Fédérer un environnement de forêt AD unique dans le cloud](tutorial-federation.md), un prérequis avant de suivre le présent tutoriel.  Si vous n’avez pas terminé ce tutoriel, faites-le avant de suivre la procédure décrite dans ce document.

## <a name="enable-phs-in-azure-ad-connect"></a>Activer PHS dans Azure AD Connect
La première étape, maintenant que nous disposons d'un environnement Azure AD Connect utilisant la fédération, consiste à activer la synchronisation de hachage de mot de passe et à autoriser Azure AD Connect à synchroniser les hachages.

Effectuez les actions suivantes :

1.  Double-cliquez sur l’icône Azure AD Connect créée sur le bureau.
2.  Cliquez sur **Configurer**.
3.  Sur la page Tâches supplémentaires, sélectionnez **Personnalisation des options de synchronisation**, puis cliquez sur **Suivant**.
4.  Entrez le nom d’utilisateur et le mot de passe de votre administrateur général.  Ce compte a été créé [ici](tutorial-federation.md#create-a-global-administrator-in-azure-ad) dans le précédent didacticiel.
5.  Dans l’écran **Connecter vos répertoires**, cliquez sur **Suivant**.
6.  Dans l'écran **Filtrage domaine ou unité organisationnelle**, cliquez sur **Suivant**.
7.  Dans l’écran **Fonctionnalités facultatives**, cochez **Synchronisation de hachage de mot de passe**, puis cliquez sur **Suivant**.
![Select](media/tutorial-phs-backup/backup1.png)</br>
8.  Dans l’écran **Prêt à configurer**, cliquez sur **Configurer**.
9.  Une fois la configuration terminée, cliquez sur **Quitter**.
10. Et voilà !  Vous avez terminé.  La synchronisation de hachage de mot de passe intervient et peut être utilisée en tant que sauvegarde si AD FS n’est plus disponible.

## <a name="switch-to-password-hash-synchronization"></a>Activer la synchronisation de hachage de mot de passe
Nous allons maintenant vous expliquer comment basculer vers la synchronisation de hachage de mot de passe. Avant de commencer, réfléchissez aux conditions dans lesquelles vous devez effectuer le basculement. N’effectuez pas le basculement pour des raisons temporaires, par exemple en cas de panne du réseau, de problème mineur lié à AD FS ou de problème qui affecte un sous-ensemble de vos utilisateurs. Si vous décidez d’effectuer le basculement car la résolution du problème prendrait trop de temps, effectuez les étapes suivantes :

1. Double-cliquez sur l’icône Azure AD Connect créée sur le bureau.
2.  Cliquez sur **Configurer**.
3.  Sélectionnez **Modifier la connexion utilisateur**, puis cliquez sur **Suivant**.
![Modification](media/tutorial-phs-backup/backup2.png)</br>
4.  Entrez le nom d’utilisateur et le mot de passe de votre administrateur général.  Ce compte a été créé [ici](tutorial-federation.md#create-a-global-administrator-in-azure-ad) dans le précédent didacticiel.
5.  Dans l'écran **Connexion de l’utilisateur**, sélectionnez **Synchronisation de hachage de mot de passe** et cochez la case **Ne pas convertir les comptes d'utilisateur**.  
6.  Vérifiez que l'option par défaut **Activer l’authentification unique** est bien sélectionnée, puis cliquez **Suivant**.
7.  Dans l'écran **Activer l'authentification unique**, cliquez sur **Suivant**.
8.  Dans l’écran **Prêt à configurer**, cliquez sur **Configurer**.
9.  Une fois la configuration terminée, cliquez sur **Quitter**.
10. Les utilisateurs peuvent désormais utiliser leurs mots de passe pour se connecter à Azure et aux services Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Tester la connexion avec un des utilisateurs

1.  Accédez à [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Connectez-vous avec un compte d’utilisateur créé dans le nouveau locataire.  Vous devez vous connecter en utilisant le format suivant : (user@domain.onmicrosoft.com). Saisissez le même mot de passe que celui utilisé par l’utilisateur pour se connecter en local.</br>
![Vérifier](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Étapes suivantes


- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Synchronisation de hachage de mot de passe](how-to-connect-password-hash-synchronization.md)|
