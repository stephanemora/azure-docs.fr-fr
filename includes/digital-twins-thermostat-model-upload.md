---
author: baanders
description: inclure un fichier pour charger un modèle sur une instance Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950590"
---
Le modèle se présente ainsi :
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pour **charger ce modèle dans votre instance Twins**, exécutez la commande Azure CLI suivante, qui charge le modèle ci-dessus en tant que JSON inline. Vous pouvez exécuter la commande dans [Azure Cloud Shell](../articles/cloud-shell/overview.md) dans votre navigateur ou sur votre machine si l’interface CLI est [installée localement](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Si vous utilisez Cloud Shell dans l’environnement PowerShell, vous devrez peut-être placer des guillemets sur les champs JSON inclus pour que leurs valeurs soient analysées correctement. Voici la commande permettant de charger le modèle avec cette modification :
>
> Charger un modèle :
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```