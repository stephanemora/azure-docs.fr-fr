---
author: baanders
description: inclure un fichier pour charger un modèle sur une instance Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304293"
---
Le modèle se présente ainsi :
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pour **charger ce modèle dans votre instance Twins**, exécutez la commande Azure CLI suivante, qui charge le modèle ci-dessus en tant que JSON inline. Vous pouvez exécuter la commande dans [Azure Cloud Shell](../articles/cloud-shell/overview.md) dans votre navigateur (utilisez l’environnement **Bash**) ou sur votre machine si l’interface CLI est [installée localement](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```