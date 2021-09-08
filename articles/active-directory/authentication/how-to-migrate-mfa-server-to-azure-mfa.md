---
title: Migrer de Serveur Azure MFA vers l’authentification multifacteur Azure - Azure Active Directory
description: Guide pas à pas pour migrer de Serveur Azure MFA en local vers l’authentification multifacteur Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4415e7ce86d2beb9e2903f23d0b6fa9ac7d3ec04
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597832"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>Migrer de Serveur Azure MFA vers l’authentification multifacteur Azure

L’authentification multifacteur (MFA) est importante dans la mesure où elle permet de sécuriser votre infrastructure et vos ressources contre des acteurs malveillants. Serveur Azure MFA n’est pas disponible pour les nouveaux déploiements et sera déconseillé. Les clients qui utilisent Serveur MFA doivent passer à l’authentification multifacteur Azure Active Directory (Azure AD) basée sur le cloud. Dans cette documentation, nous supposons que vous disposez d’un environnement hybride dans lequel :

- Vous utilisez Serveur MFA pour l’authentification multifacteur.
- Vous utilisez la fédération sur Azure AD avec les services de fédération Active Directory (AD FS) ou un autre produit de fédération de fournisseur d’identité.
  - Bien que cet article se limite à AD FS, des étapes similaires s’appliquent à d’autres fournisseurs d’identité.
- Votre instance de Serveur MFA est intégrée à AD FS. 
- Vous pouvez avoir des applications qui utilisent AD FS pour l’authentification.

Il existe plusieurs états finaux possibles pour votre migration, en fonction de votre objectif.

| <br> | Objectif : désactiver Serveur MFA UNIQUEMENT | Objectif : désactiver Serveur MFA et passer à Azure AD Authentication | Objectif : désactiver Serveur MFA et AD FS |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|Fournisseur MFA | Remplacez le fournisseur MFA de Serveur MFA par Azure AD MFA. | Remplacez le fournisseur MFA de Serveur MFA par Azure AD MFA. |   Remplacez le fournisseur MFA de Serveur MFA par Azure AD MFA. |
|Authentification utilisateur  |Continuez à utiliser la fédération pour l’authentification Azure AD. | Passez à Azure AD avec la synchronisation de hachage du mot de passe (de préférence) ou l'authentification directe **et** l'authentification unique transparente.| Passez à Azure AD avec la synchronisation de hachage du mot de passe (de préférence) ou l'authentification directe **et** l'authentification unique transparente. |
|Authentification de l’application | Continuez à utiliser l’authentification AD FS pour vos applications. | Continuez à utiliser l’authentification AD FS pour vos applications. | Déplacez les applications vers Azure AD avant de migrer vers Azure MFA. |

Si vous le pouvez, déplacez votre authentification MFA et votre authentification utilisateur vers Azure. Pour obtenir des instructions pas à pas, consultez [Passage à Azure AD MFA et à l’authentification utilisateur Azure AD](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md). 

Si vous ne pouvez pas déplacer votre authentification utilisateur, consultez les instructions pas à pas pour le [Passage à Azure AD MFA avec fédération](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md).

## <a name="prerequisites"></a>Configuration requise

- Environnement AD FS (requis si vous ne migrez pas toutes vos applications vers Azure AD avant de migrer MFA)
  - Effectuez une mise à niveau vers AD FS pour Windows Server 2019, Farm Behavior Level (FBL) 4. Cela vous permet de sélectionner le fournisseur d’authentification en fonction de l’appartenance à un groupe pour une transition utilisateur plus transparente. Bien qu’il soit possible de migrer vers AD FS pour Windows Server 2016 FBL 3, cela n’est pas aussi transparent pour les utilisateurs. Pendant la migration, les utilisateurs sont invités à sélectionner un fournisseur d’authentification (Serveur MFA ou Azure MFA) jusqu’à ce que la migration soit terminée. 
