---
title: Ligne de base stratégie bloc authentification hérités (version préliminaire) - Azure Active Directory
description: Stratégie d’accès conditionnel aux protocoles d’authentification hérités de bloc
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b562214d4bf8fd83f740e114a6d77200b4611649
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003220"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Stratégie de référence : Authentification hérités de bloc (version préliminaire)

Pour permettre à vos utilisateurs d’accéder facilement à vos applications cloud, Azure Active Directory (Azure AD) prend en charge un large éventail de protocoles d’authentification, notamment l’authentification héritée. L’authentification héritée est un terme qui fait référence à une demande d’authentification effectuée par :

* Clients Office plus anciens qui n’utilisent pas l’authentification moderne (par exemple, le client Office 2010)
* Tout client qui utilise des protocoles de messagerie hérités tels que IMAP/SMTP/POP3

Aujourd'hui, majorité des tentatives de connexion compromettre tous les proviennent de l’authentification héritée. L’authentification héritée ne prend pas en charge l’authentification multifacteur (MFA). Même si vous avez une stratégie d’authentification Multifacteur activée sur votre annuaire, un acteur peut s’authentifier à l’aide d’un protocole hérité et contourner l’authentification Multifacteur.

La meilleure façon de protéger votre compte à partir de demandes d’authentification malveillants effectuées par les protocoles hérités est de bloquer ces tentatives tous ensemble. Pour faciliter de bloquer toutes les demandes de connexion effectuées par les protocoles hérités, nous avons créé une stratégie de base qui a précisément cette.

