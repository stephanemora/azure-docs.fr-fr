---
title: Développement en équipe avec les espaces Azure Dev Spaces à l’aide de Node.js et de VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: d8c3ae10e770533c910583e6e2bc218d78df4339
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798910"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Modifier le code
Accédez à la fenêtre VS Code pour `mywebapi` et modifiez le code du gestionnaire GET `/` par défaut dans `server.js`, par exemple :

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
