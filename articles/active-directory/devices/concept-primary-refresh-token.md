---
title: Jeton d’actualisation principal (PRT) et Azure AD - Azure Active Directory
description: Quel est le rôle d’et comment gérer le principal actualiser jeton (PRT) dans Azure Active Directory ?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ecf6d04056a91f1f9dd62a5238f60177d2bf59
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420587"
---
# <a name="what-is-a-primary-refresh-token"></a>Qu’est un principal jeton d’actualisation ?

Un principal actualiser jeton (PRT) est un artefact de clé d’authentification Azure AD sur Windows 10, iOS et les appareils Android. Il s’agit d’un utilitaire JSON Web Token (JWT) émis spécialement pour Microsoft premier tiers jeton brokers pour activer l’authentification unique (SSO) entre les applications utilisées sur ces appareils. Dans cet article, nous fournirons des détails sur comment un PRT est émis, utilisé et protégé sur les appareils Windows 10.

Cet article suppose que vous comprenez déjà les États des appareils différents disponibles dans Azure AD et comment single sign-on fonctionne dans Windows 10. Pour plus d’informations sur les appareils dans Azure AD, consultez l’article [What ' s gestion des appareils dans Azure Active Directory ?](overview.md)

## <a name="key-terminology-and-components"></a>Composants et la terminologie clé

Les composants Windows suivants jouent un rôle clé dans la demande et à l’aide d’un PRT :

* **Fournisseur d’authentification de service cloud** (CloudAP) : CloudAP est le fournisseur d’authentification modernes pour Windows vous connecter, qui vérifie les utilisateurs ouvrant une session sur un appareil Windows 10. CloudAP fournit une infrastructure de plug-in de cette identité fournisseurs peuvent générer sur pour activer l’authentification à Windows à l’aide des informations d’identification du fournisseur d’identité.
* **Responsable de compte Web** (WAM) : WAM est la valeur par défaut token broker sur les appareils Windows 10. WAM fournit également une infrastructure de plug-in cette identité fournisseurs peuvent générer et activer l’authentification unique à leurs applications de partie de confiance sur ce fournisseur d’identité.
* **Le plug-in de Azure AD CloudAP**: Un Azure AD spécifique plug-in basé sur le framework CloudAP, qui vérifie les informations d’identification de l’utilisateur avec Azure AD pendant la connexion Windows.
* **Le plug-in de Azure AD WAM**: Un Azure AD spécifique plug-in basé sur le framework WAM, qui permet l’authentification unique aux applications qui s’appuient sur Azure AD pour l’authentification.
* **Dsreg**: Un composant spécifique d’Azure AD sur Windows 10, qui gère le processus d’inscription de périphérique pour tous les États de l’appareil.
* **Module de plateforme sécurisée** (TPM) : Un module de plateforme sécurisée est un composant matériel intégré à un appareil, qui fournit des fonctions de sécurité basée sur le matériel pour les clés secrètes d’utilisateur et appareil. Vous trouverez plus de détails dans l’article [Trusted Platform Module Technology Overview](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Que contient le PRT ?

Un PRT contient les revendications généralement contenues dans n’importe quel jeton d’actualisation Azure AD. En outre, il existe certaines revendications propres aux appareils incluses dans le PRT. Les voici :

* **ID d’appareil**: Un PRT est émis pour un utilisateur sur un appareil spécifique. La revendication d’ID de périphérique `deviceID` détermine l’appareil le PRT a été remise à l’utilisateur. Cette revendication est émise ultérieurement pour les jetons obtenus via la PRT. La revendication d’ID de périphérique est utilisée pour déterminer l’autorisation d’accès conditionnel basé sur l’état de l’appareil ou de conformité.
* **Clé de session**: La clé de session est une clé symétrique chiffrée, généré par le service d’authentification Azure AD, émis en tant que partie de la PRT. La clé de session agit comme la preuve de possession lorsqu’un PRT sert à obtenir des jetons pour d’autres applications.

### <a name="can-i-see-whats-in-a-prt"></a>Puis-je voir ce qui est dans un PRT ?

Un PRT est un objet blob opaque, envoyé à partir d’Azure AD dont le contenu n’est pas connu de tous les composants client. Vous ne pouvez pas voir le contenu une PRT.

## <a name="how-is-a-prt-issued"></a>Comment est émise une PRT ?

L’inscription est un prérequis pour l’authentification basées sur les appareils dans Azure AD. Un PRT est émis aux utilisateurs uniquement sur les appareils inscrits. Pour plus d’informations détaillées sur l’inscription, consultez l’article [Windows Hello entreprise et inscription des appareils](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Pendant l’inscription de périphérique, le composant dsreg génère deux ensembles de paires de clés de chiffrement :

* Clé d’appareil (dkpub/dkpriv)
* Clé de transport (tkpub/tkpriv)

Les clés privées sont liés à TPM l’appareil si l’appareil a un TPM valid et qu’il fonctionne, tandis que les clés publiques sont envoyés à Azure AD pendant le processus d’inscription de périphérique. Ces clés sont utilisées pour valider l’état de l’appareil lors de demandes PRT.

Le PRT est émis lors de l’authentification utilisateur sur un appareil Windows 10 dans deux scénarios :

* **Joint à Azure AD** ou **joints Azure AD hybrides**: Un PRT est émise au cours de l’ouverture de session Windows lorsqu’un utilisateur se connecte avec leurs informations d’identification de l’organisation. Un PRT est envoyée avec toutes les informations d’identification de Windows 10 prises en charge, par exemple, mot de passe et Windows Hello entreprise. Dans ce scénario, les plug-in Azure AD CloudAP est l’autorité principale pour le PRT.
* **Appareils inscrits à Azure AD**: Un PRT est émis lorsqu’un utilisateur ajoute un compte professionnel secondaire sur leur appareil Windows 10. Les utilisateurs peuvent ajouter un compte à Windows 10 de deux manières différentes :  
   * Ajout d’un compte via le **utiliser ce compte partout sur cet appareil** invite après vous être connecté à une application (par exemple, Outlook)
   * Ajout d’un compte à partir de **paramètres** > **comptes** > **accès scolaire ou Professionnel** > **Connect**

Dans ces scénarios, le plug-in Azure AD WAM est l’autorité principale pour le PRT étant donné que l’ouverture de session Windows ne se produit pas avec ce compte Azure AD.

## <a name="what-is-the-lifetime-of-a-prt"></a>Qu’est la durée de vie d’un PRT ?

Une fois émise, une PRT est valide pendant 14 jours et est renouvelé en continu tant que l’utilisateur utilise activement l’appareil.  

## <a name="how-is-a-prt-used"></a>Comment un PRT est-il utilisé ?

Un PRT est utilisé par les deux composants clés dans Windows :

* **Le plug-in de Azure AD CloudAP**: Pendant la connexion Windows, le plug-in Azure AD CloudAP demande un PRT à partir d’Azure AD en utilisant les informations d’identification fournies par l’utilisateur. Il met également en cache le PRT pour activer la mise en cache de connexion lorsque l’utilisateur n’a pas accès à une connexion internet.
* **Le plug-in de Azure AD WAM**: Lorsque les utilisateurs tentent d’accéder aux applications, le plug-in Azure AD WAM utilise le PRT pour activer l’authentification unique sur Windows 10. Le plug-in de Azure AD WAM utilise le PRT pour demander des jetons d’actualisation et accès pour les applications qui s’appuient sur WAM pour les demandes de jeton. Il permet également l’authentification unique sur les navigateurs en injectant le PRT dans les demandes du navigateur. Navigateur de l’authentification unique dans Windows 10 est pris en charge Microsoft Edge (en mode natif) et Chrome (via l’extension de Windows 10 comptes ou Office Online).

## <a name="how-is-a-prt-renewed"></a>Comment un PRT est renouvelé ?

Un PRT est renouvelé dans deux méthodes différentes :

* **Plug-in de Azure AD CloudAP toutes les 4 heures**: Le plug-in CloudAP renouvelle le PRT toutes les 4 heures pendant la connexion Windows. Si l’utilisateur n’a pas de connexion internet pendant ce temps, le plug-in CloudAP renouvellera le PRT une fois que l’appareil est connecté à internet.
* **Azure AD WAM plug-in lors de demandes de jeton d’application**: Le plug-in WAM permet l’authentification unique sur les appareils Windows 10 en activant des demandes de jeton sans assistance pour les applications. Le plug-in WAM peut renouveler le PRT pendant ces demandes de jeton de deux manières différentes :
   * Une application demande WAM pour un jeton d’accès en mode silencieux, mais aucun jeton d’actualisation n’est disponible pour cette application. Dans ce cas, WAM utilise le PRT pour demander un jeton pour l’application et récupère un nouveau PRT dans la réponse.
   * Une application demande WAM pour un jeton d’accès, mais le PRT n’est pas valide ou Azure AD nécessite une autorisation supplémentaire (par exemple, l’authentification multifacteur Azure). Dans ce scénario, WAM lance une connexion interactive obliger l’utilisateur à s’authentifier de nouveau ou effectuer une vérification supplémentaire et un nouveau PRT est émise sur authentification réussie.

### <a name="key-considerations"></a>Considérations relatives aux clés

* Un PRT est uniquement émis et renouvelé lors de l’authentification d’application native. Un PRT n’est pas renouvelé ou émis pendant une session de navigateur.
* Dans Azure AD joint et des appareils hybrides joints à Azure AD, le plug-in CloudAP est l’autorité principale pour un PRT. Si un PRT est renouvelé pendant une demande de jeton WAM, le PRT est envoyé au plug-in de CloudAP, qui vérifie la validité de la PRT avec Azure AD avant de l’accepter.

## <a name="how-is-the-prt-protected"></a>Comment le PRT est-il protégé ?

Un PRT est protégé par la liaison à l’appareil de l’utilisateur est connecté à. Azure AD et Windows 10 réactivez la protection de PRT via les méthodes suivantes :

* **Lors de sa première connexion**: Lors de sa première connexion, un PRT est émise par la signature des demandes à l’aide de la clé d’appareil générée par chiffrement pendant l’inscription de périphérique. Sur un appareil avec un module de plateforme sécurisée valide et qu’il fonctionne, la clé d’appareil est sécurisée par le TPM empêchant tout accès malveillant. Un PRT n’est pas émis si la signature de clé d’appareil correspondant ne peut pas être validée.
* **Au cours des demandes de jeton et le renouvellement**: Lorsqu’un PRT est émise, Azure AD émet également une clé de session chiffrée à l’appareil. Elle est chiffrée avec la clé publique de transport (tkpub) générés et envoyés à Azure AD dans le cadre de l’inscription. Cette clé de session peut uniquement être déchiffrée par la clé privée de transport (tkpriv) sécurisée par le module de plateforme sécurisée. La clé de session est la clé de preuve de Possession (POP) pour toutes les demandes envoyées à Azure AD.  La clé de session est également protégée par le module de plateforme sécurisée et aucun autre composant du système d’exploitation ne peut y accéder. Demandes de jetons ou des demandes de renouvellement PRT en toute sécurité signés par cette clé de session via le module de plateforme sécurisée et par conséquent, le ne peut pas être falsifiés. Azure AD invalidera toutes les demandes provenant de l’appareil qui ne sont pas signés par la clé de session correspondante.

Par la sécurisation de ces clés avec le TPM, les personnes malveillantes ne peut pas dérober les clés ni de relire le PRT ailleurs que le module de plateforme sécurisée n’est pas accessible même si une personne malveillante a possession physique de l’appareil.  Par conséquent, à l’aide d’un module de plateforme sécurisée considérablement améliore la sécurité de joint à Azure AD, hybrides et joints, et contre le vol d’informations d’identification, les appareils inscrits à Azure AD. Pour les performances et la fiabilité, TPM 2.0 est la version recommandée pour tous les scénarios de l’inscription d’appareil Azure AD sur Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Comment les jetons d’application et les cookies du navigateur protégés ?

**Jetons d’application**: Lorsqu’une application demande un jeton via WAM, Azure AD émet un jeton d’actualisation et un jeton d’accès. Toutefois, WAM retourne le jeton d’accès à l’application uniquement et sécurise le jeton d’actualisation dans son cache en la chiffrant avec la clé l’utilisateur data protection application programmation interface (DPAPI). WAM utilise en toute sécurité le jeton d’actualisation en signant les demandes avec la clé de session pour émettre des jetons d’accès supplémentaire. La clé DPAPI est sécurisée par une clé symétrique en fonction de Azure AD dans Azure AD elle-même. Quand l’appareil doit déchiffrer le profil utilisateur avec la clé DPAPI, Azure AD fournit la clé DPAPI chiffrée par la clé de session, le plug-in CloudAP demande TPM pour déchiffrer. Cette fonctionnalité garantit la cohérence dans la sécurisation des jetons d’actualisation et évite d’implémenter leurs propres mécanismes de protection des applications.  

**Les cookies du navigateur**: Dans Windows 10, Azure AD prend en charge l’authentification unique dans Internet Explorer et Microsoft Edge de navigateur en mode natif ou Google Chrome via l’extension de comptes Windows 10. La sécurité est conçue non seulement pour protéger les cookies, mais également les points de terminaison à laquelle les cookies sont envoyés. Les cookies du navigateur sont protégés de la même façon une PRT est, en utilisant la clé de session pour vous connecter et de protéger les cookies.

Lorsqu’un utilisateur lance une interaction du navigateur, le navigateur (ou l’extension) appelle un hôte de client natif COM. L’hôte client natif garantit que la page provient d’un des domaines autorisés. Le navigateur pourrait envoyer de qu'autres paramètres pour le client natif hébergent, y compris une valeur à usage unique, toutefois l’hôte client natif garantit la validation du nom d’hôte. L’hôte de native client demande un cookie de PRT CloudAP plug-in, qui crée et signe avec la clé de session protégé par le module de plateforme sécurisée. Comme le cookie de PRT est signé par la clé de session, il ne peut pas être falsifié. Ce cookie de PRT est inclus dans l’en-tête de demande pour Azure AD valider l’appareil que d’origine. Si vous utilisez le navigateur Chrome, uniquement l’extension explicitement définie dans le manifeste de l’hôte client natif empêche extensions arbitraires de ces demandes peut appeler. Une fois Azure AD valide le cookie PRT, il émet un cookie de session dans le navigateur. Ce cookie de session contient également la même clé de session émise avec un PRT. Lors des demandes suivantes, la clé de session est validée efficacement le cookie de liaison à l’appareil et empêche les relectures à partir d’un autre emplacement.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quand un PRT n’obtient pas une revendication MFA ?

Un PRT peut obtenir une revendication de l’authentification multifacteur (MFA) dans des scénarios spécifiques. Lorsqu’un PRT basée sur l’authentification Multifacteur est utilisé pour demander des jetons pour les applications, la revendication MFA est transférée vers ces jetons d’application. Cette fonctionnalité offre une expérience transparente aux utilisateurs en empêchant l’authentification MFA pour chaque application qui en a besoin. Un PRT permettre obtenir une revendication MFA comme suit :

* **Connectez-vous à Windows Hello for Business**: Windows Hello entreprise remplace les mots de passe et utilise des clés de chiffrement pour fournir une authentification forte à deux facteurs. Windows Hello entreprise est spécifique à un utilisateur sur un appareil, et lui-même exige l’authentification Multifacteur à approvisionner. Quand un utilisateur se connecte avec Windows Hello entreprise, PRT l’utilisateur obtient une revendication d’authentification Multifacteur. Ce scénario s’applique également aux utilisateurs se connectant avec cartes à puce si l’authentification par carte à puce génère une revendication MFA d’ADFS.
* **Authentification Multifacteur pendant la connexion interactive WAM**: Pendant une demande de jeton via WAM, si un utilisateur est requis pour effectuer l’authentification Multifacteur pour accéder à l’application, PRT renouvelé pendant cette interaction est empreinte avec une revendication d’authentification Multifacteur.
* **Authentification Multifacteur lors de l’inscription de périphérique**: Si un administrateur a configuré les paramètres de périphérique dans Azure AD pour [exiger l’authentification Multifacteur inscrire des appareils](device-management-azure-portal.md#configure-device-settings), l’utilisateur a besoin pour effectuer l’authentification Multifacteur pour terminer l’inscription. Pendant ce processus, PRT émis pour l’utilisateur a la revendication MFA obtenue lors de l’inscription. Cette fonctionnalité s’applique uniquement à l’utilisateur qui a effectué l’opération de jointure, pas à d’autres utilisateurs qui se connectent à sur cet appareil.

Windows 10 gère une liste partitionnée de PRTs pour chaque information d’identification. Par conséquent, il existe un PRT pour chacun de Windows Hello entreprise, mot de passe ou carte à puce. Ce partitionnement garantit que les revendications MFA sont isolées selon les informations d’identification utilisées et ne pas mélangés au cours des demandes de jeton.

## <a name="how-is-a-prt-invalidated"></a>Comment un PRT est invalidée ?

Un PRT est invalidé dans les scénarios suivants :

* **Utilisateur non valide**: Si un utilisateur est supprimé ou désactivé dans Azure AD, leur PRT est invalidé et ne peut pas être utilisé pour obtenir des jetons pour les applications. Si un utilisateur supprimé ou désactivé déjà connecté à un appareil avant, connectez-vous mis en cache serait les consigner dans, jusqu'à ce que CloudAP tient compte de leur état non valide. Une fois que CloudAP détermine que l’utilisateur n’est pas valide, il bloque les ouvertures de session suivantes. Un utilisateur non valide est bloqué automatiquement à partir de la connexion à de nouveaux périphériques qui n’ont pas leurs informations d’identification mises en cache.
* **APPAREIL non valide**: Si un appareil est supprimé ou désactivé dans Azure AD, le PRT obtenu sur l’appareil est non valide et ne peut pas être utilisé pour obtenir des jetons pour d’autres applications. Si un utilisateur est déjà connecté à un appareil non valide, ils peuvent continuer à le faire. Mais tous les jetons sur l’appareil sont invalidées et l’utilisateur ne dispose pas de l’authentification unique à toutes les ressources à partir de cet appareil.
* **Modification de mot de passe**: Une fois un utilisateur modifie son mot de passe, le PRT obtenu avec le mot de passe précédent est invalidé par Azure AD. Résultats de l’utilisateur de l’obtention d’un nouveau PRT de modification de mot de passe. Cette invalidation peut se produire de deux manières différentes :
   * Si l’utilisateur se connecte à Windows avec leur nouveau mot de passe, CloudAP ignore l’ancien PRT et demande d’Azure AD d’émettre un nouveau PRT avec son nouveau mot de passe. Si l’utilisateur ne dispose pas d’une connexion internet, le nouveau mot de passe ne peut pas être validé, Windows peut nécessiter l’utilisateur à entrer leur ancien mot de passe.
   * Si un utilisateur a ouvert une session avec leur ancien mot de passe ou modifié leur mot de passe après vous être connecté à Windows, l’ancien PRT est utilisé pour les demandes de jeton basées sur les WAM. Dans ce scénario, l’utilisateur est invité à s’authentifier de nouveau au cours de la demande de jeton WAM et un nouveau PRT est émise.
* **Problèmes de module de plateforme sécurisée**: Parfois, TPM d’un appareil peut instabilité ou échouent, ce qui conduit à l’inaccessibilité de clés sécurisées par le module de plateforme sécurisée. Dans ce cas, l’appareil est incapable d’obtenir un PRT ou de demande de jetons à l’aide d’un PRT existant comme il ne peut pas prouver la possession des clés de chiffrement. Par conséquent, n’importe quel PRT existant est invalidé par Azure AD. Lorsque Windows 10 détecte une défaillance, il lance un flux de récupération pour réinscrire l’appareil avec de nouvelles clés de chiffrement. Avec une jointure Hybrid Azure Ad, tout comme l’inscription initiale, la récupération se produit en mode silencieux sans entrée utilisateur. Pour les appareils inscrits à Azure AD joint ou Azure AD, la récupération doit être effectuée par un utilisateur disposant des privilèges d’administrateur sur l’appareil. Dans ce scénario, le flux de récupération est lancé par une invite de Windows qui guide l’utilisateur afin de récupérer correctement l’appareil.

## <a name="detailed-flows"></a>Flux détaillés

Les diagrammes suivants illustrent les détails sous-jacents dans l’achat, renouvellement et à l’aide d’un PRT pour demander un jeton d’accès pour une application. En outre, ces étapes décrivent également comment les mécanismes de sécurité mentionnés ci-dessus sont appliqués pendant ces interactions.

### <a name="prt-issuance-during-first-sign-in"></a>Émission PRT lors de sa première connexion

![Émission PRT lors de sa première connexion flux détaillé](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Dans Azure AD appareils joints à un, cet échange se produit de façon synchrone pour émettre un PRT avant que l’utilisateur peut ouvrir une session Windows. Dans un environnement hybride appareils joints Azure AD, sur site Active Directory est l’autorité principale. Par conséquent, l’utilisateur est en attente uniquement jusqu'à ce qu’ils puissent acquérir un ticket TGT pour vous connecter, alors que l’émission PRT se fait de façon asynchrone. Ce scénario ne s’applique pas aux appareils inscrits à Azure AD, comme l’ouverture de session n’utilise pas les informations d’identification Azure AD.

| Étape | Description |
| :---: | --- |
| A | Utilisateur entre son mot de passe dans l’interface utilisateur de connexion. LogonUI transmet les informations d’identification dans une mémoire tampon auth à LSA, ce qui réduit le passe en interne à CloudAP. CloudAP transfère cette requête pour le plug-in CloudAP. |
| b | Plug-in CloudAP lance une demande de découverte de domaine pour identifier le fournisseur d’identité pour l’utilisateur. Si le client de l’utilisateur possède un programme d’installation du fournisseur de fédération, Azure AD renvoie échange de métadonnées du fournisseur de fédération de point de terminaison de point de terminaison (MEX). Dans le cas contraire, Azure AD renvoie que l’utilisateur est géré indiquant que l’utilisateur peut s’authentifier auprès d’Azure AD. |
| C | Si l’utilisateur est géré, CloudAP obtiendra la valeur à usage unique à partir d’Azure AD. Si l’utilisateur est fédéré, plug-in CloudAP demande un jeton SAML à partir du fournisseur de fédération avec les informations d’identification de l’utilisateur. Une fois qu’il reçoit le jeton SAML, il demande une valeur à usage unique à partir d’Azure AD. |
| D | Plug-in CloudAP construit la demande d’authentification avec les informations d’identification de l’utilisateur, valeur à usage unique et une étendue de broker, signe la demande avec la clé d’appareil (dkpriv) et l’envoie à Azure AD. Dans un environnement fédéré, plug-in CloudAP utilise le jeton SAML renvoyé par le fournisseur de fédération au lieu de l’utilisateur « informations d’identification. |
| E | Azure AD valide les informations d’identification utilisateur, la valeur à usage unique, et la signature de l’appareil, vérifie que l’appareil n’est valide dans le client et émet le PRT chiffré. En même temps que le PRT, Azure AD émet également une clé symétrique, appelée la clé de Session chiffrée par Azure AD à l’aide de la clé de Transport (tkpub). En outre, la clé de Session est également incorporée dans le PRT. Cette clé de Session agit comme la clé de preuve de possession (PoP) pour les demandes suivantes avec le PRT. |
| F | Plug-in CloudAP transmet la clé de Session et PRT chiffrée à CloudAP. CloudAP demander le TPM pour déchiffrer la clé de Session à l’aide de la clé de Transport (tkpriv) et chiffrer de nouveau à l’aide de la clé du TPM. CloudAP stocke la clé de Session chiffrée dans son cache, ainsi que le PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renouvellement PRT dans des ouvertures de session

![Renouvellement PRT dans des ouvertures de session](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Étape | Description |
| :---: | --- |
| A | Utilisateur entre son mot de passe dans l’interface utilisateur de connexion. LogonUI transmet les informations d’identification dans une mémoire tampon auth à LSA, ce qui réduit le passe en interne à CloudAP. CloudAP transfère cette requête pour le plug-in CloudAP. |
| b | Si l’utilisateur a précédemment connecté à l’utilisateur, lance Windows mis en cache connectez-vous et valide les informations d’identification pour vous connecter l’utilisateur. Toutes les 4 heures, le plug-in CloudAP lance le renouvellement PRT façon asynchrone. |
| C | Plug-in CloudAP lance une demande de découverte de domaine pour identifier le fournisseur d’identité pour l’utilisateur. Si le client de l’utilisateur possède un programme d’installation du fournisseur de fédération, Azure AD renvoie échange de métadonnées du fournisseur de fédération de point de terminaison de point de terminaison (MEX). Dans le cas contraire, Azure AD renvoie que l’utilisateur est géré indiquant que l’utilisateur peut s’authentifier auprès d’Azure AD. |
| D | Si l’utilisateur est fédéré, plug-in CloudAP demande un jeton SAML à partir du fournisseur de fédération avec les informations d’identification de l’utilisateur. Une fois qu’il reçoit le jeton SAML, il demande une valeur à usage unique à partir d’Azure AD. Si l’utilisateur est géré, CloudAP obtiendra directement la valeur à usage unique à partir d’Azure AD. |
| E | Plug-in CloudAP construit la demande d’authentification avec les informations d’identification de l’utilisateur, valeur à usage unique et le PRT existant, signe la demande avec la clé de Session et l’envoie à Azure AD. Dans un environnement fédéré, plug-in CloudAP utilise le jeton SAML renvoyé par le fournisseur de fédération au lieu de l’utilisateur « informations d’identification. |
| F | Azure AD valide la signature de clé de Session en les comparant à la clé de Session incorporée dans le PRT, valide la valeur à usage unique et vérifie que l’appareil n’est valide dans le client et émet un nouveau PRT. Comme indiqué précédemment, le PRT est à nouveau accompagné de la clé de Session chiffrée par clé de Transport (tkpub). |
| G | Plug-in CloudAP transmet la clé de Session et PRT chiffrée à CloudAP. CloudAP demande le TPM pour déchiffrer la clé de Session à l’aide de la clé de Transport (tkpriv) et chiffrer de nouveau à l’aide de la clé du TPM. CloudAP stocke la clé de Session chiffrée dans son cache, ainsi que le PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilisation PRT lors de demandes de jeton d’application

![Utilisation PRT lors de demandes de jeton d’application](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Étape | Description |
| :---: | --- |
| A | Une application (par exemple, Outlook, OneNote etc.) initie une demande de jeton à WAM. WAM, demande à son tour, le plug-in Azure AD WAM pour traiter la demande de jeton. |
| b | Si un jeton d’actualisation de l’application est déjà disponible, plug-in Azure AD WAM l’utilise pour demander un jeton d’accès. Pour fournir la preuve de la liaison de l’appareil, plug-in WAM signe la demande avec la clé de Session. Azure AD valide la clé de Session et émet un jeton d’accès et un nouveau jeton d’actualisation de l’application, chiffrée par la clé de Session. Plug-in WAM demande Cloud AP plug-in pour déchiffrer les jetons, qui, à son tour, le module de plateforme sécurisée pour déchiffrer à l’aide de la clé de Session, ce qui entraîne un plug-in WAM bien à la fois les jetons. Ensuite, plug-in WAM fournit uniquement le jeton d’accès à l’application, tandis que le chiffre à nouveau le jeton d’actualisation avec DPAPI et la stocke dans son propre cache  |
| C |  Si un jeton d’actualisation de l’application n’est pas disponible, plug-in Azure AD WAM utilise le PRT pour demander un jeton d’accès. Pour fournir la preuve de possession, plug-in WAM signe la demande contenant le PRT avec la clé de Session. Azure AD valide la signature de clé de Session en les comparant à la clé de Session incorporée dans le PRT, vérifie que l’appareil est valide et qu’il émet un jeton d’accès et un jeton d’actualisation de l’application. en outre, Azure AD peut émettre un nouveau PRT (basé sur le cycle d’actualisation), tous les chiffrée par la clé de Session. |
| D | Plug-in WAM demande Cloud AP plug-in pour déchiffrer les jetons, qui, à son tour, le module de plateforme sécurisée pour déchiffrer à l’aide de la clé de Session, ce qui entraîne un plug-in WAM bien à la fois les jetons. Ensuite, les plug-in WAM fournit uniquement le jeton d’accès à l’application, tandis qu’il chiffre à nouveau le jeton d’actualisation avec DPAPI et le stocke dans son propre cache. Plug-in WAM utilisera le jeton d’actualisation à l’avenir pour cette application. Plug-in WAM restitue également la nouvelle PRT de plug-in Cloud AP, qui valide le PRT avec Azure AD avant de mettre à jour dans son propre cache. Le plug-in de cloud AP utilisera le nouveau PRT à l’avenir. |
| E | WAM fournit le jeton d’accès qui vient d’être émis à WAM, qui à son tour, lui fournit à l’application appelante|

### <a name="browser-sso-using-prt"></a>Navigateur de l’authentification unique à l’aide de PRT

![Navigateur de l’authentification unique à l’aide de PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Étape | Description |
| :---: | --- |
| A | Utilisateur ouvre une session Windows avec leurs informations d’identification pour obtenir un PRT. Une fois que l’utilisateur ouvre le navigateur, navigateur (ou extension) charge les URL à partir du Registre. |
| b | Lorsqu’un utilisateur ouvre une URL de connexion Azure AD, le navigateur ou l’extension valide l’URL avec celles obtenues à partir du Registre. Si elles correspondent, le navigateur appelle l’hôte client native pour obtenir un jeton. |
| C | L’hôte client natif valide le fait que les URL appartiennent aux fournisseurs d’identité Microsoft (compte Microsoft ou Azure AD), extrait une valeur à usage unique envoyé à partir de l’URL et effectue un appel au plug-in CloudAP pour obtenir un cookie PRT. |
| D | Le plug-in CloudAP sera créer le cookie PRT, connectez-vous à la clé de session liée aux module de plateforme sécurisée et l’envoyer à l’hôte client natif. Comme le cookie est signé par la clé de session, il ne peut pas être falsifié. |
| E | L’hôte de native client retourne ce cookie PRT au navigateur, qui inclut comme faisant partie de l’en-tête de requête appelé x-ms-RefreshTokenCredential et demande les jetons d’Azure AD. |
| F | Azure AD valide la signature de clé de Session sur le cookie PRT, valide la valeur à usage unique, vérifie que l’appareil n’est valide dans le client et émet un jeton d’ID de la page web et un cookie de session chiffrée pour le navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes liés à PRT, consultez l’article [dépannage hybrides Azure Active Directory des appareils Windows 10 et Windows Server 2016 joints à](troubleshoot-hybrid-join-windows-current.md).
