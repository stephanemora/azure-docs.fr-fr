---
title: S’authentifier auprès des locataires - Azure Resource Manager
description: Décrit comment Azure Resource Manager gère les requêtes d’authentification entre les locataires.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848766"
---
# <a name="authenticate-requests-across-tenants"></a>Requêtes d’authentification entre locataires

À la création d’une application multilocataire, vous pouvez avoir besoin de gérer des requêtes d’authentification pour des ressources qui se trouvent dans différents locataires. Une machine virtuelle dans un locataire, qui doit se joindre à un réseau virtuel d’un autre locataire, constitue un scénario courant. Azure Resource Manager fournit une valeur d’en-tête pour stocker des jetons auxiliaires, afin d’authentifier les requêtes sur différents locataires.

## <a name="header-values-for-authentication"></a>Valeurs d’en-tête pour l’authentification

La requête est dotée des valeurs d’en-tête d’authentification suivantes :

| Nom de l’en-tête | Description | Exemple de valeur |
| ----------- | ----------- | ------------ |
| Authorization | Jeton principal | JetonPorteur &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | Jetons auxiliaires | JetonPorteur &lt;auxiliary-token1&gt;, JetonPorteurChiffré &lt;auxiliary-token2&gt;, JetonPorteur &lt;auxiliary-token3&gt; |

L’en-tête auxiliaire peut contenir jusqu’à trois jetons auxiliaires. 

Dans le code de votre application multilocataire, obtenez le jeton d’authentification des autres locataires et stockez-les dans les en-têtes auxiliaires. Tous les jetons doivent provenir du même utilisateur ou de la même application. L’utilisateur ou l’application doit avoir été invité(e) dans les autres locataires.

## <a name="processing-the-request"></a>Traitement de la requête

Lorsque votre application envoie une requête à Resource Manager, cette requête est exécutée sous l’identité issue du jeton principal. Le jeton principal doit être valide et non expiré. Ce jeton doit provenir d’un client qui peut gérer l’abonnement.

Lorsque la requête fait référence à une ressource provenant d’un autre locataire, Resource Manager vérifie les jetons auxiliaires pour déterminer si la requête peut être traitée. Tous les jetons auxiliaires dans l’en-tête doivent être valides et non expirés. Si un jeton a expiré, Resource Manager retourne un code de réponse 401. La réponse comprend l’ID client et l’ID de locataire prélevés dans le jeton qui n’est pas valide. Si l’en-tête auxiliaire contient un jeton valide pour le locataire, la requête interlocataire est traitée.

## <a name="next-steps"></a>Étapes suivantes
* Pour savoir comment envoyer des requêtes d’authentification avec les API Azure Resource Manager, consultez [Utiliser l’API d’authentification de Resource Manager pour accéder aux abonnements](resource-manager-api-authentication.md).
* Pour plus d’informations sur les jetons, consultez [Jetons d’accès Azure Active Directory](/azure/active-directory/develop/access-tokens).
