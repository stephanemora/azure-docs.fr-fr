---
title: Nouvelle expérience d’inscription d’applications dans le portail Azure
titleSuffix: Microsoft identity platform
description: Présentation de la nouvelle expérience d’inscription d’application dans le portail Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 43ed0bfed1dbe48c4f66a820ecad6fedf11115fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478159"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>La nouvelle expérience d’inscription d’applications dans le Portail Azure

La nouvelle expérience relative aux [inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) dans le portail Azure a été considérablement améliorée. Si vous êtes plus à l’aise avec le portail d’inscription d’applications (apps.dev.microsoft.com) pour l’inscription ou la gestion d’applications, ce qu’on appelle ici « l’ancienne expérience », ce guide vous permet de commencer à utiliser la nouvelle expérience.

## <a name="whats-not-changing"></a>Ce qui ne change pas

- Vos applications et les configurations associées peuvent être trouvées telles quelles dans la nouvelle expérience. Vous n’avez pas besoin de réinscrire les applications et les utilisateurs de vos applications n’ont pas besoin de se reconnecter.

    > [!NOTE]
    > Vous devez vous connecter avec le compte que vous avez utilisé pour inscrire des applications afin de les trouver dans le portail Azure. Nous vous recommandons de vérifier que l’utilisateur connecté dans le portail Azure correspond à l’utilisateur qui était connecté au portail d’inscription d’applications en comparant l’adresse e-mail issue de votre profil.
    >
    > Dans certains cas, en particulier lorsque vous vous connectez à l’aide de comptes Microsoft personnels (par exemple, Outlook, Live, Xbox, etc.) avec une adresse e-mail Azure AD, nous avons découvert que lorsque vous accédez au portail Azure à partir de l’ancienne expérience, vous êtes connecté à un compte différent avec la même adresse e-mail que celle dans votre locataire Azure AD. Si vous pensez toujours que vos applications sont manquantes, déconnectez-vous et connectez-vous avec le compte approprié.

- Les applications SDK Live créées à l’aide de comptes Microsoft personnels ne sont pas encore prises en charge dans le portail Azure et seront conservées dans l’ancienne expérience pour le moment.

## <a name="key-changes"></a>Principales modifications

-   Dans l’ancienne expérience, les applications étaient enregistrées par défaut en tant qu’applications *convergées*, à savoir des applications prenant en charge tous les comptes organisationnels (multilocataire), ainsi que les comptes Microsoft personnels. Cela n’a pas pu être modifié par le biais de l’ancienne expérience, ce qui complique la création d’applications ne prenant en charge que les comptes organisationnels (multilocataire ou à locataire unique).
    La nouvelle expérience vous permet d’inscrire des applications qui prennent en charge toutes ces options. [En savoir plus sur les types d’applications](active-directory-v2-registration-portal.md).

-   Dans la nouvelle expérience, si votre compte Microsoft personnel est également dans un locataire Azure AD, vous verrez trois onglets : toutes les applications dans le locataire, les applications détenues dans le locataire et les applications de votre compte personnel. Par conséquent, si vous pensez que des applications inscrites avec votre compte Microsoft personnel sont manquantes, vérifiez l’onglet **Applications de votre compte personnel**.

-   Dans la nouvelle expérience, vous pouvez facilement basculer entre les locataires en accédant à votre profil et en choisissant de changer de répertoire.

## <a name="list-of-applications"></a>Liste des applications

