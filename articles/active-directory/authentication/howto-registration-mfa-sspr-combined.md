---
title: Prendre en main l'inscription combinée - Azure Active Directory
description: Activez l’authentification multifacteur et l’inscription à la réinitialisation de mot de passe en libre-service Azure AD combinée (préversion publique)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f454b0296a3463d7346c2945b21162e5a38c0eb7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652505"
---
# <a name="enable-combined-security-information-registration-preview"></a>Activer l’inscription d’informations de sécurité combinée (préversion)

Avant d’activer la nouvelle expérience, consultez l’article [Inscription d’informations de sécurité combinée (préversion)](concept-registration-mfa-sspr-combined.md) afin de bien comprendre les fonctionnalités et les effets de cette fonctionnalité.

![Amélioration de l’expérience d’inscription d’informations de sécurité combinée](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| L’inscription d’informations de sécurité combinée pour la réinitialisation de mot de passe en libre-service Azure Active Directory (Azure AD) et l’authentification multifacteur Azure est une fonctionnalité en préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Les organisations qui ont activé la préversion précédente pour l’inscription et la gestion d’informations de sécurité doivent effectuer les étapes ci-dessous pour activer l’expérience de préversion améliorée. Pour les organisations qui n’opèrent pas le changement, le 8 octobre 2019, Microsoft fera basculer les utilisateurs de la préversion précédente pour l’inscription et la gestion d’informations de sécurité dans l’expérience améliorée. 
> 
> Si vous n’avez activé aucune préversion, votre organisation ne sera pas impactée.

## <a name="enable-combined-registration"></a>Activer une inscription combinée

Procédez comme suit pour activer l’inscription combinée :

1. Connectez-vous au portail Azure en tant qu’administrateur d’utilisateurs.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Gérer les paramètres de préversion de la fonctionnalité utilisateur**.
3. Sous **Les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité**, choisissez d’autoriser un groupe **sélectionné** d’utilisateurs ou **Tous** les utilisateurs.

   ![Activer l’expérience en préversion des informations de sécurité combinée pour tous les utilisateurs](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Depuis mars 2019, les options d’appel téléphonique ne sont plus disponibles pour les utilisateurs MFA et SSPR dans des locataires Azure AD gratuits ou à l’essai. Cette modification n’affecte pas les messages SMS. Les options d’appels téléphoniques resteront disponibles pour les utilisateurs de locataires Azure AD payants.

> [!NOTE]
> Une fois que vous activez l’inscription combinée, les utilisateurs qui inscrivent ou confirment leur numéro de téléphone ou une application mobile via celle-ci ont la possibilité d’utiliser aussi bien le numéro que l’application pour l’authentification MFA et la réinitialisation SSPR, si ces méthodes sont activées dans les stratégies MFA et SSPR. Si vous désactivez cette expérience par la suite, les utilisateurs qui accèdent à la précédente page d’inscription SSPR à l’adresse `https://aka.ms/ssprsetup` doivent effectuer une authentification multifacteur.

Si vous avez configuré la liste Affectation site à zone dans Internet Explorer, les sites suivants doivent être dans la même zone :

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Stratégies d’accès conditionnel pour l’inscription combinée

La sécurisation de l’inscription des utilisateurs pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service est désormais possible avec les actions de l’utilisateur dans la stratégie d’accès conditionnel. Cette fonctionnalité en préversion est à la disposition des organisations qui ont activé l’[inscription combinée](../authentication/concept-registration-mfa-sspr-combined.md). Cette fonctionnalité peut être activée dans les organisations qui souhaitent que les utilisateurs s’inscrivent à l’authentification multifacteur et à la réinitialisation de mot de passe en libre-service depuis un emplacement central, tel qu’un emplacement réseau approuvé pendant l’intégration de ressources humaines. Pour plus d’informations sur la création d’emplacements approuvés dans l’accès conditionnel, consultez l’article [Qu’est-ce que la condition d’emplacement de l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Créer une stratégie pour exiger l’inscription à partir d’un emplacement approuvé

La stratégie suivante s’applique à tous les utilisateurs sélectionnés, qui tentent de s’inscrire à l’aide de l’expérience d’inscription combinée et bloque l’accès, sauf si ces derniers se connectent à partir d’un emplacement réseau approuvé.

![Créer une stratégie d’accès conditionnel pour contrôler l’inscription d’informations de sécurité](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Dans le portail **Azure**, accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Sous Nom, entrez un nom pour cette stratégie. Par exemple, **Inscription d’informations de sécurité combinée sur les réseaux approuvés**
1. Sous **Affectations**, cliquez sur **Utilisateurs et groupes**, puis sélectionnez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie

   > [!WARNING]
   > Les utilisateurs doivent être activés pour l’[inscription combinée en préversion](../authentication/howto-registration-mfa-sspr-combined.md).

1. Sous **Applications cloud ou actions**, sélectionnez **Actions utilisateur**, cochez **Inscrire des informations de sécurité (préversion)**
1. Sous **Conditions** > **Emplacements**
   1. Configurer **Oui**
   1. Inclure **N’importe quel emplacement**
   1. Exclure **Tous les emplacements approuvés**
   1. Cliquez sur **Terminé** dans le panneau des emplacements
   1. Cliquez sur **Terminé** dans le panneau des conditions
1. Sous **Contrôles d’accès** > **Octroyer**
   1. Cliquez sur **Bloquer l’accès**.
   1. Puis sur **Sélectionner**.
1. Définissez l’option **Appliquer la stratégie** sur **Activé**.
1. Puis, cliquez sur **Créer**

## <a name="next-steps"></a>Étapes suivantes

[Obliger les utilisateurs à réinscrire les méthodes d’authentification](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Méthodes disponibles pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe en libre-service](howto-sspr-deployment.md)

[Configurer Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Résoudre les problèmes d’inscription d’informations de sécurité combinée](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md)
