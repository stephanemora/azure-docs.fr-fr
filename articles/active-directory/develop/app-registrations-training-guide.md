---
title: Inscriptions d’application dans le guide de formation de portail Azure - Azure
description: Générez des flux d’authentification intégrés et sans navigateur à l’aide de l’octroi de code d’appareil.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870116"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guide de formation : Inscriptions d’application dans le portail Azure  

Vous trouverez de nombreuses améliorations dans le nouveau [inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) expérience dans le portail Azure. Si vous êtes familiarisé avec l’expérience héritée, utilisez ce guide de formation pour vous aider à démarrer à l’aide de la nouvelle expérience.

## <a name="key-changes"></a>Changements de clés

- Inscriptions d’application ne sont pas limitées à étant soit un **application web/API** ou un **natif** application. Vous pouvez utiliser la même inscription d’application pour ces dernières en inscrivant l’URI de redirection respectif.
- Les applications prises en charge l’expérience héritée qui se connectent d’organisation (comptes Azure AD) uniquement. Les applications ont été enregistrées en tant que client unique (prenant en charge des comptes professionnels uniquement à partir du répertoire de l’application a été inscrite dans) et peut être modifié pour être l’architecture mutualisée (prenant en charge de tous les comptes d’organisation). La nouvelle expérience vous permet à inscrire des applications qui peuvent prendre en charge les deux de ces options ainsi qu’une troisième option : tous les comptes de société ainsi que les comptes Microsoft personnels.
- L’expérience héritée était uniquement disponible lorsque connecté au portail Azure à l’aide d’un compte professionnel. Avec la nouvelle expérience, vous pouvez utiliser des comptes Microsoft personnels qui ne sont pas associés à un répertoire.

## <a name="list-of-applications"></a>Liste des applications