-   La nouvelle liste d’applications présente les applications qui ont été inscrites par le biais de l’expérience d’inscription d’application héritée dans le Portail Azure (applications qui connectent les comptes Azure AD uniquement), ainsi que les applications inscrites par l’intermédiaire du [portail d’inscription d’application](https://apps.dev.microsoft.com/) (applications qui connectent les comptes Azure AD et les comptes Microsoft personnels).

-   La nouvelle liste d’applications comporte deux colonnes supplémentaires : La colonne **Date de création** et la colonne **Certificats et secrets** qui indique l’état (actuel, arrivant bientôt à expiration ou expiré) des informations d’identification qui ont été inscrites dans l’application.

## <a name="new-app-registration"></a>Nouveau processus d’inscription d’application

Dans l’ancienne expérience, pour inscrire une application, il vous suffisait de fournir un nom. Les applications qui étaient créées étaient inscrites en tant qu’applications *convergées*, des applications prenant en charge tous les annuaires organisationnels (multilocataire), ainsi que les comptes Microsoft personnels.  Cela n’a pas pu être modifié par le biais de l’ancienne expérience, ce qui complique la création d’applications ne prenant en charge que les comptes organisationnels (à locataire unique ou à locataires multiples). [En savoir plus sur les types de comptes pris en charge](v2-supported-account-types.md)

Dans la nouvelle expérience, vous devez fournir un nom pour l’application et choisir les types de comptes pris en charge. Si vous le souhaitez, vous pouvez également indiquer un URI de redirection.
Si vous fournissez un URI de redirection, vous devez spécifier s’il s’agit d’un URI web/public (natif/mobile et de bureau). Pour plus d’informations sur l’inscription d’une application à l’aide de la nouvelle expérience d’inscription d’application, consultez [ce démarrage rapide](quickstart-register-app.md).

## <a name="app-management-page"></a>Page de gestion des applications

L’ancienne expérience ne proposait qu’une seule page de gestion des applications, pour les applications avec les sections suivantes : Propriétés, Secrets de l’application, Plateformes, Propriétaires, Autorisations Microsoft Graph, Profil et Options avancées.

La nouvelle expérience dans le Portail Azure représente ces fonctionnalités dans des pages distinctes. Voici où se trouvent les fonctionnalités équivalentes :

- Propriétés - Le Nom et l’ID d’application se trouvent sur la page Vue d’ensemble.
- La section Secrets de l’application se trouve sur la page Certificats et secrets.
- La section Configuration des plateformes se trouve sur la page Authentification.
- La section Autorisations Microsoft Graph se trouve sur la page Autorisations de l’API, avec d’autres autorisations.
- La section Profil se trouve sur la page Personnalisation
- Options avancées - La prise en charge du SDK Live se trouve sur la page Authentification.

## <a name="application-secretscertificates--secrets"></a>Secrets de l’application/Certificats et secrets

Dans la nouvelle expérience, la page **Secrets de l’application** s’intitule désormais **Certificats et secrets**. En outre, les **Clés publiques** sont appelées **Certificats**, et les **Mots de passe** sont désignés sous le terme de **Secrets client**. Nous avons choisi de ne pas intégrer cette fonctionnalité dans la nouvelle expérience pour des raisons de sécurité. Par conséquent, vous ne pouvez plus générer une nouvelle paire de clés.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plateformes/Authentification : URL de réponse/URl de redirection
Dans l’ancienne expérience, une application disposait de la section Plateformes pour web, natif et API web afin de configurer les URL De redirection, l’URL de déconnexion et le flux implicite.

Dans la nouvelle expérience, les URL de réponse figurent dans la section Authentification d\'une application. En outre, elles sont appelées URI de redirection et le format des URI de redirection a changé. Vous devez désormais les associer à un type d’application (web ou public - mobile et de bureau). [En savoir plus](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Les API web sont configurées dans la page Exposer une API.

> [!NOTE]
> Essayez la nouvelle expérience des paramètres d’authentification vous permettant de configurer les paramètres de votre application en fonction de la plateforme ou de l’appareil que vous souhaitez cibler. [En savoir plus](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Autorisations Microsoft Graph/Autorisations d’API

-   Lors de la sélection d’une API dans l’ancienne expérience, vous pouviez uniquement choisir parmi les API Microsoft Graph. Dans la nouvelle expérience, vous pouvez choisir parmi de nombreuses API Microsoft, notamment Microsoft Graph, des API de votre organisation et vos API. Trois onglets sont à votre disposition : API Microsoft, API utilisées par mon organisation ou Mes API. La barre de recherche figurant sur API utilisées par mon organisation utilise les recherches d’onglet par le biais des principaux de service dans le locataire.

    > [!NOTE]
    > Cet onglet n’apparaît pas si votre application n’est pas associée à un locataire. Pour plus d’informations sur la procédure à suivre pour demander des autorisations à l’aide de la nouvelle expérience, consultez [ce démarrage rapide](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   L’ancienne expérience n’avait pas de bouton **Accorder des autorisations**. Dans la nouvelle expérience, une section Donner son consentement comportant un bouton **Accorder le consentement administrateur** apparaît dans la section Autorisations d’API d’une application. Seul un administrateur peut accorder le consentement et ce bouton est activé uniquement pour les administrateurs. Lorsqu’un administrateur sélectionne le bouton **Accorder le consentement administrateur**, le consentement de l’administrateur est accordé pour toutes les autorisations demandées.

## <a name="profile"></a>Profil
Dans l’ancienne expérience, la section Profil contenait les champs Logo, URL de la page d’accueil, URL des conditions d’utilisation du service et URL de la déclaration de confidentialité. Dans la nouvelle expérience, ces champs se trouvent sur la page Personnalisation.

## <a name="application-manifest"></a>Manifeste d’application
Dans la nouvelle expérience, la page Manifeste vous permet de modifier et de mettre à jour les attributs de l’application. Pour plus d’informations, consultez l’article [Manifeste d’application](reference-app-manifest.md).

## <a name="new-ui"></a>Nouvelle interface utilisateur
Il existe une nouvelle interface utilisateur pour les propriétés, alors qu’auparavant, ces dernières étaient uniquement définissables à l’aide de l’éditeur de manifeste ou de l’API, ou n’existaient pas du tout.

-   Le flux d’octroi implicite (oauth2AllowImplicitFlow) figure sur la page Authentification. Contrairement à l’ancienne expérience, vous pouvez activer des jetons d’accès et/ou des jetons d’ID.

-   Les champs Étendues définies par cette API (oauth2Permissions) et Applications clientes autorisées (preAuthorizedApplications) sont configurables par le biais de la page Exposer une API. Pour plus d’informations sur la configuration d’une application en tant qu’API web et sur l’exposition des autorisations/étendues, consultez [ce démarrage rapide](quickstart-configure-app-expose-web-apis.md).

-   Le champ Domaine de l’éditeur (présenté aux utilisateurs dans [l’invite de consentement de \'l’application](application-consent-experience.md)) figure sur le volet Personnalisation. Pour plus d’informations sur la configuration d’un domaine d’éditeur, consultez [cette procédure](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limites

La nouvelle expérience présente les limites suivantes :

-   La nouvelle expérience ne prend pas encore en charge les inscriptions d’applications pour les locataires Azure AD B2C.

-   La nouvelle expérience ne prend pas encore en charge les applications SDK Live créées avec des comptes Microsoft personnels.

-   L’interface utilisateur ne gère pas la modification de la valeur des comptes pris en charge. Vous devez utiliser le manifeste de l’application, sauf si \'vous basculez entre une application Azure AD à locataire unique et une application multilocataire.

   > [!NOTE]
   > Si vous êtes un utilisateur de compte Microsoft personnel dans un locataire Azure AD et que l’administrateur du locataire a restreint l’accès au portail Azure, votre accès risque d’être refusé. Toutefois, si vous y accédez via le raccourci en tapant Inscriptions d’applications dans la barre de recherche ou en l’épinglant, vous pourrez accéder à la nouvelle expérience utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser la nouvelle expérience d’inscription d’application, consultez [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](quickstart-register-app.md).
