---
title: Vue d’ensemble de l’intégration de l’API de soumission de l’Espace partenaires
description: Vue d’ensemble de l’intégration de l’API de soumission de l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 5c6431fef8c91b41f19d27d19b59efca14986b7f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058749"
---
# <a name="partner-center-submission-api-onboarding"></a>Intégration de l’API de soumission de l’Espace partenaires

Utilisez l’API de soumission de l’Espace partenaires pour interroger, soumettre et publier programmatiquement des offres commerciales sur la Place de marché. Cette API est utile si votre compte gère de nombreuses offres et que vous souhaitez automatiser et optimiser le processus de soumission de ces offres.

## <a name="api-prerequisites"></a>Prérequis de l’API

L’API de soumission de l’Espace partenaires nécessite quelques ressources programmatiques :

- une application Azure Active Directory
- un jeton d’accès Azure AD (Azure Active Directory)

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Étape 1 : Suivre les prérequis de l’API de soumission de l’Espace partenaires

Avant de commencer à écrire du code pour appeler l’API de soumission de l’Espace partenaires, veillez à répondre aux prérequis suivants.

- Vous (ou votre organisation) devez disposer d’un annuaire Azure AD et de l’autorisation [Administrateur général](../active-directory/roles/permissions-reference.md) sur l’annuaire. Si vous utilisez déjà Microsoft 365 ou d’autres services professionnels de Microsoft, vous disposez déjà d’un annuaire Azure AD. Sinon, vous pouvez [créer un annuaire Azure AD](manage-tenants.md#create-a-new-tenant) dans l’Espace partenaires sans frais supplémentaires.
- Vous devez [associer une application Azure AD à votre compte Espace partenaires](manage-aad-apps.md) et récupérer votre ID de locataire, votre ID client et votre clé. Vous en avez besoin pour obtenir le jeton d’accès Azure AD que vous allez utiliser dans les appels à l’API de soumission au Microsoft Store.

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>Associer une application Azure AD à votre compte Espace partenaires

Pour utiliser l’API de soumission de l’Espace partenaires, vous devez associer une application Azure AD à votre compte Espace partenaires, récupérer l’ID de locataire ainsi que l’ID client de l’application, et générer une clé. L’application Azure AD représente l’application ou le service à partir duquel vous appellerez l’API de soumission de l’Espace partenaires. Vous avez besoin de l’ID de locataire, de l’ID client et de la clé pour obtenir un jeton d’accès Azure AD à passer à l’API.

> [!NOTE]
> Vous ne devez effectuer cette tâche qu’une seule fois. Une fois que vous les avez, vous pouvez réutiliser l’ID tenant, l’ID client et la clé chaque fois que vous devez créer un jeton d’accès Azure AD.

1. Dans l’Espace partenaires, [associez le compte Espace partenaires de votre organisation](manage-tenants.md) à l’annuaire Azure AD de votre organisation.

1. Dans la page **Utilisateurs** de la section **Paramètres de compte** de l’Espace partenaires, [ajoutez l’application Azure AD](manage-aad-apps.md) représentant l’application ou le service que vous allez utiliser pour accéder aux soumissions de votre compte Espace partenaires. Veillez à attribuer à cette application le rôle **Gestionnaire**. Si l’application n’existe pas encore dans votre annuaire Azure AD, [créez une application Azure AD](manage-aad-apps.md#add-new-azure-ad-applications) dans l’Espace partenaires.

1. Retournez à la page **Utilisateurs**, sélectionnez le nom de votre application Azure AD pour accéder à ses paramètres, puis copiez les valeurs de l’**ID de locataire** et de l’**ID client**.

1. Sélectionnez **Ajouter une nouvelle clé**. Sur l’écran suivant, copiez la valeur de la **Clé**. Vous ne pourrez plus accéder à cette information une fois que vous aurez quitté cette page. Pour plus d’informations, voir [Gérer les clés pour une application Azure AD](manage-aad-apps.md#manage-keys-for-an-azure-ad-application).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Étape 2 : Obtenir un jeton d’accès Azure AD

Avant d’appeler les méthodes de l’API de soumission de l’Espace partenaires, vous devez d’abord obtenir un jeton d’accès Azure AD à passer à l’en-tête **Authorization** de chacune des méthodes de l’API. Un jeton d’accès expire 60 minutes après son émission. Vous pouvez ensuite l’actualiser pour pouvoir l’utiliser dans les prochains appels à l’API.

Pour obtenir le jeton d’accès, suivez les instructions de la section [Appels de service à service à l’aide des informations d’identification du client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) pour envoyer une requête `HTTP POST` au point de terminaison `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Voici un exemple de requête :

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Pour la valeur tenant_id dans l’URI POST ainsi que pour les paramètres client_id et client_secret, spécifiez l’ID de locataire, l’ID client et la clé de l’application, que vous avez récupérés à partir de l’Espace partenaires au cours de la section précédente. Pour le paramètre resource, vous devez spécifier https://api.partner.microsoft.com.

### <a name="step-3-use-the-partner-center-submission-api"></a>Étape 3 : Utiliser l’API de soumission de l’Espace partenaires

Une fois que vous disposez d’un jeton d’accès Azure AD, appelez les méthodes de l’API de soumission de l’Espace partenaires. Pour créer ou mettre à jour des soumissions, on appelle généralement plusieurs méthodes de l’API de soumission de l’Espace partenaires dans un ordre spécifique. Pour plus d’informations sur les différents scénarios et la syntaxe de chaque méthode, consultez l’[API d’ingestion](https://apidocs.microsoft.com/services/partneringestion/) dans Swagger.

## <a name="next-steps"></a>Étapes suivantes

- Commencez à utiliser l’API de soumission de l’Espace partenaires comme indiqué ci-dessus
