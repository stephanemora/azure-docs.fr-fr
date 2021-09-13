---
title: Accorder le consentement administrateur au niveau locataire à une application - Azure AD
description: Découvrez comment accorder un consentement au niveau locataire à une application afin d’éviter que les utilisateurs finaux donnent leur consentement lors de la connexion à une application.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/21/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee3ff6fc063de5eebd4a7cc300fdc0a5ceeba560
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038384"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Accorder le consentement administrateur au niveau locataire à une application

  Découvrez comment accorder le consentement administrateur au niveau locataire à une application. Cet article présente les différentes façons d’y parvenir.

Pour plus d’informations sur le consentement des applications, consultez [Infrastructure de consentement d’Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prérequis

Pour accorder le consentement administrateur au niveau locataire, vous devez vous connecter comme utilisateur autorisé à donner un consentement au nom de l’organisation. Cela comprend l’[Administrateur général](../roles/permissions-reference.md#global-administrator) et l’[Administrateur de rôle privilégié](../roles/permissions-reference.md#privileged-role-administrator). Pour les applications ne nécessitant pas de permissions d’application pour Microsoft Graph ou Azure AD Graph, cela comprend également l’[Administrateur d’application](../roles/permissions-reference.md#application-administrator) et l’[Administrateur d’application de Cloud](../roles/permissions-reference.md#cloud-application-administrator). Un utilisateur peut également être autorisé à accorder le consentement au niveau locataire si un [rôle d’annuaire personnalisé](../roles/custom-create.md) comprenant l’[autorisation d’accorder des autorisations à des applications](../roles/custom-consent-permissions.md) lui est attribué.

> [!WARNING]
> Le fait d’accorder le consentement administrateur au niveau locataire à une application permettra à l’application et à l’éditeur de l'application d’accéder aux données de votre organisation. Examinez attentivement les autorisations demandées par l’application avant d’accorder le consentement.

> [!IMPORTANT]
> Lorsqu’une application a reçu le consentement administrateur au niveau locataire, tous les utilisateurs peuvent se connecter à l’application, sauf si elle a été configurée pour exiger l’attribution de l’utilisateur. Pour restreindre à des utilisateurs spécifiques l’accès à une application, demandez l’affectation d’utilisateurs, puis affectez des utilisateurs ou des groupes à l’application. Pour plus d'informations, voir [Méthodes d'affectation d'utilisateurs et de groupes](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Accorder le consentement administrateur à partir du Portail Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Accorder le consentement administrateur dans des applications d’entreprise

Vous pouvez accorder le consentement administrateur au niveau locataire via *Applications d’entreprise* si l’application a déjà été approvisionnée dans votre locataire. Par exemple, une application peut être approvisionnée dans votre locataire si au moins un utilisateur a déjà donné son consentement à l’application. Pour plus d’informations, consultez [Comment et pourquoi les applications sont ajoutées à Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Pour accorder le consentement administrateur au niveau locataire à une application figurant dans **Applications d’entreprise** :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un rôle permettant d’autoriser le consentement de l’administrateur (voir[Prérequis](#prerequisites)).
2. Sélectionnez **Azure Active Directory** puis **Applications d’entreprise**.
3. Sélectionnez l’application à laquelle vous souhaitez accorder le consentement administrateur au niveau locataire.
4. Sélectionnez **Autorisations**, puis cliquez sur **Accorder le consentement administrateur**. Dans cet exemple, nous utilisons des applications 10,000ft Plans.

   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="Capture d’écran montre comment accorder le consentement de l’administrateur au niveau du locataire.":::

5. Examinez attentivement les autorisations requises par l’application.
6. Si vous acceptez les autorisations requises par l’application, accordez le consentement. Si ce n’est pas le cas, cliquez sur **Annuler** ou fermez la fenêtre.

> [!WARNING]
> L’octroi du consentement administrateur au niveau du locataire via des **applications d’entreprise** révoque toutes les autorisations accordées précédemment à l’ensemble du locataire. Les autorisations qui ont été précédemment accordées par les utilisateurs en leur nom propre ne seront pas concernées.

### <a name="grant-admin-consent-in-app-registrations"></a>Accorder le consentement administrateur dans les inscriptions d’applications

Pour les applications développées par votre organisation ou inscrites directement dans votre locataire Azure AD, vous pouvez également accorder le consentement administrateur au niveau locataire à partir de **Inscriptions d’applications** dans le Portail Azure.

Pour accorder le consentement administrateur au niveau locataire dans **Inscriptions d'applications** :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un rôle permettant d’autoriser le consentement de l’administrateur (voir[Prérequis](#prerequisites)).
2. Sélectionnez **Azure Active Directory**, puis **Inscription d’applications**.
3. Sélectionnez l’application à laquelle vous souhaitez accorder le consentement administrateur au niveau locataire.
4. Sélectionnez **Autorisations d’API**, puis cliquez sur **Accorder le consentement administrateur**.
5. Examinez attentivement les autorisations requises par l’application.
6. Si vous acceptez les autorisations requises par l’application, accordez le consentement. Si ce n’est pas le cas, cliquez sur **Annuler** ou fermez la fenêtre.

> [!WARNING]
> L’octroi du consentement administrateur au niveau du locataire via des **inscriptions d’applications** révoque toutes les autorisations accordées précédemment à l’ensemble du locataire. Les autorisations qui ont été précédemment accordées par les utilisateurs en leur nom propre ne seront pas concernées.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construire l’URL pour accorder le consentement de l’administrateur au niveau du locataire

Lorsque vous accordez le consentement administrateur au niveau du locataire à l’aide de l’une des méthodes décrites ci-dessus, une fenêtre s’ouvre à partir du Portail Azure pour demander le consentement administrateur au niveau locataire. Si vous connaissez l’ID client (également appelé ID d’application) de l’application, vous pouvez générer la même URL pour accorder le consentement administrateur au niveau locataire.

L’URL de consentement administrateur au niveau locataire suit le format suivant :

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

où :

* `{client-id}` est l’ID client de l’application (également appelé ID d’application).
* `{tenant-id}` est l’ID de locataire de votre organisation ou un nom de domaine vérifié.

Comme toujours, examinez attentivement les autorisations demandées par une application avant d’accorder le consentement.

> [!WARNING]
> L’octroi du consentement administrateur au niveau du locataire via cette URL révoque toutes les autorisations accordées précédemment à l’ensemble du locataire. Les autorisations qui ont été précédemment accordées par les utilisateurs en leur nom propre ne seront pas concernées.

## <a name="next-steps"></a>Étapes suivantes

[Configurer le consentement de l’utilisateur final pour une application](configure-user-consent.md)

[Configurer le workflow du consentement administrateur](configure-admin-consent-workflow.md)

[Autorisations et consentement dans la plateforme d’identités Microsoft](../develop/v2-permissions-and-consent.md)

[Azure AD sur Microsoft Q&A](/answers/topics/azure-active-directory.html)
