---
title: Serveur FHIR Postman dans Azure – API Azure pour FHIR
description: Dans ce didacticiel, nous allons examiner les étapes nécessaires à l’utilisation d’un poster pour accéder à un serveur FHIR. Postman est utile pour déboguer les applications qui accèdent à des API.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 72e5711ca813378e291d48bdaaa5803693d91482
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284020"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Accéder à l’API Azure pour FHIR avec Postman

Une application cliente peut accéder à l’API Azure pour FHIR via une [API REST](https://www.hl7.org/fhir/http.html). Pour envoyer des requêtes, afficher les réponses et déboguer votre application lors de sa création, utilisez un outil de test d’API de votre choix. Dans ce didacticiel, nous allons vous guider tout au long de la procédure d’accès au serveur FHIR à l’aide de [poster](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Prérequis

- Un point de terminaison FHIR dans Azure. 

  Pour déployer l’API Azure pour FHIR (service géré), vous pouvez utiliser le [portail Azure](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)ou [Azure CLI](fhir-paas-cli-quickstart.md).

- Une [application cliente confidentielle](register-confidential-azure-ad-client-app.md) inscrite pour accéder au service FHIR.
- Vous avez accordé des autorisations à l’application cliente confidentielle, par exemple « FHIR Data Contributor », pour accéder au service FHIR. Pour plus d’informations, consultez [configurer Azure RBAC pour FHIR](./configure-azure-rbac.md).
- Postman installé. 
    
  Pour plus d’informations sur la publication, consultez [prise en main du poste de publication](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>Serveur FHIR et informations d’authentification

Pour utiliser le poste de publication, les paramètres d’authentification suivants sont requis :

- L’URL de votre serveur FHIR, par exemple, `https://MYACCOUNT.azurehealthcareapis.com`

- L’élément `Authority` du fournisseur d’identité de votre serveur FHIR, par exemple, `https://login.microsoftonline.com/{TENANT-ID}`

- Configuré `audience` qui est généralement l’URL du serveur FHIR, par exemple `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` ou `https://azurehealthcareapis.com` .

- L' `client_id` ID d’application ou de l' [application cliente confidentielle](register-confidential-azure-ad-client-app.md) utilisée pour accéder au service FHIR.

- Le `client_secret` secret d’application ou de l’application cliente confidentielle.

Enfin, vous devez vérifier que `https://www.getpostman.com/oauth2/callback` est une URL de réponse inscrite pour votre application cliente.

## <a name="connect-to-fhir-server"></a>Se connecter au serveur FHIR

Ouvrez le billet, puis sélectionnez **obtenir** pour effectuer une requête `https://fhir-server-url/metadata` .

![Énoncé de capacité des métadonnées Postman](media/tutorial-postman/postman-metadata.png)

L’URL des métadonnées de l’API Azure pour FHIR est `https://MYACCOUNT.azurehealthcareapis.com/metadata`. 

Dans cet exemple, l’URL du serveur FHIR est `https://fhirdocsmsft.azurewebsites.net` , et l’instruction Capability du serveur est disponible à l’adresse `https://fhirdocsmsft.azurewebsites.net/metadata` . Ce point de terminaison est accessible sans authentification.

Si vous tentez d’accéder à des ressources restreintes, une réponse « échec de l’authentification » se produit.

![Échec de l’authentification](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Obtention d’un jeton d’accès
Sélectionnez **Obtenir un nouveau jeton d’accès**.

Pour obtenir un jeton d’accès valide, sélectionnez **autorisation** , puis sélectionnez **OAuth 2,0** dans le menu déroulant **type** .

![Définir OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Sélectionnez **Obtenir un nouveau jeton d’accès**.

![Demander un nouveau jeton d’accès](media/tutorial-postman/postman-request-token.png)

Dans la boîte de dialogue **récupérer un nouveau jeton d’accès** , entrez les informations suivantes :

| Champ                 | Exemple de valeur                                                                                                   | Commentaire                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (Nom du jeton)            | MYTOKEN                                                                                                         | Nom de votre choix          |
| Grant Type (Type d’autorisation)            | Code d’autorisation                                                                                              |                            |
| Callback URL (URL de rappel)          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| Auth URL (URL d’authentification)              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` a la valeur `https://MYACCOUNT.azurehealthcareapis.com` pour l’API Azure pour FHIR |
| Access Token URL (URL du jeton d’accès)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| Client ID (ID client)             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | ID de l'application             |
| Client Secret (Clé secrète client)         | `XXXXXXXX`                                                                                                      | Clé client secrète          |
| Scope (Étendue) | `<Leave Blank>` | L’étendue n’est pas utilisée ; par conséquent, il peut être laissé vide.  
| État                 | `1234`     | L' [État](https://learning.postman.com/docs/sending-requests/authorization/) est une valeur opaque pour empêcher la falsification de requête intersites. Elle est facultative et peut prendre une valeur arbitraire telle que « 1234 ».                           |
| Client Authentication (Authentification du client) | Envoyer les informations d’identification du client dans le corps                                                                                 |                 

Sélectionnez le **jeton de demande** pour être guidé via le Azure Active Directory le workflow d’authentification, et un jeton sera renvoyé à la publication. En cas d’échec de l’authentification, reportez-vous à la console du poste de publication pour plus d’informations. **Remarque**: dans le ruban, sélectionnez **affichage**, puis sélectionnez **afficher la console du poste**. Le raccourci clavier vers la console poster est **ALT-CTRL + C**.

Faites défiler l’écran pour afficher l’écran du jeton retourné, puis sélectionnez **utiliser le jeton**.

![Utiliser le jeton](media/tutorial-postman/postman-use-token.png)

Reportez-vous au champ **jeton d’accès** pour afficher le jeton qui vient d’être rempli. Si vous sélectionnez **Envoyer** pour répéter la `Patient` recherche de ressource, un **État** `200 OK` est retourné. Un état retourné `200 OK` indique une requête http réussie.

![200 OK](media/tutorial-postman/postman-200-OK.png)

Dans l’exemple de *recherche de patients* , la base de données ne contient aucun patient dans lequel le résultat de la recherche est vide.

Vous pouvez inspecter le jeton d’accès à l’aide d’un outil tel que [JWT.ms](https://jwt.ms). Vous trouverez ci-dessous un exemple de contenu.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Si vous cherchez à résoudre un problème, nous vous recommandons de vérifier en premier lieu que vous disposez de l’audience appropriée (revendication `aud`). Si votre jeton provient de l’émetteur correct ( `iss` revendication) et s’il dispose de l’audience appropriée ( `aud` revendication), mais que vous ne parvenez toujours pas à accéder à l’API FHIR, il est probable que l’utilisateur ou le principal du service ( `oid` revendication) n’a pas accès au plan de données FHIR. Nous vous recommandons d’utiliser [le contrôle d’accès en fonction du rôle (RBAC) Azure](configure-azure-rbac.md) pour assigner des rôles de plan de données aux utilisateurs. Si vous utilisez un locataire Azure Active Directory secondaire externe pour votre plan de données, vous devez [configurer le RBAC local pour](configure-local-rbac.md) les assignations FHIR.

Il est également possible d’obtenir un jeton pour l' [API Azure pour FHIR à l’aide de l’Azure CLI](get-healthcare-apis-access-token-cli.md). Si vous utilisez un jeton obtenu avec le Azure CLI, vous devez utiliser le jeton de *porteur* du type d’autorisation. Collez le jeton directement.

## <a name="inserting-a-patient"></a>Insertion d’un patient

Avec un jeton d’accès valide, vous pouvez maintenant insérer un nouveau patient. Dans le billet, modifiez la méthode en sélectionnant **publication**, puis ajoutez le document JSON suivant dans le corps de la demande.

[!code-json[](../samples/sample-patient.json)]

Sélectionnez **Envoyer** pour déterminer que le patient est correctement créé.

![Capture d’écran montrant que le patient est correctement créé.](media/tutorial-postman/postman-patient-created.png)

Si vous répétez la recherche de patient, vous devriez maintenant voir l’enregistrement du patient.

![Patient créé](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez accédé à l’API Azure pour FHIR à l’aide de la publication. Pour plus d’informations sur les fonctionnalités de l’API Azure pour FHIR, consultez.
 
>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge](fhir-features-supported.md)