![Authentification héritée de bloc avec l’accès conditionnel](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**L’authentification héritée de bloc** est [stratégie de référence](concept-baseline-protection.md) qui bloque toutes les demandes d’authentification effectuées à partir des protocoles hérités. L’authentification moderne doit être utilisée pour vous connecter avec succès pour tous les utilisateurs. Utilisé conjointement avec les autres stratégies de base, toutes les demandes provenant des protocoles hérités seront bloqués et tous les utilisateurs devront MFA chaque fois que nécessaire. Cette stratégie ne bloque pas Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identifier, utilisez l’authentification hérités

Avant que vous pouvez bloquer l’authentification héritée dans votre annuaire, vous devez d’abord comprendre si vos utilisateurs disposent d’applications qui utilisent l’authentification héritée et comment il affecte votre annuaire global. Journaux de connexion AD Azure peuvent servir à comprendre si vous utilisez l’authentification héritée.

1. Accédez à la **Azure portal** > **Azure Active Directory** > **connexions**.
1. Ajouter la colonne de l’application cliente si elle n’est pas affichée en cliquant sur **colonnes** > **application cliente**.
1. Filtrer par **application cliente** > **autres Clients** et cliquez sur **appliquer**.

Filtrage va uniquement afficher vous connectez-vous tentatives qui ont été apportées par les protocoles d’authentification hérités. Cliquez sur chaque tentative de connexion individuelle afficher des détails supplémentaires. Le **application cliente** champ sous le **informations de base** onglet permet d’indiquer quel protocole d’authentification hérité a été utilisée.

Ces journaux indique quels utilisateurs sont toujours en fonction d’authentification héritée et les applications qui utilisent les protocoles hérités pour effectuer des demandes d’authentification. Pour les utilisateurs qui n’apparaissent pas dans ces journaux et sont confirmées ne pas utiliser l’authentification héritée, implémenter une stratégie d’accès conditionnel ou activer la **stratégie de référence : authentification hérités de bloc** pour ces utilisateurs uniquement.

## <a name="moving-away-from-legacy-authentication"></a>Abandon de l’authentification héritée

Une fois que vous avez une meilleure idée de la personne qui utilise l’authentification héritée dans votre répertoire et les applications qui en dépendent, l’étape suivante est la mise à niveau vos utilisateurs pour utiliser l’authentification moderne. L’authentification moderne est une méthode de gestion des identités qui offre la plus sécurisée de l’authentification des utilisateurs et d’autorisation. Si vous avez une stratégie d’authentification Multifacteur en place sur votre annuaire, l’authentification moderne permet de s’assurer que l’utilisateur est invité pour l’authentification Multifacteur si nécessaire. Il est l’alternative la plus sécurisée pour les protocoles d’authentification hérités.

Cette section fournit une présentation détaillée sur la façon de mettre à jour votre environnement pour l’authentification moderne. Lire les étapes ci-dessous avant d’activer une authentification hérités blocage de stratégie dans votre organisation.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Étape 1 : Activer l’authentification moderne dans votre répertoire

Activation de l’authentification moderne, la première étape consiste à s’assurer de que votre annuaire prend en charge l’authentification moderne. L’authentification moderne est activée par défaut pour les répertoires créés l’ou après le 1er août 2017. Si votre annuaire a été créé avant cette date, vous devez activer manuellement l’authentification moderne pour votre annuaire en procédant comme suit :

1. Vérifiez si votre répertoire prend déjà en charge l’authentification moderne en exécutant `Get-CsOAuthConfiguration` à partir de la [Skype entreprise Online PowerShell module](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Si votre commande vide `OAuthServers` propriété, l’authentification moderne est désactivée. Mettre à jour le paramètre pour activer l’authentification moderne à l’aide `Set-CsOAuthConfiguration`. Si votre `OAuthServers` propriété contient une entrée, vous êtes fin prêt.

Veillez à effectuer cette étape avant de poursuivre. Il est essentiel que les configurations de votre répertoire sont modifiées tout d’abord, car ils déterminent le protocole sera utilisé par tous les clients Office. Même si vous utilisez des clients Office qui prennent en charge l’authentification moderne, ils seront par défaut des protocoles hérités si l’authentification moderne est désactivée sur votre annuaire.

### <a name="step-2-office-applications"></a>Étape 2 : Applications Office

Une fois que vous avez activé l’authentification moderne dans votre annuaire, vous pouvez commencer la mise à jour des applications en activant l’authentification moderne pour les clients Office. Office 2016 clients ou version ultérieure prend en charge l’authentification moderne par défaut. Aucune étape supplémentaire est nécessaire.

Si vous n’utilisez pas les clients Office 2013 Windows ou une version antérieure, nous vous recommandons de la mise à niveau vers Office 2016 ou version ultérieure. Même après la fin de l’étape précédente de l’activation de l’authentification moderne dans votre annuaire, les applications Office plus anciennes continueront à utiliser les protocoles d’authentification hérités. Si vous utilisez des clients Office 2013 et que vous ne pouvez pas immédiatement la mise à niveau vers Office 2016 ou version ultérieure, suivez les étapes décrites dans l’article suivant pour [activer l’authentification moderne Office 2013 sur les appareils Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Pour aider à protéger votre compte pendant que vous utilisez l’authentification héritée, nous recommandons à l’aide de mots de passe forts sur votre annuaire. Découvrez [protection de mot de passe Azure AD](../authentication/concept-password-ban-bad.md) à exclure des mots de passe faibles sur votre annuaire.

Office 2010 ne prend pas en charge l’authentification moderne. Vous devez mettre à niveau de tous les utilisateurs avec Office 2010 vers une version plus récente de Microsoft Office. Nous vous recommandons de la mise à niveau vers Office 2016 ou version ultérieure, comme il bloque l’authentification héritée par défaut.

Si vous utilisez MacOS, nous vous recommandons de mettre à niveau vers Office pour Mac 2016 ou version ultérieure. Si vous utilisez le client de messagerie native, vous devrez avoir MacOS version 10.14 ou version ultérieure sur tous les appareils.

### <a name="step-3-exchange-and-sharepoint"></a>Étape 3 : Exchange et SharePoint

Pour que les clients Outlook basée sur Windows utilisent l’authentification moderne, Exchange Online doit être l’authentification moderne activée ainsi. Si l’authentification moderne est désactivée pour Exchange Online, les clients Outlook basée sur Windows qui prennent en charge l’authentification moderne (Outlook 2013 ou version ultérieure) utilisera l’authentification de base pour se connecter aux boîtes aux lettres Exchange Online.

SharePoint Online est activé par défaut de l’authentification moderne. Pour les répertoires créés après le 1er août 2017, l’authentification moderne est activée par défaut dans Exchange Online. Toutefois, si vous aviez précédemment désactivé l’authentification moderne ou si vous utilisez un répertoire créé avant cette date, suivez les étapes décrites dans l’article suivant pour [activer l’authentification moderne dans Exchange Online](https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Étape 4 : Skype Entreprise

Pour empêcher les requêtes d’authentification hérités effectuées par Skype pour entreprises, il est nécessaire activer l’authentification moderne pour Skype entreprise Online. Pour les répertoires créés après le 1er août 2017, l’authentification moderne pour Skype pour entreprises est activée par défaut.

Pour activer l’authentification moderne dans Skype pour entreprises, nous vous suggérons de transition vers Microsoft Teams, qui prend en charge l’authentification moderne par défaut. Toutefois, si vous ne parvenez pas à tr à ce stade, vous devez activer l’authentification moderne pour Skype entreprise Online afin que Skype pour les clients entreprise démarre à l’aide de l’authentification moderne. Suivez ces étapes dans l’article [Skype pour les topologies Business pris en charge avec l’authentification moderne](https://docs.microsoft.com/en-us/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), pour savoir comment activer l’authentification moderne pour Skype for Business.

En plus de permettre l’authentification moderne pour Skype entreprise Online, nous vous recommandons de moderne authentification être activée pour Exchange Online, lorsque vous activez l’authentification moderne pour Skype for Business. Ce processus aidera à synchroniser l’état de l’authentification moderne dans Exchange Online et Skype entreprise online et empêche plusieurs invites de connexion pour Skype pour les clients d’entreprise.

### <a name="step-5-using-mobile-devices"></a>Étape 5 : À l’aide d’appareils mobiles

Applications sur votre appareil mobile doivent bloquer l’authentification héritée. Nous vous recommandons d’utiliser Outlook pour Mobile. Outlook Mobile prend en charge l’authentification moderne par défaut et répondra aux autres stratégies de protection de base MFA.

Pour pouvoir utiliser le client de messagerie iOS native, vous devrez exécuter iOS version 11.0 ou version ultérieure pour garantir que le client de messagerie a été mis à jour pour bloquer l’authentification héritée.

### <a name="step-6-on-premises-clients"></a>Étape 6 : Clients locaux

Si vous êtes un client de hybride à l’aide d’Exchange Server en local et Skype entreprise en local, les deux services doivent être mis à jour pour activer l’authentification moderne. Lorsque vous utilisez l’authentification moderne dans un environnement hybride, vous êtes toujours l’authentification des utilisateurs en local. L’histoire d’autoriser leur accès aux ressources (fichiers ou des e-mails) change.

Avant de commencer l’activation de l’authentification moderne en local, veillez à ce que vous remplissez theIf vous remplissez les conditions, vous êtes maintenant prêt à activer l’authentification moderne en local.

Pour activer l’authentification moderne, voir les articles suivants :

* [Comment configurer Exchange Server en local pour utiliser l’authentification moderne hybride](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Comment utiliser l’authentification moderne (ADAL) avec Skype entreprise](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de référence

La stratégie **stratégie de référence : Authentification hérités de bloc (version préliminaire)** est préconfigurée et s’affichera en haut lorsque vous accédez au panneau d’accès conditionnel dans le portail Azure.

Pour activer cette stratégie et protéger vos administrateurs :

1. Se connecter à la **Azure portal** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **stratégie de référence : Authentification hérités de bloc (version préliminaire)**.
1. Définissez **activer la stratégie** à **utiliser immédiatement la stratégie**.
1. Ajoutez les exclusions d’utilisateur en cliquant sur **utilisateurs** > **sélectionner les utilisateurs exclus** et en choisissant les utilisateurs qui doivent être exclues. Cliquez sur **sélectionnez** puis **fait**.
1. Cliquez sur **enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection d’accès conditionnel de base](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/azure-ad-secure-steps.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)