---
title: Planifier un déploiement Azure Active Directory unique de session
description: Guide pour vous aider à planifier, déployer et gérer l’authentification unique dans votre organisation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499248"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planifier un déploiement de l’authentification unique

Authentification unique (SSO) signifie que l’accès à toutes les applications et ressources a besoin d’un utilisateur en vous connectant une seule fois à l’aide d’un seul compte d’utilisateur. Avec l’authentification unique, les utilisateurs peuvent accéder des applications nécessaires tout sans devoir s’authentifier une deuxième fois.

## <a name="benefits-of-sso"></a>Avantages de l’authentification unique

Authentification unique (SSO) ajoute le confort et sécurité lorsque les utilisateurs se connectent aux applications dans Azure Active Directory (Azure AD). 

De nombreuses organisations s’appuient sur les logiciels en tant qu’une application de service (SaaS), tels que Office 365, Box et Salesforce, pour la productivité des utilisateurs finaux. Historiquement, le personnel informatique devait créer et mettre à jour les comptes d’utilisateur dans chaque application SaaS et les utilisateurs nécessaires à mémoriser un mot de passe pour chacun.

La place de marché Azure a plus de 3 000 applications pré-intégrées connexion de l’authentification unique, ce qui facilite les intégrer dans votre client.

## <a name="licensing"></a>Gestion des licences

