---
title: Authentification sans mot de passe avec Azure AD
titleSuffix: Active Directory
description: Ce guide s’adresse aux directeurs d’entreprise, directeurs des systèmes d’information, responsables de la sécurité des systèmes d’information, architectes en chef de la gestion des identités, architectes d’entreprise et autres décideurs dans les domaines de la sécurité et de l’informatique en charge de choisir une méthode d’authentification sans mot de passe pour leurs implémentations Azure Active Directory.
keywords: sans mot de passe, azure ad
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 5fc6a4a23573995cf791a21ec7cf3a7d68d048e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064476"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Un monde sans mot de passe avec Azure Active Directory

Le moment est venu de rompre la relation que vous entretenez avec les mots de passe. Les mots de passe nous ont été très utiles par le passé, mais dans l'environnement de travail numérique d'aujourd'hui, ils sont devenus un vecteur d’attaque relativement facile pour les pirates. Les pirates adorent les mots de passe et rien d'étonnant à cela lorsqu'on sait que la plupart des mots de passe rejetés dans Azure Active Directory (Azure AD) contiennent des termes tels que l'année, le mois, la saison ou une équipe sportive locale. En outre, des [recherches ont montré](https://aka.ms/passwordguidance) que les recommandations classiques en matière de gestion des mots de passe, telles que les exigences de longueur, de complexité et les fréquences de changement, sont contre-productives pour diverses raisons liées à la nature humaine.

Les attaques par pulvérisation de mots de passe, hameçonnage et rejeu sont couramment utilisées pour compromettre les comptes des utilisateurs. Les fonctionnalités Azure AD, telles que le [verrouillage intelligent](../../active-directory/authentication/howto-password-smart-lockout.md), les [mots de passe interdits](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) et la [protection des mots de passe](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) contribuent à vous protéger contre ces types d'attaques. De même, l'implémentation d'une [authentification multifacteur](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) ou d'une vérification en deux étapes renforce la sécurité en exigeant une deuxième forme d’authentification. Mais à terme, une solution sans mot de passe reste la meilleure solution pour garantir une méthode d'authentification optimale.

Cet article marque le début de votre parcours. Il vous aide à comprendre et à implémenter les solutions sans mot de passe Microsoft et à choisir une ou plusieurs des options parmi les suivantes :

* **Windows Hello Entreprise**. Dans Windows 10, Windows Hello Entreprise remplace les mots de passe par une authentification à deux facteurs forte sur les PC et appareils mobiles. Cette authentification se compose d’un nouveau type d’informations d’identification utilisateur qui sont liées à un appareil et utilise la reconnaissance biométriques ou un code PIN.

* **Connexion sans mot de passe avec Microsoft Authenticator**. L’application Microsoft Authenticator vous permet de vous connecter à n’importe quel compte Azure AD sans utiliser de mot de passe. À l’instar de la technologie de Windows Hello Entreprise, Microsoft Authenticator a recours à l’authentification par clé pour activer des informations d’identification utilisateur qui sont liées à un appareil et utilisent un code biométrique ou confidentiel.

* **Clés de sécurité FIDO2**. FIDO2 fournit des informations d'identification de connexion cryptographiques uniques sur tous les sites web, et stockées sur un appareil local tel que Windows Hello ou des clés de sécurité externes. Ces clés de sécurité résistent aux risques de hameçonnage, de vol de mot de passe et d'attaque par rejeu. Combinée à la vérification de l'utilisateur via un code biométrique ou confidentiel, la solution à deux facteurs répond aux besoins de sécurité modernes.

## <a name="the-future-of-passwordless-authentication"></a>L'avenir de l'authentification sans mot de passe

De nos jours, les banques, les sociétés émettrices de cartes de crédit et autres organisations et services en ligne protègent souvent votre compte en vous demandant de vérifier votre identité deux fois : une fois en entrant votre mot de passe, puis une autre fois par téléphone, SMS ou via une application. Certes, l’authentification multifacteur répond à la question de la sécurité en cas de mots de passe partagés, volés ou devinés, mais pas à la difficulté que représente leur mémorisation. À l’ère du cloud, utilisateurs et entreprises entendent se doter de méthodes d’authentification sans mot de passe hautement sécurisées *et* pratiques.

![Aspect pratique et sécurité](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello Entreprise, la connexion sans mot de passe Microsoft Authenticator et les clés de sécurité FIDO2 ont en commun une architecture simple qui offre aux utilisateurs une méthode d’authentification aussi sécurisée que pratique. Tous trois reposent la technologie de clé publique/privée, requièrent une intervention locale comme l'entrée d'un code biométrique ou confidentiel, et sont liés à un seul appareil avec clés privées stockées de manière sécurisée et jamais partagées.

## <a name="windows-hello-for-business"></a>Windows Hello Entreprise

Dans Windows 10, Windows Hello Entreprise remplace les mots de passe par une authentification à deux facteurs forte sur les PC et appareils. L'authentification relève d'un nouveau type d'informations d'identification utilisateur lié à un appareil et implique un geste biométrique ou un code confidentiel pour permettre aux utilisateurs de s'authentifier auprès d'Azure AD et d'Active Directory en local. Se connecter à un appareil utilisant Windows Hello Entreprise est facile. Vous utilisez un code confidentiel ou un geste biométrique, comme une empreinte digitale ou une reconnaissance faciale.

### <a name="windows-hello-for-business-scenarios"></a>Scénarios Windows Hello Entreprise

Windows Hello Entreprise est idéal pour les professionnels de l’information qui disposent de leur propre PC Windows. Les données biométriques et les informations d’identification sont directement liées aux PC de l’utilisateur et dès lors, personne d'autre ne peut y accéder. Avec l’intégration PKI et la prise en charge intégrée de l'authentification unique (SSO), Windows Hello Entreprise offre une méthode d'accès simple et pratique aux ressources d'entreprise localement et dans le cloud.

### <a name="windows-hello-for-business-deployment-considerations"></a>Considérations relatives au déploiement de Windows Hello Entreprise

Windows Hello Entreprise est un système distribué qui utilise plusieurs composants à des fins d'inscription, d'approvisionnement et d’authentification des appareils. Dès lors, son déploiement implique une bonne planification au sein des différentes équipes de l'organisation. Le [guide de planification](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello Entreprise peut vous aider à déterminer le type de déploiement de Windows Hello Entreprise dont vous avez besoin, ainsi que les options à prendre en compte.

Lorsque vous déployez Windows Hello Entreprise, vous pouvez choisir parmi de nombreuses options. Ce large éventail d'options permet à la plupart des organisations de déployer Windows Hello Entreprise. Examinez les types de déploiements pris en charge suivants :

* [Déploiement d'approbation Azure AD Hybride avec clé](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Déploiement d'approbation Azure AD Hybride avec certificat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Guides de déploiement d'authentification unique Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Déploiement local avec clé](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Déploiement local un certificat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Proposer de nombreuses options peut donner l'impression d'un déploiement complexe. Cela étant, la plupart des organisations se rendront compte qu'elles ont déjà implémenté la majeure partie de l'infrastructure sur laquelle repose le déploiement de Windows Hello Entreprise. Quoi qu'il en soit, il apparaît important de comprendre que Windows Hello Entreprise est un système distribué pour lequel une planification appropriée est vivement conseillée.

Nous vous recommandons de lire [Planification d'un déploiement Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) pour vous aider à choisir le modèle de déploiement le mieux adapté à votre organisation. Ensuite, selon votre planification, consultez le [Guide de déploiement de Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) pour garantir un bon déploiement de Windows Hello Entreprise au sein de votre environnement existant.

### <a name="how-windows-hello-for-business-works"></a>Fonctionnement de Windows Hello Entreprise

#### <a name="user-sets-up-windows-hello-for-business"></a>L'utilisateur configure Windows Hello Entreprise

Après une vérification initiale en deux étapes de l'utilisateur lors de l'inscription, Windows Hello est configuré sur l'appareil de cet utilisateur et Windows lui demande de définir un geste, qui peut être biométrique (empreinte digitale ou reconnaissance faciale, par exemple) ou un code confidentiel. Lorsque c'est chose faite, l’utilisateur effectue le geste qui permet de vérifier son identité. Windows utilise ensuite Windows Hello pour authentifier les utilisateurs.

Selon les fonctionnalités de votre appareil Windows 10, vous disposez d'une enclave sécurisée intégrée, appelée module de plate-forme sécurisée (TPM, Trusted Platform Module) matériel, ou d'un module TPM logiciel. Le module TPM stocke la clé privée, dont le déverrouillage requiert une reconnaissance faciale, une empreinte digitale ou un code confidentiel. Les données biométriques ne sont pas itinérantes et jamais envoyées à des appareils ou serveurs externes. Un attaquant ne peut en aucun cas voler des données biométriques, car Windows Hello stocke uniquement les données d'identification biométriques sur l'appareil.

> [!TIP]
> De prime abord, un code confidentiel ressemble à un mot de passe, mais il est en réalité plus sécurisé. Contrairement à un mot de passe, un code confidentiel est lié à un appareil spécifique sur lequel il a été configuré. Une personne qui vole votre mot de passe peut accéder à votre compte depuis n’importe où. Mais une personne qui vole votre code confidentiel doit aussi voler votre appareil ! En outre, un code confidentiel étant conservé localement sur l'appareil, il n’est transmis nulle part ailleurs et ne peut donc pas être intercepté ou volé.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Utilisateur se servant de Windows Hello Entreprise pour se connecter

Les données biométriques et les codes confidentiels Windows Hello Entreprise utilisent un chiffrement asymétrique (clé publique/clé privée) à des fins d'authentification. Lors de l'authentification, le chiffrement est lié à la clé de session TLS, qui sécurise le processus d'authentification de sorte qu'une attaque par interception (MiTM) ne puisse voler le jeton ou l'artefact de sécurité qui en résulte et le réexécuter à partir d'autres emplacements.

Windows Hello Entreprise offre une expérience de connexion pratique qui authentifie l’utilisateur sur Azure AD et les ressources Active Directory. Les appareils joints à Azure AD s'authentifient sur Azure lors de la connexion et peuvent aussi s'authentifier sur Active Directory. Les appareils joints à Azure Active Directory Hybride s’authentifient sur Active Directory lors de la connexion, et s’authentifient sur Azure Active Directory en arrière-plan.

![Voir l’image source](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Les étapes suivantes illustrent l’authentification de la connexion sur Azure Active Directory.

![Écran de verrouillage Windows 10](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. L'utilisateur se connecte à Windows l’aide d'un geste biométrique ou d'un code confidentiel. Cette opération déverrouille la clé privée Windows Hello Entreprise et est transmise au fournisseur SSP (Security Support Provider) d'authentification cloud, appelé fournisseur Cloud AP.

2. Le fournisseur Cloud AP demande un nonce à Azure Active Directory.

3. Azure AD renvoie un nonce qui est valide pendant 5 minutes.

4. Le fournisseur Cloud AP signe le nonce à l’aide de la clé privée de l’utilisateur et le renvoie signé à Azure Active Directory.

5. Azure Active Directory vérifie le nonce signé à l’aide de la clé publique de l'utilisateur par rapport à la signature du nonce. Une fois la signature validée, Azure AD valide le nonce signé renvoyé. Après avoir validé le nonce, Azure AD crée un PRT avec la clé de session chiffrée sur la clé de transport de l’appareil et le retourne au fournisseur Cloud AP.

6. Le fournisseur Cloud AP reçoit le PRT chiffré avec la clé de session. Le fournisseur Cloud AP utilise la clé de transport privée de l'appareil pour déchiffrer la clé de session et protège cette dernière à l’aide du module TPM de l’appareil.

7. Le fournisseur Cloud AP retourne une réponse d’authentification réussie à Windows après quoi, l’utilisateur peut accéder à Windows, au cloud, ainsi qu'aux applications locales sans devoir procéder à une nouvelle authentification (SSO).

Pour plus d'informations sur le processus d’authentification dans d’autres scénarios impliquant Windows Hello Entreprise, consultez [Windows Hello Entreprise - Authentification](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>L'utilisateur gère ses informations d’identification Windows Hello Entreprise

Le [service de réinitialisation de code confidentiel de Microsoft](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset) est une fonctionnalité d'Azure AD qui permet aux utilisateurs de réinitialiser leur code confidentiel, si besoin. À l'aide d'une stratégie de groupe, Microsoft Intune ou d'un GPM compatible, un administrateur peut configurer les appareils Windows 10 de sorte qu'ils utilisent, en toute sécurité, le service de réinitialisation du code confidentiel qui permet aux utilisateurs de réinitialiser leur code confidentiel oublié via les paramètres ou l'écran de verrouillage ci-dessus, sans devoir procéder à une nouvelle inscription.

Les utilisateurs doivent parfois recourir aux mots de passe. La [réinitialisation de mot de passe en libre-service](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR) est une fonctionnalité d’Azure Active Directory qui permet aux utilisateurs de réinitialiser leurs mots de passe, sans devoir contacter le service informatique. Les utilisateurs doivent s’inscrire ou être inscrits à ce service avant de l’utiliser. Pendant l’inscription, l’utilisateur choisit une ou plusieurs méthodes d’authentification activées par son organisation. SSPR permet aux utilisateurs de rapidement débloquer leur compte et de continuer à travailler, quels que soient l’heure ou l’endroit où ils se trouvent. En permettant aux utilisateurs de débloquer leur compte eux-mêmes, votre organisation peut réduire les pertes de productivité et les coûts de support élevés pour les problèmes les plus courants liés aux mots de passe.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Connexion sans mot de passe avec Microsoft Authenticator

La connexion sans mot de passe avec Microsoft Authenticator permet également de se connecter à des comptes Azure AD à l'aide d'une connexion par téléphone. Vous devez quand même vérifier votre identité en fournissant un élément que vous connaissez et une chose que vous avez, mais la connexion par téléphone vous permet d’ignorer la saisie du mot de passe et effectue l’ensemble de la vérification d’identité sur votre appareil mobile à l'aide d'une empreinte digitale, d'une reconnaissance faciale ou d'un code confidentiel.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Scénarios sans mot de passe Microsoft Authenticator

L'application Microsoft Authenticator permet aux utilisateurs de vérifier leur identité et de s'authentifier sur leur compte professionnel ou personnel. Elle peut également être utilisée pour renforcer un mot de passe avec un code secret à usage unique ou une notification Push, ou pour remplacer le besoin d'un mot de passe. Au lieu d'utiliser un mot de passe, les utilisateurs confirment leur identité à l'aide de leur téléphone portable par empreinte digitale, reconnaissance faciale ou de l'iris, ou code confidentiel. Basé sur une technologie sécurisée similaire à celle de Windows Hello, cet outil est empaqueté dans une application simple sur appareil mobile, ce qui en fait une solution pratique pour les utilisateurs. L’application Microsoft Authenticator est disponible pour Android et iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Considérations relatives au déploiement de Microsoft Authenticator

Les conditions préalables à l'utilisation de l'application Microsoft Authenticator à des fins de connexion sans mot de passe à Azure AD sont les suivantes :

* Les utilisateurs finaux sont activés pour Azure Multi-Factor Authentication

* Il est recommandé que les utilisateurs inscrivent leurs appareils à l’aide de Microsoft Intune ou d’une solution de gestion des périphériques mobiles (GPM) tierce pour simplifier le déploiement de l’application

Si ces conditions sont remplies, les administrateurs activent la connexion par téléphone sans mot de passe dans le locataire à l'aide de [Windows PowerShell.](../../active-directory/authentication/howto-authentication-passwordless-phone.md) Une fois la connexion activée par téléphone dans le locataire, les utilisateurs finaux peuvent choisir de se connecter via leur téléphone en sélectionnant leur compte professionnel ou scolaire dans l'écran **Comptes** de l'application, puis **Activer la connexion par téléphone**.

Si la connexion sans mot de passe est activée par un administrateur, les utilisateurs finaux devront répondre aux exigences suivantes :

* Inscription dans Azure Multi-Factor Authentication

* Installation de la dernière version de Microsoft Authenticator sur des appareils exécutant iOS 8.0 ou une version ultérieure, ou Android 6.0 ou une version ultérieure

* Ajout d’un compte professionnel ou scolaire avec notifications Push à l’application

Pour éviter de vous retrouver bloqué ou de devoir recréer des comptes sur un nouvel appareil, il vous est recommandé d'utiliser Microsoft Authenticator afin de [sauvegarder les informations d'identification de votre compte](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) dans le cloud. Après la sauvegarde, vous pouvez également utiliser l’application pour récupérer vos informations sur un nouvel appareil, ce qui évite de se retrouver bloqué ou d’avoir à recréer des comptes.

Dans la mesure où la plupart des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre organisation forme les utilisateurs sur ce processus. Le fait d’en être averti peut réduire la probabilité que les utilisateurs appellent votre support technique pour des [problèmes](../../active-directory/authentication/howto-authentication-passwordless-phone.md) liés à la connexion à l'aide de l'application Microsoft Authenticator.

> [!NOTE]
> La présence d'un utilisateur itinérant dans un lieu sans connectivité Internet constitue un point de défaillance de cette solution. Les clés de sécurité FIDO2 et Windows Hello Entreprise ne sont pas soumis à la même restriction.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Fonctionnement de la connexion sans mot de passe avec Microsoft Authenticator

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>L'utilisateur configure la connexion sans mot de passe avec Microsoft Authenticator

Pour utiliser l'application Microsoft Authenticator en tant que solution sans mot de passe à des fins de connexion à un compte Azure AD, l'administrateur et les utilisateurs finaux doivent procéder comme indiqué ci-dessous.

Tout d’abord, l'administrateur doit [activer l’utilisation de l’application en tant qu’informations d’identification](../../active-directory/authentication/howto-authentication-passwordless-phone.md) dans le locataire à l’aide de Windows PowerShell. L’administrateur doit également activer les utilisateurs finaux pour Azure Multi-Factor Authentication (Azure MFA) et configurer l’application Microsoft Authenticator en tant que [méthode de vérification](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods).

Les utilisateurs finaux doivent [télécharger et installer](../../active-directory/user-help/user-help-auth-app-download-install.md) l’application Microsoft Authenticator et [configurer leur compte](../../active-directory/user-help/security-info-setup-auth-app.md) de manière à utiliser Microsoft Authenticator en tant que méthode de vérification.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Utilisateur se servant de Microsoft Authenticator à des fins de connexion sans mot de passe

L’application Microsoft Authenticator vous permet de vous connecter à n’importe quel compte Azure AD sans utiliser de mot de passe. Bien que l'écran de verrouillage Windows 10 n'inclut pas l'application Microsoft Authenticator en tant qu'option de connexion, les utilisateurs peuvent entrer leur nom d'utilisateur, puis recevoir une notification Push sur leur appareil mobile à des fins de vérification. Les utilisateurs confirment leur présence en faisant correspondre un numéro sur l'écran de connexion, puis avec une reconnaissance faciale, une empreinte digitale ou un code confidentiel pour déverrouiller la clé privée et finaliser l'authentification. Cette méthode de vérification multifacteur est plus sécurisée qu'un mot de passe, et plus pratique que la saisie d'un mot de passe et d'un code.

![Connexion à Microsoft Authenticator](./media/ad-passwordless/azure-ad-pwdless-image4.png)

L'authentification sans mot de passe à l'aide de Microsoft Authenticator suit le même schéma de base que Windows Hello Entreprise, mais elle est un peu plus complexe en ce sens que l'utilisateur doit être identifié pour permettre à Azure AD de déterminer la version de l'application Microsoft Authenticator utilisée.

![Processus Microsoft Authenticator](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. L'utilisateur entre son nom d’utilisateur.

2. Azure AD détecte que l'utilisateur dispose d'informations d'identification fortes et démarre le flux correspondant.

3. La notification est envoyée à l’application via Apple Push Notification Service (APNS) sur les appareils iOS ou Firebase Cloud Messaging (FCM) sur les appareils Android.

4. L'utilisateur reçoit la notification Push et ouvre l'application.

5. L’application appelle Azure AD et reçoit un test de preuve de présence et un nonce.

6. L’utilisateur répond au test à l'aide en entrant un code biométrique ou un code confidentiel pour déverrouiller la clé privée.

7. Le nonce est signé avec la clé privée, puis renvoyé à Azure AD.

8. Azure AD procède à la validation des clés publique/privée et renvoie un jeton.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>L'utilisateur gère sa connexion sans mot de passe avec les informations d’identification Microsoft Authenticator

Avec l’[inscription combinée](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md), les utilisateurs peuvent s’inscrire et bénéficier des avantages de l'authentification multifacteur et de la réinitialisation de mot de passe en libre-service. L'utilisateur s'inscrit et gère ces paramètres en accédant à sa page [Mon profil](https://aka.ms/mysecurityinfo). En plus de la réinitialisation de mot de passe en libre-service, l'inscription combinée prend en charge plusieurs méthodes et actions d'authentification.

## <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

FIDO2 est la dernière version de la norme FIDO Alliance et comprend deux composants : l'authentification web W3C (WebAuthN) et le protocole FIDO Alliance Client-to-Authenticator Protocol (CTAP2) correspondant. Les normes FIDO2 permettent aux utilisateurs d'utiliser des systèmes d'authentification matériels, mobiles et biométriques pour s'authentifier facilement avec bon nombre d'applications et de sites web dans des environnements de bureau et mobiles.

Microsoft et des partenaires du secteur collaborent sur les périphériques de sécurité FIDO2 pour Windows Hello afin de proposer une authentification simple et sécurisée sur les appareils partagés. Les clés de sécurité FIDO2 vous permettent de transporter vos informations d’identification et de vous authentifier en toute sécurité sur un appareil Windows 10 joint à [Azure AD](https://aka.ms/azuread418) appartenant à votre organisation.

WebAuthN définit une API qui permet de développer et d'implémenter une authentification sans mot de passe forte via des applications et services web. Le protocole CTAP permet aux périphériques externes, tels que les clés de sécurité compatibles FIDO, de fonctionner avec WebAuthN et de faire office d'authentificateurs. Avec l’authentification web, les utilisateurs peuvent se connecter aux services en ligne par reconnaissance faciale, empreinte digitale, code confidentiel ou clés de sécurité FIDO2 portables, tout en tirant parti d'informations d’identification de clé publique fortes plutôt que de mots de passe. Actuellement, WebAuthN est pris en charge par Microsoft Edge, et une prise en charge par Chrome et Firefox est en cours de développement.

Les périphériques et les jetons qui adhèrent aux protocoles FIDO2, WebAuthN et CTAP constituent une solution multiplateforme d'authentification forte sans mot de passe. Les partenaires Microsoft travaillent sur différents facteurs de forme de clé de sécurité, tels que les clés de sécurité USB et les cartes à puce compatibles NFC.

### <a name="fido2-security-keys-scenarios"></a>Scénarios avec clés de sécurité FIDO2

Les clés de sécurité FIDO2 permettent de se connecter à Azure AD en sélectionnant la clé de sécurité en tant que fournisseur d'informations d'identification sur l'écran de verrouillage Windows 10. Aucun nom d'utilisateur ou mot de passe n'est requis, ce qui en fait la solution idéale pour les employés de terrain partageant des ordinateurs avec plusieurs utilisateurs. Ces clés constituent aussi une excellente solution d’authentification lorsque les stratégies d'entreprise imposent aux utilisateurs de séparer physiquement leurs informations d'identification et leurs appareils. Les utilisateurs peuvent choisir de se connecter aux sites web à l'aide de leur clé de sécurité FIDO2 depuis le navigateur Microsoft Edge sur Windows 10, version 1809 ou ultérieure.

### <a name="fido2-security-keys-deployment-considerations"></a>Considérations relatives au déploiement de clés de sécurité FIDO2

Les administrateurs peuvent activer la prise en charge de FIDO2 dans Azure AD et attribuer cette fonctionnalité à des utilisateurs ou groupes. Des stratégies peuvent également être créées pour déterminer la manière dont les clés sont approvisionnées et configurer des restrictions, telles que le fait d'autoriser ou d'interdire un jeu spécifique de clés de sécurité matérielles. Les clés doivent être physiquement distribuées aux utilisateurs finaux.

**La configuration requise pour activer la connexion sans mot de passe à Azure AD et aux sites web à l’aide de clés de sécurité FIDO2 comprend notamment :**

* Azure AD

* Azure Multi-Factor Authentication

* Inscription combinée en préversion

* La préversion des clés de sécurité FIDO2 nécessite une clé de sécurité FIDO2 compatible

* L'authentification web (WebAuthN) nécessite Microsoft Edge sur Windows 10 version 1809 ou version ultérieure

* La connexion Windows basée sur FIDO2 requiert Windows 10, version 1809 ou une ultérieure, joint à Azure AD (Windows 10 version 1903 ou ultérieure offre une expérience optimale)

Les facteurs de forme compatibles FIDO2 incluent des périphériques USB, NFC et Bluetooth. Nous vous recommandons de choisir le facteur de forme le plus adapté à vos besoins, car certains navigateurs et plateformes ne sont pas encore compatibles avec FIDO2.

Nous recommandons également à chaque organisation de créer un protocole à l'attention des utilisateurs et administrateurs en cas de perte ou de vol d'une clé de sécurité. Il est impératif de signaler toute clé perdue ou volée pour permettre aux administrateurs ou à l'utilisateur de supprimer la clé de sécurité du profil concerné et d'en approvisionner une nouvelle.

### <a name="how-fido2-security-keys-works"></a>Fonctionnement des clés de sécurité FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>L'utilisateur configure la clé de sécurité FIDO2

Les administrateurs peuvent [approvisionner manuellement les clés](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) et les distribuer aux utilisateurs finaux, mais [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods) prend en charge l'approvisionnement et l'activation du fournisseur d'informations d'identification FIDO2 sur l'écran de verrouillage Windows 10. Les administrateurs doivent également utiliser le [portail Azure](https://portal.azure.com/) pour activer les appareils à jeton matériel en tant que méthode d’authentification sans mot de passe.

Le déploiement des clés de sécurité FIDO2 implique aussi que les utilisateurs inscrivent leurs clés à l'aide de l'[inscription combinée](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md). Avec l’inscription combinée, les utilisateurs s’inscrivent une seule fois et bénéficient des avantages de l'authentification multifacteur et de la réinitialisation de mot de passe en libre-service (SSPR).

En plus de sélectionner le jeton matériel en tant que méthode d'authentification multifacteur par défaut, il est recommandé de sélectionner une option de vérification supplémentaire.

* Microsoft Authenticator -- notification

* Application Microsoft Authenticator ou jeton matériel -- code

* appel téléphonique

* SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Utilisateur se servant d'une clé de sécurité FIDO2 pour se connecter

FIDO2 fournit une couche d'abstraction entre le facteur de forme utilisé en tant qu'authentificateur et la cryptographie par clé publique/privée pour permettre aux authentificateurs de plateforme intégrés, tels que Windows Hello et les clés de sécurité, de se résoudre en clé privée et de mettre à disposition une clé publique pouvant faire office d'identifiant pour accéder aux ressources externes. Les clés de sécurité FIDO2 sont équipées de leur propre enclave sécurisée intégrée qui stocke la clé privée et implique un geste biométrique ou un code confidentiel à des fins de déverrouillage. Les informations d'identification ne peuvent pas être réutilisées, relues ou partagées entre services, et ne sont pas sujettes au hameçonnage, attaques de l'intercepteur ou violations de serveur.

![Connexion FIDO2](./media/ad-passwordless/azure-ad-pwdless-image6.png)

Les clés de sécurité FIDO2 permettent une authentification sécurisée, indépendamment du facteur de forme. La clé de sécurité contient les informations d'identification et doit être protégée par un second facteur, tel qu'une empreinte digitale (intégrée à la clé de sécurité) ou un code confidentiel à saisir lors de la connexion à Windows. Les partenaires Microsoft travaillent sur différents facteurs de forme de clé de sécurité parmi lesquels, les clés de sécurité USB et les cartes à puce NFC.

> [!NOTE]
> Une clé de sécurité DOIT implémenter certaines fonctionnalités et extensions du protocole FIDO2 CTAP pour être [compatible avec Microsoft](https://aka.ms/fido2securitykeys). Microsoft a testé la compatibilité de ces solutions avec Windows 10 et Azure Active Directory.

![Processus de connexion FIDO2](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. L’utilisateur connecte le périphérique FIDO2 à l’ordinateur.

2. Windows détecte la clé de sécurité FIDO2.

3. Windows envoie une requête d’authentification.

4. Azure AD renvoie un nonce.

5. L’utilisateur fait le geste qui convient pour déverrouiller la clé privée stockée dans l’enclave sécurisée de la clé de sécurité FIDO2.

6. La clé de sécurité FIDO2 signe le nonce avec la clé privée.

7. La demande de jeton PRT contenant le nonce signé est envoyée à Azure AD.

8. Azure AD vérifie le nonce signé à l’aide de la clé publique FIDO2.

9. Azure AD renvoie le PRT pour activer l’accès aux ressources locales.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>L'utilisateur gère les informations d’identification de sa clé de sécurité FIDO2

Comme pour l’application Microsoft Authenticator, la gestion des informations d’identification des clés de sécurité FIDO2 s’appuie sur l’expérience combinée d’inscription pour les utilisateurs finaux.

## <a name="deciding-a-passwordless-method"></a>Choix d’une méthode sans mot de passe

Choisir entre ces trois options sans mot de passe dépend des exigences en matière de sécurité, de plateforme et d'applications de votre entreprise.

Voici quelques facteurs à prendre en compte au moment de choisir une technologie Microsoft sans mot de passe :

||**Windows Hello Entreprise**|**Connexion avec l’application Microsoft Authenticator**|**Clés de sécurité FIDO2**|
|:-|:-|:-|:-|
|**Conditions préalables**| Windows 10 version 1809 ou ultérieure<br>Azure Active Directory| Application Microsoft Authenticator<br>Téléphone (appareils iOS et Android exécutant Android version 6.0 ou ultérieure).|Windows 10 version 1809 ou ultérieure<br>Azure Active Directory|
|**Mode**|Plateforme|Logiciel|Matériel|
|**Systèmes et appareils**|PC avec module de plateforme sécurisée (TPM) intégré<br>Code confidentiel et reconnaissance biométrique |Code confidentiel et reconnaissance biométrique sur téléphone|Périphériques de sécurité FIDO2 compatibles Microsoft|
|**Expérience utilisateur**|Connectez-vous à l’aide d’un code confidentiel ou d'une reconnaissance biométrique (faciale, iris ou empreinte digitale) aux appareils Windows.<br>L'authentification Windows Hello est liée à l'appareil ; l'utilisateur a besoin de l'appareil et d'un composant de connexion tel qu'un code confidentiel ou un facteur biométrique pour accéder aux ressources de l'entreprise.|Connectez-vous à l’aide d’un téléphone mobile en utilisant une empreinte digitale, une reconnaissance faciale ou de l'iris, ou un code confidentiel.<br>Les utilisateurs se connectent à un compte professionnel ou personnel à partir de leur PC ou téléphone mobile.|Connectez-vous à l’aide du périphérique de sécurité FIDO2 (biométrie, code confidentiel et carte NFC)<br>L'utilisateur peut accéder à l'appareil selon les contrôles de l’organisation et s’authentifier par code confidentiel ou biométrie à l'aide de périphériques tels que des clés de sécurité USB et des cartes à puce NFC, des clés ou autres objets connectés portables.|
|**Scénarios possibles**| Expérience sans mot de passe avec appareil Windows.<br>Applicable pour le PC de travail dédié avec possibilité d'authentification unique à l'appareil et aux applications.|Solution sans mot de passe à l'aide d'un téléphone mobile.<br>Applicable pour accéder aux applications professionnelles et personnelles sur le web à partir de n’importe quel appareil.|Expérience sans mot de passe pour les employés avec données biométriques, code confidentiel et carte à puce NFC.<br>Applicable pour les PC partagés et lorsqu'un téléphone mobile n’est pas une option viable (personnel du support technique, borne publique ou équipe hospitalière)|

Utilisez le tableau suivant pour choisir la méthode répondant à vos besoins et à ceux de vos utilisateurs.

|Utilisateur|Scénario|Environnement|Technologie sans mot de passe|
|:-|:-|:-|:-|
|**Administrateur**|Sécuriser l’accès à un appareil pour les tâches de gestion|Appareil Windows 10 attribué|Windows Hello Entreprise et/ou clé de sécurité FIDO2|
|**Administrateur**|Tâches de gestion sur des appareils non Windows| Appareil mobile ou non Windows|Connexion sans mot de passe avec l'application Microsoft Authenticator|
|**Professionnel de l'information**|Productivité|Appareil Windows 10 attribué|Windows Hello Entreprise et/ou clé de sécurité FIDO2|
|**Professionnel de l'information**|Productivité| Appareil mobile ou non Windows|Connexion sans mot de passe avec l'application Microsoft Authenticator|
|**Employé de terrain**|Borne dans une usine, un magasin de détail ou un point d'entrée de données|Appareils Windows 10 partagés|Clés de sécurité FIDO2|

## <a name="getting-started"></a>Prise en main

L'authentification sans mot de passe est l'avenir et ouvre la voie à un environnement plus sécurisé. Il est recommandé aux organisations d'envisager ce changement et de réduire leur dépendance aux mots de passe. Pour commencer, prenez en compte les objectifs suivants :

* Activer les utilisateurs pour MFA + application Microsoft Authenticator + accès conditionnel.

* Déployer la protection de mot de passe Azure AD + mettre à jour les stratégies.

* Activer les utilisateurs pour SSPR avec inscription combinée.

* Déployer l'application Microsoft Authenticator à des fins de mobilité.

* Déployer Windows Hello Entreprise (1909 : pour être à jour).

* Déployer des périphériques FIDO2 pour les utilisateurs qui ne peuvent pas utiliser de téléphones.

* Dans la mesure du possible, désactiver l’authentification basée sur un mot de passe.

Pour atteindre ces objectifs, nous recommandons l’approche suivante :

1. Activez Azure Active Directory pour tirer pleinement parti de fonctionnalités telles qu'Azure MFA et l'accès conditionnel.

2. Activez l'authentification multifacteur pour renforcer la sécurité.

3. Activez la réinitialisation du mot de passe en libre-service si les utilisateurs doivent à nouveau utiliser un mot de passe.

4. Déployez la connexion par téléphone Microsoft Authenticator pour plus de mobilité.

5. Déployez Windows Hello Entreprise sur tous vos appareils Windows 10.

6. Préparez-vous à adopter les clés de sécurité FIDO2.

> [!NOTE]
> Reportez-vous à la [page sur les licences ](https://azure.microsoft.com/pricing/details/active-directory/) Azure Active Directory pour des détails sur les exigences en termes de licences des méthodes sans mot de passe.

## <a name="conclusion"></a>Conclusion

Au cours des dernières années, Microsoft a continué d'œuvrer en faveur d’un monde sans mots de passe. Avec Windows 10, Microsoft a introduit Windows Hello Entreprise, une information d'identification forte à deux facteurs qui permet une authentification unique sur Azure Active Directory et Active Directory. À l’instar de la technologie de Windows Hello Entreprise, l'application Microsoft Authenticator a recours à l’authentification par clé pour activer une information d’identification utilisateur qui est liée à un appareil mobile et utilise un code biométrique ou confidentiel. Les clés de sécurité FIDO2 vous permettent désormais de conserver vos informations d'identification et de vous connecter à Azure AD en sélectionnant la clé de sécurité en tant que fournisseur d'informations d'identification sur l'écran de verrouillage Windows 10. Ces trois solutions sans mot de passe réduisent les risques de hameçonnage, d'attaque par mot de passe et par rejeu, et offrent aux utilisateurs un moyen hautement sécurisé et pratique de se connecter et d'accéder aux données dont ils ont besoin où qu'ils se trouvent.

L'adoption de technologies d'authentification multifacteur modernes, telles que la biométrie et la cryptographie de clé publique, dans un large éventail d'appareils accessibles est l'une des étapes les plus efficaces permettant de réduire de manière significative le risque pour l'identité d'une entreprise. En matière d'authentification sécurisée, une approche sans mot de passe s'inscrit dans le long terme et n'a de cesse d'évoluer. Compte tenu des nouvelles exigences, les entreprises peuvent se préparer en élaborant un plan de transition vers des technologies sans mot de passe.

## <a name="next-steps"></a>Étapes suivantes

* Présentation de [l'authentification sans mot de passe](../../active-directory/authentication/concept-authentication-passwordless.md)
* [Comment activer l'authentification sans mot de passe dans Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
