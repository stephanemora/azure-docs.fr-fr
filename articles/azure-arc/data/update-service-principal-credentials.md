---
title: Mettre à jour les informations d’identification du principal de service
description: Mettre à jour les informations d’identification d’un principal de service
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669540"
---
# <a name="update-service-principal-credentials"></a>Mettre à jour les informations d’identification du principal de service

Lorsque les informations d’identification du principal de service changent, vous devez mettre à jour les secrets dans le contrôleur de données.

Par exemple, si vous avez déployé le contrôleur de données à l’aide d’un ensemble spécifique de valeurs pour l’ID de l’abonné du principal de service, l’ID client et la clé secrète client, puis modifiez une ou plusieurs de ces valeurs, vous devez mettre à jour les secrets dans le contrôleur de données.  Voici les instructions pour mettre à jour l’ID d’abonné, l’ID client ou la clé secrète client. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Arrière-plan

Le principal de service a été créé au niveau de [Créer le principal de service](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Étapes

1. Accédez au secret du principal de service dans l’éditeur par défaut.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Par exemple, pour modifier le secret du principal de service sur un contrôleur de données dans l'espace de noms `arc`, exécutez la commande suivante :

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   La commande `kubecl edit` ouvre le fichier .yml des informations d'identification dans l’éditeur par défaut. 


1. Modifiez le secret du principal de service. 

   Dans l’éditeur par défaut, remplacez les valeurs de la section données par les informations d’identification mises à jour.

   Exemple :

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Modifiez les valeurs par `clientID`, `clientSecret` et/ou `tenantID` selon le cas. 

> [!NOTE]
>Les valeurs doivent être encodées en base64. Ne modifiez pas les autres propriétés.

Si une valeur incorrecte est fournie pour `clientId`, `clientSecret` ou `tenantID`, un message d’erreur s’affiche comme suit dans les journaux de conteneur pod/Controller`control-xxxx` :

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Étapes suivantes

[Récupérer le nom d’utilisateur et le mot de passe pour se connecter au contrôleur de données arc](retrieve-the-username-password-for-data-controller.md)

[Créer un principal du service](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
