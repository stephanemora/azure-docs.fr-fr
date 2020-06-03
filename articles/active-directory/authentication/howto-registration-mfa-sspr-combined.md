---
title: Prendre en main l'inscription combinée - Azure Active Directory
description: Activez l’inscription combinée à l’authentification multifacteur et à la réinitialisation de mot de passe en libre-service Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40266f1b340ebe0ab665c576ff3be0e62ba7c705
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798274"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Activation de l’inscription combinée des informations de sécurité dans Azure Active Directory

Avant l’inscription combinée, les utilisateurs s’inscrivaient séparément à des méthodes d’authentification pour Azure Multi-Factor Authentication et la réinitialisation de mot de passe en libre-service (SSPR). Certains étaient troublés par le fait que des méthodes similaires servaient pour Azure MFA et la réinitialisation de mot de passe en libre-service (SSPR), mais qu’ils devaient s’inscrire aux deux fonctionnalités. Désormais, avec l’inscription combinée, les utilisateurs peuvent s’inscrire une seule fois et bénéficier des avantages d’Azure MFA et la réinitialisation de mot de passe en libre-service (SSPR).

Avant d’activer la nouvelle expérience, consultez l’article [Inscription combinée des informations de sécurité](concept-registration-mfa-sspr-combined.md) pour bien comprendre ses fonctionnalités et ses effets.

![Amélioration de l’expérience d’inscription d’informations de sécurité combinée](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Activer une inscription combinée

Procédez comme suit pour activer l’inscription combinée :

1. Connectez-vous au portail Azure en tant qu’administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Gérer les paramètres de préversion de la fonctionnalité utilisateur**.
3. Sous **, les utilisateurs peuvent utiliser l’expérience combinée d’inscription des informations de sécurité**, choisissez d’autoriser un groupe d’utilisateurs **sélectionné** ou **tous** les utilisateurs.

   ![Activer l’expérience combinée d’inscription des informations de sécurité pour des utilisateurs](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Une fois que vous activez l’inscription combinée, les utilisateurs qui inscrivent ou confirment leur numéro de téléphone ou une application mobile via celle-ci ont la possibilité d’utiliser aussi bien le numéro que l’application pour Azure MFA et la réinitialisation de mot de passe en libre-service (SSPR), si ces méthodes sont activées dans les stratégies Azure MFA et SSPR. Si vous désactivez cette expérience par la suite, les utilisateurs qui accèdent à la précédente page d’inscription SSPR à l’adresse `https://aka.ms/ssprsetup` doivent effectuer une authentification multifacteur.

Si vous avez configuré la liste Affectation site à zone dans Internet Explorer, les sites suivants doivent être dans la même zone :

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Stratégies d’accès conditionnel pour l’inscription combinée

La sécurisation de l’inscription des utilisateurs pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service est désormais possible avec les actions de l’utilisateur dans la stratégie d’accès conditionnel. Cette fonctionnalité est accessible aux organisations qui ont activé la [fonctionnalité d’inscription combinée](../authentication/concept-registration-mfa-sspr-combined.md). Cette fonctionnalité peut être activée dans les organisations qui souhaitent que les utilisateurs s’inscrivent à l’authentification multifacteur et à la réinitialisation de mot de passe en libre-service depuis un emplacement central, tel qu’un emplacement réseau approuvé pendant l’intégration de ressources humaines.

Pour plus d’informations sur la création d’emplacements approuvés dans l’accès conditionnel, consultez l’article [Qu’est-ce que la condition d’emplacement de l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Créer une stratégie pour exiger l’inscription à partir d’un emplacement approuvé

La stratégie suivante s’applique à tous les utilisateurs sélectionnés qui tentent de s’inscrire à l’aide de l’expérience d’inscription combinée ; elle leur bloque l’accès sauf s’ils se connectent à partir d’un emplacement réseau approuvé.

1. Dans le portail **Azure**, accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **+ Nouvelle stratégie**.
1. Donnez un nom à cette stratégie, par exemple *Inscription combinée des informations de sécurité sur les réseaux approuvés*.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**. Choisissez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie, puis sélectionnez **Terminé**.

   > [!WARNING]
   > Les utilisateurs doivent avoir activé l’inscription combinée.

1. Sous **Applications ou actions cloud**, sélectionnez **Actions utilisateurs**. Cochez **Inscrire les informations de sécurité**, puis sélectionnez **Terminé**.

    ![Création d’une stratégie d’accès conditionnel personnalisée pour contrôler l’inscription des informations de sécurité](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Sous **Conditions** > **Emplacements**, configurez les options suivantes :
   1. Configurez **Oui**.
   1. Incluez **N’importe quel emplacement**.
   1. Excluez **Tous les emplacements approuvés**.
1. Sélectionnez **Terminé** dans la fenêtre *Emplacements*, puis **Terminé** dans la fenêtre *Conditions*.
1. Sous **Contrôles d’accès** > **Octroi**, choisissez **Bloquer l’accès**, puis **Sélectionner**.
1. Définissez l’option **Appliquer la stratégie** sur **Activé**.
1. Pour finaliser la stratégie, sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, consultez [Résolution des problèmes liés à l’inscription combinée des informations de sécurité](howto-registration-mfa-sspr-combined-troubleshoot.md) ou [Présentation de la condition d’emplacement dans l’accès conditionnel Azure Active Directory](../conditional-access/location-condition.md).

Pour activer les fonctionnalités de votre locataire Azure AD, consultez les tutoriels [Activation de la réinitialisation de mot de passe en libre-service](tutorial-enable-sspr.md) et [Activation d’Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Découvrez comment [obliger les utilisateurs à réinscrire les méthodes d’authentification](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Vous pouvez également consulter les [méthodes disponibles pour Azure Multi-Factor Authentication et SSPR](concept-authentication-methods.md).
