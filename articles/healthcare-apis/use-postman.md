---
title: Accéder au service FHIR des API de santé Azure à l’aide du poste
description: Cet article explique comment accéder au service FHIR des API de santé Azure avec la publication.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: 775c8a4366a381d90f8e24772097249b8cba844a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187591"
---
# <a name="access-using-postman"></a>Accès à l’aide du poste

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, nous allons examiner les étapes d’accès au service FHIR des API de santé (Ecoutez-vous par le service FHIR) avec le [poste](https://www.getpostman.com/).

## <a name="prerequisites"></a>Prérequis

* Service FHIR déployé dans Azure. Pour plus d’informations sur le déploiement du service FHIR, consultez [déployer un service FHIR](fhir/fhir-portal-quickstart.md).
* Une application cliente inscrite pour accéder au service FHIR. Pour plus d’informations sur l’inscription d’une application cliente, consultez [inscrire une application cliente de service dans Azure Active Directory](register-application.md). 
* Autorisations accordées à l’application cliente et à votre compte d’utilisateur, par exemple, « FHIR Data Contributor », pour accéder au service FHIR. 
* Publication installée localement. pour plus d’informations sur la publication, consultez [Prise en main avec le poster](https://www.getpostman.com/).

## <a name="using-postman-create-workspace-collection-and-environment"></a>À l’aide de la publication : créer un espace de travail, une collection et un environnement

Si vous débutez avec la publication, suivez les étapes ci-dessous. Dans le cas contraire, vous pouvez ignorer cette étape.
 
Poster présente le concept d’espace de travail pour vous permettre, à vous et votre équipe, de partager des API, des collections, des environnements et d’autres composants. Vous pouvez utiliser la valeur par défaut « mon espace de travail » ou « espace de travail d’équipe » ou créer un nouvel espace de travail pour vous ou pour votre équipe.
 
[![Capture d’écran de la création d’un espace de travail dans le billet. ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

Ensuite, créez un nouveau regroupement dans lequel vous pouvez regrouper toutes les demandes d’API REST associées. Dans l’espace de travail, sélectionnez **créer des regroupements**. Vous pouvez conserver le nom par défaut **nouveau regroupement** ou le renommer. La modification est enregistrée automatiquement.

[![Capture d’écran de la création d’une collection. ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

Vous pouvez également importer et exporter des regroupements de publications. Pour plus d’informations, consultez [la documentation de la publication](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/).

[![Capture d’écran de l’importation de données. ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>Créer ou mettre à jour des variables d’environnement

Bien que vous puissiez utiliser l’URL complète dans la demande, il est recommandé de stocker l’URL et d’autres données dans des variables et de les utiliser.

Pour accéder au service FHIR, nous devrons créer ou mettre à jour les variables suivantes.

* **tenantid** : locataire Azure dans lequel le service FHIR est déployé. Il se trouve à partir de l’option de menu **vue d’ensemble** de l’inscription d’application.
* **subid** : abonnement Azure dans lequel le service FHIR est déployé. Il se trouve à partir de l’option de menu **vue d’ensemble du service FHIR** .
* **ClientID** : ID d’inscription du client de l’application.
* **clientsecret** – secret d’inscription du client d’application.
* **fhirurl** : URL complète du service FHIR. Par exemple : `https://xxx.azurehealthcareapis.com`. Il se trouve à partir de l’option de menu **vue d’ensemble du service FHIR** .
* **bearerToken** : variable qui stocke le jeton d’accès Azure Active Directory (Azure AD) dans le script. Laissez cette valeur vide.

> [!NOTE]
> Vérifiez que vous avez configuré l’URL de redirection, `https://www.getpostman.com/oauth2/callback` , dans l’inscription de l’application cliente.

[![Capture d’écran de la variable d’environnement. ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>Connecter au serveur FHIR

Ouvrir le billet, sélectionner l' **espace de travail**, le **regroupement** et l' **environnement** que vous souhaitez utiliser. Sélectionnez l' `+` icône pour créer une nouvelle demande. 

[![Capture d’écran de la création d’une nouvelle demande. ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Instruction d’extraction de capacité

Entrez `{{fhirurl}}/metadata` dans la `GET` demande et appuyez sur `Send` . Vous devez voir la déclaration de fonctionnalité du service FHIR.

[![Capture d’écran des paramètres de l’instruction de fonctionnalité. ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![Capture d’écran de la demande d’enregistrement. ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>recevoir un jeton d’accès Azure AD

Le service FHIR est sécurisé par Azure AD. L’authentification par défaut ne peut pas être désactivée. pour accéder au service FHIR, vous devez d’abord recevoir un jeton d’accès Azure AD. pour plus d’informations, consultez [Plateforme d’identités Microsoft des jetons d’accès](./../active-directory/develop/access-tokens.md).

Créer une nouvelle `POST` demande :

1. Entrez dans l’en-tête de la demande : `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. Sélectionnez l’onglet **corps** et sélectionnez **x-www-form-urlencoded**. Entrez les valeurs suivantes dans la section clé et valeur :
    - **grant_type**: `Client_Credentials`
    - **client_id**: `{{clientid}}`
    - **client_secret**: `{{clientsecret}}`
    - **ressource**: `{{fhirurl}}`
    
3. Sélectionnez l’onglet **test** , puis entrez dans la section de texte : `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. Sélectionnez **Enregistrer** pour enregistrer les paramètres.
5. Appuyez sur **Envoyer**. vous devez voir une réponse avec le jeton d’accès Azure AD, qui est automatiquement enregistré dans la variable `bearerToken` . Vous pouvez ensuite l’utiliser dans toutes les demandes de l’API du service FHIR.

  [![Capture d’écran du bouton Envoyer. ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

Vous pouvez examiner le jeton d’accès à l’aide d’outils en ligne tels que [https://jwt.ms](https://jwt.ms) . Sélectionnez l’onglet **revendications** pour afficher les descriptions détaillées de chaque revendication dans le jeton.

[![Capture d’écran des revendications de jeton d’accès. ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>Obtient la ressource FHIR

une fois que vous avez obtenu un jeton d’accès Azure AD, vous pouvez accéder aux données FHIR. Dans une nouvelle `GET` demande, entrez `{{fhirurl}}/Patient` .

Sélectionnez le **jeton du porteur** comme type d’autorisation.  Entrez `{{bearerToken}}` dans la section **jeton** . Sélectionnez **Envoyer**. En guise de réponse, vous devriez voir une liste de patients dans votre ressource FHIR.

[![Capture d’écran de la sélection du jeton du porteur. ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>Créer ou mettre à jour votre ressource FHIR

une fois que vous avez obtenu un jeton d’accès Azure AD, vous pouvez créer ou mettre à jour les données FHIR. Par exemple, vous pouvez créer un nouveau patient ou mettre à jour un patient existant.
 
Créez une nouvelle demande, remplacez la méthode par « poster », puis entrez la valeur dans la section demande.

`{{fhirurl}}/Patient`

Sélectionnez **jeton du porteur** comme type d’autorisation.  Entrez `{{bearerToken}}` dans la section **jeton** . Sélectionnez l’onglet **corps** . Sélectionnez l’option **RAW** et **JSON** en tant que format de corps de texte. Copiez et collez le texte dans la section du corps. 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
Sélectionnez **Envoyer**. Vous devez voir un nouveau patient dans la réponse JSON.

[![Capture d’écran du bouton Envoyer pour créer un nouveau patient. ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>Exporter des données FHIR

une fois que vous avez obtenu un jeton d’accès Azure AD, vous pouvez exporter des données FHIR vers un compte de stockage Azure.

Créer une nouvelle `GET` demande : `{{fhirurl}}/$export?_container=export`

Sélectionnez le **jeton du porteur** comme type d’autorisation.  Entrez `{{bearerToken}}` dans la section **jeton** . Sélectionnez **les en-têtes** pour ajouter deux nouveaux en-têtes :

- **Accepter**: `application/fhir+json`
- **Préférer**:  `respond-async`

Appuyez sur **Envoyer**. Vous devez noter une `202 Accepted` réponse. Sélectionnez l’onglet **en-têtes** de la réponse et prenez note de la valeur dans la **zone Content-Location**. Vous pouvez utiliser la valeur pour interroger l’état de la tâche d’exportation.

[![Capture d’écran de la publication pour créer un nouveau patient 202 réponse acceptée. ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à accéder au service FHIR dans les API de santé Azure avec la publication. Pour plus d’informations sur le service FHIR dans les API de santé Azure, consultez

>[!div class="nextstepaction"]
>[Qu’est-ce que le service FHIR ?](overview.md)
