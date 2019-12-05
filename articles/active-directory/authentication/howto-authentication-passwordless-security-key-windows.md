---
title: Connexion par clé de sécurité sans mot de passe pour Windows – Azure Active Directory
description: Activer la connexion par clé de sécurité sans mot de passe à Azure AD à l’aide de clés de sécurité FIDO2 (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05230e39175e71f4eec2c99cd6cbd2f44f05df30
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766359"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Activer la connexion par clé de sécurité sans mot de passe à des appareils Windows 10 (préversion)

Ce document met l’accent sur l’activation de l’authentification sans mot de passe, basée sur une clé de sécurité FIDO2, avec des appareils Windows 10. À la fin de cet article, vous serez en mesure de vous connecter aux applications web et à vos appareils Windows 10 joints à Azure AD avec votre compte Azure AD, à l’aide d’une clé de sécurité FIDO2.

|     |
| --- |
| Les clés de sécurité FIDO2 sont une fonctionnalité d’évaluation publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Configuration requise

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Inscription d’informations de sécurité combinée – Préversion](concept-registration-mfa-sspr-combined.md)
- [Clés de sécurité FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibles
- WebAuthN nécessite Windows 10 version 1809 ou plus
- [Les appareils joints à Azure AD](../devices/concept-azure-ad-join.md) nécessitent Windows 10 version 1809 ou ultérieure
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (facultatif)
- Package d’approvisionnement (facultatif)

### <a name="unsupported-scenarios"></a>Scénarios non pris en charge

- Le déploiement de Windows Server joint à un domaine Active Directory Domain Services (AD DS) (appareils locaux uniquement) **n’est pas pris en charge**.
- Les scénarios RDP, VDI et Citrix ne sont **pas pris en charge** avec la clé de sécurité.
- S/MIME n’est **pas pris en charge** avec la clé de sécurité.
- L’opération « Run As» n’est **pas prise en charge** avec la clé de sécurité.
- La connexion à un serveur à l’aide d’une clé de sécurité n’est **pas prise en charge**.
- Si vous n’avez pas utilisé votre clé de sécurité pour vous connecter à votre appareil une fois en ligne, vous ne pouvez pas l’utiliser pour vous connecter ou le déverrouiller hors connexion.
- Connexion ou déverrouillage d’un appareil Windows 10 avec une clé de sécurité contenant plusieurs comptes Azure AD. Ce scénario utilisera le dernier compte ajouté à la clé de sécurité. WebAuthN permettra aux utilisateurs de choisir le compte qu’ils souhaitent utiliser.

## <a name="prepare-devices-for-preview"></a>Préparer les appareils pour la préversion

Les appareils joints à Azure AD que vous utiliserez doivent exécuter Windows 10 version 1809 ou plus. Vous bénéficierez de la meilleure expérience sur Windows 10 version 1903 ou ultérieure.

## <a name="enable-security-keys-for-windows-sign-in"></a>Activer les clés de sécurité pour la connexion Windows

Les organisations peuvent choisir d’utiliser une ou plusieurs des méthodes suivantes pour activer l’utilisation de clés de sécurité pour la connexion Windows sur la base des exigences de l’organisation.

- [Activer avec Intune](#enable-with-intune)
- [Déploiement Intune ciblé](#targeted-intune-deployment)
- [Activer avec un package d’approvisionnement](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Activer avec Intune

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Microsoft Intune** > **Inscription d’appareils** > **Inscription Windows** > **Windows Hello Entreprise** > **Propriétés**.
1. Sous **Paramètres** définissez **Utiliser des clés de sécurité pour la connexion** sur **Activé**.

La configuration de clés de sécurité pour la connexion ne dépend pas de la configuration de Windows Hello Entreprise.

### <a name="targeted-intune-deployment"></a>Déploiement Intune ciblé

Pour cibler des groupes d’appareils spécifiques pour activer le fournisseur d’informations d’identification, utilisez les paramètres personnalisés suivants via Intune.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Microsoft Intune** > **Configuration de l’appareil** > **Profils** > **Créer un profil**.
1. Configurez le nouveau profil avec les paramètres suivants
   1. Nom : Clés de sécurité pour la connexion Windows
   1. Description : Permet d’utiliser des clés de sécurité FIDO lors de la connexion à Windows
   1. Plateforme : Windows 10 et versions ultérieures
   1. Type de profil : Personnalisée
   1. Paramètres OMA-URI personnalisés :
      1. Nom : Activer les clés de sécurité FIDO pour la connexion à Windows
      1. OMA-URI : ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Type de données : Integer
      1. Valeur : 1
1. Cette stratégie peut être attribuée à des utilisateurs, appareils ou groupes spécifiques. Vous trouverez plus d’informations dans l’article [Attribuer des profils d’utilisateur et d’appareil dans Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Création de stratégies de configuration d’appareil Intune personnalisées](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Activer avec un package d’approvisionnement

Pour les appareils non gérés par Intune, un package d’approvisionnement peut être installé pour activer la fonctionnalité. L’application Windows Configuration Designer peut être installée à partir du [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22).

1. Lancez Windows Configuration Designer.
1. Sélectionnez **Fichier** > **Nouveau projet**.
1. Donnez un nom à votre projet et notez le chemin d’accès auquel votre projet a été créé.
1. Sélectionnez **Suivant**.
1. Laissez **Package d’approvisionnement** sélectionné comme **Flux de travail de projet sélectionné** et sélectionnez **Suivant**.
1. Sélectionnez **Toutes les éditions de bureau de Windows** sous **Choisir les paramètres à afficher et configurer**, et sélectionnez **Suivant**.
1. Sélectionnez **Terminer**.
1. Dans votre projet nouvellement créé, accédez à **Paramètres d’exécution** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Définissez **UseSecurityKeyForSignIn** sur **Activé**.
1. Sélectionnez **Exporter** > **Package d’approvisionnement**
1. Laissez les valeurs par défaut dans la fenêtre **Générer** sous **Décrire le package d’approvisionnement**, et sélectionnez **Suivant**.
1. Laissez les valeurs par défaut dans la fenêtre **Générer** sous **Sélectionner les détails de sécurité pour le package d’approvisionnement**, et sélectionnez **Suivant**.
1. Prenez note du chemin d’accès (ou modifiez-le) dans la fenêtre **Générer** sous **Sélectionnez l’emplacement d’enregistrement du package d’approvisionnement**, et sélectionnez **Suivant**.
1. Sélectionnez **Générer** sur la page **Générer le package d’approvisionnement**.
1. Enregistrez les deux fichiers créés (ppkg et cat) dans un emplacement où vous pourrez les appliquer aux machines plus tard.
1. Suivez les instructions dans l’article [Appliquer un package d’approvisionnement](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package) pour appliquer le package d’approvisionnement que vous avez créé.

> [!NOTE]
> Les appareils exécutant Windows 10 version 1809 doivent également activer le mode de PC partagé (EnableSharedPCMode). Pour en savoir plus sur l’activation de ces fonctionnalités, consultez l’article [Configurer un PC partagé ou invité avec Windows10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>Se connecter à Windows à l’aide d’une clé de sécurité FIDO2

Dans l’exemple ci-dessous, un utilisateur appelé Bala Sandhu a déjà approvisionné sa clé de sécurité FIDO2 en suivant les étapes décrites de l’article précédent, portant sur [l’activation de la connexion par clé de sécurité sans mot de passe](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala peut choisir le fournisseur d’informations d’identification de clé de sécurité à partir de l’écran de verrouillage Windows 10, et insérer la clé de sécurité pour se connecter à Windows.

![Connexion par clé de sécurité depuis l’écran de verrouillage Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gérer la clé de sécurité biométrique, le code confidentiel, ou réinitialiser la clé de sécurité

* Windows 10 version 1903 ou ultérieure
   * Les utilisateurs peuvent ouvrir les **Paramètres Windows** sur leur appareil > **Comptes** > **Clé de sécurité**
   * Les utilisateurs peuvent modifier leur code confidentiel, mettre à jour les informations biométriques ou réinitialiser leur clé de sécurité

## <a name="troubleshooting-and-feedback"></a>Résolution des problèmes de commentaires

Si vous avez des commentaires ou si vous rencontrez des problèmes lors de l’affichage de la préversion de cette fonctionnalité, partagez vos remarques via l’application de concentrateur de commentaires Windows.

1. Lancez le **concentrateur de commentaires** et assurez-vous que vous êtes connecté.
1. Classez vos commentaires dans les catégories suivantes avant de les envoyer :
   1. Catégorie : Sécurité et confidentialité
   1. Sous-catégorie : FIDO
1. Pour capturer des journaux, utilisez l’option : **Recréer mon problème**

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="does-this-work-in-my-on-premises-environment"></a>Cela fonctionne-t-il dans mon environnement local ?

Cette fonctionnalité ne fonctionne pas pour un environnement Active Directory Domain Services (AD DS) entièrement local.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Mon organisation requiert une authentification à deux facteurs pour accéder aux ressources. Que puis-je faire pour prendre en charge cette exigence ?

Les clés de sécurité sont disponibles dans différents facteurs de forme. Contactez le fabricant de l’appareil pour savoir comment activer ses appareils à l’aide d’un code PIN ou biométrique en tant que deuxième facteur.

### <a name="can-admins-set-up-security-keys"></a>Les administrateurs peuvent-ils configurer des clés de sécurité ?

Nous nous efforçons actuellement d’assurer la disponibilité générale de cette fonctionnalité.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Où puis-je trouver des clés de sécurité conformes ?

[Clés de sécurité FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Que dois-je faire si je perds ma clé de sécurité ?

Vous pouvez supprimer des clés de sécurité à partir du Portail Microsoft Azure, en accédant à la page Informations relatives à la sécurité et en supprimant les clés.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’inscription des appareils](../devices/overview.md)

[En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
