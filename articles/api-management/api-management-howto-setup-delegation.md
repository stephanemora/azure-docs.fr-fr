---
title: Délégation de l'inscription des utilisateurs et des abonnements aux produits
description: Découvrez comment déléguer à un tiers l'enregistrement de l'utilisateur et la souscription à des produits dans la gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 796bea3c64ef7fc03367707461d13e0ea2514b8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657738"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Délégation de l'inscription des utilisateurs et des abonnements aux produits

Délégation vous permet d’utiliser votre site Web existant pour la gestion des développeurs de connexion / s’inscrire et abonnement aux produits, au lieu d’utiliser la fonctionnalité intégrée dans le portail des développeurs. Ceci permet à votre site web de conserver les données utilisateur et de valider ces étapes de façon personnalisée.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Délégation des développeurs de se connecter et de s’inscrire

Pour déléguer le développeur de connexion et vous inscrire à votre site Web existant, vous devez créer un point de terminaison de délégation spécifique sur votre site. Il doit agir en tant que le point d’entrée pour cette demande initiée à partir du portail des développeurs gestion des API.

Le processus final se présente comme suit :

1. Le développeur clique sur la connexion ou inscription lien sur le portail des développeurs gestion des API
2. Le navigateur est redirigé vers le point de terminaison de délégation.
3. Point de terminaison de délégation en retour redirige vers présente une interface ou demandant à l’utilisateur pour se connecter ou s’inscrire
4. Si l'authentification est réussie, l'utilisateur revient à la page du portail des développeurs Gestion des API d'où il est parti.

Pour commencer, configurons Gestion des API pour que les demandes soient acheminées via votre point de terminaison de délégation. Dans le portail des éditeurs de la gestion des API, cliquez sur **Sécurité**, puis sur l’onglet **Délégation**. Cliquez sur la case à cocher pour activer « Déléguer de connexion & s’inscrire ».

![Delegation page][api-management-delegation-signin-up]

* Décidez de l'URL de votre point de terminaison de délégation spécial, puis entrez-la dans le champ **URL du point de terminaison de délégation** . 
* Dans le champ Clé d’authentification de la délégation, entrez le secret utilisé pour calculer une signature qui vous sera fournie pour vérification afin de vous assurer que la demande provient bien de Gestion des API Azure. Vous pouvez cliquer sur le bouton **Générer** pour que Gestion des API génère une clé de manière aléatoire pour vous.

À présent, vous devez créer le **point de terminaison de délégation**. Il doit effectuer certaines actions :

1. Recevoir une demande au format suivant :
   
   > *http :\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {URL de la page source} & salt = {chaîne} & sig = {chaîne}*
   > 
   > 
   
    Paramètres de requête pour la connexion / s’inscrire cas :
   
   * **operation** : identifie le type de demande de délégation. Il ne peut s’agir ici que de **SignIn**
   * **returnUrl**: l’URL de la page où l’utilisateur a cliqué sur une connexion ou le lien d’inscription
   * **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
   * **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.
2. Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculez un code de hachage HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **returnUrl** et **salt** ([exemple de code ci-dessous]) :
     
     > HMAC(**salt** + ’\n’ + **returnUrl**)
     > 
     > 
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.
3. Vérifiez que vous recevez une demande pour la connexion / s’inscrire : le **opération** paramètre de requête sera défini sur «**SignIn**».
4. Présenter à l’utilisateur avec l’interface utilisateur pour se connecter ou s’inscrire
5. Si l'utilisateur s'inscrit, vous devez créer un compte dans Gestion des API. [Créez un utilisateur] avec l'API REST de gestion de Gestion des API. Ce cas, veillez à définir l’ID d’utilisateur à la même valeur que dans votre magasin de l’utilisateur ou à un ID que vous pouvez effectuer le suivi de.
6. Lorsque l'utilisateur est bien authentifié :
   
   * [Demandez un jeton d'authentification unique (SSO)] via l'API REST Gestion des API.
   * Ajoutez un paramètre de requête returnUrl à l'URL SSO reçue de l'appel d'API ci-dessus :
     
     > par exemple, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * Redirigez l'utilisateur vers l'URL générée.

En plus de l’opération **SignIn**, vous pouvez également effectuer la gestion des comptes en suivant les étapes précédentes et en utilisant l’une des opérations suivantes :

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Vous devez transmettre les paramètres de requête suivants pour les opérations de gestion de compte.

* **operation**: identifie le type de demande de délégation dont il s’agit (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: l’ID d’utilisateur du compte à gérer
* **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
* **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.

## <a name="delegate-product-subscription"></a>Délégation de l’abonnement aux produits
Délégation de l’abonnement au produit fonctionne de la même façon que la délégation d’utilisateur de connexion/inscription. Le processus final se présente comme suit :

1. Développeur sélectionne un produit dans le portail des développeurs gestion des API et clique sur le bouton d’abonnement.
2. Navigateur est redirigé vers le point de terminaison de délégation.
3. Point de terminaison de délégation effectue les étapes d’abonnement de produit requis. C’est à vous permet de concevoir les étapes. Elles peuvent inclure la redirection vers une autre page pour demander des informations de facturation, poser des questions supplémentaires, ou simplement stocker les informations et ne pas nécessitant une action de l’utilisateur.

Pour activer la fonctionnalité, dans la page **Délégation**, cliquez sur **Déléguer l’abonnement au produit**.

Ensuite, assurez-vous que le point de terminaison de délégation effectue les actions suivantes :

1. Recevoir une demande au format suivant :
   
   > *http :\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {product pour vous abonner aux} & userId = {requête utilisateur} & salt = {chaîne} & sig = {chaîne}*
   >
   
    Paramètres de requête pour le cas abonnement à un produit :
   
   * **operation**: identifie le type de demande de délégation. Pour l'abonnement à un produit, les options valides sont les suivantes :
     * « Subscribe » : une demande d’abonnement de l’utilisateur à un produit donné avec l’identifiant fourni (voir ci-dessous).
     * « Unsubscribe » : une demande de désabonnement de l’utilisateur pour un produit.
     * « Renew » : une demande de renouvellement d’abonnement (par exemple, susceptible d’expirer).
   * **productId**: l’ID du produit auquel l’utilisateur demande l’abonnement
   * **ID d’abonnement**: sur *Unsubscribe* et *Renew* -l’ID de l’abonnement au produit
   * **userId**: l’ID de l’utilisateur de la demande est effectuée pour
   * **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
   * **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.

2. Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculer un hachage HMAC-SHA512 d’une chaîne basée sur le **productId**, **userId**, et **salt** paramètres de requête :
     
     > HMAC(**salt** + ’\n’ + **productId** + ’\n’ + **userId**)
     > 
     > 
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.
3. Traiter l’abonnement au produit en fonction du type d’opération demandé dans **opération** , par exemple, facturation, autres questions, etc.
4. Après avoir correctement abonné l'utilisateur au produit de votre côté, abonnez l'utilisateur au produit Gestion des API en [appelant l'API REST pour l'abonnement au produit].

## <a name="delegate-example-code"></a> Exemple de Code

Ces exemples montrent de code comment à :

* Prendre le *clé de validation de délégation*, laquelle est définie dans l’écran délégation du portail de publication
* Créer un code HMAC, qui est ensuite utilisé pour valider la signature, ce qui prouve la validité de l’élément returnUrl transmis.

Le même code fonctionne pour productId et userId avec de légères modifications.

**Code C# pour générer le hachage de returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Code NodeJS pour générer le hachage de returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la délégation, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Demandez un jeton d'authentification unique (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[Créez un utilisateur]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[appelant l'API REST pour l'abonnement au produit]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[exemple de code ci-dessous]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
