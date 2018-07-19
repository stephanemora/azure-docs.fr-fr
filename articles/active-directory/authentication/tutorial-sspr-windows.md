---
title: Réinitialisation du mot de passe libre-service d’Azure AD à partir de l’écran de connexion Windows 10 | Microsoft Docs
description: Configurer la réinitialisation de mot de passe Azure AD depuis l’écran de connexion Windows 10 et la fonction J’ai oublié mon code PIN
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 2a6fbd9e52e07141ae1d8c630bde6ab23801fb18
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054499"
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Réinitialisation du mot de passe Azure AD depuis l’écran de connexion

Vous avez déjà déployé la fonction de réinitialisation de mot de passe libre-service d’Azure AD (SSPR), mais les utilisateurs continuent d’appeler le support technique lorsqu’ils oublient leur mot de passe. En effet, ils ne parviennent pas à accéder à un navigateur web pour accéder à la fonction SSPR.

Avec la nouvelle mise à jour d’avril 2018 de Windows 10, les utilisateurs qui ont des appareils **joints à Azure AD** ou **joints à Azure AD hybrides** peuvent voir et utiliser un lien de réinitialisation de mot de passe sur leur écran de connexion. Lorsqu’ils cliquent sur ce lien, ils bénéficient de l’expérience SSPR qu’ils connaissent déjà.

Pour permettre aux utilisateurs de réinitialiser leur mot de passe Azure AD à partir de l’écran de connexion Windows 10, vous devez :

* Mise à jour d’avril 2018 de Windows 10 ou client plus récent [joint à Azure AD](../device-management-azure-portal.md) ou [joint à Azure AD hybride](../device-management-hybrid-azuread-joined-devices-setup.md).
* activer la réinitialisation du mot de passe libre-service Azure AD.
* Configurez et déployez le paramètre permettant d’activer le lien de réinitialisation du mot de passe via l’une des méthodes suivantes :
   * [Profil de configuration d’appareil Intune](tutorial-sspr-windows.md#configure-reset-password-link-using-intune). Cette méthode nécessite un appareil inscrit à Intune.
   * [Clé de Registre](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Configurer le lien de réinitialisation du mot de passe à l’aide d’Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Créer une stratégie de configuration d’appareil dans Intune

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Intune**.
2. Créez un profil de configuration d’appareil en cliquant sur **Configuration de l’appareil** > **Profils** > **Créer un profil**
   * Attribuez un nom explicite au profil
   * Indiquez éventuellement une description explicite du profil
   * Plateforme **Windows 10 ou plus**
   * Type de profil **personnalisé**

   ![CreateProfile][CreateProfile]

3. Configurez les **paramètres**
   * **Ajoutez** le paramètre OMA-URI suivant pour activer le lien de réinitialisation du mot de passe
      * Entrez un nom explicite pour expliquer l’action du paramètre
      * Indiquez éventuellement une description explicite du paramètre
      * Paramètre **OMA-URI** défini sur `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * Paramètre **Type de données** défini sur la valeur **Entier**
      * Paramètre **Valeur** défini sur **1**
      * Cliquez sur **OK**
   * Cliquez sur **OK**
4. Cliquez sur **Créer**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Affecter une stratégie de configuration d’appareil dans Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Créer un groupe pour appliquer la stratégie de configuration d’appareil à

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Azure Active Directory**.
2. Accédez à **Utilisateurs et groupes** > **Tous les groupes** > **Nouveau groupe**
3. Donnez un nom au groupe et, sous **Type d’appartenance**, choisissez **Affecté**
   * Sous **Membres**, choisissez les appareils Windows 10 joints au domaine Azure Active Directory auxquels vous souhaitez appliquer la stratégie.
   * Cliquez sur **Sélectionner**
4. Cliquez sur **Créer**

Vous trouverez plus d’informations sur la création de groupes dans l’article [Gérer l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Attribuer la stratégie de configuration d’appareil au groupe d’appareils

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Intune**.
2. Pour accéder au profil de configuration d’appareil créé précédemment, cliquez sur **Configuration de l’appareil** > **Profiles** et cliquez sur le profil créé précédemment
3. Assigner le profil à un groupe d’appareils 
   * Cliquez sur **Affectations** sous **Inclure** > **Sélectionner les groupes à inclure**
   * Choisissez le groupe créé précédemment et cliquez sur **Sélectionner**
   * Dans le menu Paramètres, cliquez sur **Enregistrer**

   ![Affectation][Assignment]

Vous avez désormais créé et attribué une stratégie de configuration d’appareil pour activer le lien de réinitialisation de mot de passe sur l’écran d’ouverture de session, via Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurer le lien de réinitialisation du mot de passe à l’aide du registre

Nous vous recommandons d’utiliser cette méthode uniquement pour tester la procédure de modification du mot de passe.

1. Se connecter à un appareil joint à Azure Active Directory à l’aide d’informations d’identification d’administration
2. Exécutez **regedit** en tant qu’administrateur
3. Définissez la clé de Registre suivante
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Maintenant que la stratégie est configurée et attribuée, quelles sont les modifications affectant l’utilisateur ? Comment peut-il savoir qu’il est possible de réinitialiser un mot de passe sur l’écran d’ouverture de session ?

![LoginScreen][LoginScreen]

Lorsque les utilisateurs tentent de se connecter, ils voient maintenant un lien de réinitialisation du mot de passe qui ouvre l’expérience de réinitialisation de mot de passe libre-service au niveau de l’écran d’ouverture de session. Cette fonctionnalité permet aux utilisateurs de réinitialiser leur mot de passe sans avoir à utiliser un autre appareil pour accéder à un navigateur web.

Pour en savoir plus sur l’utilisation de cette fonction, les utilisateurs peuvent consulter la section [Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problèmes courants

Lorsque vous testez cette fonctionnalité à l’aide de Hyper-V, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Accédez à la machine virtuelle que vous utilisez pour le test, cliquez sur **Vue** et désélectionnez **Session étendue**.

Lorsque vous testez cette fonctionnalité à l’aide du Bureau à distance, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Actuellement, la réinitialisation du mot de passe n’est pas prise en charge à partir d’un Bureau à distance.

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [Comment faire pour déployer la fonction SSPR ?](howto-sspr-deployment.md)
* [Comment faire pour activer la réinitialisation du code PIN à partir de l’écran de connexion ?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [Plus d’informations sur les stratégies d’authentification GPM](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Créer le profil de configuration d’appareil Intune pour activer le lien de réinitialisation du mot de passe sur l’écran d’ouverture de session de Windows 10"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Attribuer la stratégie de configuration d’appareil Intune à un groupe d’appareils Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Lien de réinitialisation du mot de passe sur l’écran d’ouverture de session Windows 10"
