---
title: 'Tutoriel : Signer et envoyer des requêtes à l’API SMS d’ACS avec Postman'
titleSuffix: An Azure Communication Services tutorial
description: Découvrez comment signer et envoyer des requêtes pour les services ACS avec Postman pour l’envoi d’un message SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c21d8696c0c4edd743aab6cbe7866d27ce46a314
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492606"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Tutoriel : Signer et envoyer des requêtes avec Postman
Dans ce tutoriel, nous allons configurer et utiliser Postman pour effectuer une requête auprès d’Azure Communication Services (ACS) à l’aide du protocole HTTP. À la fin de ce tutoriel, vous aurez envoyé avec succès un message SMS à l’aide des services ACS et de Postman, et pourrez utiliser Postman pour explorer d’autres API dans ACS.

Dans ce tutoriel, nous allons :
> [!div class="checklist"]
> * Téléchargement de Postman
> * Configuration de Postman pour signer des requêtes HTTP
> * Faire une requête à l’API SMS d’ACS pour envoyer un message.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Le compte gratuit vous octroie 200 $ de crédits Azure pour essayer la combinaison de services de votre choix.
- Une ressource Communication Services active et la chaîne de connexion. [Découvrez comment créer une ressource Communication Services](../quickstarts/create-communication-resource.md).
- Un numéro de téléphone ACS qui peut envoyer des SMS, consultez [Obtenir un numéro de téléphone](../quickstarts/telephony-sms/get-phone-number.md) pour en obtenir un.

## <a name="downloading-and-installing-postman"></a>Téléchargement et installation de Postman

