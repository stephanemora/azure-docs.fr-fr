---
title: Exposer des API à partir de fonctions à l’aide du service Gestion des API Azure
description: Créez une définition OpenAPI permettant aux autres applications et services d’appeler votre fonction dans Azure.
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: ec25b03b3d93fcd69297899653279fea86eabb02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375672"
---
# <a name="expose-serverless-apis-from-http-endpoints-using-azure-api-management"></a>Exposer des API serverless à partir de points de terminaison HTTP à l’aide du service Gestion des API Azure

Azure Functions s’intègre au service Gestion des API Azure dans le portail pour vous permettre d’exposer vos points de terminaison de fonction de déclencheur HTTP en tant qu’API REST. Les API REST sont souvent décrites à l’aide d’une définition OpenAPI. Ce fichier JSON (ou YAML) contient des informations sur les opérations disponibles dans une API. Il inclut des informations sur la façon de structurer les données de requête et de réponse pour l’API. En intégrant votre application de fonction, vous pouvez faire en sorte que le service Gestion des API génère ces définitions OpenAPI.  

Cet article explique comment intégrer votre application de fonction au service Gestion des API. Cette intégration fonctionne pour les applications de fonction développées dans n’importe quel [langage pris en charge](supported-languages.md). Vous pouvez également [importer votre application de fonction à partir du service Gestion des API Azure](../api-management/import-function-app-as-api.md).

Pour les fonctions de bibliothèque de classes C#, vous pouvez également [utiliser Visual Studio](openapi-apim-integrate-visual-studio.md) pour créer et publier des API serverless qui s’intègrent au service Gestion des API.  

## <a name="create-the-api-management-instance"></a>Créer une instance du service Gestion des API

Pour créer une instance du service Gestion des API liée à votre application de fonction :

1. Sélectionnez l’application de fonction, choisissez **Gestion des API** dans le menu de gauche, puis sélectionnez **Créer nouveau** sous **Gestion des API**.

    :::image type="content" source="media/functions-openapi-definitions/select-all-settings-openapi.png" alt-text="Choisir Gestion des API":::


1. Utilisez les paramètres de Gestion des API de la manière spécifiée dans le tableau suivant :

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom** | Nom globalement unique | Un nom est généré sur la base du nom de votre application de fonction. |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel cette nouvelle ressource est créée. |  
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Même ressource que votre application de fonction, qui doit être définie pour vous. |
    | **Lieu** | Emplacement du service | Nous vous conseillons de choisir le même emplacement que votre application de fonction. |
    | **Nom de l’organisation** | Contoso | Nom de l’organisation utilisé dans le portail des développeurs et pour les notifications par e-mail. |
    | **E-mail de l’administrateur** | votre e-mail | E-mail ayant reçu les notifications système de la Gestion des API. |
    | **Niveau tarifaire** | Consommation | Le niveau Consommation n’est pas disponible dans toutes les régions. Pour des informations tarifaires complètes, voir la [page de tarification de la Gestion des API](https://azure.microsoft.com/pricing/details/api-management/). |

    ![Créer un service de Gestion des API](media/functions-openapi-definitions/new-apim-service-openapi.png)

1. Choisissez **Créer** pour créer l’instance de Gestion des API, ce qui peut prendre plusieurs minutes.

1. Une fois qu’Azure a créé l’instance, l’option **Activer Application Insights** est disponible sur la page. Sélectionnez-la pour envoyer les journaux au même emplacement que l’application de fonction.

## <a name="import-functions"></a>Importer des fonctions

Une fois l’instance du service Gestion des API créée, vous pouvez importer les points de terminaison de vos fonctions déclenchées par HTTP. Cet exemple importe un point de terminaison nommé TurbineRepair.   

1. Dans la page Gestion des API, sélectionnez **Lier l’API**.

1. La fenêtre **Importer Azure Functions** s’ouvre avec la fonction **TurbineRepair** en surbrillance. Cliquez sur **Sélectionner** pour continuer.

    ![Importer Azure Functions dans Gestion des API](media/functions-openapi-definitions/import-function-openapi.png)

1. Dans la page **Créer à partir de Function App**, acceptez les valeurs par défaut, puis sélectionnez **Créer**.

    :::image type="content" source="media/functions-openapi-definitions/create-function-openapi.png" alt-text="Créer à partir de Function App":::

    Azure crée l’API pour la fonction.

## <a name="download-the-openapi-definition"></a>Télécharger la définition OpenAPI

Une fois vos fonctions importées, vous pouvez télécharger la définition OpenAPI à partir de l’instance du service Gestion des API.

1. Sélectionnez **Télécharger la définition OpenAPI** en haut de la page.
   
   ![Télécharger la définition OpenAPI](media/functions-openapi-definitions/download-definition.png)

2. Enregistrez le fichier JSON téléchargé, puis ouvrez-le. Vérifiez la définition.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant affiner la définition dans la page Gestion des API du portail. Vous pouvez aussi [en savoir plus sur la Gestion des API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Modifier la définition OpenAPI dans la Gestion des API](../api-management/edit-api.md)