- Autorisations
  - Rôle Administrateur Enterprise dans Active Directory pour configurer la batterie AD FS pour Azure AD MFA
  - Rôle Administrateur général dans Azure AD pour effectuer la configuration d’Azure AD à l’aide d’Azure AD PowerShell


## <a name="considerations-for-all-migration-paths"></a>Considérations pour tous les chemins de migration

La migration de Serveur MFA vers Azure AD MFA implique davantage que le simple déplacement des numéros de téléphone MFA enregistrés. Le serveur MFA de Microsoft peut être intégré à de nombreux systèmes et vous devez évaluer l’utilisation de MFA Server par ces systèmes pour comprendre les meilleures méthodes d’intégration à Azure AD MFA. 

### <a name="migrating-mfa-user-information"></a>Migration des informations utilisateur MFA

Lorsque l’on réfléchit au déplacement d’utilisateurs par lots, il est courant d’envisager leur déplacement par région, par département ou par rôle, comme les administrateurs. Quelle que soit la stratégie choisie, veillez à déplacer les utilisateurs de façon itérative, en commençant par les groupes test et pilote, et prévoyez un plan de restauration. 

Vous pouvez migrer les jetons matériels et les numéros de téléphone MFA enregistrés des utilisateurs, mais vous ne pouvez pas migrer les inscriptions d’appareils, comme leurs paramètres d’application Microsoft Authenticator. Les utilisateurs devront s’inscrire, ajouter un nouveau compte sur l’application Authenticator et supprimer l’ancien compte. 

Pour que les utilisateurs puissent différencier le compte récemment ajouté et l’ancien compte associé à Serveur MFA, assurez-vous que le nom de compte pour l’application mobile sur Serveur MFA permet de distinguer les deux comptes. Par exemple, le nom de compte qui apparaît sous Application mobile sur Serveur MFA a été renommé Serveur MFA local. Le nom de compte sur l’application Authenticator sera modifié avec la prochaine notification push à l’utilisateur. 

La migration des numéros de téléphone peut également entraîner la migration de numéros périmés et inciter les utilisateurs à conserver une authentification MFA par téléphone au lieu de configurer des méthodes plus sécurisées comme Microsoft Authenticator en mode sans mot de passe. Nous vous recommandons donc d’inviter tous les utilisateurs à effectuer l’inscription [combinée des informations de sécurité](howto-registration-mfa-sspr-combined.md), quel que soit le chemin de migration que vous choisissez.


#### <a name="migrating-hardware-security-keys"></a>Migration des clés de sécurité matérielles

Azure AD prend en charge les jetons matériels OATH. Pour migrer les jetons de Serveur MFA vers Azure AD MFA, les [jetons doivent être chargés dans Azure AD au moyen d’un fichier CSV](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview), communément appelé « fichier d’origine ». Le fichier d’origine contient les clés secrètes et les numéros de série des jetons, ainsi que d’autres informations nécessaires pour charger les jetons dans Azure AD. 

Si vous ne disposez plus du fichier d’origine avec les clés secrètes, il n’est pas possible d’exporter les clés secrètes à partir de Serveur MFA. Si vous n’avez plus accès aux clés secrètes, contactez le fournisseur de votre matériel pour obtenir de l’aide.

Le kit de développement logiciel (SDK) du service web de Serveur MFA peut être utilisé pour exporter le numéro de série des jetons OATH attribués à un utilisateur donné. À l’aide de ces informations et du fichier d’origine, les administrateurs informatiques peuvent importer les jetons dans Azure AD et attribuer le jeton OATH à l’utilisateur spécifié en fonction du numéro de série. L’utilisateur devra également être contacté au moment de l’importation pour fournir les informations de mot de passe à usage unique de l’appareil afin de finaliser l’inscription. Reportez-vous à la rubrique GetUserInfo > userSettings > OathTokenSerialNumber dans le fichier d’aide de Serveur Multi-Factor Authentication sur votre instance de Serveur MFA. 


