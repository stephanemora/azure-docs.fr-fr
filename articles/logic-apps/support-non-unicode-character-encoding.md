---
title: Prendre en charge le codage de caractères non Unicode dans Logic Apps
description: Utilisez du texte non Unicode dans Logic Apps. Convertissez les charges utiles textuelles en UTF-8 à l’aide de l’encodage en base64 et Azure Functions.
ms.date: 04/29/2021
ms.topic: conceptual
ms.reviewer: logicappspm
ms.service: logic-apps
ms.openlocfilehash: cabdb2a644870d363265fa39290eb503f72730a9
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326909"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>Prendre en charge le codage de caractères non Unicode dans Logic Apps

Lorsque vous travaillez avec des charges utiles textuelles, Azure Logic Apps suppose que le texte est encodé dans un format Unicode, par exemple UTF-8. Vous risquez de rencontrer des problèmes de réception, d’envoi ou de traitement des caractères avec des encodages différents dans votre workflow. Par exemple, vous pouvez obtenir des caractères corrompus dans des fichiers plats lorsque vous travaillez avec des systèmes hérités qui ne prennent pas en charge Unicode.

Pour utiliser du texte ayant un autre codage de caractères, appliquez l’encodage en base64 à la charge utile non Unicode. Cette étape empêche Logic Apps de supposer que le texte est au format UTF-8. Vous pouvez ensuite convertir tout encodage pris en charge par .NET en UTF-8 à l’aide d’Azure Functions. 

Cette solution fonctionne avec les workflows *multilocataires* et *à locataire unique*. Vous pouvez également [utiliser cette solution avec le connecteur AS2](#convert-payloads-for-as2).

## <a name="convert-payload-encoding"></a>Convertir l’encodage de la charge utile

Tout d’abord, vérifiez que votre déclencheur peut identifier correctement le type de contenu. Cette étape permet de s’assurer que Logic Apps ne présume plus que le texte est en UTF-8. 

Pour les déclencheurs avec le paramètre **Déduire le type de contenu**, choisissez **Non**. Si votre déclencheur n’a pas cette option, le type de contenu est défini par le message entrant. 

Si vous utilisez le déclencheur de requête HTTP pour le contenu `text/plain`, vous devez définir le paramètre `charset` dans l’en-tête `Content-Type` de l’appel. Les caractères peuvent être corrompus si vous ne définissez pas le paramètre `charset`, ou si le paramètre ne correspond pas au format d’encodage de la charge utile. Pour plus d’informations, consultez [la façon de gérer le type de contenu `text/plain`](logic-apps-content-type.md#text-plain).

Par exemple, le déclencheur HTTP convertit le contenu entrant en UTF-8 lorsque l’en-tête `Content-Type` est défini avec le paramètre `charset` correct :

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

Si vous définissez l’en-tête `Content-Type` sur `application/octet-stream`, vous pouvez également recevoir des caractères qui ne sont pas en UTF-8. Pour plus d’informations, consultez [la façon de gérer le type de contenu `application/octet-stream`](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream).

## <a name="base64-encode-content"></a>Contenu encodé en base64

Avant d’[encoder le contenu en base64](workflow-definition-language-functions-reference.md#base64), assurez-vous d’avoir [converti le texte en UTF-8](#convert-payload-encoding). Si vous décodez le contenu en base64 en une chaîne avant de convertir le texte en UTF-8, les caractères risquent d’être corrompus.

Ensuite, convertissez tout encodage pris en charge par .NET en un autre encodage pris en charge par .NET. Consultez l’[exemple de code Azure Functions](#azure-functions-version) ou l’[exemple de code .NET](#net-version) :

> [!TIP]
> Pour les applications logiques *à locataire unique*, vous pouvez améliorer les performances et réduire la latence en exécutant localement la fonction de conversion.

### <a name="azure-functions-version"></a>Version d’Azure Functions

L’exemple suivant concerne Azure Functions version 2 : 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>Version de .NET

L’exemple suivant est destiné à être utilisé avec **.NET standard** et Azure Functions **version 2** :

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

En utilisant ces mêmes concepts, vous pouvez également [envoyer une charge utile non Unicode à partir de votre workflow](#send-non-unicode-payload).

## <a name="sample-payload-conversions"></a>Exemples de conversions de charge utile

Dans cet exemple, l’exemple de chaîne d’entrée encodé en base64 est un nom personnel, *H&eacute;lo&iuml;se*, qui contient des caractères accentués.

Exemple d’entrée :

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

Exemple de sortie :

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>Envoyer une charge utile non Unicode

Si vous devez envoyer une charge utile non Unicode à partir de votre workflow, effectuez les étapes de [conversion de la charge utile en UTF-8](#convert-payload-encoding) dans l’ordre inverse. Conservez le texte en UTF-8 le plus longtemps possible dans votre système. Ensuite, utilisez la même fonction pour convertir les caractères UTF-8 encodés en base64 dans l’encodage requis. Enfin, appliquez le décodage base64 au texte et envoyez votre charge utile.

## <a name="convert-payloads-for-as2"></a>Convertir les charges utiles pour AS2

Vous pouvez également utiliser cette solution avec des charges utiles non Unicode dans le [connecteur AS2 v2](logic-apps-enterprise-integration-as2.md). Si vous ne convertissez pas en UTF-8 les charges utiles que vous transmettez à AS2, vous risquez de rencontrer des problèmes d’interprétation des charges utiles. Ces problèmes peuvent entraîner un décalage du code de hachage MIC entre les partenaires en raison de caractères mal interprétés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Encoder et décoder des fichiers plats dans Azure Logic Apps avec Enterprise Integration Pack](logic-apps-enterprise-integration-flatfile.md)
