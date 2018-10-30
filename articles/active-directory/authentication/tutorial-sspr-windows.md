---
title: Réinitialisation du mot de passe libre-service (SSPR) d’Azure AD à partir de l’écran de connexion Windows 10
description: Dans ce tutoriel, vous allez activer la réinitialisation de mot de passe au niveau de l’écran de connexion Windows 10 afin de réduire les appels au support technique.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 79a6636043499cffb7eded409cdc27c56de98e33
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430219"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Tutoriel : Réinitialisation du mot de passe Azure AD depuis l’écran de connexion

Dans ce tutoriel, vous allez autoriser les utilisateurs à réinitialiser leurs mots de passe depuis l’écran de connexion Windows 10. Avec la nouvelle mise à jour d’avril 2018 de Windows 10, les utilisateurs qui ont des appareils **joints à Azure AD** ou **joints à Azure AD hybrides** peuvent utiliser un lien de réinitialisation de mot de passe sur leur écran de connexion. Lorsque les utilisateurs cliquent sur ce lien, ils bénéficient de l’expérience SSPR qu’ils connaissent déjà.

> [!div class="checklist"]
> * Configurer le lien de réinitialisation du mot de passe à l’aide d’Intune
> * Configurer éventuellement à l’aide du Registre Windows
> * Comprendre ce que vos utilisateurs verront

## <a name="prerequisites"></a>Prérequis

* Mise à jour d’avril 2018 de Windows 10, ou client plus récent :
   * [joints à Azure AD](../device-management-azure-portal.md) ou 
   * [joints à Azure AD hybrides](../device-management-hybrid-azuread-joined-devices-setup.md)
* activer la réinitialisation du mot de passe libre-service Azure AD.

## <a name="configure-reset-password-link-using-intune"></a>Configurer le lien de réinitialisation du mot de passe à l’aide d’Intune

Déployer la modification de la configuration est la méthode la plus souple pour activer la réinitialisation du mot de passe à partir de l’écran de connexion à l’aide d’Intune. Intune vous permet de déployer les modifications de configuration vers un groupe spécifique de machines que vous définissez. Cette méthode nécessite un appareil inscrit à Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Créer une stratégie de configuration d’appareil dans Intune

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Intune**.
2. Créez un profil de configuration d’appareil en cliquant sur **Configuration de l’appareil** > **Profils** > **Créer un profil**
   * Attribuez un nom explicite au profil
   * Indiquez éventuellement une description explicite du profil
   * Plateforme **Windows 10 ou plus**
   * Type de profil **personnalisé**

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

Vous avez désormais créé et attribué une stratégie de configuration d’appareil pour activer le lien de réinitialisation de mot de passe sur l’écran de connexion, via Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurer le lien de réinitialisation du mot de passe à l’aide du registre

1. Se connecter à un PC Windows à l’aide d’informations d’identification d’administration
2. Exécutez **regedit** en tant qu’administrateur
3. Définissez la clé de Registre suivante
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Maintenant que la stratégie est configurée et attribuée, quelles sont les modifications affectant l’utilisateur ? Comment peut-il savoir qu’il est possible de réinitialiser un mot de passe sur l’écran de connexion ?

![Écran de connexion][LoginScreen]

Lorsque les utilisateurs tentent de se connecter, ils voient maintenant un lien de réinitialisation du mot de passe qui ouvre l’expérience de réinitialisation de mot de passe libre-service au niveau de l’écran de connexion. Cette fonctionnalité permet aux utilisateurs de réinitialiser leur mot de passe sans avoir à utiliser un autre appareil pour accéder à un navigateur web.

Pour en savoir plus sur l’utilisation de cette fonction, les utilisateurs peuvent consulter la section [Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problèmes courants

Lorsque vous testez cette fonctionnalité à l’aide de Hyper-V, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Accédez à la machine virtuelle que vous utilisez pour le test, cliquez sur **Vue** et désélectionnez **Session étendue**.

Lorsque vous testez cette fonctionnalité à l’aide du Bureau à distance, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Actuellement, la réinitialisation du mot de passe n’est pas prise en charge à partir d’un Bureau à distance.

Si l’écran de verrouillage Windows est désactivé à l’aide d’une clé de registre ou d’une stratégie de groupe, **Réinitialiser le mot de passe** ne sera pas disponible.

Si Ctrl + Alt + Suppr est requis par la stratégie, ou si les notifications de verrouillage d’écran sont désactivées, **Réinitialiser le mot de passe** ne fonctionnera pas. Windows 10 19H1 résoudra cette exigence.

Le journal d’audit Azure AD inclut des informations sur l’adresse IP et le ClientType associés à la réinitialisation de mot de passe.

![Exemple de réinitialisation du mot de passe de l’écran d’ouverture de session dans le journal d’audit Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Si vos ordinateurs Windows 10 sont placés derrière un serveur proxy ou pare-feu, le trafic HTTPS (443) vers passwordreset.microsoftonline.com et ajax.aspnetcdn.com devrait être autorisé.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne souhaitez plus utiliser la fonctionnalité que vous avez configurée dans le cadre de ce tutoriel, supprimez le profil de configuration d’appareil Intune que vous avez créé ou la clé de Registre.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez autorisé les utilisateurs à réinitialiser leurs mots de passe depuis l’écran de connexion Windows 10. Continuez avec le tutoriel suivant pour savoir comment Azure Identity Protection peut être intégré à la réinitialisation de mot de passe libre-service et aux expériences Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Évaluer les risques lors de la connexion](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Attribuer la stratégie de configuration d’appareil Intune à un groupe d’appareils Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Lien de réinitialisation du mot de passe sur l’écran de connexion Windows 10"
