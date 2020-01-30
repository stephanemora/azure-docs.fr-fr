---
title: Nouveau guide de formation sur les inscriptions d’applications dans le portail Azure
description: Présente la nouvelle expérience d’inscription d’applications dans le Portail Azure
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: b110929051098917d7c3f73161ca8694d4698070
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698218"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Nouveau guide de formation sur les inscriptions d’applications dans le portail Azure

La nouvelle expérience relative aux [inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) dans le Portail Azure a été considérablement améliorée. Si vous aviez l’habitude d’utiliser le processus hérité dans le portail Azure, consultez ce guide de formation pour vous familiariser avec la nouvelle expérience.

Dans Azure Active Directory, la nouvelle expérience d’inscription d’application décrite ici est en disponibilité générale (GA). Dans Azure Active Directory B2C (Azure AD B2C), elle est en préversion.

## <a name="key-changes"></a>Principales modifications

- Les inscriptions d’applications ne se limitent pas aux *applications/API web* ou aux applications *natives*. Vous pouvez utiliser le même processus d’inscription d’application pour ces dernières en inscrivant les URI de redirection correspondantes.

- L’expérience héritée prenait en charge les applications qui se connectaient uniquement à l’aide des comptes d’organisation (Azure AD). Les applications ont été inscrites en tant que locataire unique. Les applications ne prenaient en charge que les comptes de l’organisation à partir du répertoire dans lequel l’application a été inscrite. Les applications peuvent être modifiées pour être multilocataires et prendre en charge tous les comptes de l’organisation. Le nouveau processus vous permet d’inscrire des applications pouvant gérer ces deux options, ainsi qu’une troisième option : tous les comptes d’organisation et comptes Microsoft personnels.

- L’expérience héritée était uniquement disponible lorsque vous vous connectiez au Portail Azure à l’aide d’un compte d’organisation. Dans le cadre du nouveau processus, vous pouvez utiliser des comptes Microsoft personnels qui ne sont pas associés à un répertoire.

## <a name="list-of-applications"></a>Liste des applications

La nouvelle liste inclut des applications qui ont été inscrites à l’aide de la fonctionnalité Inscriptions des applications du portail Azure. Ces applications se connectent à l’aide de comptes Azure AD. La nouvelle liste d’applications affiche également les applications inscrites via le portail d’inscription des applications. Ces applications se connectent à l’aide d’Azure AD et de comptes Microsoft personnels.

>[!NOTE]
>Le portail d’inscription des applications a été déconseillé

La nouvelle liste d’applications ne comporte pas de colonne **Type d’application**, car une inscription d’application peut être de plusieurs types. La liste comporte deux colonnes supplémentaires : **Date de création** et **Certificats et secrets**. **Certificats et secrets** affiche l’état des informations d’identification qui ont été enregistrées sur l’application. Les États incluent **En cours**, **Arrive bientôt à expiration**et **Expiré**.

## <a name="new-app-registration"></a>Nouveau processus d’inscription d’application

Dans le cadre de l’expérience d’inscription d’application héritée, vous deviez fournir les informations suivantes : **Nom**, **Type d’application** et **URL de connexion/URI de redirection**. Les applications qui ont été créées n’étaient que des applications à locataire unique Azure AD. Les applications ne prenaient en charge que les comptes de l’organisation à partir du répertoire dans lequel l’application a été inscrite.

