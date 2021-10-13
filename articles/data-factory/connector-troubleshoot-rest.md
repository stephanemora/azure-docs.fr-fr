---
title: Résoudre les problèmes liés au connecteur REST
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes avec le connecteur REST liés aux connecteurs dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390570"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>Dépanner le connecteur REST dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur REST dans Azure Data Factory et Azure Synapse.

## <a name="error-code-restsinkcallfailed"></a>Code d’erreur : RestSinkCallFailed

- **Message** : `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Cause** : Cette erreur se produit quand une fabrique de données ou un pipeline Synapse communique avec le point de terminaison REST via le protocole HTTP et que l’opération de requête échoue.

- **Recommandation** : Vérifiez le code d’état HTTP ou le message dans le message d’erreur et corriger le problème du serveur distant.

## <a name="error-code-restsourcecallfailed"></a>Code d’erreur : RestSourceCallFailed

- **Message** : `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **Cause** : Cette erreur se produit quand Azure Data Factory communique avec le point de terminaison REST via le protocole HTTP et que l’opération de requête échoue.

- **Recommandation** : Vérifiez le code d’état HTTP, l’URL de la requête ou la charge utile de la réponse dans le message d’erreur et corrigez le problème du serveur distant.

## <a name="error-code-restsinkunsupportedcompressiontype"></a>Code d’erreur : RestSinkUNSupportedCompressionType

- **Message** : `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **Recommandation** : Vérifiez les types de compression pris en charge pour le récepteur REST.

## <a name="unexpected-network-response-from-the-rest-connector"></a>Réponse réseau inattendue du connecteur REST

- **Symptômes** : Le point de terminaison reçoit parfois une réponse inattendue (400, 401, 403, 500) du connecteur REST.

- **Cause** : Le connecteur source REST utilise l’URL et la méthode/l’en-tête/le corps HTTP du service/du jeu de données/de la copie source comme paramètres lors de la construction d’une requête HTTP. Le problème est probablement dû à des erreurs dans un ou plusieurs paramètres spécifiés.

- **Résolution** : 
    - Utilisez 'curl' dans une fenêtre d’invite de commandes pour vérifier si le paramètre est la cause ou non (les en-têtes **Accept** et **User-Agent** doivent toujours être inclus) :
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Si la commande retourne la même réponse inattendue, corrigez les paramètres ci-dessus en utilisant 'curl' jusqu’à obtenir la réponse attendue. 

      Vous pouvez également utiliser 'curl--help' pour exécuter des options plus avancées de la commande.

    - Si seul le connecteur REST retourne une réponse inattendue, contactez le support technique de Microsoft pour poursuivre la résolution des problèmes.
    
    - Notez que 'curl' peut ne pas convenir pour reproduire le problème de validation du certificat SSL. Dans certains scénarios, la commande 'curl' a été exécutée avec succès sans rencontrer de problème de validation du certificat SSL. Toutefois, lorsque la même URL est exécutée dans un navigateur, aucun certificat SSL n’est réellement retourné pour permettre au client d’établir une relation de confiance avec le serveur.

      Les outils comme **Postman** et **Fiddler** sont recommandés dans le cas ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
