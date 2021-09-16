---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 4f75dc57d6f94983b390d45de657029dd157cf64
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123483931"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Comment répond le serveur proxy API Management avec les certificats SSL durant l’établissement d'une liaison TLS

### <a name="clients-calling-with-server-name-indication-sni-header"></a>Clients appelant avec l’en-tête Indication du nom du serveur (SNI)
Si vous avez un ou plusieurs domaines personnalisés configurés pour le proxy (passerelle), API Management peut répondre aux requêtes HTTPS à partir des deux éléments suivants :
* Domaine personnalisé (par exemple, `contoso.com`)
* Domaine par défaut (par exemple, `apim-service-name.azure-api.net`). 

En fonction des informations contenues dans l’en-tête SNI, API Management répond avec le certificat de serveur approprié.

### <a name="clients-calling-without-sni-header"></a>Clients appelant sans l’en-tête SNI
Si vous utilisez un client qui n’envoie pas l’en-tête [SNI](https://tools.ietf.org/html/rfc6066#section-3), API Management crée des réponses en fonction de la logique suivante :

* **Si le service n’a qu’un seul domaine personnalisé configuré pour le proxy**, le certificat par défaut est le certificat émis pour le domaine personnalisé du proxy.
* **Si le service a configuré plusieurs domaines personnalisés pour le proxy (pris en charge aux niveaux **Développeur** et **Premium**)** , vous pouvez désigner le certificat par défaut en définissant la propriété [defaultSslBinding](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) sur True (« defaultSslBinding » : « true »). Si vous ne définissez pas la propriété, le certificat par défaut est le certificat émis pour le domaine du proxy par défaut hébergé sur `*.azure-api.net`.

## <a name="support-for-putpost-request-with-large-payload"></a>Prise en charge pour la requête PUT/POST avec une charge utile de grande taille

Le serveur proxy API Management prend en charge les requêtes avec des charges utiles de grande taille (> 40 Ko) lors de l’utilisation de certificats côté client dans le protocole HTTPS. Pour éviter que la requête du serveur ne gèle, les clients peuvent définir la propriété [« negotiateClientCertificate » : « true »](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) sur le nom d’hôte du proxy. 

Si la propriété est définie sur true, le certificat client est demandé au moment de la connexion SSL/TLS, avant tout échange de requête HTTP. Étant donné que le paramètre s’applique au niveau du **Nom d’hôte du proxy** toutes les demandes de connexion demandent le certificat client. Vous pouvez contourner cette limitation et configurer jusqu’à 20 domaines personnalisés pour le proxy (uniquement pris en charge au niveau **Premium**).