### <a name="additional-migrations"></a>Migrations supplémentaires

La décision d’effectuer une migration de Serveur MFA vers Azure MFA ouvre la voie à d’autres migrations. L’exécution d’autres migrations dépend de nombreux facteurs, notamment :

- Votre volonté d’utiliser l’authentification Azure AD pour les utilisateurs
- Votre volonté de déplacer vos applications vers Azure AD

Étant donné que Serveur MFA est profondément intégré à l’authentification des utilisateurs et des applications, vous pouvez envisager de déplacer ces deux fonctions vers Azure dans le cadre de votre migration MFA, puis de désactiver AD FS. 

Nos recommandations : 

- Utilisez Azure AD pour l’authentification, car il garantit une sécurité et une gouvernance plus robustes
- Déplacez les applications vers Azure AD si possible

Pour sélectionner la méthode d’authentification des utilisateurs la mieux adaptée à votre organisation, consultez [Choisir la méthode d’authentification appropriée pour votre solution d’identité hybride Azure AD](../hybrid/choose-ad-authn.md). Nous vous recommandons d’utiliser la synchronisation de hachage du mot de passe (PHS).

### <a name="passwordless-authentication"></a>Authentification sans mot de passe

Dans le cadre de l’inscription des utilisateurs pour utiliser Microsoft Authenticator comme deuxième facteur, nous vous recommandons d’activer la connexion par téléphone sans mot de passe. Pour plus d’informations, y compris pour d’autres méthodes sans mot de passe telles que FIDO et Windows Hello Entreprise, consultez [Planifier un déploiement d’authentification sans mot de passe avec Azure AD](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app).

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Réinitialisation de mot de passe en libre-service Microsoft Identity Manager 

La réinitialisation de mot de passe en libre-service (SSPR) Microsoft Identity Manager (MIM) peut utiliser Serveur MFA pour invoquer des codes à usage unique par SMS dans le cadre du flux de réinitialisation du mot de passe. MIM ne peut pas être configuré pour utiliser Azure MFA. Nous vous recommandons d’évaluer la migration de votre service SSPR vers Azure AD SSPR.

Vous pouvez profiter de la possibilité pour les utilisateurs de s’inscrire à Azure MFA pour utiliser l’expérience d’inscription combinée pour qu’ils s’inscrivent à Azure AD SSPR.


### <a name="radius-clients-and-azure-ad-mfa"></a>Clients RADIUS et Azure AD MFA

Serveur MFA prend en charge RADIUS pour invoquer l’authentification multifacteur pour les applications et appareils réseau qui prennent en charge le protocole. Si vous utilisez RADIUS avec Serveur MFA, nous vous recommandons de déplacer les applications clientes vers des protocoles modernes tels que SAML, OpenID Connect ou OAuth sur Azure AD. Si l’application ne peut pas être mise à jour, vous pouvez déployer NPS (Network Policy Server) avec l’extension Azure MFA. L’extension NPS (Network Policy Server) sert d’adaptateur entre les applications RADIUS et Azure AD MFA pour fournir un deuxième facteur d’authentification. Cela vous permet de déplacer vos clients RADIUS vers Azure MFA et de désactiver votre instance de Serveur MFA.

#### <a name="important-considerations"></a>Considérations importantes

Il existe des limitations lors de l’utilisation de NPS pour les clients RADIUS et nous vous recommandons d’évaluer les clients RADIUS pour déterminer si vous pouvez les mettre à niveau vers des protocoles d’authentification modernes. Vérifiez avec le fournisseur de services les versions de produit prises en charge et leurs fonctionnalités. 

