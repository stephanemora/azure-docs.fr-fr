---
title: Sécuriser les API utilisées en tant que connecteurs d’API dans les flux d’utilisateur d’inscription en libre-service d’Azure AD
description: Sécurisez vos API RESTful personnalisées utilisées en tant que connecteurs d’API dans les flux d’utilisateur d’inscription en libre-service.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9f580c903ccc806ab4cfe5229c4931a4810bf8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481479"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>Sécuriser vos API utilisées en tant que connecteur d’API dans les flux d’utilisateur d’inscription en libre-service d’Azure Active Directory External Identities

Lors de l’intégration d’une API REST dans un flux d’utilisateur d’inscription en libre-service d’Azure Active Directory External Identities, vous devez protéger votre point de terminaison d’API REST en recourant à l’authentification. L’authentification par API REST garantit que seuls les services disposant des informations d’identification appropriées, comme Azure AD, peuvent appeler votre point de terminaison. Cet article explique comment sécuriser une API REST. 

## <a name="prerequisites"></a>Prérequis
Suivez les étapes décrites dans le guide [Procédure pas à pas : Ajouter un connecteur d'API à un flux d'utilisateurs d'inscription](self-service-sign-up-add-api-connector.md).

Vous pouvez protéger votre point de terminaison d’API à l’aide de l’authentification de base HTTP ou de l’authentification par certificat client HTTPS. Dans les deux cas, vous fournissez les informations d’identification qu’Azure AD utilisera lors de l’appel de votre point de terminaison d’API. Votre point de terminaison d'API vérifie ensuite les informations d'identification et prend les décisions relatives aux autorisations.


## <a name="http-basic-authentication"></a>Authentification HTTP de base

L’authentification de base HTTP est définie dans le document [RFC 2617](https://tools.ietf.org/html/rfc2617). L’authentification de base fonctionne comme suit : Azure AD envoie une requête HTTP avec les informations d’identification du client (`username` et `password`) dans l’en-tête `Authorization`. Les informations d'identification sont mises en forme en tant que chaîne `username:password` codée en base64. Votre API est ensuite chargée de vérifier ces valeurs pour prendre d’autres décisions d’autorisation.

Pour configurer un connecteur d'API avec l'authentification de base HTTP, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD**.
3. Sélectionnez **Connecteurs d’API**, puis choisissez le **Connecteur d’API** que vous souhaitez configurer.
4. Dans le champ **Type d'authentification**, sélectionnez **De base**.
5. Entrez le **Nom d'utilisateur** et le **Mot de passe** de votre point de terminaison d'API REST.
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="Configuration d’authentification de base pour un connecteur d’API.":::
6. Sélectionnez **Enregistrer**.

## <a name="https-client-certificate-authentication"></a>Authentification par certificat client HTTPS

L’authentification par certificat client est une authentification mutuelle basée sur un certificat dans laquelle le client, Azure AD, fournit son certificat client au serveur pour prouver son identité. Cela se produit dans le cadre de la négociation SSL. Votre API est chargée de valider les certificats appartenant à un client valide, par exemple Azure AD, et de prendre des décisions d’autorisation. Le certificat client est un certificat numérique X.509. 

> [!IMPORTANT]
> Dans les environnements de production, le certificat doit être signé par une autorité de certification.

### <a name="create-a-certificate"></a>Créer un certificat

#### <a name="option-1-use-azure-key-vault-recommended"></a>Option 1 : utiliser Azure Key Vault (recommandé)

Pour créer un certificat, vous pouvez utiliser [Azure Key Vault](../../key-vault/certificates/create-certificate.md), qui propose des options pour les certificats auto-signés et des intégrations auprès de fournisseurs d'émetteurs de certificats pour les certificats signés. Paramètres recommandés :
- **Objet** : `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Type de contenu** : `PKCS #12`
- **Type d'action de la durée de vie** : `Email all contacts at a given percentage lifetime` ou `Email all contacts a given number of days before expiry`
- **Type de clé** : `RSA`
- **Taille de la clé** : `2048`
- **Clé privée exportable** : `Yes` (afin de pouvoir exporter un fichier `.pfx`)

Vous pouvez ensuite [exporter le certificat](../../key-vault/certificates/how-to-export-certificate.md).

#### <a name="option-2-prepare-a-self-sized-certificate-using-powershell-module"></a>Option 2 : préparer un certificat auto-dimensionné à l’aide du module PowerShell

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>Configurer votre connecteur d'API

Pour configurer un connecteur d'API avec l'authentification par certificat client, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD**.
3. Sélectionnez **Connecteurs d’API**, puis choisissez le **Connecteur d’API** que vous souhaitez configurer.
4. Dans le champ **Type d'authentification**, sélectionnez **Certificat**.
5. Dans la zone **Charger le certificat**, sélectionnez le fichier .pfx de votre certificat avec une clé privée.
6. Dans la zone **Entrer le mot de passe**, saisissez le mot de passe du certificat.
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="Configuration d’authentification par certificat pour un connecteur d’API.":::
7. Sélectionnez **Enregistrer**.

### <a name="perform-authorization-decisions"></a>Prendre des décisions d’autorisation 
Votre API doit implémenter l'autorisation basée sur les certificats clients envoyés afin de protéger les points de terminaison d'API. Pour Azure App Service et Azure Functions, consultez [Configurer l'authentification mutuelle TLS](../../app-service/app-service-web-configure-tls-mutual-auth.md) afin de savoir comment activer et *valider le certificat à partir de votre code d'API*.  Vous pouvez également utiliser la gestion des API Azure en tant que couche devant un service API pour [vérifier les propriétés de certificat client](
../../api-management/api-management-howto-mutual-certificates-for-clients.md) par rapport aux valeurs souhaitées.

### <a name="renewing-certificates"></a>Renouvellement de certificats
Nous vous recommandons de définir des alertes de rappel avant la date d'expiration de votre certificat. Vous devez générer un nouveau certificat et répéter les étapes ci-dessus lorsque les certificats utilisés sont sur le point d’expirer. Pour « roder » l’utilisation d’un nouveau certificat, votre service d’API peut continuer à accepter les anciens et nouveaux certificats pendant un laps de temps limité pendant le déploiement du nouveau certificat. 

Pour charger un nouveau certificat sur un connecteur d'API existant, sélectionnez le connecteur d'API sous **Connecteurs d'API**, puis cliquez sur **Charger un nouveau certificat**. Le dernier certificat chargé qui n’a pas expiré, et dont la date de début est dépassée, sera automatiquement utilisé par Azure AD.

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="Fournir un nouveau certificat à un connecteur d’API lorsqu’il en existe déjà un.":::

## <a name="api-key-authentication"></a>Authentification par clé API

Certains services utilisent un mécanisme de « clé API » pour obfusquer l’accès à vos points de terminaison HTTP pendant le développement en obligeant l’appelant à inclure une clé unique comme un en-tête HTTP ou un paramètre de requête HTTP. Pour [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), vous pouvez effectuer cette opération en incluant `code` comme paramètre de requête dans l’**URL du point de terminaison** de votre connecteur d’API. Par exemple, `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>). 

Ce mécanisme ne doit pas être utilisé seul en production. Par conséquent, la configuration de l'authentification de base ou par certificat est toujours requise. Si vous ne souhaitez implémenter aucune méthode d’authentification (non recommandé) à des fins de développement, vous pouvez choisir l’authentification « de base » dans la configuration du connecteur d’API et utiliser des valeurs temporaires pour `username` et `password`, que votre API peut ignorer pendant l’implémentation de l’autorisation adéquate.

## <a name="next-steps"></a>Étapes suivantes
- Lancez-vous avec nos [exemples de démarrage rapide](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).