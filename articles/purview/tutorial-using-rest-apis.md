---
title: Utilisation des API REST pour les plans de données Purview
description: Ce tutoriel explique comment utiliser les API REST Azure Purview pour accéder au contenu de votre instance Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/17/2021
ms.openlocfilehash: 5074e0017a5811b9418771aeef3b0883e10891c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212814"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutoriel : Utiliser les API REST

Ce tutoriel montre comment utiliser les API REST Azure Purview. Quiconque souhaite envoyer des données dans une instance Azure Purview, inclure Purview dans le cadre d’un processus automatisé ou créer sa propre expérience utilisateur sur l’instance Purview peut utiliser les API REST pour le faire.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Pour commencer, vous devez disposer d’un compte Azure Purview existant. Si vous n’avez pas de catalogue, consultez le [guide de démarrage rapide pour créer un compte Azure Purview](create-catalog-portal.md).

## <a name="create-a-service-principal-application"></a>Créer un principal de service (application)

Pour qu’un client d’API REST accède au catalogue, le client doit avoir un principal de service (application) et une identité que le catalogue reconnaît et approuve conformément à sa configuration. Lorsque vous adressez au catalogue des appels d’API REST, ils utilisent l’identité du principal de service.

Les clients qui ont utilisé des principaux de service existants (ID d’application) ont connu un taux élevé d’échecs. Par conséquent, nous vous recommandons de créer un nouveau principal de service pour appeler les API.

Pour créer un principal de service :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le portail, recherchez et sélectionnez **Azure Active Directory**.
1. Dans la page **Azure Active Directory**, sélectionnez **Inscriptions des applications** dans le volet de gauche.
1. Sélectionnez **Nouvelle inscription**.
1. Dans la page **Inscrire une application** :
    1. Entrez un **nom** pour l’application (nom du principal de service).
    1. Sélectionnez **Comptes dans cet annuaire d’organisation uniquement ( _&lt;nom de votre locataire&gt;_ uniquement - Locataire unique)** .
    1. Pour **URI de redirection (facultatif)** , sélectionnez **Web** et entrez une valeur. Cette valeur n’a pas besoin d’être un URI valide.
    1. Sélectionnez **Inscription**.
1. Dans la page du nouveau principal de service, copiez les valeurs des paramètres **Nom d’affichage** et **ID d’application (client)** à enregistrer pour plus tard.

   L’ID d’application est la valeur `client_id` dans l’exemple de code.

Pour utiliser le principal de service (application), vous devez obtenir son mot de passe. Voici comment faire :

1. Dans le portail Azure, recherchez et sélectionnez **Azure Active Directory**, puis sélectionnez **Inscriptions d’applications** dans le volet de gauche.
1. Sélectionnez votre principal de service (application) dans la liste.
1. Sélectionnez **Certificats et secrets** dans le volet de gauche.
1. Sélectionnez **Nouveau secret client**.
1. Dans la page **Ajouter un secret client**, entrez une **description**, sélectionnez un délai d’expiration sous **Expire**, puis sélectionnez **Ajouter**.

   Dans la page **Secrets client**, la chaîne dans la colonne **Valeur** de votre nouveau secret est votre mot de passe. Enregistrez cette valeur.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Capture d’écran montrant un secret client":::

## <a name="set-up-authentication-using-service-principal"></a>Configurer l’authentification à l’aide d’un principal de service

Une fois le principal de service créé, vous devez affecter des rôles de plan de données de votre compte Purview au principal de service créé ci-dessus. Il convient de suivre les étapes ci-dessous pour attribuer un rôle afin d’établir l’approbation entre le principal de service et le compte Purview.

1. Accédez à votre instance [Purview Studio](https://web.purview.azure.com/resource/).
1. Sélectionnez Data Map dans le volet de navigation de gauche.
1. Sélectionnez Collections.
1. Sélectionnez la collection racine dans le menu Collections. Il s’agit de la collection supérieure dans la liste, qui a le même nom que votre compte Purview.
1. Sélectionnez l’onglet Attributions de rôles.
1. Affectez les rôles suivants au principal de service créé ci-dessus pour accéder à divers plans de données dans Purview.
    1. Rôle « Conservateur de données » pour accéder au plan de données Catalogue.
    1. Rôle « Administrateur de sources de données » pour accéder au plan de données Analyse. 
    1. Rôle « Administrateur de collections » pour accéder au plan de données Compte.
    1. Rôle « Administrateur de collections » pour accéder au plan de données Stratégie de métadonnées.

> [!Note]
> Seul un administrateur de collections peut affecter des rôles de plan de données dans Purview : [Contrôle d’accès dans Azure Purview](./catalog-permissions.md).

## <a name="get-token"></a>Obtenir un jeton
Vous pouvez envoyer une demande POST à l’URL suivante pour obtenir un jeton d’accès.

https://login.microsoftonline.com/{your-tenant-id}/oauth2/token

Les paramètres suivants doivent être transmis à l’URL ci-dessus.

- **client_id** : ID client de l’application inscrite dans Azure Active Directory, affecté à un rôle de plan de données pour le compte Purview.
- **client_secret** : secret client créé pour l’application ci-dessus.
- **grant_type** : ce doit être « client_credentials ».
- **resource** : ce doit être « https://purview.azure.net  ».
 
Voici un exemple de token de réponse :

```json
    {
        "token_type": "Bearer",
        "expires_in": "86399",
        "ext_expires_in": "86399",
        "expires_on": "1621038348",
        "not_before": "1620951648",
        "resource": "https://purview.azure.net",
        "access_token": "<<access token>>"
    }
```

Utilisez le jeton d’accès ci-dessus pour appeler les API de plan de données.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les sources de données](manage-data-sources.md)
> [API REST de plan de données Purview](/rest/api/purview/)
