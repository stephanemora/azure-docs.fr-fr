---
title: Réinitialisation du mot de passe libre-service (SSPR) d’Azure AD à partir de l’écran de connexion Windows 10
description: Dans ce tutoriel, vous allez activer la réinitialisation de mot de passe au niveau de l’écran de connexion Windows 10 afin de réduire les appels au support technique.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea65120a2a735477d048b9012e160e0cdafe8835
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253067"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Didacticiel : Réinitialisation du mot de passe Azure AD depuis l’écran de connexion

Dans ce tutoriel, vous allez autoriser les utilisateurs à réinitialiser leurs mots de passe depuis l’écran de connexion Windows 10. Avec la nouvelle mise à jour d’avril 2018 de Windows 10, les utilisateurs qui ont des appareils **joints à Azure AD** ou **joints à Azure AD hybrides** peuvent utiliser un lien de réinitialisation de mot de passe sur leur écran de connexion. Lorsque les utilisateurs cliquent sur ce lien, ils bénéficient de l’expérience SSPR qu’ils connaissent déjà. Si un utilisateur est verrouillé, ce processus ne déverrouille pas les comptes dans l’instance Active Directory locale.

> [!div class="checklist"]
> * Configurer le lien de réinitialisation du mot de passe à l’aide d’Intune
> * Configurer éventuellement à l’aide du Registre Windows
> * Comprendre ce que vos utilisateurs verront

## <a name="prerequisites"></a>Prérequis

* Vous devez exécuter au moins Windows 10, avec la mise à jour d’avril 2018 (v1803), et les appareils doivent être :
   * [joints à Azure AD](../device-management-azure-portal.md) ou
   * [joints à Azure AD Hybride](../device-management-hybrid-azuread-joined-devices-setup.md), avec une connexion réseau à un contrôleur de domaine.
* Vous devez activer la réinitialisation de mot de passe en libre-service Azure AD.
* Si vos appareils Windows 10 se trouvent derrière un pare-feu ou un serveur proxy, vous devez ajouter les URL `passwordreset.microsoftonline.com` et `ajax.aspnetcdn.com` à votre liste d’URL autorisées pour le trafic HTTPS (port 443).
* SSPR pour Windows 10 est uniquement pris en charge avec les proxys de niveau machine
* Passez en revue les limitations ci-dessous avant d’essayer cette fonctionnalité dans votre environnement.
* Si vous utilisez une image, avant d’exécuter sysprep, vérifiez que le cache web est effacé pour le compte Administrateur intégré avant d’effectuer l’étape CopyProfile. Vous trouverez plus d’informations à ce sujet dans l’article de support [Performances médiocres lors de l’utilisation du profil d’utilisateur par défaut personnalisé](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).

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

Quand des utilisateurs tentent de se connecter, ils voient maintenant un lien de réinitialisation du mot de passe qui ouvre l’expérience de réinitialisation de mot de passe libre-service au niveau de l’écran de connexion. Cette fonctionnalité permet aux utilisateurs de réinitialiser leur mot de passe sans avoir à utiliser un autre appareil pour accéder à un navigateur web.

Pour en savoir plus sur l’utilisation de cette fonction, les utilisateurs peuvent consulter la section [Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

Le journal d’audit Azure AD inclut des informations sur l’adresse IP et le ClientType associés à la réinitialisation de mot de passe.

![Exemple de réinitialisation du mot de passe de l’écran d’ouverture de session dans le journal d’audit Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Quand des utilisateurs réinitialisent leur mot de passe à partir de l’écran de connexion d’un appareil Windows 10, un compte temporaire à faibles privilèges appelé « defaultuser1 » est créé. Ce compte sert à sécuriser le processus de réinitialisation du mot de passe. Le compte lui-même a un mot de passe généré de manière aléatoire, ne s’affiche pas pour la connexion à l’appareil, et est supprimé automatiquement une fois que l’utilisateur a réinitialisé son mot de passe. Il peut exister plusieurs profils « defaultuser », mais vous pouvez les ignorer sans risque.

## <a name="limitations"></a>Limites

Lorsque vous testez cette fonctionnalité à l’aide de Hyper-V, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Accédez à la machine virtuelle que vous utilisez pour le test, cliquez sur **Vue** et désélectionnez **Session étendue**.

Lorsque vous testez cette fonctionnalité à l’aide du Bureau à distance ou d’une session de machine virtuelle étendue, le lien « Réinitialiser le mot de passe » n’apparaît pas.

* Actuellement, la réinitialisation du mot de passe n’est pas prise en charge à partir d’un Bureau à distance.

Si Ctrl+Alt+Suppr est exigé par la stratégie dans les versions de Windows 10 avant v1809, **Réinitialiser le mot de passe** ne fonctionnera pas.

Si les notifications sur l’écran de verrouillage sont désactivées, **Réinitialiser le mot de passe** ne fonctionnera pas.

Les paramètres de stratégie suivants sont connus pour interférer avec la possibilité de réinitialiser les mots de passe

   * HideFastUserSwitching est défini sur activé ou 1
   * DontDisplayLastUserName est défini sur activé ou 1
   * NoLockScreen est défini sur activé ou 1
   * EnableLostMode est défini sur l’appareil
   * Explorer.exe est remplacé par un interpréteur de commandes personnalisé

Cette fonctionnalité ne fonctionne pas pour les réseaux avec l’authentification de réseau 802.1x déployée et l’option « Immédiatement avant l’ouverture de session de l’utilisateur ». Pour les réseaux avec l’authentification de réseau 802.1x déployée, il est recommandé d’utiliser l’authentification de la machine pour activer cette fonctionnalité.

Pour les scénarios Joint au domaine hybride, le workflow SSPR se déroule correctement sans qu’un contrôleur de domaine Active Directory soit nécessaire. Si l’utilisateur effectue le processus de réinitialisation du mot de passe quand la communication avec un contrôleur de domaine Active Directory n’est pas disponible, par exemple en cas de travail à distance, il ne peut pas se connecter à l’appareil tant que celui-ci ne peut pas communiquer avec un contrôleur de domaine et mettre à jour les informations d’identification mises en cache. **Une connectivité à un contrôleur de domaine est nécessaire pour utiliser le nouveau mot de passe pour la première fois.**

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne souhaitez plus utiliser la fonctionnalité que vous avez configurée dans le cadre de ce tutoriel, supprimez le profil de configuration d’appareil Intune que vous avez créé ou la clé de Registre.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez autorisé les utilisateurs à réinitialiser leurs mots de passe depuis l’écran de connexion Windows 10. Continuez avec le tutoriel suivant pour savoir comment Azure Identity Protection peut être intégré à la réinitialisation de mot de passe libre-service et aux expériences Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Évaluer les risques lors de la connexion](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Attribuer la stratégie de configuration d’appareil Intune à un groupe d’appareils Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Lien de réinitialisation du mot de passe sur l’écran de connexion Windows 10"
