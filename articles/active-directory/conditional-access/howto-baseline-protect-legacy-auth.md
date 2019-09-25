---
title: Stratégie de base de référence pour bloquer l’authentification héritée (préversion) - Azure Active Directory
description: Stratégie d’accès conditionnel pour bloquer les protocoles d’authentification héritée
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
ms.openlocfilehash: 9b74491c76747bd635547f6f22964fe792dbf01a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104082"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Stratégie de base de référence : Bloquer l’authentification héritée (préversion)

Pour permettre à vos utilisateurs d’accéder facilement à vos applications cloud, Azure Active Directory (Azure AD) prend en charge un large éventail de protocoles d’authentification, notamment l’authentification héritée. L’authentification héritée est un terme qui fait référence à une requête d’authentification effectuée par :

* D’anciennes versions de clients Office qui n’utilisent pas l’authentification moderne (par exemple, le client Office 2010)
* Tout client qui utilise des protocoles de messagerie hérités tels que IMAP/SMTP/POP3

Aujourd’hui, la majorité des tentatives de connexion compromettantes ont pour origine l’authentification héritée. L’authentification héritée ne prend pas en charge l’authentification multifacteur (MFA). Même si l’une de vos stratégies d’authentification multifacteur est activée dans votre annuaire, un individu mal intentionné peut s’authentifier à l’aide d’un protocole hérité et contourner l’authentification multifacteur.

La meilleure façon de protéger votre compte contre les requêtes d’authentification malveillantes provenant de protocoles hérités est de bloquer toutes les requêtes de ce type. Dans pour faciliter la sécurisation de votre environnement, nous avons créé cette stratégie de base afin de bloquer l’authentification héritée.

**Bloquer l’authentification héritée** est une [stratégie de base de référence](concept-baseline-protection.md) qui bloque toutes les requêtes d’authentification provenant de protocoles hérités. Tous les utilisateurs doivent employer l’authentification moderne pour se connecter. Si vous utilisez cette stratégie conjointement à d’autres stratégies de base de référence, toutes les requêtes provenant de protocoles hérités seront bloquées et tous les utilisateurs devront s’authentifier avec l’authentification multifacteur (MFA) chaque fois que nécessaire. Cette stratégie ne bloque pas Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identifier l’utilisation de l’authentification héritée

Avant de pouvoir bloquer l’authentification héritée dans votre annuaire, vous devez savoir si vos utilisateurs disposent d’applications qui utilisent l’authentification héritée, puis déterminer quel impact cela a sur l’ensemble de votre annuaire. Les journaux de connexion Azure AD peuvent servir à déterminer si vous utilisez une authentification héritée.

1. Accédez au **portail Azure** > **Azure Active Directory** > **Connexions**.
1. Si elle n’est pas affichée, ajoutez la colonne Application cliente en cliquant sur **Colonnes** > **Application cliente**.
1. **Ajouter des filtres** > **Application cliente** > sélectionnez toutes les options pour **Autres clients**, puis cliquez sur **Appliquer**.

Grâce au filtrage, vous afficherez uniquement les tentatives de connexion effectuées via des protocoles d’authentification héritée. Cliquez sur chaque tentative de connexion pour afficher des détails supplémentaires. Le champ **Application cliente** affiché sous l’onglet **Informations de base** indique quel protocole d’authentification héritée a été utilisé.

Ces journaux identifient les utilisateurs qui continuent de tirer parti de la fonction d’authentification héritée, ainsi que les applications qui utilisent les protocoles hérités pour effectuer des requêtes d’authentification. Pour les utilisateurs qui ne figurent pas dans ces journaux et dont vous êtes certain qu’ils n’utilisent pas l’authentification héritée, implémentez une stratégie d’accès conditionnel, ou activez la **Stratégie de base de référence : Bloquer l’authentification héritée** uniquement pour ces utilisateurs.

## <a name="moving-away-from-legacy-authentication"></a>Abandon de l’authentification héritée