- L’extension NPS n’utilise pas les stratégies d’accès conditionnel Azure AD. Si vous conservez RADIUS et utilisez l’extension NPS, toutes les requêtes d’authentification dirigées vers NPS obligeront l’utilisateur à effectuer une authentification multifacteur.
- Les utilisateurs doivent s’inscrire à Azure AD MFA avant d’utiliser l’extension NPS. Sinon, l’extension ne parvient pas à authentifier l’utilisateur, ce qui peut générer des appels au support technique.
- Lorsque l’extension NPS invoque l’authentification multifacteur, la requête MFA est envoyée à la méthode MFA par défaut de l’utilisateur. 
  - Étant donné que la connexion a lieu sur des applications tierces, il est peu probable que l’utilisateur verra une notification visuelle indiquant que l’authentification multifacteur est requise et qu’une requête a été envoyée sur son appareil.
  - Pendant que l’authentification multifacteur est exigée, les utilisateurs doivent avoir accès à leur méthode d’authentification par défaut pour finaliser l’authentification multifacteur. Ils ne peuvent pas choisir une autre méthode. Leur méthode d’authentification par défaut sera utilisée même si elle a été désactivée dans les méthodes d’authentification du locataire et les stratégies MFA.
  - Les utilisateurs peuvent modifier leur méthode MFA par défaut dans la page Informations de sécurité (aka.ms/mysecurityinfo).
- Les méthodes MFA disponibles pour les clients RADIUS sont contrôlées par les systèmes clients qui envoient les requêtes d’accès RADIUS.
  - Les méthodes MFA qui nécessitent une entrée utilisateur après la saisie d’un mot de passe ne peuvent être utilisées qu’avec les systèmes qui prennent en charge les réponses aux demandes d’accès avec RADIUS. Les méthodes d’entrée peuvent inclure les mots de passe à usage unique, les jetons OATH matériels ou l’application Microsoft Authenticator.
  - Certains systèmes peuvent limiter les méthodes d’authentification multifacteur disponibles aux appels téléphoniques et notifications push de Microsoft Authenticator.


>[!NOTE]
>Les méthodes d’authentification disponibles dépendent de l’algorithme de chiffrement de mot de passe utilisé entre le client RADIUS et le système NPS, ainsi que des méthodes d’entrée que le client peut utiliser. Pour plus d’informations, consultez [Déterminer les méthodes d’authentification que vos utilisateurs peuvent employer](howto-mfa-nps-extension.md). 

Les intégrations courantes de clients RADIUS incluent des applications telles que les [passerelles des services Bureau à distance](howto-mfa-nps-extension-rdg.md) et les [serveurs VPN](howto-mfa-nps-extension-vpn.md). D’autres peuvent inclure :

- Citrix Gateway
  - [Citrix Gateway](https://docs.citrix.com/en-us/citrix-gateway) prend en charge l’intégration des extensions RADIUS et NPS ainsi que l’intégration de SAML.
- Cisco VPN
  - Cisco VPN prend en charge à la fois l’authentification RADIUS et l’[authentification SAML pour SSO](../saas-apps/cisco-anyconnect.md).
  - En passant de l’authentification RADIUS à SAML, vous pouvez intégrer Cisco VPN sans déployer l’extension NPS.
- Tous les VPN
  - Nous vous recommandons, si possible, de fédérer votre VPN en tant qu’application SAML. Cela vous permettra d’utiliser l’accès conditionnel. Pour plus d’informations, consultez une [liste des fournisseurs de VPN intégrés à Azure AD App](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations).


### <a name="resources-for-deploying-nps"></a>Ressources pour le déploiement de NPS

- [Ajout d’une nouvelle infrastructure NPS](/windows-server/networking/technologies/nps/nps-top)
- [Meilleures pratiques pour le déploiement de NPS](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Script de vérification de l’intégrité de l’extension Azure MFA NPS](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [Intégration de l’infrastructure NPS existante à Azure AD MFA](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>Étapes suivantes

- [Migration vers Azure AD MFA avec fédération](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [Migration vers l’authentification utilisateur Azure AD et Azure AD MFA](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