Postman est une application de bureau qui est capable d’effectuer des requêtes d’API sur n’importe quelle API HTTP. On l’utilise couramment pour les tests et l’exploration des API. Nous allons télécharger la dernière [version de bureau à partir du site web de Postman](https://www.postman.com/downloads/). Postman dispose de versions pour Windows, Mac et Linux. Vous devez donc télécharger la version appropriée pour votre système d’exploitation. Une fois le téléchargement effectué, ouvrez l’application. Un écran de démarrage s’affiche, vous invitant à vous connecter ou à créer un compte Postman. La création d’un compte est facultative et peut être ignorée en cliquant sur le lien « Ignorer et accéder à l’application ». La création d’un compte entraînera l’enregistrement de vos paramètres de requête d’API sur Postman, ce qui vous permettra ensuite de récupérer vos requêtes sur d’autres ordinateurs.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="L’écran d’accueil de Postman indique la possibilité de créer un compte ou d’ignorer cette étape et d’accéder à l’application.":::

Une fois que vous avez créé un compte ou que vous avez ignoré la création de compte, vous voyez la fenêtre principale de Postman.

## <a name="creating-and-configuring-a-postman-collection"></a>Création et configuration d’une collection Postman

Postman peut organiser les requêtes de nombreuses façons. Aux fins de ce tutoriel, nous allons créer une collection Postman. Pour ce faire, sélectionnez le bouton Collections sur le côté gauche de l’application :

:::image type="content" source="media/postman/collections-tab.png" alt-text="Écran principal de Postman avec l’onglet Collections mis en surbrillance.":::

Une fois le bouton sélectionné, cliquez sur « Créer une collection » pour démarrer le processus de création de collection. Un nouvel onglet s’ouvre dans la zone centrale de Postman. Nommez la collection comme vous le souhaitez. Ici, la collection est nommée « ACS » :

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman avec une collection ACS ouverte et le nom de la collection mis en surbrillance.":::

Une fois que votre collection est créée et nommée, vous pouvez la configurer.

### <a name="adding-collection-variables"></a>Ajout de variables de collection

Pour gérer l’authentification et faciliter les requêtes, nous allons spécifier deux variables de collection au sein de la collection ACS nouvellement créée. Ces variables sont disponibles pour toutes les requêtes au sein de votre collection ACS. Pour commencer à créer des variables, accédez à l’onglet Variables de la collection.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman avec un onglet Variables de la collection ACS.":::

Dans l’onglet Collection, créez deux variables :
- key : cette variable doit être l’une des clés de votre page de clés Azure Communication Services dans le portail Azure. Par exemple : `oW...A==`.
- endpoint : cette variable doit être votre point de terminaison Azure Communication Services, tel qu’affiché sur la page de clés. **Veillez à supprimer le trait oblique en fin de chaîne**. Par exemple : `https://contoso.communication.azure.com`.

Entrez ces valeurs dans la colonne « Valeur initiale » de l’écran Variables. Une fois cela fait, appuyez sur le bouton « Tout conserver », juste au-dessus du tableau à droite. Une fois cela correctement configuré, l’écran de Postman doit ressembler à ceci :

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman avec les variables de la collection ACS Collection configurées correctement.":::

Vous pouvez en apprendre davantage sur les variables en lisant la [documentation de Postman à leur sujet](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Création d’un script de pré-requête

L’étape suivante consiste à créer un script de pré-requête dans Postman. Un script de pré-requête est un script qui s’exécute avant chaque requête Postman et qui peut modifier les paramètres de la requête à votre place. Nous allons l’utiliser pour signer nos requêtes HTTP afin qu’elles puissent être autorisées par les services ACS. Pour plus d’informations sur les conditions requises pour la signature, vous pouvez [lire notre guide sur l’authentification](https://docs.microsoft.com/rest/api/communication/authentication).

Nous allons créer ce script dans la collection de sorte qu’il s’exécute sur n’importe quelle requête de la collection. Pour ce faire, dans l’onglet Collection, cliquez sur le sous-onglet « Script de pré-requête ».

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman avec un sous-onglet Script de pré-requête de la collection ACS sélectionné.":::

Dans ce sous-onglet, vous pouvez créer un script de pré-requête en l’entrant dans la zone de texte ci-dessous. Il peut être plus facile d’écrire cela dans un éditeur de code complet tel que [Visual Studio Code](https://code.visualstudio.com/) avant de le coller ici. Nous allons étudier chaque partie du script de ce tutoriel. N’hésitez pas à passer à la fin si vous souhaitez simplement copier dans Postman et commencer. Commençons par écrire le script.

### <a name="writing-the-pre-request-script"></a>Écriture du script de pré-requête

La première chose à faire est de créer une chaîne de temps universel coordonné (UTC) et de l’ajouter à l’en-tête HTTP « Date ». Nous stockons également cette chaîne dans une variable afin de l’utiliser ultérieurement lors de la signature :

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Nous allons ensuite hacher le corps de la requête à l’aide de l’algorithme SHA 256 et le placer dans l’en-tête `x-ms-content-sha256`. Postman comprend des [bibliothèques standard](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) pour le hachage et la signature à l’échelle globale, nous n’avons donc pas besoin de les installer ou de les exiger :

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

À présent, nous allons utiliser notre variable de point de terminaison précédemment spécifiée pour identifier la valeur de l’en-tête HTTP Host. Nous devons effectuer cette opération, car l’en-tête Host n’est pas défini tant que ce script n’a pas été traité :

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Une fois ces informations créées, nous pouvons générer la chaîne, que nous allons signer pour la requête HTTP, qui se compose de plusieurs des valeurs créées précédemment :

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Enfin, nous devons signer cette chaîne à l’aide de notre clé ACS, puis l’ajouter à notre requête dans l’en-tête `Authorization` :

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Script de pré-requête final

Le script de pré-requête final devrait ressembler à ceci :

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Entrez ou collez le script final dans la zone de texte de l’onglet Script de pré-requête :

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman avec un script de pré-requête de collection ACS entré.":::

Une fois que vous l’avez entré, appuyez sur CTRL + S ou appuyez sur le bouton Enregistrer pour enregistrer le script dans la collection. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Bouton Enregistrer script de pré-requête de Postman.":::

## <a name="creating-a-request-in-postman"></a>Créeation d’une requête dans Postman

Maintenant que tout est configuré, nous sommes prêts à créer une demande ACS dans Postman. Pour commencer, cliquez sur l’icône Plus (+) en regard de la collection ACS :

:::image type="content" source="media/postman/create-request.png" alt-text="Bouton Plus de Postman.":::

Cette opération crée un nouvel onglet pour notre requête dans Postman. Nous devons ensuite procéder à sa configuration. Nous allons faire une requête à l’API d’envoi de SMS. Veillez donc à vous reporter à la [documentation de cette API pour obtenir de l’aide](https://docs.microsoft.com/rest/api/communication/sms/send). Nous allons configurer la requête Postman.

Commencez par définir le type de requête sur `POST`, puis entrez `{{endpoint}}/sms?api-version=2021-03-07` dans le champ URL de la requête. Cette URL utilise la variable `endpoint` créée précédemment pour l’envoyer automatiquement à votre ressource ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Une requête Postman, avec le type défini sur POST et l’URL définie correctement.":::

Sélectionnez maintenant l’onglet Corps de la requête, puis modifiez l’option se trouvant en dessous sur « raw ». Sur la droite, vous verrez une liste déroulante qui indique « Texte », remplacez-la par JSON :

:::image type="content" source="media/postman/postman-json.png" alt-text="Définition du corps de la requête sur raw et JSON":::

Cela permet de configurer la requête d’envoi et de recevoir les informations au format JSON.

Dans la zone de texte ci-dessous, vous devez entrer un corps de requête, qui doit être au format suivant :

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Pour la valeur « de », vous devez [obtenir un numéro de téléphone](../quickstarts/telephony-sms/get-phone-number.md) dans le portail ACS comme mentionné précédemment. Entrez-le sans espace et préfixé par votre indicatif de pays. Par exemple : `+15555551234`. Votre « message » peut être tout ce que vous souhaitez envoyer, mais `Hello from ACS` est un bon exemple. La valeur « à » doit être le numéro d’un téléphone auquel vous avez accès et qui peut recevoir des messages SMS. L’utilisation de votre propre mobile est une bonne idée.

Une fois que vous avez entré ces informations, vous devez enregistrer cette requête dans le collection ACS que nous avons créée précédemment. Cela nous permet de nous assurer qu’elle récupère les variables et le script de pré-requête que nous avons créés précédemment. Pour ce faire, cliquez sur le bouton « Enregistrer » dans le coin supérieur droit de la zone de requête.

:::image type="content" source="media/postman/postman-save.png" alt-text="Bouton Enregistrer pour une requête Postman.":::

Une fenêtre de boîte de dialogue s’affiche et vous demande si vous souhaitez appeler la requête et où vous souhaitez l’enregistrer. Vous pouvez la nommer comme vous le souhaitez, mais veillez à sélectionner votre collection ACS dans la moitié inférieure de la boîte de dialogue :

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="La boîte de dialogue Enregistrer la requête de Postman avec la collection ACS sélectionnée.":::

## <a name="sending-a-request"></a>Envoi d’une requête

Maintenant que tout est configuré, vous devriez être en mesure d’envoyer la requête et de recevoir un message SMS sur votre téléphone. Pour ce faire, assurez-vous que votre demande créée est sélectionnée, puis appuyez sur le bouton « Envoyer » sur la droite :

:::image type="content" source="media/postman/postman-send.png" alt-text="Une requête Postman, avec le bouton Envoyer mis en surbrillance.":::

Si tout s’est bien passé, vous devriez maintenant voir la réponse d’ACS, qui devrait être le code d’état 202 :

:::image type="content" source="media/postman/postman-202.png" alt-text="Une requête Postman correctement envoyée avec un code d’état 202.":::

Le téléphone mobile qui possède le numéro que vous avez fourni pour la valeur « à », devrait également avoir reçu un message SMS. Vous avez maintenant configuré Postman, qui est opérationnel et peut communiquer avec les services ACS et envoyer des SMS.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer les API ACS](https://docs.microsoft.com/rest/api/communication/)
> [En savoir plus sur l’authentification](https://docs.microsoft.com/rest/api/communication/authentication)
> [En savoir plus sur Postman](https://learning.postman.com/)

Vous pouvez également :

- [Ajouter un système de conversation à votre application](../quickstarts/chat/get-started.md)
- [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)
- [En savoir plus sur l’architecture client et serveur](../concepts/client-and-server-architecture.md)
- [En savoir plus sur l’authentification](../concepts/authentication.md)