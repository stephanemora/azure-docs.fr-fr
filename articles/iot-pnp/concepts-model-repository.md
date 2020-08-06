---
title: Comprendre les concepts du référentiel de modèles IoT Azure | Microsoft Docs
description: En tant que développeur de solutions ou professionnel en informatique, découvrez les concepts de base du référentiel de modèles IoT Azure.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d736721e2676a42da90aead3144f8016329f730
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475496"
---
# <a name="azure-iot-model-repository"></a>Référentiel de modèles IoT Azure

Le référentiel de modèles IoT Azure permet aux fabricants d’appareils de gérer et de partager les modèles d’appareil IoT Plug-and-Play. Les modèles d’appareil sont des documents LD JSON définis à l’aide du [langage DTDL (Digital Twins Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Les modèles stockés dans le service de référentiel de modèles peuvent être partagés avec les développeurs de solutions en privé par le biais du contrôle d’accès ou publiquement, sans nécessiter d’authentification pour intégrer et développer la solution cloud IoT Plug-and-Play.

Vous pouvez accéder au référentiel de modèles à l’aide des outils suivants :

- Portail du [référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo)
- [API REST du référentiel de modèles IoT Azure](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Commandes du référentiel de modèles IoT Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest)

## <a name="public-models"></a>Modèles publics

Les modèles de jumeaux numériques publics stockés dans le référentiel de modèles sont librement accessibles pour que tous les utilisent et les intègrent à leur application sans aucune authentification. En outre, les modèles publics permettent aux fabricants d’appareils et aux développeurs de solutions de partager et de réutiliser leurs modèles d’appareils IoT Plug-and-Play dans un écosystème ouvert.

Pour obtenir des instructions sur la publication d’un modèle dans le référentiel de modèles afin de le rendre public, reportez-vous à la section [Publier un modèle](#publish-a-model) sous la rubrique **Modèles d’entreprise**.

Pour consulter un modèle public à l’aide du portail du référentiel de modèles :

1. Accédez au [portail du référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo).

1. Sélectionnez **Afficher les modèles publics**.

    ![Afficher les modèles publics](./media/concepts-model-repository/public-models.png)

Pour afficher un modèle public par programmation à l’aide de l’API REST, consultez la documentation relative à l’API REST [Get Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync).

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Pour afficher un modèle public à l’aide de l’interface CLI, consultez la commande Azure CLI [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show).

## <a name="company-models"></a>Modèles d’entreprise

Le référentiel de modèles d’entreprise est un locataire dans le référentiel de modèles IoT Azure pour que votre organisation puisse créer et gérer des modèles de jumeaux numériques rédigés par des utilisateurs au sein de votre entreprise ou organisation. Les modèles d’entreprise ne sont accessibles qu’aux utilisateurs authentifiés de votre entreprise ou organisation. Un administrateur client du référentiel de modèles peut attribuer des autorisations et contrôler l’accès des autres utilisateurs de l’entreprise ou de l’organisation aux modèles du référentiel de modèles d’entreprise.

### <a name="set-up-your-company-model-repository"></a>Configurer votre référentiel de modèles d’entreprise

Utilisez votre *compte Azure Active Directory (Azure AD) professionnel ou scolaire* pour accéder au référentiel de modèles. Si votre organisation possède déjà un locataire Azure AD, vous pouvez en utiliser les comptes d’utilisateur et les principaux de service.

Pour savoir comment configurer un locataire Azure AD et créer un utilisateur ou un principal de service dans un locataire Azure AD, consultez la section [Informations supplémentaires](#additional-information).

- Si vous êtes le premier utilisateur de votre organisation à accéder au référentiel de modèles ou à vous connecter au portail, vous recevez le rôle **Administrateur client**. Ce rôle vous permet d’attribuer des rôles à d’autres utilisateurs dans le locataire du référentiel de votre organisation.

- Vous pouvez recevoir d’autres rôles d’un **Administrateur client**, comme **Lire des modèles** ou **Créer des modèles**.

### <a name="understand-access-management"></a>Comprendre la gestion des accès

Le tableau suivant récapitule les capacités prises en charge dans le référentiel de modèles d’entreprise et les autorisations qui leur sont associées :

| Fonctionnalité  | Autorisation| Description|
|-------------|-----------|------------|
|Lire des modèles|Lire des modèles|Par défaut, tous les utilisateurs du locataire de l’entreprise peuvent consulter leurs modèles d’entreprise. En outre, l’utilisateur peut également consulter les modèles privés partagés par d’autres entreprises.|
|Gérer l’accès|Gérer l’accès|Gérez l’attribution de rôle d’utilisateur (ajout ou suppression) pour les autres utilisateurs de l’organisation.|
|Créer des modèles|Créer des modèles|Créez des modèles dans le référentiel de modèles d’entreprise.|
|Publier des modèles|Publier des modèles|Publiez des modèles pour les rendre publics afin que quiconque puisse les consulter.|

Le tableau suivant récapitule les rôles pris en charge et leurs capacités dans le référentiel de modèles qui peuvent être utilisés pour la gestion des accès.

|Role|Fonctionnalité|
|----|----------|
|TenantAdministrator|Gérer l’accès, Lire des modèles|
|Creator|Créer des modèles, Lire des modèles|
|Publisher|Publier des modèles, Lire des modèles|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Passage d’un jeton de sécurité lors de l’accès aux modèles d’entreprise avec une API REST

Lorsque vous appelez les API REST pour gérer des modèles d’entreprise privés ou partagés, vous devez fournir un jeton d’autorisation pour l’utilisateur ou le principal du service au format JWT. Consultez la section [Informations supplémentaires](#additional-information) pour savoir comment obtenir un jeton JWT pour un utilisateur ou un principal de service.

Le jeton JWT doit être passé dans l’en-tête d’autorisation HTTP de l’API lors du ciblage des modèles d’entreprise ou des modèles partagés. Le jeton JWT n’est pas nécessaire pour cibler des modèles publics.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Consulter des modèles d’entreprise ou partagés

Pour lire un modèle, vous devez avoir le rôle *Lecteur* du locataire du référentiel ou le modèle doit être partagé avec vous. Vous pouvez voir la liste des modèles non publiés qui ont été partagés avec vous ainsi que la liste des modèles publiés qui ont été partagés avec vous. Par défaut, les utilisateurs peuvent lire les modèles de leur entreprise, les modèles qui ont été partagés avec eux par d’autres entreprises et tous les modèles publics.

Pour consulter un modèle d’entreprise ou partagé à l’aide du portail :

1. Connectez-vous au [portail du référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo).

1. Développez **Modèles d’entreprise** – **Non publiés** dans le volet gauche.

    ![Consulter des modèles de société](./media/concepts-model-repository/view-company-models.png)

1. Développez **Modèles partagés – Non publiés** dans le volet gauche.

    ![Consulter des modèles partagés](./media/concepts-model-repository/view-shared-models.png)

Pour consulter un modèle d’entreprise ou partagé à l’aide de l’API REST, consultez la documentation relative à l’API REST [Get Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync). Pour plus d’informations sur la transmission d’un en-tête d’autorisation JWT dans la requête HTTP, consultez [Passage d’un jeton de sécurité lors de l’accès aux modèles d’entreprise avec une API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api).

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Pour afficher un modèle d’entreprise ou un modèle partagé à l’aide de l’interface CLI, consultez la commande Azure CLI [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show).

### <a name="manage-roles"></a>Gérer les rôles

L’administrateur client peut attribuer des rôles aux utilisateurs dans le locataire de référentiel afin qu’ils puissent créer des modèles privés pour l’entreprise ou l’organisation, publier des modèles ou gérer des rôles pour d’autres utilisateurs.

Pour ajouter un utilisateur à un rôle du locataire de référentiel de modèles à l’aide du portail :

1. Connectez-vous au [portail du référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo).

1. Sélectionnez **Gestion de l’accès** dans le volet gauche, puis sélectionnez **+Ajouter**. Dans le volet **Ajouter une autorisation**, saisissez l’adresse professionnelle de l’utilisateur que vous souhaitez ajouter au rôle :

    ![Ajouter une adresse professionnelle](./media/concepts-model-repository/add-user.png)

1. Sélectionnez le rôle auquel vous souhaitez ajouter l’utilisateur dans la liste déroulante **Rôle**. Sélectionnez ensuite **Enregistrer** :

    ![Choisir un rôle](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Charger un modèle

Pour charger un modèle dans le référentiel de modèles d’entreprise, vous devez avoir le rôle **Créateur** du locataire du référentiel.

Ces modèles ne sont pas publiés et sont accessibles uniquement par les utilisateurs au sein de votre organisation par défaut. Vous pouvez également partager un ou plusieurs modèles non publiés avec des utilisateurs externes.

Les modèles chargés ne sont pas modifiables.

Les ID de modèle de ces modèles doivent être globalement uniques sur tous les locataires de référentiel pour tous les modèles chargés.

Pour charger un modèle à l’aide du portail :

1. Connectez-vous au [portail du référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo).

1. Développez **Modèles d’entreprise** dans le volet gauche, puis sélectionnez **Créer un modèle**. Sélectionnez ensuite **Importer JSON**.

    ![Créer un modèle](./media/concepts-model-repository/create-model.png)

1. Sélectionnez les fichiers à charger. Si le portail valide votre modèle, sélectionnez **Enregistrer**.

Pour charger un modèle à l’aide de l’API REST, consultez l’API [Create a Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync). Pour plus d’informations sur la transmission d’un en-tête d’autorisation JWT dans la requête HTTP, consultez [Passage d’un jeton de sécurité lors de l’accès aux modèles d’entreprise avec une API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api).

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

Pour charger un modèle à l’aide de l’interface CLI, consultez la commande Azure CLI [Create a Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create).

### <a name="publish-a-model"></a>Publier un modèle

Pour publier un modèle, les conditions suivantes doivent être remplies :

1. Votre organisation doit être membre de [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) pour publier un modèle. Pour créer un compte Espace partenaires, consultez [Créer un compte Espace partenaires](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). Une fois votre compte approuvé, vous pouvez publier vos modèles. Pour plus d’informations, consultez le [FAQ de l’Espace partenaires](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. L’utilisateur doit avoir le rôle *Éditeur* du locataire de référentiel.

Les modèles qui sont créés et publiés par des utilisateurs au sein de votre organisation sont visibles en tant que *modèles publiés*. Ces modèles sont publics et accessibles par quiconque sous **Modèles publics**.

Pour publier un modèle à l’aide du portail :

1. Connectez-vous au [portail du référentiel de modèles IoT Azure](https://aka.ms/iotmodelrepo).

2. Développez **Modèles d’entreprise** dans le volet gauche, puis sélectionnez le modèle que vous souhaitez publier. Sélectionnez ensuite **Publier**.

    ![Publier un modèle](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Si vous recevez une notification indiquant que vous n’avez pas d’ID MPN (Microsoft Partner Network), suivez les étapes d’inscription indiquées dans la notification. Pour plus d’informations, consultez les conditions préalables au début de cette section.

Pour publier un modèle à l’aide de l’API REST, consultez la documentation relative à l’API REST [Publish a model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync). Fournissez le paramètre de chaîne de requête `update-metadata=true` pour publier un modèle à l’aide de l’API REST. Pour plus d’informations sur la transmission d’un en-tête d’autorisation JWT dans la requête HTTP, consultez [Passage d’un jeton de sécurité lors de l’accès aux modèles d’entreprise avec une API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api).

Pour publier un modèle à l’aide de l’interface CLI, consultez la commande Azure CLI [Publish a model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish).

### <a name="share-a-model"></a>Partager un modèle

Vous pouvez partager les modèles d’entreprise que vous avez créés avec des utilisateurs d’organisations externes. De cette façon, vous pouvez permettre aux collaborateurs de voir et de développer des solutions avec vos modèles d’entreprise privés.

Par exemple, un fabricant d’appareils peut souhaiter conserver des modèles privés pour l’entreprise ou l’organisation. Certains de ses clients peuvent exiger que les capacités de leur appareil restent confidentielles.

Le partage de modèles entre entreprises ou organisations permet un accès sécurisé aux modèles qui ne sont pas publics.

Pour partager un modèle d’entreprise à l’aide du portail :

- Si vous êtes le créateur d’un modèle, les boutons **Partager** et **Partagé avec** sont actifs lorsque vous consultez le modèle dans la section **Modèles d’entreprise**.

    ![Partager le modèle](./media/concepts-model-repository/share-model.png)

- Pour partager le modèle avec un utilisateur externe, sélectionnez **Partager**. Dans le volet **Partager un modèle**, entrez l’adresse e-mail de l’utilisateur externe et sélectionnez **Enregistrer**.

- Pour afficher les utilisateurs avec lesquels vous avez partagé le modèle, sélectionnez **Partagé avec**.

- Pour cesser de partager le modèle avec un utilisateur spécifique, sélectionnez l’utilisateur dans la liste des utilisateurs dans le volet **Partagé avec**. Sélectionnez ensuite **Supprimer** et confirmez votre choix lorsque vous y êtes invité.

    ![Cesser le partage](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Informations supplémentaires

Les rubriques suivantes peuvent vous être utiles lorsque vous travaillez avec Azure AD :

- Pour créer un locataire Azure AD, voir [Créer un locataire dans Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). La plupart des organisations disposent déjà de locataires Azure AD.

- Pour ajouter des utilisateurs ou des utilisateurs invités à un locataire Azure AD, consultez [Ajouter ou supprimer des utilisateurs à l’aide d’Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Pour ajouter un principal de service à un locataire Azure AD, consultez [Comment utiliser le portail pour créer une application et un principal de service Azure AD pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Pour savoir comment obtenir un jeton JWT à partir d’Azure AD à utiliser lors de l’appel d’API REST, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons maintenant de consulter [Architecture IoT Plug-and-Play](concepts-architecture.md).