- **Gestion des licences Azure AD** -SSO pour les applications SaaS pré-intégrées est gratuit. Toutefois, le nombre d’objets dans votre répertoire et les fonctionnalités que vous souhaitez effectuer le déploiement peut nécessiter des licences supplémentaires. Pour obtenir une liste complète des conditions de licence, consultez [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licences d’application** -vous aurez besoin des licences appropriées pour vos applications SaaS répondre aux besoins de votre entreprise. Travailler avec le propriétaire de l’application pour déterminer si les utilisateurs affectés à l’application ont des licences appropriées pour leurs rôles dans l’application. Si Azure AD gère l’approvisionnement automatique basée sur les rôles, les rôles attribués dans Azure AD doivent être alignées avec le nombre de licences détenues au sein de l’application. Nombre de licences détenues dans l’application incorrect peut entraîner des erreurs pendant l’approvisionnement/la mise à jour d’un utilisateur.

## <a name="plan-your-sso-team"></a>Planifier votre équipe de l’authentification unique

- **Engager les parties prenantes droit** - lors de la technologie de projets en panne, il est généralement dû à des attentes qui ne correspondent pas sur l’impact, les résultats et les responsabilités. Pour éviter ces pièges, [vous assurer que vous êtes recours aux parties prenantes droit](https://aka.ms/deploymentplans) et que les parties prenantes comprennent leur rôle.
- **Planifier les communications** -la Communication est essentielle à la réussite de tout nouveau service. Communiquer de manière proactive pour vos utilisateurs à propos de ce qui va changer leur expérience, quand elle est remplacée et comment obtenir la prise en charge s’ils rencontrent des problèmes. Passez en revue les options de [comment les utilisateurs finaux accèderont leur l’authentification unique des applications compatibles](end-user-experiences.md)et d’élaborer vos communications en fonction de votre sélection. 

## <a name="plan-your-sso-protocol"></a>Planifier votre protocole de l’authentification unique

Une implémentation de l’authentification unique basée sur les protocoles de fédération améliore la sécurité, fiabilité, et utilisateur final rencontre et est plus facile à implémenter. De nombreuses applications sont déjà intégrées dans Azure AD avec [étape par étape guides disponibles](../saas-apps/tutorial-list.md). Vous les trouverez sur notre [place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/). Vous trouverez des informations détaillées sur chaque méthode d’authentification unique dans l’article [l’authentification unique aux applications dans Azure Active Directory](what-is-single-sign-on.md).

Il existe deux façons principales dans lequel vous pouvez autoriser les utilisateurs à authentification unique à vos applications :

- **Avec la seule l’authentification fédérée** Azure AD authentifie l’utilisateur à l’application à l’aide de leur compte Azure AD. Cette méthode est prise en charge pour les applications qui prise en charge de protocoles, tels que SAML 2.0, WS-Federation ou OpenID Connect et qui est le mode le plus riche de l’authentification unique. Nous recommandons à l’aide de l’authentification unique fédérée avec Azure AD quand une application prend en charge, au lieu de l’authentification unique basée sur le mot de passe et ADFS.

- **Avec le mot de passe authentification unique** utilisateurs se connectent à l’application avec un nom d’utilisateur et le mot de passe la première fois, ils y accéder. Après la première connexion, Azure AD fournit le nom d’utilisateur et le mot de passe à l’application. L’authentification unique par mot de passe permet de sécuriser le stockage et la lecture des mots de passe des applications avec une extension de navigateur web ou une application mobile. Cette option s’appuie sur le processus de connexion existant fourni par l’application permet à un administrateur gérer les mots de passe et ne nécessite pas l’utilisateur connaisse le mot de passe.

### <a name="considerations-for-federation-based-sso"></a>Considérations pour l’authentification unique basée sur la fédération

- **À l’aide d’OpenID Connect et OAuth** : si l’application que vous vous connectez prend en charge, utilisez la méthode OIDC et OAuth 2.0 pour activer votre authentification unique à cette application. Cette méthode nécessite moins de configuration et offre une expérience utilisateur plus riche. Pour plus d’informations, consultez [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), et [guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurations de point de terminaison pour l’authentification unique SAML** -si vous utilisez SAML, vos développeurs devez des informations spécifiques avant de configurer l’application. Pour plus d’informations, consultez [configurer des Options de base SAML](configure-single-sign-on-portal.md).
- **Certificat de gestion pour l’authentification unique SAML** : lorsque vous activez l’authentification unique fédérée pour votre application, Azure AD crée un certificat qui est par défaut valide pour trois ans. Vous pouvez personnaliser la date d’expiration pour ce certificat si nécessaire. Assurez-vous que vous disposez des processus en place pour renouveler les certificats avant leur expiration. Pour plus d’informations, consultez [gestion des certificats de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considérations pour l’authentification avec mot de passe

À l’aide d’Azure AD pour l’authentification avec mot de passe nécessite le déploiement d’une extension de navigateur qui en toute sécurité récupère les informations d’identification et remplissez les formulaires d’ouverture de session. Définir un mécanisme permettant de déployer l’extension à l’échelle avec [navigateurs pris en charge](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Les options sont les suivantes :

- [Stratégie de groupe pour Internet Explorer ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) pour Internet Explorer ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Utilisateur pilotée par le téléchargement et la configuration pour Chrome, Firefox, Microsoft Edge ou Internet Explorer ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Pour plus d’informations, consultez [comment configurer l’authentification unique de mot de passe](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Capture des métadonnées de formulaires de connexion pour les applications qui ne figurent pas dans la galerie

Microsoft prend en charge les métadonnées de capture sur une application web pour le mot de passe de stockage (capture les champs nom d’utilisateur et mot de passe). Accédez à l’URL de connexion pendant le processus de configuration de l’application pour capturer les métadonnées de formulaires. Demandez au propriétaire de l’application pour l’URL de connexion exact. Ces informations sont utilisées pendant le processus d’authentification, mappage des informations d’identification Azure AD à l’application lors de l’authentification.

Pour plus d’informations, consultez [What ' s accès aux applications et l’authentification unique avec Azure AD ? : l’authentification unique basée sur le mot de passe](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indications que les métadonnées dans les formulaires doivent être rétabli

Lorsque les applications modifiez leur mise en page HTML, vous devrez peut-être capturer à nouveau les métadonnées pour ajuster pour que les modifications. Les symptômes courants qui indiquent que la mise en page HTML a modification sont les suivantes :

- Utilisateurs de la création de rapports que vous cliquez sur l’application obtient « bloqué » dans la page de connexion
- Utilisateurs de la création de rapports que le nom d’utilisateur ou mot de passe n’est pas rempli

#### <a name="shared-accounts"></a>Comptes partagés

La connexion du point de vue, des applications avec des comptes partagés ne sont pas différentes à partir d’une application qui utilise l’authentification unique pour les utilisateurs individuels de la galerie. Toutefois, il existe quelques étapes supplémentaires requises lors de la planification et configuration d’une application conçue pour utiliser des comptes partagés :

1. Travailler avec les utilisateurs d’applications métier pour documenter les éléments suivants :
   1. Ensemble d’utilisateurs de l’organisation qui utilisera l’application
   1. Jeu existant d’informations d’identification dans l’application associée à l’ensemble des utilisateurs 
1. Pour chaque combinaison de l’ensemble d’utilisateurs et les informations d’identification, créez un groupe de sécurité dans le cloud ou en local selon vos besoins.
1. Réinitialiser les informations d’identification partagées. Une fois que l’application est déployée dans Azure AD, personnes n’avez pas besoin du mot de passe du compte partagé. Dans la mesure où Azure AD stocke le mot de passe, vous pouvez la définir à être très long et complexe. 
1. Configurer la substitution automatique du mot de passe si l’application le prend en charge. De cette façon, même pas l’administrateur qui a effectué l’installation initiale saura le mot de passe du compte partagé. 

## <a name="plan-your-authentication-method"></a>Planifier votre méthode d’authentification

Le choix de la méthode d’authentification est une première décision essentielle dans la configuration d’une solution d’identité hybride Azure AD. L’implémentation de la méthode d’authentification est configurée à l’aide d’Azure AD Connect, qui provisionne également les utilisateurs dans le cloud.

Pour choisir une méthode d’authentification, vous devez prendre en compte l’infrastructure existante, le temps nécessaire à l’implémentation, sa complexité et les coûts associés. Ces facteurs sont différents pour chaque organisation et peuvent varier au fil du temps. Vous devez choisir celui qui correspond le mieux à votre scénario spécifique. Pour plus d’informations, consultez [choisir la méthode d’authentification appropriée pour votre solution d’identité hybride Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Planifier votre sécurité et gouvernance 

Identité est le nouveau contrôle pivot principal pour la sécurité et les investissements, car les périmètres réseau sont devenus de plus en plus poreux et moins efficace avec l’explosion des appareils BYOD et aux applications du cloud. 

### <a name="plan-access-reviews"></a>Planifier les révisions d’accès

[Révisions d’accès](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) permettent aux organisations de gérer les appartenances aux groupes efficacement, l’accès aux applications d’entreprise et les attributions de rôles. Vous devez planifier à réviser l’accès utilisateur régulièrement pour vous assurer que seulement les bonnes personnes un accès ininterrompu.

Voici quelques-unes des rubriques clés à planifier lors de la configuration des révisions d’accès :

1. Identifiant une cadence pour les révisions d’accès qui correspond à vos besoins métier. Cela peut être aussi fréquente qu’une fois par semaine, mensuel, une fois par an ou en guise d’exercice à la demande.

1. Créer des groupes qui représentent les réviseurs de rapports d’accès de l’application. Vous devez vous assurer que les parties prenantes plus familiarisés avec l’application et ses utilisateurs cibles et les cas d’utilisation sont des participants dans vos révisions d’accès

1. Fin d’une révision d’accès inclut le retirer les autorisations d’accès application aux utilisateurs qui n’en avez plus besoin d’accéder. Planifier la gestion des demandes de support potentiels à partir des utilisateurs non autorisés. Un utilisateur supprimé reste supprimé dans Azure AD pendant 30 jours au cours de laquelle elles peuvent être restaurées par un administrateur si nécessaire. Au bout de 30 jours, cet utilisateur est définitivement supprimé. À l’aide du portail Azure Active Directory, un administrateur Global peut supprimer explicitement définitivement un utilisateur récemment supprimé avant que cette période soit atteinte.

### <a name="plan-auditing"></a>Planifier l’audit

Azure AD fournit [rapports contenant des informations techniques et commerciaux](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Sécurité et rapports d’activité sont disponibles. Rapports de sécurité affichent les utilisateurs avec indicateur de risque et les connexions risquées. Rapports d’activité vous aider à comprendre le comportement des utilisateurs de votre organisation en détaillant l’activité de connexion et en fournissant des pistes d’audit de toutes les connexions. Vous pouvez utiliser des rapports pour gérer les risques, d’augmenter la productivité et de surveiller la conformité.

| Type de rapport | Révision d’accès | Rapports de sécurité | Rapport de connexion |
|-------------|---------------|------------------|----------------|
| Utiliser pour passer en revue | Autorisations d’application et l’utilisation. | Comptes potentiellement compromis | Qui est l’accès aux applications |
| Actions possibles | Auditer l’accès ; révoquer des autorisations | Révoquer l’accès ; forcer la réinitialisation de la sécurité | Révoquer l’accès |

Azure AD conserve la plupart des données d’audit pendant 30 jours et rend les données disponibles via le portail d’administration Azure ou via une API que vous pouvez télécharger dans vos systèmes d’analyse.

### <a name="consider-using-microsoft-cloud-application-security"></a>Envisagez d’utiliser la sécurité des applications Microsoft Cloud

Microsoft Cloud App Security (MCAS) est une solution de Cloud accès sécurité Service Broker (CASB). Il vous donne une visibilité sur vos applications et services cloud fournit analytique sophistiquées pour identifier et combattre les cybermenaces et vous permet de contrôler la façon dont vos données sont transmises.

Déploiement MCAS vous permet de :

- Utilisez Cloud Discovery pour mapper et identifier votre environnement cloud et les applications cloud à l’aide de votre organisation.
- Approbation et Annuler-approuver des applications dans votre cloud
- Utilisez des connecteurs d’application faciles à déployer qui tirent parti des API du fournisseur, pour la visibilité et gouvernance des applications auxquelles vous vous connectez à
- Utiliser la protection de contrôle d’accès conditionnel pour obtenir une visibilité en temps réel et contrôler l’accès et les activités au sein de vos applications cloud
- Vous permet de contrôle continu via le paramètre et de préserver, de stratégies.

Contrôle de Session Microsoft Cloud Application Security (MCAS) est disponible pour n’importe quel navigateur sur n’importe quelle plateforme principale sur n’importe quel système d’exploitation. Applications mobiles et les applications de bureau peuvent également être bloquées ou autorisées. En intégrant en mode natif avec Azure AD, les applications qui sont configurées avec SAML ou Open ID Connect avec l’authentification unique dans Azure AD peut être pris en charge, y compris [plusieurs applications proposées](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Pour plus d’informations sur MCAS, consultez le [vue d’ensemble de Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS est un service d’abonnement basé sur l’utilisateur. Vous pouvez consulter les détails de licence dans le [fiche technique de la licence de MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Utilisez l’accès conditionnel

Avec l’accès conditionnel, vous pouvez automatiser les décisions de contrôle d’accès basé sur des critères pour vos applications cloud.

Des stratégies d’accès conditionnel sont appliquées au terme de la première authentification à un facteur. Par conséquent, l’accès conditionnel n’est pas conçu comme une première ligne de défense pour les scénarios tels que les attaques par déni de service (DoS), mais il peut utiliser des signaux à partir de ces événements pour déterminer l’accès. Par exemple le niveau de risque de connexion, l’emplacement de la demande et ainsi de suite peut être utilisé. Pour plus d’informations sur l’accès conditionnel, consultez [la vue d’ensemble](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) et [plan de déploiement](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Exigences techniques de l’authentification unique Azure

La section suivante décrit en détail la configuration requise pour configurer votre application spécifique, y compris les environnements nécessaires, points de terminaison, le mappage de revendications, les attributs requis, certificats et protocoles utilisés. Vous aurez besoin de ces informations pour configurer l’authentification unique dans le [portail Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Détails du mécanisme d’authentification

Pour toutes les applications SaaS pré-intégrées, Microsoft fournit un didacticiel et vous n’aurez pas besoin de ces informations. Si l’application n’est pas dans notre marketplace d’applications / de la galerie, vous devrez peut-être collecter les éléments de données suivants :

- **Fournisseur d’identité actuelle l’application utilise pour l’authentification unique, le cas échéant** , par exemple : AD FS, PingFederate, Okta
- **Protocoles pris en charge par l’application cible** , par exemple, SAML 2.0 OpenID Connect, OAuth, authentification basée sur les formulaires, WS-Fed, WS-Trust
- **Protocole qui est configuré avec Azure AD** , par exemple, SAML 2.0 ou 1.1, OpenID Connect, OAuth, basée sur les formulaires, WS-Fed

### <a name="attribute-requirements"></a>Exigences des attributs

Il existe un ensemble préconfiguré d’attributs et les mappages d’attributs entre les objets utilisateur Azure AD et les objets d’utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets tels que des groupes. Planifier le mappage des attributs d’utilisateur à partir d’Azure AD à votre application et [personnaliser les mappages d’attributs par défaut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) en fonction de votre entreprise a besoin.

### <a name="certificate-requirements"></a>Configuration requise des certificats

Le certificat pour l’application doit être à jour, ou il existe un risque de ne pas pouvoir accéder à l’application des utilisateurs. La plupart des certificats d’application SaaS conviennent de 36 mois. Vous modifiez cette durée de certificat dans le panneau de l’application. Veillez à documenter l’expiration et savoir comment vous gérerez votre renouvellement du certificat. 

Il existe deux façons de gérer vos certificats. 

- **Substitution de certificat automatique** -Microsoft prend en charge [substitution de clé de signature dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Bien que cela soit notre méthode préférée pour la gestion des certificats, pas tous les éditeurs de logiciels prend en charge ce scénario.

- **Mise à jour manuelle** -chaque application possède son propre certificat qui expire, selon la façon dont elle est définie. Avant l’expiration du certificat de l’application, créer un nouveau certificat et l’envoyer à l’éditeur de logiciels. Ces informations peuvent être extraites à partir des métadonnées de fédération. [En savoir plus sur les métadonnées de fédération ici.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implémentez l’authentification unique

Utilisez les phases suivantes pour planifier et déployer votre solution dans votre organisation :

### <a name="user-configuration-for-sso"></a>Configuration de l’utilisateur pour l’authentification unique

- **Identifier les utilisateurs de test**

   Contactez le propriétaire de l’application et demandez-lui de créer au moins trois d’utilisateurs de test au sein de l’application. Vérifiez les informations que vous allez utiliser comme identificateur principal sont remplies correctement et qu’il correspond à un attribut qui est disponible dans Azure AD. Dans la plupart des cas cela permet de mapper à la « NameID » pour les applications SAML. Pour les jetons JWT, elle est « preferred_username ».
   
   Créer l’utilisateur dans Azure AD soit manuellement en tant qu’un utilisateur basé sur le cloud ou la synchronisation d’un utilisateur du système local en utilisant le moteur de synchronisation Azure AD Connect. Vérifiez que les informations correspond aux revendications envoyées à l’application.

- **Configurer l’authentification unique**

   À partir de la [la liste des applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), recherchez et ouvrez le didacticiel de l’authentification unique pour votre application, puis suivez les étapes du didacticiel pour configurer correctement votre application SaaS.

   Si vous ne trouvez pas votre application, consultez [documentation personnalisée Application](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Cela vous guidera sur l’ajout d’une application qui ne se trouve pas dans la galerie Azure AD.

   Si vous le souhaitez, vous pouvez utiliser les revendications émises dans le jeton SAML pour l’application d’entreprise avec [documentation d’aide de Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Vérifiez que cela mappe à ce que vous attendez à recevoir dans la réponse SAML pour votre application. Si vous rencontrez des problèmes lors de la configuration, utilisez nos conseils sur [comment déboguer une SSO integration](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

L’intégration d’application personnalisée est une fonctionnalité de licences Azure AD Premium P1 ou P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fournir des communications de modification de l’authentification unique aux utilisateurs finaux

Implémenter votre plan de communication. Assurez-vous que vous indiquez à vos utilisateurs finaux qu’une modification arrive, quand il est arrivé, que faire maintenant et comment demander de l’aide.

### <a name="verify-end-user-scenarios-for-sso"></a>Vérifiez les scénarios d’utilisateur final pour l’authentification unique

Vous pouvez utiliser les cas de test suivants pour exécuter des tests sur l’entreprise et les appareils personnels pour vous assurer de vos configurations de l’authentification unique fonctionnent comme prévu. Les scénarios ci-dessous supposent qu’un utilisateur est accédant à une URL d’application et passer par un flux d’authentification initialisé par le fournisseur de service (flux d’authentification initialisée par SP).

| Scénario | Résultat attendu sur le flux d’authentification unique initiée par l’utilisateur |
|----------|---------------------------------------------------|
| Connectez-vous à l’application avec Internet Explorer tandis que sur le réseau d’entreprise. | Authentification de Windows intégrée (IWA) se produit sans invite supplémentaire. |
| Connectez-vous à l’application avec Internet Explorer tandis que hors du réseau d’entreprise avec la nouvelle tentative de connexion. | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur se connecte avec succès et le navigateur vous invite à entrer pour l’authentification Multifacteur. |
| Connexion à l’application avec Internet Explorer tandis que hors du réseau d’entreprise avec une session en cours et ne l’ont jamais effectuée. | Utilisateur ne reçoit pas d’invite pour le premier facteur. Utilisateur reçoit invite pour l’authentification Multifacteur. |
| Connexion à l’application avec Internet Explorer tandis que hors du réseau d’entreprise avec une session en cours et a déjà effectuée dans cette session. | Utilisateur ne reçoit pas d’invite pour le premier facteur. Utilisateur ne reçoit pas d’authentification Multifacteur. CSO utilisateur dans l’application. |
| Connexion à l’application avec Firefox/Chrome/Safari tandis que hors du réseau d’entreprise avec une session en cours et a déjà effectuée dans cette session. | Utilisateur ne reçoit pas d’invite pour le premier facteur. Utilisateur ne reçoit pas d’authentification Multifacteur. Utilisateur de l’authentification unique dans l’application. |
| Connectez-vous à l’application avec Firefox/Chrome/Safari tandis que hors du réseau d’entreprise avec la nouvelle tentative de connexion. | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur se connecte avec succès et le navigateur vous invite à entrer pour l’authentification Multifacteur. |
| Connectez-vous à l’application avec Chrome/Firefox, tandis que sur un réseau d’entreprise avec une session active. | Utilisateur ne reçoit pas d’invite pour le premier facteur. Utilisateur ne reçoit pas d’authentification Multifacteur. Utilisateur de l’authentification unique dans l’application. |
| Connectez-vous à l’application avec application des applications mobiles avec une nouvelle tentative de connexion. | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur se connecte avec succès et le client ADAL demande MFA. |
| Utilisateur non autorisé tente de se connecter à l’application avec l’URL de connexion. | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur ne parvient pas à se connecter avec le premier facteur. |
| Utilisateur autorisé tente de se connecter, mais entre un mot de passe incorrect. | Utilisateur navigue vers l’URL de l’application et reçoit l’erreur de nom d’utilisateur/mot de passe incorrect. |
| Utilisateur autorisé clique sur le lien dans un message électronique et est déjà authentifié. | Utilisateur clique sur l’URL et est connecté à l’application sans invite supplémentaire. |
| Utilisateur autorisé clique sur le lien dans un message électronique et n’est pas encore authentifié. | Utilisateur clique sur l’URL et l’utilisateur est sollicité pour s’authentifier avec le premier facteur. |
| Autorisé utilisateur se connecte à l’application avec application mobile application (initié par SP) avec une nouvelle tentative de connexion. | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur se connecte avec succès et le client ADAL demande MFA. |
| Utilisateur non autorisé tente de se connecter à l’application avec l’URL de connexion (initié par SP). | Invite de basée sur les formulaires sur le serveur AD FS. Utilisateur ne parvient pas à se connecter avec le premier facteur. |
| Utilisateur autorisé tente de se connecter, mais entre un mot de passe incorrect.| Utilisateur navigue vers l’URL de l’application et reçoit l’erreur de nom d’utilisateur/mot de passe incorrect. |
| Utilisateur autorisé se déconnecte et se connecte nouveau. | Si l’URL de déconnexion est configuré, l’utilisateur est connecté en dehors de tous les services et les invite à vous authentifier. |
| Utilisateur autorisé se déconnecte et se connecte nouveau. | Si l’URL de déconnexion n’est pas configuré, utilisateur sera automatiquement connecté à l’aide d’un jeton existant à partir de la session de navigateur Azure AD existante. |
| Utilisateur autorisé clique sur le lien dans un message électronique et est déjà authentifié. | Utilisateur clique sur l’URL et est connecté à l’application sans invite supplémentaire. |
| Utilisateur autorisé clique sur le lien dans un message électronique et n’est pas encore authentifié. | Utilisateur clique sur l’URL et l’utilisateur est sollicité pour s’authentifier avec le premier facteur. |

## <a name="manage-sso"></a>Gérer l’authentification unique

Cette section décrit les exigences et recommandations pour gérer avec succès l’authentification unique.

### <a name="required-administrative-roles"></a>Rôles d’administration requis

Utilisez toujours le rôle avec le moins d’autorisations permettant d’accomplir la tâche requise dans Azure Active Directory. Microsoft recommande [passez en revue les différents rôles disponibles](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) et choisir celui qui convient pour résoudre vos besoins pour chaque personne pour cette application. Certains rôles peuvent doivent être appliqués temporairement et supprimé une fois le déploiement terminé.

| Persona| contrôleur | Rôle Azure AD (si nécessaire) |
|--------|-------|-----------------------------|
| Help desk admin | Support de niveau 1 | Aucun |
| Administrateur d’identité | Configurer et déboguer les problèmes avoir un impact sur Azure AD | Administrateur général |
| Administrateur d’application | Attestation de l’utilisateur dans l’application, configuration sur les utilisateurs disposant d’autorisations | Aucun |
| Administrateurs d’infrastructure | Propriétaire de la substitution de certificat | Administrateur général |
| Propriétaire/des parties prenantes d’entreprise | Attestation de l’utilisateur dans l’application, configuration sur les utilisateurs disposant d’autorisations | Aucun |

Nous vous recommandons d’utiliser [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) pour gérer vos rôles pour fournir la passez en revue supplémentaires l’audit, le contrôle et accès pour les utilisateurs avec des autorisations de répertoire.

### <a name="sso-certificate-lifecycle-management"></a>Gestion de cycle de vie de certificat SSO

Il est important d’identifier les rôles de droite et envoyer par courrier électronique des listes de distribution chargés de gérer le cycle de vie du certificat de signature entre Azure AD et l’application est configurée avec l’authentification unique. Voici quelques-unes des rôles clés, que nous vous recommandons d’avoir en place :

- Propriétaire de la mise à jour des propriétés de l’utilisateur dans l’application
- Propriétaire d’appel pour la prise en charge des applications de réparation
- Liste de distribution de courrier électronique étroitement surveillés pour les notifications de modification associées aux certificats

La durée de vie maximale d’un certificat est de trois ans. Nous vous recommandons d’établir un processus de la façon dont vous allez gérer une modification du certificat entre Azure AD et votre application. Cela peut aider à éviter ou réduire une panne en raison d’une expiration du certificat ou forcer la substitution de certificat.

### <a name="rollback-process"></a>Processus de restauration

Après avoir terminé les tests en fonction de vos cas de test, il est temps de passer en production avec votre application. Passage en production signifie que vous allez implémenter vos configurations planifiées et testées dans votre client de production et déployez-le pour vos utilisateurs. Toutefois, il est important de planifier les éléments à suivre si votre déploiement n’accède pas comme prévu. Si la configuration de l’authentification unique échoue pendant le déploiement, vous devez comprendre comment atténuer toute panne et de réduire l’impact sur vos utilisateurs.

La disponibilité des méthodes d’authentification dans l’application détermine la meilleure stratégie. Assurez-vous que vous avez une documentation détaillée pour les propriétaires d’application exactement comment revenir à l’état de configuration de connexion d’origine au cas où votre déploiement rencontre des problèmes.

- **Si votre application prend en charge plusieurs fournisseurs d’identité**, par exemple LDAP et AD FS et Ping, ne supprimez pas la configuration de l’authentification unique existante pendant le déploiement. Au lieu de cela, désactivez-la pendant la migration au cas où vous deviez basculer revenir plus tard. 

- **Si votre application ne prend pas en charge plusieurs fournisseurs d’identité** mais permet aux utilisateurs de se connecter à l’aide de l’authentification par formulaire (nom d’utilisateur/mot de passe), assurez-vous que les utilisateurs peuvent revenir à cette approche en cas d’échec de la nouvelle mise en œuvre de configuration SSO.

### <a name="access-management"></a>gestion de l’accès

Nous vous recommandons de choisir une approche de mise à l’échelle lors de la gestion de l’accès aux ressources. Approches courantes incluent l’utilisation de groupes locaux en synchronisant via Azure AD Connect, [création de groupes dynamiques dans Azure AD en fonction des attributs](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), ou en créant [groupes en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) dans Azure AD géré par un propriétaire de la ressource.

### <a name="monitor-security"></a>Analyser la sécurité

Nous vous recommandons de configurer une cadence régulière dans laquelle vous passez en revue les différents aspects de la sécurité des applications SaaS et d’effectuer les actions correctives qui sont requises.

### <a name="troubleshooting"></a>Résolution de problèmes

Les liens suivants présentent des scénarios de dépannage. Vous souhaiterez peut-être créer un guide pour votre personnel de support qui intègre ces scénarios et les étapes pour les corriger.

#### <a name="consent-issues"></a>Problèmes de consentement

- [Erreur de consentement inattendue](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erreur de consentement de l’utilisateur](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problèmes de connexion

- [Problèmes de connexion à partir d’un portail personnalisé](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problèmes de connexion à partir du panneau d’accès](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erreur sur la page de connexion de l’application](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problème de connexion dans une application Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problèmes d’authentification unique pour les applications répertoriées dans la galerie d’applications Azure

- [Problème avec le mot de passe SSO pour les applications répertoriées dans la galerie d’applications Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problème avec l’authentification unique fédérée pour les applications répertoriées dans la galerie d’applications Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problèmes d’authentification unique pour les applications non répertoriées dans la galerie d’applications Azure

- [Problème avec le mot de passe SSO pour les applications non répertoriées dans la galerie d’applications Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problème avec l’authentification unique fédérée pour les applications non répertoriées dans la galerie d’applications Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Étapes suivantes

[Déboguer l’authentification unique SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mappage de revendications pour les applications par le biais de PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personnalisation des revendications émises dans le jeton SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocole SAML de déconnexion unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (pour les utilisateurs externes tels que les partenaires et fournisseurs)

[Accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Protection des identités Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Accès par authentification unique](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Didacticiel de l’authentification unique d’application](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
