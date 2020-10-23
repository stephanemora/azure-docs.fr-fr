---
title: Élément d’interface utilisateur ServicePrincipalSelector
description: Décrit l’élément d’interface utilisateur Microsoft.Common.ServicePrincipalSelector pour le portail Azure. Fournit une liste déroulante permettant de choisir un identificateur d’application et une zone de texte pour entrer un mot de passe ou une empreinte de certificat.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575994"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.ServicePrincipalSelector

Contrôle qui permet aux utilisateurs de sélectionner un principal de service existant ou d’en inscrire un nouveau. Lorsque vous sélectionnez **Créer nouveau**, vous suivez les étapes pour inscrire une nouvelle application. Lorsque vous sélectionnez une application existante, le contrôle fournit une zone de texte pour entrer un mot de passe ou une empreinte de certificat.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

La vue par défaut est déterminée par les valeurs de la propriété `defaultValue`. Si la propriété `principalId` contient un identificateur global unique (GUID) valide, le contrôle recherche l’ID d’objet de l’application. La valeur par défaut s’applique si l’utilisateur n’effectue pas de sélection dans la liste déroulante.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Vue initiale de Microsoft.Common.ServicePrincipalSelector":::

Lorsque vous sélectionnez **Créer nouveau** ou un identificateur d’application existant dans la liste déroulante, le **type d’authentification** s’affiche pour entrer un mot de passe ou une empreinte de certificat dans la zone de texte.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Vue initiale de Microsoft.Common.ServicePrincipalSelector":::

## <a name="schema"></a>schéma

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques

- Les propriétés requises sont les suivantes :
  - `name`
  - `type`
  - `label`
  - `defaultValue` : Spécifie les valeurs `principalId` et `name` par défaut.

- Les propriétés facultatives sont les suivantes :
  - `toolTip` : Attache une info-bulle `infoBalloon` à chaque étiquette.
  - `visible` : Masquez ou affichez le contrôle.
  - `options` : Spécifie si l’option d’empreinte de certificat doit être proposée ou non.
  - `constraints` : Contraintes Regex pour la validation du mot de passe.

## <a name="example"></a>Exemple

Un exemple du contrôle `Microsoft.Common.ServicePrincipalSelector` est fourni ci-dessous. La propriété `defaultValue` définit `principalId` sur `<default guid>` en tant qu’espace réservé pour un GUID d’identificateur d’application par défaut.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Exemple de sortie

L’`appId` est l’ID de l’inscription d’application que vous avez sélectionnée ou créée. L’`objectId` est un tableau d’objectIds pour les principaux de service configurés pour l’inscription d’application sélectionnée.

Quand aucune sélection n’est effectuée dans la liste déroulante, la valeur de la propriété `newOrExisting` est **nouvelle** :

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Quand vous sélectionnez **Créer nouveau** ou un identificateur d’application existant dans la liste déroulante, la valeur de la propriété `newOrExisting` est **existante** :

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
- Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
