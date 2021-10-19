---
title: Accéder aux API de santé Azure à l’aide du client REST
description: Cet article explique comment accéder aux API de santé à l’aide de l’extension client REST dans VSCode
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042349"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>Accès aux API de santé (version préliminaire) à l’aide de l’extension client REST dans Visual Studio Code

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à accéder aux API de santé à l’aide [de l’extension client Rest dans Visual Studio code](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

## <a name="install-rest-client-extension"></a>Installer l’extension du client REST

sélectionnez l’icône Extensions dans le volet gauche de votre Visual Studio Code et recherchez « Client REST ». Recherchez l’extension et l’installation du [client Rest](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) .

[![Extension ](media/rest-install.png) VSCode du client Rest ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>Créer un `.http` fichier et définir des variables

Créez un nouveau fichier dans Visual Studio Code. Entrez une `GET` ligne de commande de demande dans le fichier, puis enregistrez-la sous `test.http` . Le suffixe de fichier `.http` active automatiquement l’environnement du client Rest. Cliquez sur on `Send Request` pour accéder aux métadonnées. 

[![Envoyer une demande ](media/rest-send-request.png) ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>Obtient les valeurs de l’application cliente

> [!Important]
> Avant d’appeler l’API REST du serveur FHIR (autre que l’obtention des métadonnées), vous devez terminer l’inscription de l' **[application](register-application.md)**. Prenez note de votre ID de **locataire** Azure, de l' **ID client**, de la **clé secrète client** et de l' **URL du service**.

Bien que vous puissiez utiliser des valeurs telles que l’ID client directement dans les appels à l’API REST, il est recommandé de définir quelques variables pour ces valeurs et d’utiliser les variables à la place.

Dans votre `test.http` fichier, incluez les informations suivantes obtenues lors de l’inscription de votre application : 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>recevoir un jeton d’accès Azure AD

Après avoir inclus les informations ci-dessous dans votre `test.http` fichier, appuyez sur `Send Request` . Vous verrez une réponse HTTP qui contient votre jeton d’accès.

La ligne commençant par `@name` contient une variable qui capture la réponse http contenant le jeton d’accès. La variable, `@token` , est utilisée pour stocker le jeton d’accès.

>[!Note] 
>Le `grant_type` de `client_credentials` est utilisé pour obtenir un jeton d’accès.

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![Obtient le jeton ](media/rest-config.png) d’accès ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` Données patients FHIR

Vous pouvez maintenant obtenir une liste de patients ou un patient spécifique avec la `GET` demande. La ligne avec `Authorization` correspond aux informations d’en-tête de la `GET` demande. Vous pouvez également envoyer `PUT` des `POST` demandes ou pour créer/mettre à jour des ressources FHIR.

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[![Recevoir un patient ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>Exécuter PowerShell ou CLI

Vous pouvez exécuter des scripts PowerShell ou CLI dans Visual Studio Code. Appuyez sur `CTRL` la `~` touche et sélectionnez PowerShell ou bash. Vous trouverez plus d’informations sur le [Terminal intégré](https://code.visualstudio.com/docs/editor/integrated-terminal).

### <a name="powershell-in-visual-studio-code"></a>PowerShell dans Visual Studio Code
[![exécution de PowerShell ](media/rest-powershell.png) ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>CLI en Visual Studio Code
[![exécution de l’interface CLI ](media/rest-cli.png) ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>Dépannage

Si vous ne parvenez pas à récupérer les métadonnées, ce qui ne nécessite pas de jeton d’accès basé sur la spécification HL7, vérifiez que votre serveur FHIR fonctionne correctement.

Si vous ne parvenez pas à obtenir un jeton d’accès, vérifiez que l’application cliente est correctement inscrite et que vous utilisez les valeurs correctes à partir de l’étape d’inscription de l’application.

Si vous ne parvenez pas à obtenir des données à partir du serveur FHIR, assurez-vous que l’application cliente (ou le principal du service) dispose des autorisations d’accès telles que « FHIR Data Contributor » sur le serveur FHIR.