Avec le nouveau processus, vous devez fournir un **Nom** pour l’application et choisir les **Types de comptes pris en charge**. Si vous le souhaitez, vous pouvez également indiquer un **URI de redirection**. Si vous fournissez un URI de redirection, vous devez spécifier s’il s’agit d’un URI web/public (mobile et de bureau). Pour plus d’informations, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](quickstart-register-app.md). Pour Azure AD B2C, voir [Inscrire une application dans Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Différences entre le portail d’inscription des applications et la page Inscriptions d’applications

### <a name="the-legacy-properties-page"></a>Page de propriétés héritée

L’expérience héritée comportait une page **Propriétés**. La page **Propriétés** présentait les champs suivants :

- **Nom**
- **ID d’objet**
- **ID d’application**
- **URI ID d’application**
- **Logo**
- **URL de la page d’accueil**
- **URL de déconnexion**
- **URL des conditions d’utilisation**
- **URL de la déclaration de confidentialité.**
- **Type d’application**
- **Multilocataire**

La nouvelle expérience n’a pas cette page. Voici où se trouvent les fonctionnalités équivalentes :

- Les champs **Nom**, **Logo**, **URL de la page d’accueil**, **URL des conditions d’utilisation du service** et **URL de la déclaration de confidentialité** figurent désormais sur la page **Personnalisation** de l’application.
- Les champs **ID d’objet** et **ID d’application (client)** apparaissent sur la page **Vue d’ensemble**.
- Les fonctionnalités contrôlées par la bascule **Mutualisé** dans l’expérience héritée ont été remplacées par le champ **Types de comptes pris en charge** sur la page **Authentification**. Pour plus d’informations, consultez [Démarrage rapide : Modifiez les comptes pris en charge par une application](quickstart-modify-supported-accounts.md).
- Le champ **URL de déconnexion** figure désormais sur la page **Authentification**.
- Le champ **Type d’application** n’est plus valide. À la place, les URI de redirection (qui apparaissent sur la page **Authentification**) déterminent les types d’applications pris en charge.
- Le champ **URI ID d’application** s’intitule désormais **URI de l’ID d’application** et figure dans le panneau **Exposer une API**. Dans l’expérience héritée, cette propriété était inscrite automatiquement au format suivant : `https://{tenantdomain}/{appID}` par exemple, `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Dans la nouvelle expérience, elle est générée automatiquement sous la forme `api://{appID}`, mais doit être enregistrée explicitement. Dans les locataires Azure AD B2C, le format `https://{tenantdomain}/{appID}` est toujours utilisé.

### <a name="reply-urlsredirect-urls"></a>URL de réponse/URl de redirection

Dans l’expérience héritée, une application comportait une page **URL de réponse**. Dans la nouvelle expérience, les URL de réponse figurent dans la page **Authentification** d’une application. Ils sont désormais appelés **URI de redirection**.

Le format des URI de redirection a changé. Vous devez désormais les associer à un type d’application (web ou public). Pour des raisons de sécurité, les caractères génériques et les schémas `http://` ne sont pas pris en charge, à l’exception de *http://localhost* .

### <a name="keyscertificates--secrets"></a>Clés/Certificats et secrets

Dans l’expérience héritée, une application comportait une page **Clés**. Dans la nouvelle expérience, cette page s’intitule désormais **Certificats et secrets**.

Les **clés publiques** sont désormais appelées **certificats**. Les **mots de passe** sont désormais désignés sous le terme **Secrets des clients**.

### <a name="required-permissionsapi-permissions"></a>Autorisations nécessaires/API autorisées

Dans l’expérience héritée, une application comportait une page **Autorisations nécessaires**. Dans la nouvelle expérience, cette page s’intitule désormais **API autorisées**.

Quand vous avez sélectionné une API dans l’expérience héritée, vous pouvez choisir parmi une petite liste d’API Microsoft. Vous pouvez également effectuer une recherche dans les principaux de service du locataire. Dans la nouvelle expérience, vous pouvez choisir l’API parmi plusieurs onglets : **API Microsoft**, **API utilisées par mon organisation** ou **Mes API**. La barre de recherche figurant sur **API utilisées par mon organisation** utilise les recherches d’onglet par le biais des principaux de service dans le locataire.

> [!NOTE]
> Cet onglet n’apparaît pas si votre application n’est pas associée à un locataire. Pour obtenir plus d’informations sur la demande d’autorisations, consultez [Démarrage rapide : configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md).

Dans le cadre de l’expérience héritée, un bouton **Accorder des autorisations** figurait en haut de la page **Autorisations demandées**. Dans la nouvelle expérience, une page **Donner son consentement** comportant un bouton **Accorder le consentement administrateur** apparaît dans la section **API autorisées** d’une application. Les boutons fonctionnent également différemment entre les deux expériences.

Dans l’expérience héritée, la logique variait en fonction de l’utilisateur connecté et des autorisations demandées. La logique était la suivante :

- Si les demandes portaient uniquement sur des autorisations avec consentement de l’utilisateur et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur était en mesure d’accorder le consentement de l’utilisateur pour les autorisations demandées.
- Si au moins une autorisation nécessitant le consentement de l’administrateur était demandée et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur obtenait un message d’erreur lorsqu’il tentait de donner son consentement.
- Si l’utilisateur connecté était un administrateur, le consentement de l’administrateur était accordé pour toutes les autorisations demandées.

Dans la nouvelle expérience, seul un administrateur peut donner son consentement. Lorsqu’un administrateur sélectionne **Accorder le consentement administrateur**, le consentement de l’administrateur est accordé pour toutes les autorisations demandées.

## <a name="deleting-an-app-registration"></a>Suppression d’une inscription d’application

Dans l’expérience héritée, vous pouviez supprimer uniquement les applications à locataire unique. Le bouton de suppression était désactivé pour les applications mutualisées. Dans la nouvelle expérience, vous pouvez supprimer les applications quel que soit leur état, mais vous devez confirmer cette action. Pour plus d’informations, consultez [Démarrage rapide : Supprimer une application inscrite à l’aide de la plateforme d’identités Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifeste d’application

La nouvelle expérience n’utilise pas la même version de format JSON que l’expérience héritée dans l’éditeur de manifeste. Pour plus d’informations, consultez [Manifeste de l’application Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nouvelle interface utilisateur

La nouvelle expérience ajoute des contrôles d’interface utilisateur pour les propriétés suivantes :

- La page **Authentification** a un **flux d’octroi implicite** (`oauth2AllowImplicitFlow`). Contrairement à l’expérience héritée, vous pouvez activer des **jetons d’accès** et/ou des **jetons d’ID**.
- La page **Exposer une API** contient des **étendues définies par cette** API (`oauth2Permissions`) et des **applications clientes autorisées** (`preAuthorizedApplications`). Pour plus d’informations sur la configuration d’une application en tant qu’API web et sur l’exposition des autorisations/étendues, consultez [Démarrage rapide : Configurer une application pour exposer des API web](quickstart-configure-app-expose-web-apis.md).
- La page **Personnalisation** contient le **domaine de l’éditeur**. Le domaine de l’éditeur est visible aux utilisateurs sur l’[invite de consentement de l’application](application-consent-experience.md). Pour plus d’informations, consultez [Procédure : Configurez un domaine d’éditeur d’application](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limites

La nouvelle expérience présente les limites suivantes :

- Le format des clés secrètes clients (mots de passe d’application) diffère de celui de l’expérience héritée ; il est susceptible d’arrêter l’interface de ligne de commande.
- L’interface utilisateur ne gère pas la modification de la valeur des comptes pris en charge. Vous devez utiliser le manifeste de l’application, sauf si vous basculez entre une application Azure AD à locataire unique et une application mutualisée.
