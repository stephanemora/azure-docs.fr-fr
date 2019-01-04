---
title: Multi-Factor Authentication dans Azure Active Directory B2C | Microsoft Docs
description: Activation de Multi-Factor Authentication dans des applications accessibles aux consommateurs sécurisées par Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6c44bebad6b54ab673fcbf3b2ef5e5d34c8c6882
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847726"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Activer l'authentification multifacteur dans Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C s’intègre directement à [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) pour vous permettre d’ajouter une deuxième couche de sécurité aux expériences d’inscription et de connexion dans vos applications. Vous activez l’authentification multifacteur sans écrire la moindre ligne de code. Si vous déjà créé des flux d’utilisateur d’inscription et de connexion, vous pouvez toujours activer l’authentification multifacteur.

Cette fonctionnalité permettent aux applications de gérer des scénarios tels que le suivant :

- Vous n’avez pas besoin de l'authentification multifacteur pour accéder à une application, mais en avez besoin pour une autre. Par exemple, le client peut se connecter à une application d’assurance automobile avec un compte local ou social, mais il doit confirmer le numéro de téléphone avant d’accéder à l’application d’assurance habitation inscrite dans le même annuaire.
- Vous n’avez pas besoin l'authentification multifacteur pour accéder à une application en général, mais en avez besoin pour accéder à des portions sensibles de celle-ci. Par exemple, le client peut se connecter à une application bancaire avec un compte local ou social pour consulter le solde de son compte, mais il doit confirmer le numéro de téléphone avant d’effectuer un virement.

## <a name="set-multi-factor-authentication"></a>Définir l'authentification multifacteur

Quand vous créez un flux d’utilisateur, vous avez la possibilité d’activer l’authentification multifacteur.

![Définir l'authentification multifacteur](./media/active-directory-b2c-reference-mfa/add-policy.png)

Définissez **Authentification multifacteur** sur **Activé**.

Vous pouvez utiliser **Exécuter maintenant** sur le flux d’utilisateur à des fins de vérification. Vérifiez le scénario suivant :

Un compte client est créé dans votre locataire avant l’étape d'authentification multifacteur. Lors de cette étape, le client est invité à fournir un numéro de téléphone et à le confirmer. Si la vérification aboutit, le numéro de téléphone est associé au compte en vue d’une utilisation ultérieure. Même si le client annule ou abandonne, il peut être invité à confirmer à nouveau un numéro de téléphone lors de la connexion suivante avec activation de l'authentification multifacteur.

## <a name="add-multi-factor-authentication"></a>Ajouter une authentification multifacteur

Il est possible d’activer l’authentification multifacteur sur un flux d’utilisateur que vous avez précédemment créé. 

Pour activer l’authentification multifacteur :

1. Ouvrez le flux d’utilisateur, puis sélectionnez **Propriétés**. 
2. En regard d’**Authentification multifacteur**, sélectionnez **Activé**.
3. Cliquez sur **Enregistrer** dans la partie supérieure de la page.