- La nouvelle liste d’applications affiche les applications qui ont été inscrits via l’application héritée expérience des enregistrements dans le portail Azure (les applications qui connectent les comptes Azure AD), ainsi que les applications inscrit cependant le [portail d’inscription des applications](https://apps.dev.microsoft.com/) (les applications qui connectent dans Azure AD et les comptes Microsoft personnels).
- La nouvelle liste d’applications n’a pas un **type d’Application** colonne (dans la mesure où une inscription d’application unique peut contenir plusieurs types) et possède deux colonnes supplémentaires : un **créé sur** colonne et une **certificats & secrets** colonne qui affiche l’état (actuel, expire bientôt ou arrivé à expiration) des informations d’identification qui ont été inscrits sur l’application.

## <a name="new-app-registration"></a>Nouvelle inscription d’application

Dans l’expérience héritée, pour inscrire une application vous deviez fournir : **Nom**, **type d’Application**, et **Sign-on l’URI de redirection d’URL**. Les applications qui ont été créées ont été Azure AD uniquement applications à locataire unique ce qui signifie qu’ils uniquement pris en charge des comptes de société à partir du répertoire de dans que l’application a été inscrite.

Dans la nouvelle expérience, vous devez fournir un **nom** pour l’application et choisissez le **pris en charge les types de comptes**. Vous pouvez éventuellement fournir un **l’URI de redirection**. Si vous fournissez un URI de redirection, vous devez spécifier s’il s’agit de web/public (mobile et de bureau). Pour plus d’informations sur la façon d’inscrire une application en utilisant les nouvelles inscriptions expérience, consultez [ce démarrage rapide](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>La page de propriétés héritée

L’expérience héritée avait un **propriétés** page qui n’a pas la nouvelle expérience. Le **propriétés** panneau avait les champs suivants : **Nom**, **ID d’objet**, **ID d’Application**, **URI ID d’application**, **Logo**, **URL de la page d’accueil** , **URL de déconnexion**, **termes du contrat de service URL**, **URL de la déclaration confidentialité**, **type d’Application**, et  **Architecture mutualisée.**

Voici où vous trouverez la fonctionnalité équivalente dans la nouvelle expérience :

- **Nom**, **Logo**, **URL de la page d’accueil**, **termes du contrat de service URL**, et **URL de la déclaration confidentialité** est présent sur l’application  **Marque** page.
- **ID d’objet** et **ID d’Application (client)** se trouve sur le **vue d’ensemble** page.
- La fonctionnalité contrôlée par le **mutualisée** bascule dans l’expérience héritée a été remplacé par **pris en charge les types de comptes** sur le **authentification** page. Pour plus d’informations sur comment plusieurs locataires mappe aux options du type de compte pris en charge, consultez [ce démarrage rapide](quickstart-modify-supported-accounts.md).
- **URL de déconnexion** est présent sur le **authentification** page.
- **Type d’application** n’est plus un champ valide. Au lieu de cela, les URI de redirection (que vous trouverez sur le **authentification** page) déterminer quels types d’application sont prises en charge.
- **URI ID d’application** s’appelle désormais **URI ID d’Application** et vous pouvez le trouver sur le **exposer une API** panneau. Dans l’expérience héritée, cette propriété a été inscrit automatiquement en utilisant le format suivant : `https://{tenantdomain}/{appID}` (par exemple, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Dans le nouveau format, il est généré automatiquement en tant que `api://{appID}`, mais elle doit être enregistrée explicitement.

## <a name="reply-urlsredirect-urls"></a>URL de redirection/URL de réponse

Dans l’expérience héritée, une application devait un **URL de réponse** page. Dans la nouvelle expérience, URL de réponse se trouve sur l’application **authentification** section. En outre, elles sont appelées **URI de redirection**. En outre, le format de redirection URI a changé. Ils sont nécessaires pour être associé à un type d’application (web ou public). En outre, pour des raisons de sécurité, les jeux de caractères génériques et http:// ne sont pas pris en charge (à l’exception de http://localhost).

## <a name="keyscertificates--secrets"></a>Les certificats/clés et secrets

Dans l’expérience héritée, une application devait **clés** page. Dans la nouvelle expérience, il a été renommé en **certificats et clés secrètes**. En outre, **clés publiques** sont appelés **certificats** et **les mots de passe** sont appelés **clés secrètes de Client**.

## <a name="required-permissionsapi-permissions"></a>Autorisations d’autorisations/API requises

- Dans l’expérience héritée, une application devait un **autorisations requises** page. Dans la nouvelle expérience, il a été renommé en **autorisations d’API**.
- Lorsque vous sélectionnez une API dans l’expérience héritée, vous pouvez choisir à partir d’une liste réduite de APIs Microsoft ou de recherche à l’aide des principaux de service dans le client. Dans la nouvelle expérience, vous pouvez choisir parmi plusieurs onglets : **Microsoft APIs**, **mon organisation utilise des API**, ou **mes API**. La barre de recherche sur **API mon organisation** utilise les recherches d’onglet par le biais principaux de service dans le client. 

   > [!NOTE]
   > Vous ne verrez cet onglet si votre application n’est pas associée à un client. Pour plus d’informations sur la façon de demander des autorisations à l’aide de la nouvelle expérience, consultez [ce démarrage rapide](quickstart-configure-app-access-web-apis.md).

- L’expérience héritée avait un **accorder des autorisations** bouton en haut de la **autorisations demandées** page. Dans la nouvelle expérience, il existe un **donner leur consentement** section avec un **accorder le consentement de l’administrateur** bouton sur l’application **autorisations d’API** section. En outre, il existe des différences de la façon la fonction boutons :
   - Dans l’expérience héritée, la logique varie en fonction de l’utilisateur connecté et les autorisations demandées. La logique a été :
      - Si seuls les autorisations en mesure de consentement de l’utilisateur ont été demandées et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur a été en mesure d’accorder le consentement de l’utilisateur pour les autorisations demandées.
      - Si au moins une autorisation qui requiert le consentement de l’administrateur a été demandée et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur obtenu une erreur lorsque vous tentez de donner leur consentement.
      - Si l’utilisateur connecté est un administrateur, le consentement de l’administrateur a été accordé pour toutes les autorisations demandées.
   - Dans la nouvelle expérience, seul un administrateur peut donner leur consentement. Quand un administrateur sélectionne le **accorder le consentement de l’administrateur** bouton, le consentement de l’administrateur est accordé à toutes les autorisations demandées.

## <a name="deleting-an-app-registration"></a>Suppression d’une inscription d’application

Dans l’expérience héritée, une application devait être locataire unique doit être supprimé. Le bouton de suppression a été désactivé pour les applications mutualisées. Dans la nouvelle expérience, les applications peuvent être supprimées dans n’importe quel état, mais vous devez confirmer l’action. Pour plus d’informations sur la suppression d’inscriptions, consultez [ce démarrage rapide](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifeste d’application

Les expériences existantes et nouvelles utilisent différentes versions pour le format de l’objet JSON dans l’éditeur de manifeste. Pour plus d’informations, consultez l’article [Manifeste d’application](reference-app-manifest.md).

## <a name="new-ui"></a>Nouvelle interface utilisateur

Il est nouvelle interface utilisateur pour les propriétés qui pouvait auparavant uniquement être définie à l’aide de l’éditeur de manifeste ou de l’API ou n’existe pas.

- **Flux d’octroi implicite** (oauth2AllowImplicitFlow) sont accessibles sur le **authentification** page. Contrairement à l’expérience héritée, vous pouvez activer **de jetons d’accès** ou **les jetons d’id**, ou les deux.
- **Étendues définies par cette API** (oauth2Permissions) et **autorisé d’applications clientes** (preAuthorizedApplications) peut être configurées via le **exposer une API** page. Pour plus d’informations sur la façon de configurer une application à une API web et à exposer les étendues d’autorisations, consultez [ce démarrage rapide](quickstart-configure-app-expose-web-apis.md).
- **Domaine de l’éditeur** (qui est affiché aux utilisateurs sur le [invite de consentement de l’application](application-consent-experience.md)) sont accessibles sur le **Branding panneau** page. Pour plus d’informations sur la façon de configurer un domaine de serveur de publication, consultez [cette procédure](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limites

La nouvelle expérience présente les limitations suivantes :

- La nouvelle expérience n’est actuellement pas disponible dans les locataires Azure AD B2C.
- Le format des clés secrètes de client (mots de passe) est différent de celui de l’expérience héritée et sauts de CLI.
- Modification de la valeur pour les comptes pris en charge n’est pas pris en charge dans l’interface utilisateur. Vous devez utiliser le manifeste d’application, sauf si vous basculez entre Azure AD à locataire unique et architecture mutualisé.
