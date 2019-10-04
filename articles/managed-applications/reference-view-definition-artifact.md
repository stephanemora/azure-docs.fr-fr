---
title: Référence d’affichage d’un artefact de définition d’applications managées Azure
description: Cette article est une référence pour afficher un artefact de définition.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: e60f26fe0a7144d768bac020d62c61cb92594914
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336651"
---
# <a name="reference-view-definition-artifact"></a>Référence : Afficher un artefact de définition

Cet article est une référence pour un artefact *viewDefinition.json* dans des applications managées Azure. Pour plus d’informations sur la configuration des vues de création, consultez [Afficher un artefact de définition](concepts-view-definition.md).

## <a name="view-definition"></a>Afficher une définition

Le fichier JSON suivant montre un exemple de fichier *viewDefinition.json* pour les applications managées Azure :

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer une application managée avec des actions et des ressources personnalisées](tutorial-create-managed-app-with-custom-provider.md)
- [Reference : Artefact d’éléments d’interface utilisateur](reference-createuidefinition-artifact.md)
- [Reference : Artefact de modèle de déploiement](reference-main-template-artifact.md)