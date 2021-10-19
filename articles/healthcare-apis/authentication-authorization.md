---
title: Authentification et autorisation des API Azure Healthcare
description: Cet article fournit une vue d’ensemble de l’authentification et de l’autorisation des API de santé.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 53f31790d5b92234bfd35e723e377e682cd13a4d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252515"
---
# <a name="authentication--authorization-for-the-healthcare-apis-preview"></a>Authentification & autorisation pour les API de santé (version préliminaire)

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cet article fournit une vue d’ensemble du processus d’authentification et d’autorisation pour accéder aux services des API de santé.

## <a name="authentication"></a>Authentification

les api de santé sont un ensemble de services managés sécurisés à l’aide de [Azure Active Directory (Azure AD)](../active-directory/index.yml), un fournisseur d’identité global qui prend en charge [OAuth 2,0](https://oauth.net/2/).

Pour que les services des API de santé accèdent aux ressources Azure, telles que les comptes de stockage et les concentrateurs d’événements, vous devez **activer l’identité gérée par le système** et **accorder les autorisations appropriées** à l’identité gérée. Pour plus d’informations, consultez [identités gérées Azure](../active-directory/managed-identities-azure-resources/overview.md).

Les API de santé ne prennent pas en charge d’autres fournisseurs d’identité. Toutefois, les clients peuvent utiliser leur propre fournisseur d’identité pour sécuriser les applications et leur permettre d’interagir avec les API de santé en gérant les applications clientes et les contrôles d’accès aux données utilisateur.

les applications clientes sont inscrites dans le Azure AD et peuvent être utilisées pour accéder aux api de santé. Les contrôles d’accès aux données utilisateur sont effectués dans les applications ou services qui implémentent la logique métier.

### <a name="application-roles"></a>Rôles d’application

Les utilisateurs authentifiés et les applications clientes des API de santé doivent être accordés avec les rôles d’application appropriés.

Le service FHIR des API de santé fournit les rôles suivants :

* **Lecteur de données FHIR**: peut lire (et Rechercher) des données FHIR.
* **FHIR Data Writer**: peut lire, écrire et supprimer de manière réversible des données FHIR.
* **Exportateur de données FHIR**: peut lire et exporter des données (opérateur $Export).
* **FHIR Data Contributor**: peut effectuer toutes les opérations du plan de données.
* **Convertisseur de données FHIR**: peut utiliser le convertisseur pour effectuer la conversion de données.

Le service DICOM des API de santé fournit les rôles suivants :

* **Propriétaire des données DICOM**: peut lire, écrire et supprimer des données DICOM.
* **Données DICOM lues**: peuvent lire les données DICOM.

le IoT Connector ne nécessite pas de rôles d’application, mais il s’appuie sur le récepteur de données Azure Event Hubs pour récupérer les données stockées dans le Event Hub de l’abonnement du client.

## <a name="authorization"></a>Autorisation

une fois accordé avec les rôles d’application appropriés, les utilisateurs authentifiés et les applications clientes peuvent accéder aux services des api de santé en obtenant un **jeton d’accès valide** émis par Azure AD et effectuer des opérations spécifiques définies par les rôles d’application.
 
* Pour le service FHIR, le jeton d’accès est spécifique au service ou à la ressource.
* Pour le service DICOM, le jeton d’accès est accordé à la `dicom.healthcareapis.azure.com` ressource, et non à un service spécifique.
* pour le IoT Connector, le jeton d’accès n’est pas nécessaire, car il n’est pas exposé aux utilisateurs ou aux applications clientes.

### <a name="steps-for-authorization"></a>Étapes d’autorisation

il existe deux façons courantes d’obtenir un jeton d’accès, présentées en détail dans la documentation de Azure AD : le workflow du [code d’autorisation](../active-directory/develop/v2-oauth2-auth-code-flow.md) et les [informations d’identification du client](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Pour obtenir un jeton d’accès pour les API de santé, Voici les étapes à suivre pour utiliser le **Workflow de code d’autorisation**:

1. **le client envoie une demande au point de terminaison d’autorisation Azure AD.** Azure AD redirige le client vers une page de connexion où l’utilisateur s’authentifie à l’aide des informations d’identification appropriées (par exemple : nom d’utilisateur et mot de passe, ou une authentification à deux facteurs). **Une fois l’authentification réussie, un code d’autorisation est renvoyé au client.** Azure AD autorise uniquement le renvoi de ce code d’autorisation à une URL de réponse inscrite configurée dans l’inscription de l’application cliente.

2. **l’application cliente échange le code d’autorisation pour un jeton d’accès au point de terminaison de jeton Azure AD.** Lors de la demande d’un jeton, l’application cliente devra peut-être fournir une clé secrète client (que vous pouvez ajouter au cours de l’inscription de l’application).
 
3. **Le client envoie une demande aux API de santé**, par exemple une `GET` demande de recherche de tous les patients dans le service FHIR. Lors de la demande, il **comprend le jeton d’accès dans un `HTTP` en-tête de demande**, par exemple **`Authorization: Bearer xxx`** .

4. **Le service API de santé vérifie que le jeton contient des revendications appropriées (propriétés dans le jeton).** S’il est valide, il termine la demande et retourne les données au client.

Dans le **Workflow des informations d’identification du client**, les autorisations sont accordées directement à l’application elle-même. Lorsque l’application présente un jeton à une ressource, la ressource impose que l’application elle-même ait l’autorisation d’effectuer une action, car aucun utilisateur n’est impliqué dans l’authentification. Par conséquent, il est différent du **Workflow de code d’autorisation** des manières suivantes :

- L’utilisateur ou le client n’a pas besoin de se connecter de manière interactive
- Le code d’autorisation n’est pas obligatoire.
- Le jeton d’accès est obtenu directement via les autorisations de l’application.

### <a name="access-token"></a>Access token (Jeton d’accès)

Le jeton d’accès est une collection de propriétés (revendications) signée et encodée en [Base64](https://en.wikipedia.org/wiki/Base64) qui transmettent des informations sur l’identité, les rôles et les privilèges du client accordés à l’utilisateur ou au client.

Les API de santé attendent généralement un [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token). Il se compose de trois parties : 
* En-tête
* Charge utile (les revendications)
* Signature, comme indiqué dans l’image ci-dessous. Pour plus d’informations, consultez [jetons d’accès Azure](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

[![Signature de jeton Web Jason. ](media/azure-access-token.png) ](media/azure-access-token.png#lightbox)

Vous pouvez utiliser des outils en ligne tels que [https://jwt.ms](https://jwt.ms/) pour afficher le contenu d’un jeton. Par exemple, vous pouvez afficher les détails des revendications.

|**Type de revendication**          |**Valeur**               |**Remarques**                               |
|------------------------|------------------------|----------------------------------------|
|aud                     |https://xxx.fhir.azurehealthcareapis.com|Identifie le destinataire du jeton. Dans les jetons `id_tokens`, l'audience est l'ID attribué à votre application dans le portail Azure. Votre application doit valider cette valeur et rejeter le jeton si la valeur ne correspond pas.|
|iss                     |https://sts.windows.net/{tenantid}/|Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié. Si le jeton a été émis par le terminal v2.0, l’URI se termine par `/v2.0`. La partie GUID qui indique que l’utilisateur est un utilisateur consommateur d’un compte Microsoft est `9188040d-6c67-4c5b-b112-36a304b66dad`. Votre application doit utiliser la partie GUID de la revendication pour restreindre l’ensemble des locataires qui peuvent se connecter à l’application, le cas échéant.|
|iat                     |(horodatage)            |« Issued At » (Délivré le) indique quand l'authentification de ce jeton a eu lieu.|
|nbf                     |(horodatage)            |La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit PAS être accepté pour être traité.|
|exp                     |(horodatage)            |La revendication « exp » (délai d'expiration) indique le délai d'expiration à partir duquel le JWT ne doit PAS être accepté pour être traité. Il est important de noter qu’une ressource peut rejeter le jeton avant ce moment également, si par exemple une modification de l’authentification est requise ou si une révocation de jeton a été détectée.|
|aio                     |E2ZgYxxx                |Revendication interne utilisée par Azure AD pour enregistrer des données afin de réutiliser les jetons. Cette valeur doit être ignorée.|
|appid                   |e97e1b8c-xxx            |Il s’agit de l’ID d’application du client qui utilise le jeton. L'application peut agir pour elle-même ou pour le compte d'un utilisateur. L'ID d'application représente généralement un objet d’application, mais elle peut également représenter un objet du principal du service dans Azure AD.|
|appidacr                |1                       |Indique comment le client a été authentifié. Pour un client public, la valeur est « 0 ». Si l’ID client et la clé secrète client sont utilisés, la valeur est « 1 ». Si un certificat client a été utilisé pour l’authentification, la valeur est « 2 ».|
|idp                     |https://sts.windows.net/{tenantid}/|Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur, à moins que le compte d’utilisateur ne se trouve pas dans le même locataire que l’émetteur-invités, par exemple. Si la revendication n’est pas présente, cela signifie que la valeur d’ISS peut être utilisée à la place. pour les comptes personnels utilisés dans un contexte d’organisation (par exemple, un compte personnel invité à un Azure AD locataire), la revendication idp peut être « live.com » ou un URI STS contenant le 9188040d-6c67-4c5b-b112-36a304b66dad de locataire compte Microsoft.|
|oid                     |Par exemple, tenantid         |Il s’agit de l’identificateur immuable d’un objet dans le système d’identité Microsoft, dans le cas présent, un compte d’utilisateur. Cet ID identifie de façon unique l’utilisateur entre les applications : deux applications différentes qui se connectent au même utilisateur reçoivent la même valeur dans la revendication OID. la Microsoft Graph retourne cet id en tant que propriété id pour un compte d’utilisateur donné. Étant donné que l’OID permet à plusieurs applications de corréler les utilisateurs, l’étendue du profil est requise pour recevoir cette revendication. Remarque : si un seul utilisateur existe dans plusieurs locataires, l’utilisateur contiendra un ID d’objet différent dans chaque locataire. ils sont considérés comme différents comptes, même si l’utilisateur se connecte à chaque compte avec les mêmes informations d’identification.|
|RH                       |0. ARoxxx              |Revendication interne utilisée par Azure pour revalider des jetons. Elle doit être ignorée.|
|sub                      |Par exemple, tenantid        |Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. L’objet est un identificateur par paire ; il est spécifique à un ID d’application donné. Par conséquent, si un utilisateur se connecte à deux applications différentes à l’aide de deux ID clients différents, ces applications reçoivent deux valeurs différentes pour la revendication de l’objet. Ceci peut être souhaitable ou non en fonction de vos besoins d’architecture et de confidentialité.|
|tid                      |Par exemple, tenantid        |GUID représentant le client Azure AD d’où provient l’utilisateur. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de client immuable de l’organisation à laquelle appartient l’utilisateur. Pour les comptes personnels, la valeur est 9188040d-6c67-4c5b-b112-36a304b66dad. L’étendue du profil est requise pour recevoir cette revendication.
|uti                      |bY5glsxxx             |Revendication interne utilisée par Azure pour revalider des jetons. Elle doit être ignorée.|
|ver                      |1                     |Indique la version du jeton.|
 
**Par défaut, le jeton d’accès est valide pendant une heure. Vous pouvez obtenir un nouveau jeton ou le renouveler à l’aide du jeton d’actualisation avant qu’il n’expire.**

pour obtenir un jeton d’accès, vous pouvez utiliser des outils tels que poster, l’extension cliente Rest dans Visual Studio Code, PowerShell, CLI, la fonction de boucles et les [bibliothèques d’authentification Azure AD](../active-directory/develop/reference-v2-libraries.md).

## <a name="encryption"></a>Chiffrement

Lorsque vous créez un nouveau service d’API Azure Healthcare, vos données sont chiffrées à l’aide de **clés gérées par Microsoft** par défaut. 

* Le service FHIR fournit le chiffrement des données au repos lorsque les données sont conservées dans le magasin de données.
* Le service DICOM fournit le chiffrement des données au repos lorsque les données d’images, y compris les métadonnées incorporées, sont conservées dans le magasin de données. Lorsque les métadonnées sont extraites et rendues persistantes dans le service FHIR, elles sont chiffrées automatiquement.
* IoT Connector, après le mappage et la normalisation des données, conserve les messages de l’appareil dans le service FHIR, qui est chiffré automatiquement. dans les cas où les messages de l’appareil sont envoyés à Azure event hubs, qui utilise stockage Azure pour stocker les données, les données sont automatiquement chiffrées avec stockage Azure le chiffrement du Service (Azure SSE).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris l’authentification et l’autorisation des API de santé. Pour savoir comment déployer une instance du service API de santé, consultez

>[!div class="nextstepaction"]
>[Déployer un espace de travail API de santé (préversion) à l’aide du portail faisant](healthcare-apis-quickstart.md)
