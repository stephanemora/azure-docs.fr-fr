---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "75893969"
---
Lors de la création d’un espace de travail Azure Machine Learning ou d’une ressource utilisée par l’espace de travail, vous pouvez recevoir un message d’erreur semblable aux messages suivants :

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Par défaut, la plupart des fournisseurs de ressources sont enregistrés automatiquement. Si vous recevez ce message, vous devez inscrire le fournisseur mentionné.

Pour plus d’informations sur l’inscription d’un fournisseur de ressources, consultez [Résoudre les erreurs d’inscription de fournisseurs de ressources](../articles/azure-resource-manager/templates/error-register-resource-provider.md).