Une fois que vous savez qui utilise l’authentification héritée dans votre annuaire et quelles applications en dépendent, l’étape suivante consiste à effectuer une mise à niveau pour que vos utilisateurs utilisent l’authentification moderne. L’authentification moderne est une méthode de gestion des identités qui offre plus de sécurité pour l’authentification et l’autorisation des utilisateurs. Si vous disposez d’une stratégie d’authentification multifacteur dans votre annuaire, l’authentification moderne garantit que l’utilisateur sera invité à s’authentifier par le biais de l’authentification multifacteur chaque fois que nécessaire. Il s’agit de l’alternative la plus sécurisée aux protocoles d’authentification hérités.

Cette section explique chacune des étapes nécessaires à la mise à jour de votre environnement vers l’authentification moderne. Avant d’activer une stratégie de blocage de l’authentification héritée au sein de votre organisation, lisez les étapes ci-dessous.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Étape 1 : Activer l’authentification moderne dans votre annuaire

Lorsque vous activez l’authentification moderne, la première étape consiste à vérifier que votre annuaire prend en charge l’authentification moderne. L’authentification moderne est activée par défaut dans les annuaires depuis le 1er août 2017. Si votre annuaire a été créé avant cette date, vous devrez activer manuellement l’authentification moderne en procédant de la façon suivante :

