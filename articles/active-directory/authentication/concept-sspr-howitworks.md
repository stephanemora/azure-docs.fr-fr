---
title: Analyse approfondie de la réinitialisation de mot de passe en libre-service – Azure Active Directory
description: Comment fonctionne la réinitialisation de mot de passe en libre-service ?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848576"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Fonctionnement : Réinitialisation de mot de passe en libre-service Azure AD

Comment fonctionne la réinitialisation de mot de passe libre-service (SSPR) ? Que signifie cette option dans l’interface ? Poursuivez la lecture pour en savoir plus sur SSPR Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Fonctionnement du portail de réinitialisation de mot de passe

Quand un utilisateur accède au portail de réinitialisation du mot de passe, un flux de travail est lancé pour déterminer les éléments suivants :

   * Comment la page doit être localisée ?
   * Le compte d’utilisateur est-il valide ?
   * À quelle organisation l’utilisateur appartient-il ?
   * Où le mot de passe de l’utilisateur est-il géré ?
   * L’utilisateur a-t-il une licence pour utiliser la fonctionnalité ?

Lisez les étapes suivantes pour en savoir plus sur la logique sous-jacente à la page de réinitialisation de mot de passe :

1. L’utilisateur sélectionne le lien **Vous ne parvenez pas à accéder à votre compte ?** ou il accède directement à [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Selon les paramètres régionaux du navigateur, l’expérience est proposée dans la langue appropriée. L’expérience de réinitialisation du mot de passe est localisée dans les mêmes langues que celles prises en charge par Office 365.
   * Pour afficher le portail de réinitialisation de mot de passe dans une autre langue localisée, ajoutez "?mkt=" à la fin de l’URL de réinitialisation du mot de passe avec l’exemple qui suit la localisation en espagnol [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. L’utilisateur entre un ID utilisateur et passe un test CAPTCHA.
3. Azure AD vérifie que l’utilisateur peut utiliser cette fonctionnalité en procédant aux vérifications suivantes :
   * Il vérifie que cette fonctionnalité est activée pour l’utilisateur et qu’il possède une licence Azure AD.
     * Si ce n’est pas le cas, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Il vérifie que l’utilisateur a défini les méthodes d'authentification appropriées sur son compte, conformément à la stratégie de l’administrateur.
     * Si la stratégie n’exige qu’une seule méthode, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins une des méthodes d'authentification activées par la stratégie de l’administrateur.
       * Si les méthodes d'authentification ne sont pas configurées, l’utilisateur est invité à contacter l’administrateur pour réinitialiser son mot de passe.
     * Si la stratégie exige deux méthodes, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins deux méthodes d'authentification activées par la stratégie de l’administrateur.
       * Si les méthodes d'authentification ne sont pas configurées, l’utilisateur est invité à contacter l’administrateur pour réinitialiser son mot de passe.
     * Si un rôle d’administrateur Azure est affecté à l’utilisateur, la stratégie de mot de passe fort à deux verrous est appliquée. Vous trouverez plus d’informations sur cette stratégie dans la section [Différences en matière de stratégie de réinitialisation par l’administrateur](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Il vérifie si le mot de passe de l’utilisateur est géré localement (fédération, authentification directe ou synchronisation du hachage de mot de passe).
     * Si la réécriture est déployée et que le mot de passe est géré localement, l’utilisateur est autorisé à s’authentifier et à réinitialiser son mot de passe.
     * Si la réécriture n’est pas déployée et que le mot de passe est géré localement, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4. S’il est établi que l’utilisateur est en mesure de réinitialiser son mot de passe, il reçoit des instructions pour mener à bien le processus de réinitialisation.

## <a name="authentication-methods"></a>Méthodes d’authentification

Si SSPR est activé, vous devez sélectionner au moins l’une des options suivantes pour les méthodes d’authentification. Ces options sont parfois appelées « portails ». Il est vivement recommandé que **choisir au moins deux méthodes d’authentification** afin que les utilisateurs disposent de davantage de flexibilité au le cas où une des méthodes nécessaires ne serait pas accessible. Vous trouverez plus d’informations sur les méthodes énoncées ci-dessous dans l’article [Que sont les méthodes d’authentification ?](concept-authentication-methods.md).

* Notification sur l’application mobile
* Code de l’application mobile
* Email
* Téléphone mobile
* Téléphone de bureau
* Questions de sécurité

Les utilisateurs peuvent uniquement réinitialiser leur mot de passe s’ils ont des données présentes dans les méthodes d’authentification que l’administrateur a activées.

> [!IMPORTANT]
> Depuis mars 2019, les options d’appel téléphonique ne sont plus disponibles pour les utilisateurs MFA et SSPR dans des locataires Azure AD gratuits ou à l’essai. Cette modification n’affecte pas les messages SMS. Les appels téléphoniques continueront à être disponibles pour les utilisateurs de locataires Azure AD payants. Cette modification affecte uniquement les locataires Azure AD gratuits ou à l’essai.

> [!WARNING]
> Les rôles d’administrateur Azure auxquels sont assignés des comptes seront nécessaires pour utiliser les méthodes définies dans la section [Différences en matière de stratégie de réinitialisation par l’administrateur](concept-sspr-policy.md#administrator-reset-policy-differences).

![Sélection des méthodes d’authentification dans le portail Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Nombre de méthodes d’authentification requises

Cette option détermine le nombre minimal de méthodes d’authentification ou de portails disponibles que l’utilisateur doit appliquer pour réinitialiser ou débloquer son mot de passe. Elle peut être définie sur un ou deux.

Les utilisateurs peuvent choisir de fournir plusieurs méthodes d’authentification si l’administrateur les active.

Si un utilisateur n’a pas le nombre minimal requis de méthodes inscrites, une page d’erreur s’affiche et lui indique de contacter un administrateur pour que ce dernier réinitialise son mot de passe.

#### <a name="mobile-app-and-sspr"></a>Application mobile et réinitialisation de mot de passe en libre-service

Lorsque vous utilisez une application mobile, comme l’application Microsoft Authenticator, vous devez tenir compte des mises en garde suivantes concernant la méthode de réinitialisation de mot de passe :

* Lorsque les administrateurs demandent d’utiliser une méthode de réinitialisation de mot de passe, le code de vérification est la seule option disponible.
* Lorsque les administrateurs demandent d’utiliser deux méthodes de réinitialisation de mot de passe, les utilisateurs seront en mesure d’effectuer la réinitialisation **SOIT** avec une notification, **SOIT** avec un code de vérification en plus de n’importe quelle autre méthode activée.

| Nombre de méthodes requises pour la réinitialisation | Une | Deux |
| :---: | :---: | :---: |
| Fonctionnalités d’application mobile disponibles | Code | Code ou notification |

Les utilisateurs n’auront pas la possibilité d’inscrire leur application mobile lors de l’inscription pour la réinitialisation de mot de passe en libre-service depuis [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). En revanche, les utilisateurs peuvent inscrire leur application mobile sur [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou dans la nouvelle préversion d’inscription des informations de sécurité sur [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Vous devez activer la fonction [Inscription convergée pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur Azure (préversion publique)](concept-registration-mfa-sspr-converged.md) pour permettre aux utilisateurs de bénéficier de la nouvelle expérience sur [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> L’application d’authentification ne peut pas être sélectionnée comme seule méthode d’authentification lors de la configuration d’une stratégie à 1 porte. De même, l’application d’authentification et seulement une méthode supplémentaire ne peuvent pas être sélectionnées lors de la configuration d’une stratégie à 2 portes.
> Ainsi, lors de la configuration des stratégies SSPR qui englobent l’application d’authentification en tant que méthode, il faut au moins une méthode supplémentaire sélectionnée lors de la configuration d’une stratégie à 1 porte, et au moins deux méthodes supplémentaires sélectionnées lors de la configuration d’une stratégie à 2 portes.
> La raison de cette exigence est due au fait que l’expérience d’inscription SSPR actuelle ne comporte pas l’option d’inscription de l’application d’authentification. L’option permettant d’inscrire l’application d’authentification est incluse dans la nouvelle [Inscription convergée pour la réinitialisation de mot de passe en libre-service et Azure Multi-Factor Authentication (préversion publique)](concept-registration-mfa-sspr-converged.md).
> Autoriser des stratégies qui utilisent uniquement l’application d’authentification (pour les stratégies à 1 porte), ou l’application d’authentification et seulement une méthode supplémentaire (pour les stratégies à 2 portes) peut aller jusqu’à empêcher l’inscription des utilisateurs pour SSPR, tant qu’ils ne sont pas configurés pour utiliser la nouvelle expérience d’inscription.

### <a name="change-authentication-methods"></a>Changer les méthodes d’authentification

Si vous démarrez avec une stratégie qui présente une seule méthode d’authentification requise pour la réinitialisation ou le déverrouillage et que vous optez ensuite pour deux méthodes, que se passe-t-il ?

| Nombre de méthodes inscrites | Nombre de méthodes requises | Résultat |
| :---: | :---: | :---: |
| 1 ou plus | 1 | Réinitialisation ou déverrouillage **possible** |
| 1 | 2 | Réinitialisation ou déverrouillage **impossible** |
| 2 ou plus | 2 | Réinitialisation ou déverrouillage **possible** |

Si vous changez les types de méthodes d’authentification qu’un utilisateur peut employer, vous risquez d’empêcher par inadvertance les utilisateurs d’employer SSPR s’ils ne disposent pas de la quantité minimale de données.

Exemple :
1. La stratégie d’origine est configurée avec deux méthodes d’authentification requises. Elle utilise uniquement le numéro de téléphone professionnel et les questions de sécurité. 
2. L’administrateur change la stratégie pour ne plus utiliser les questions de sécurité, mais permet l’utilisation d’un téléphone mobile et d’une autre adresse de messagerie.
3. Les utilisateurs dont les champs de téléphone mobile ou d’autre adresse de messagerie ne sont pas renseignés ne peuvent pas réinitialiser leur mot de passe.

## <a name="registration"></a>Inscription

### <a name="require-users-to-register-when-they-sign-in"></a>Obliger les utilisateurs à s’inscrire durant la connexion

L’activation de cette option nécessite que l’utilisateur termine l’inscription pour la réinitialisation de mot de passe s’il se connecte à des applications à l’aide d’Azure AD. Ce workflow inclut les applications suivantes :

* Office 365
* Portail Azure
* Volet d'accès
* Applications fédérées
* Applications personnalisées utilisant Azure AD

Quand l’obligation d’inscription est désactivée, les utilisateurs peuvent s’inscrire manuellement. Ils peuvent visiter [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou sélectionner le lien **Réinitialiser mon mot de passe** sous l’onglet **Profil** dans le Panneau d’accès.

> [!NOTE]
> Les utilisateurs peuvent ignorer le portail d’inscription à la réinitialisation du mot de passe en sélectionnant **Annuler** ou en fermant la fenêtre. Dans ce cas, tant qu’ils ne procèdent pas à leur inscription, ils sont invités à s’inscrire chaque fois qu’ils se connectent.
>
> Cette interruption ne déconnecte pas les utilisateurs s’ils sont déjà connectés.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Définir le nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification

Cette option détermine la période de temps entre la configuration et la reconfirmation des informations d’authentification, et n’est disponible que si vous activez l’option **Obliger les utilisateurs à s’inscrire durant la connexion ?** .

Les valeurs valides sont comprises entre 0 et 730 jours, « 0 » signifiant que les utilisateurs ne sont jamais invités à reconfirmer leurs informations d’authentification.

## <a name="notifications"></a>Notifications

### <a name="notify-users-on-password-resets"></a>Notifier les utilisateurs lors des réinitialisations de mot de passe ?

Si cette option est définie sur **Oui**, les utilisateurs qui réinitialisent leur mot de passe reçoivent un e-mail les avertissant que leur mot de passe a été changé. L’e-mail est envoyé par le biais du portail SSPR à son adresse de messagerie principale et à son autre adresse de messagerie sur fichier dans Azure AD. Personne d’autre n’est informé de l’événement de réinitialisation.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ?

Si cette option est définie sur **Oui**, *tous les administrateurs* reçoivent un e-mail sur leur adresse de messagerie principale sur fichier dans Azure AD. Cet e-mail les informe qu’un autre administrateur a changé son mot de passe à l’aide de SSPR.

Exemple : quatre administrateurs font partie d’un environnement. L’administrateur A réinitialise son mot de passe à l’aide de SSPR. Les administrateurs B, C et D reçoivent un e-mail les informant de la réinitialisation de mot de passe.

## <a name="on-premises-integration"></a>Intégration locale

Si vous installez, configurez et activez Azure AD Connect, vous disposez des options supplémentaires suivantes pour les intégrations locales. Si ces options sont grisées, la réécriture n’a pas été correctement configurée. Pour plus d’informations, consultez [Configuration de la réécriture du mot de passe](howto-sspr-writeback.md).

![La validation de la réécriture du mot de passe est activée et opérationnelle][Writeback]

Cette page fournit un état rapide du client de réécriture local. L’un des messages suivants s’affiche en fonction de la configuration actuelle :

* Votre client de réécriture local est opérationnel.
* Azure AD Connect est en ligne et connecté à votre client de réécriture local. Cependant, il semble que la version installée d’Azure AD Connect est obsolète. Pensez à [mettre à niveau Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) pour vous assurer que vous disposez des dernières fonctionnalités de connectivité et des correctifs de bogues importants.
* Malheureusement, nous ne pouvons pas vérifier l’état de votre client de réécriture local, car la version installée d’Azure AD Connect est obsolète. [Mettez à niveau Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) pour être en mesure de vérifier l’état de votre connexion.
* Malheureusement, nous ne sommes pas en mesure de nous connecter à votre client d’écriture différée local pour le moment. [Résolvez les problèmes avec Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) pour restaurer la connexion.
* Malheureusement, nous ne pouvons pas nous connecter à votre client de réécriture local, car la réécriture du mot de passe n’a pas été configurée correctement. [Configurez la réécriture du mot de passe](howto-sspr-writeback.md) pour restaurer la connexion.
* Malheureusement, nous ne sommes pas en mesure de nous connecter à votre client d’écriture différée local pour le moment. Cela peut être dû à des problèmes temporaires de notre côté. Si le problème persiste, [résolvez les problèmes avec Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) pour restaurer la connexion.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Réécriture du mot de passe dans votre répertoire local

Ce contrôle détermine si la réécriture du mot de passe est activée pour ce répertoire. Si c’est le cas, il indique l’état du service de réécriture local. Ce contrôle est utile si vous voulez désactiver temporairement la réécriture du mot de passe sans devoir reconfigurer Azure AD Connect.

* Si le commutateur est défini sur **Oui**, la réécriture est activée et les utilisateurs fédérés, utilisant l’authentification directe ou synchronisés par hachage du mot de passe peuvent réinitialiser leur mot de passe.
* Si le commutateur est défini sur **Non**, la réécriture est désactivée et les utilisateurs fédérés, utilisant l’authentification directe ou synchronisés par hachage du mot de passe ne peuvent pas réinitialiser leur mot de passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe

Ce contrôle indique si les utilisateurs qui visitent le portail de réinitialisation de mot de passe doivent avoir la possibilité de déverrouiller leurs comptes Active Directory locaux sans devoir réinitialiser leur mot de passe. Par défaut, Azure AD déverrouille les comptes quand il effectue une réinitialisation de mot de passe. Ce paramètre vous permet de séparer ces deux opérations.

* Si la valeur est **Oui**, les utilisateurs peuvent réinitialiser leur mot de passe et déverrouiller le compte ou déverrouiller celui-ci sans devoir réinitialiser le mot de passe.
* Si la valeur est **Non**, les utilisateurs doivent réinitialiser leur mot de passe quand ils déverrouillent leur compte.

### <a name="on-premises-active-directory-password-filters"></a>Filtres de mot de passe Active Directory locaux

La réinitialisation de mot de passe en libre-service Azure AD est identique à la réinitialisation de mot de passe initiée par l’administrateur dans Active Directory. Si vous utilisez un filtre de mot de passe tiers pour appliquer les règles de mot de passe personnalisé, et que vous avez besoin que ce filtre de mot de passe soit vérifié au cours de la réinitialisation de mot de passe en libre-service Azure AD, vérifiez que la solution de filtre de mot de passe tiers est configurée pour le scénario de réinitialisation de mot de passe administrateur. [La protection de mot de passe Azure AD pour Windows Server Active Directory](concept-password-ban-bad-on-premises.md) est prise en charge par défaut.

## <a name="password-reset-for-b2b-users"></a>Réinitialisation de mot de passe pour les utilisateurs B2B

La réinitialisation et la modification du mot de passe sont totalement prises en charge sur toutes les configurations B2B. La réinitialisation du mot de passe utilisateur B2B est prise en charge dans les trois cas suivants :

* **Utilisateurs d’une organisation partenaire disposant d’un locataire Azure AD** : si l’organisation avec laquelle vous avez un partenariat dispose d’un locataire Azure AD, nous *respectons les stratégies de réinitialisation du mot de passe activées sur ce locataire*. Pour que la réinitialisation de mot de passe fonctionne, l’organisation partenaire doit simplement vérifier qu’Azure AD SSPR est activé. Aucun frais supplémentaire n’est appliqué pour les clients Office 365, et l’utilisateur peut activer cette fonctionnalité en suivant les étapes décrites dans notre guide [Bien démarrer avec la gestion des mots de passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
* **Utilisateurs qui s’inscrivent par le biais de l’inscription en libre-service** : si l’organisation avec laquelle vous avez un partenariat a utilisé la fonctionnalité [d’inscription en libre-service](../users-groups-roles/directory-self-service-signup.md) pour accéder à un locataire, nous l’autorisons à réinitialiser le mot de passe en indiquant l’adresse e-mail qu’elle a utilisée pour l’inscription.
* **Utilisateurs B2B** : tous les utilisateurs B2B créés à l’aide des nouvelles [fonctionnalités B2B d’Azure AD](../active-directory-b2b-what-is-azure-ad-b2b.md) peuvent également réinitialiser leur mot de passe en indiquant l’adresse e-mail utilisée pour l’inscription.

Pour tester ce scénario, accédez à https://passwordreset.microsoftonline.com avec l’un de ces utilisateurs partenaires. Si ces utilisateurs disposent d’une autre adresse de messagerie ou d’un e-mail d’authentification, la réinitialisation du mot de passe fonctionne comme prévu.

> [!NOTE]
> Les comptes Microsoft qui disposent d’un accès invité à votre locataire Azure AD, tels que les comptes Hotmail.com, Outlook.com ou correspondant à d’autres adresses e-mail personnelles, ne peuvent pas utiliser Azure AD SSPR. Les utilisateurs de ces comptes doivent réinitialiser leur mot de passe à l’aide des informations fournies dans l’article [Lorsque vous n’avez pas accès à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD :

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](howto-sspr-deployment.md)
* [Réinitialiser ou modifier votre mot de passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](../user-help/active-directory-passwords-reset-register.md)
* [Vous avez une question relative à la licence ?](concept-sspr-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](howto-sspr-authenticationdata.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](concept-sspr-howitworks.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](concept-sspr-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](howto-sspr-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](howto-sspr-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](concept-sspr-howitworks.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Intégration locale - Configuration de la réécriture de mot de passe et informations de dépannage"
