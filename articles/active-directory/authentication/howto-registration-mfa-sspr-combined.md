---
title: Prise en main d’enregistrement combiné pour Azure AD SSPR et une authentification multifacteur (version préliminaire) - Azure Active Directory
description: Activer combinées à l’authentification multifacteur Azure AD et l’inscription (version préliminaire) de réinitialisation de mot de passe libre-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cafe81a1b5ab6d26e71eff05e355d2ed79c4d5e7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119384"
---
# <a name="enable-combined-security-information-registration-preview"></a>Activer combinée sécurité informations inscription (version préliminaire)

Avant d’activer la nouvelle expérience, consultez l’article [combinée d’inscription des informations de sécurité (version préliminaire)](concept-registration-mfa-sspr-combined.md) afin de bien comprendre les fonctionnalités et les effets de cette fonctionnalité.

![Expérience améliorée de sécurité combinée d’informations d’inscription](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| L’inscription d’informations de sécurité combinée pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service d’Azure Active Directory (Azure AD) est une fonctionnalité en préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Activer une inscription combinée

Suivez ces étapes pour activer l’inscription combinée :

1. Connectez-vous au portail Azure en tant qu’un administrateur de l’utilisateur ou un administrateur global.
2. Accédez à **Azure Active Directory** > **paramètres utilisateur** > **gérer les paramètres des fonctionnalités en version préliminaire de panneau accès**.
3. Sous **les utilisateurs peuvent utiliser les fonctionnalités de l’inscription et la gestion des informations de sécurité en version préliminaire - Actualiser**, choisir d’activer pour un **sélectionnés** groupe d’utilisateurs ou pour **tous les** utilisateurs.

   ![Activer l’expérience de préversion des info de sécurité combinée pour tous les utilisateurs](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> À compter de mars 2019, les options d’appel téléphonique ne sont pas disponibles pour l’authentification multifacteur et les utilisateurs SSPR dans les locataires gratuit/essai Azure AD. Les messages SMS ne sont pas affectés par cette modification. Les options d’appel téléphonique seront également disponibles pour les utilisateurs de payé des locataires Azure AD.

> [!NOTE]
> Une fois que vous activez combinée d’inscription, les utilisateurs qui inscrivent ou confirmer son numéro de téléphone ou application mobile via la nouvelle expérience peut les utiliser pour l’authentification multifacteur et SSPR, si ces méthodes sont activées dans le multi-Factor Authentication et SSPR stratégies. Si vous désactivez ensuite cette expérience, les utilisateurs qui vont à l’inscription SSPR précédente page à `https://aka.ms/ssprsetup` devront effectuer l’authentification multifacteur pour accéder à la page.

Si vous avez configuré la liste sites aux zones attribution dans Internet Explorer, les sites suivants doivent être dans la même zone :

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Stratégies d’accès conditionnel pour l’inscription combinée

Sécurisation de quand et comment inscrire des utilisateurs pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service est désormais possible avec les actions de l’utilisateur dans la stratégie d’accès conditionnel. Cette fonctionnalité en version préliminaire est disponible pour les organisations qui ont activé la [combinée d’aperçu de l’inscription](../authentication/concept-registration-mfa-sspr-combined.md). Cette fonctionnalité peut être activée dans les organisations où ils veulent les utilisateurs s’inscrivent pour Azure multi-Factor Authentication et SSPR depuis un emplacement central tel qu’un emplacement réseau approuvé pendant l’intégration de ressources humaines. Pour plus d’informations sur la création des emplacements approuvés dans l’accès conditionnel, consultez l’article [qu’est la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Créer une stratégie pour exiger l’inscription à partir d’un emplacement approuvé

La stratégie suivante s’applique à tous les utilisateurs sélectionnés, qui tentent de s’inscrire à l’aide de l’expérience combinée d’inscription et bloque l’accès, sauf si elles sont connectent à partir d’un emplacement réseau approuvé la mention.

![Créer une stratégie d’accès conditionnel pour contrôler l’enregistrement d’informations de sécurité](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Dans le **Azure portal**, accédez à **Azure Active Directory** > **accès conditionnel**
1. Sélectionnez **Nouvelle stratégie**.
1. Dans nom, entrez un nom pour cette stratégie. Par exemple, **combinées de l’inscription d’informations de sécurité sur les réseaux approuvés**
1. Sous **affectations**, cliquez sur **utilisateurs et groupes**, puis sélectionnez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie

   > [!WARNING]
   > Les utilisateurs doivent être activés pour le [combinée d’aperçu de l’inscription](../authentication/howto-registration-mfa-sspr-combined.md).

1. Sous **Cloud des applications ou des actions**, sélectionnez **actions utilisateur**, vérifiez **enregistrer les informations de sécurité (version préliminaire)**
1. Sous **Conditions** > **emplacements**
   1. Configurer **Oui**
   1. Inclure **n’importe quel emplacement**
   1. Exclure **tous les emplacements approuvés**
   1. Cliquez sur **fait** dans le panneau des emplacements
   1. Cliquez sur **fait** dans le panneau de Conditions
1. Sous **contrôles d’accès** > **Grant**
   1. Cliquez sur **bloquer l’accès**
   1. Puis cliquez sur **sélectionnez**
1. Définissez **activer la stratégie** à **sur**
1. Puis cliquez sur **créer**

## <a name="next-steps"></a>Étapes suivantes

[Méthodes disponibles pour l’authentification multifacteur et SSPR](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe libre-service](howto-sspr-deployment.md)

[Configurer l’authentification multifacteur Azure](howto-mfa-getstarted.md)

[Résolution des problèmes combinée d’enregistrement d’informations de sécurité](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qu’est la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md)