1. Vérifiez si votre annuaire prend déjà en charge l’authentification moderne en exécutant `Get-CsOAuthConfiguration` à partir du [module PowerShell de Skype Entreprise sur le web](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Si votre commande retourne une propriété `OAuthServers` vide, l’authentification moderne est désactivée. Mettez à jour le paramètre pour activer l’authentification moderne à l’aide de `Set-CsOAuthConfiguration`. Si votre propriété `OAuthServers` contient une entrée, vous êtes prêt.

Veillez à effectuer cette étape avant de poursuivre. Il est essentiel de modifier les configurations de votre annuaire en premier, car ce sont elles qui déterminent le protocole qui sera utilisé par tous les clients Office. Même si vous utilisez des clients Office qui prennent en charge l’authentification moderne, ceux-ci utiliseront par défaut les protocoles hérités si l’authentification moderne est désactivée dans votre annuaire.

### <a name="step-2-office-applications"></a>Étape 2 : Applications Office

Une fois que vous avez activé l’authentification moderne dans votre annuaire, vous pouvez commencer la mise à jour des applications en activant l’authentification moderne pour les clients Office. Les clients Office 2016 ou version ultérieure prennent en charge l’authentification moderne par défaut. Aucune étape supplémentaire n’est nécessaire.

Si vous n’utilisez pas les clients Windows Office 2013 (ou version antérieure), nous vous recommandons d’effectuer une mise à niveau vers Office 2016 ou version ultérieure. Même après l’activation de l’authentification moderne dans votre annuaire, les anciennes versions d’applications Office continueront à utiliser les protocoles d’authentification hérités. Si vous utilisez des clients Office 2013 et n’êtes pas encore en mesure d’effectuer une mise à niveau vers Office 2016 ou une version ultérieure, suivez les étapes décrites dans l’article suivant : [Activer l’authentification moderne pour Office 2013 sur les appareils Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Pour protéger votre compte lorsque vous utilisez l’authentification héritée, nous vous recommandons d’utiliser des mots de passe forts dans l’ensemble de votre annuaire. Pour interdire les mots de passe faibles dans votre annuaire, consultez [Protection de mot de passe Azure AD](../authentication/concept-password-ban-bad.md).

Office 2010 ne prend pas en charge l’authentification moderne. Vous devez effectuer une mise à niveau pour tous les utilisateurs d’Office 2010. Nous vous recommandons d’effectuer une mise à niveau vers Office 2016 ou une version ultérieure, car il bloque par défaut l’authentification héritée.

Si vous utilisez MacOS, nous vous recommandons d’effectuer une mise à niveau vers Office pour Mac 2016 ou une version ultérieure. Si vous utilisez le client de messagerie natif, MacOS version 10.14 ou version ultérieure devra être installé sur tous les appareils.

### <a name="step-3-exchange-and-sharepoint"></a>Étape 3 : Exchange et SharePoint

Pour que les clients Outlook Windows utilisent l’authentification moderne, l’authentification moderne doit également être activée dans Exchange Online. Si ce n’est pas le cas, les clients Outlook Windows qui prennent en charge l’authentification moderne (Outlook 2013 ou version ultérieure) utiliseront l’authentification de base pour se connecter aux boîtes aux lettres Exchange Online.

L’authentification moderne est activée par défaut dans SharePoint Online. Pour les annuaires créés après le 1er août 2017, l’authentification moderne est activée par défaut dans Exchange Online. Toutefois, si vous aviez précédemment désactivé l’authentification moderne ou si vous utilisez un annuaire ayant été créé avant cette date, suivez les étapes décrites dans l’article suivant : [Activer l’authentification moderne dans Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Étape 4 : Skype Entreprise

Si vous souhaitez bloquer les requêtes d’authentification héritée provenant de Skype Entreprise, vous devez activer l’authentification moderne pour Skype Entreprise sur le web. Pour les annuaires créés après le 1er août 2017, l’authentification moderne est activée par défaut dans Skype Entreprise.

Nous vous suggérons de passer à Microsoft Teams, qui prend en charge l’authentification moderne par défaut. Toutefois, si vous ne pouvez pas encore migrer vers Microsoft Teams, vous devrez activer l’authentification moderne pour Skype Entreprise sur le web, afin que les clients Skype Entreprise commencent à utiliser l’authentification moderne. Pour savoir comment activer l’authentification moderne dans Skype Entreprise, suivez les étapes de l’article [Topologies Skype Entreprise prenant en charge l’authentification moderne](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

Après avoir activé l’authentification moderne pour Skype Entreprise sur le web, nous vous recommandons d’activer l’authentification moderne pour Exchange Online lorsque vous l’activez pour Skype Entreprise. Vous pourrez ainsi synchroniser l’état de l’authentification moderne d’Exchange Online avec celui de Skype Entreprise sur le web, et éviter ainsi l’affichage de plusieurs invites de connexion à Skype Entreprise.

### <a name="step-5-using-mobile-devices"></a>Étape 5 : Utilisation des appareils mobiles

Les applications de votre appareil mobile doivent elles aussi bloquer l’authentification héritée. Nous vous recommandons d’utiliser Outlook pour mobile. Outlook pour mobile prend en charge l’authentification moderne par défaut, et respecte d’autres stratégies de protection de base de référence MFA.

Pour utiliser le client de messagerie iOS natif, vous devrez exécuter iOS version 11.0 ou ultérieure afin de garantir que le client de messagerie bloquera l’authentification héritée.

### <a name="step-6-on-premises-clients"></a>Étape 6 : Clients locaux

Si vous êtes un client hybride utilisant à la fois Exchange Server et Skype Entreprise localement, vous devez mettre à jour ces deux services afin qu’ils permettent l’authentification moderne. Lorsque vous utilisez l’authentification moderne dans un environnement hybride, l’authentification des utilisateurs se fait toujours localement. La méthode qui est employée pour les autoriser à accéder aux ressources (fichiers ou e-mails) n’est plus la même.

Avant de commencer l’activation de l’authentification moderne en local, vérifiez que vous avez respecté les conditions préalables.
Vous êtes maintenant prêt à activer l’authentification moderne en local.

Pour connaître les étapes nécessaires à l’activation de l’authentification moderne, consultez les articles suivants :

* [Guide pratique pour configurer Exchange Server localement en vue d’utiliser l’authentification moderne hybride](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Guide pratique pour utiliser l’authentification moderne (ADAL) avec Skype Entreprise](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de base de référence

La **Stratégie de base de référence : Bloquer l’authentification héritée (préversion)** est préconfigurée, et s’affiche dans la partie supérieure de la fenêtre lorsque vous accédez au panneau Accès conditionnel du portail Azure. Ce paramètre prend effet uniquement après une connexion réussie, de sorte que les utilisateurs ont toujours la possibilité d’essayer d’utiliser l’authentification héritée.

Pour activer cette stratégie et protéger votre organisation :

1. Connectez-vous au **portail Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **Stratégie de base de référence : Bloquer l’authentification héritée (préversion)** .
1. Définissez **Activer la stratégie** sur **Utiliser la stratégie immédiatement**.
1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection de base de référence pour l’accès conditionnel](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)
