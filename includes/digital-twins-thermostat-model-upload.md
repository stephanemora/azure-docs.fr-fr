---
author: baanders
description: inclure un fichier pour charger un modèle sur une instance Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 9fcbfc0b0fccb5f130f0c554bac93748176387b7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438974"
---
Le modèle se présente ainsi :
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pour **charger ce modèle dans votre instance Twins**, exécutez la commande Azure CLI suivante, qui charge le modèle ci-dessus en tant que JSON inline. Vous pouvez exécuter la commande dans [Azure Cloud Shell](../articles/cloud-shell/overview.md) dans votre navigateur (utilisez l’environnement **Bash**) ou sur votre machine si [l’interface CLI est installée localement](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --dt-name <instance-name> --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' 
```