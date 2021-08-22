---
title: Récupérer le nom d’utilisateur et le mot de passe pour se connecter au contrôleur de données arc
description: Récupérer le nom d’utilisateur et le mot de passe pour se connecter au contrôleur de données arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 89ba0139409a1ceef37a773403c0b7623f6c6f14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532127"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Récupérer le nom d’utilisateur et le mot de passe pour se connecter au contrôleur de données arc

Vous pouvez être dans une situation où vous devez récupérer le nom d’utilisateur et le mot de passe pour le contrôleur de données. Voici les commandes dont vous avez besoin pour l’exécution. 

Si vous êtes l’administrateur Kubernetes du cluster. Par conséquent, vous avez le droit d’exécuter des commandes pour récupérer à partir du secret Kubernetes stocke les informations qui sont conservées par Azure Arc.

> [!NOTE]
>  Si vous avez utilisé un autre nom pour l’espace de noms dans lequel le contrôleur de données a été créé, veillez à modifier le paramètre `-n arc` dans les commandes ci-dessous afin d’utiliser le nom de l’espace de noms dans lequel vous avez créé le contrôleur de données.


## <a name="linux"></a>Linux

Exécutez la commande suivante pour récupérer le nom d’utilisateur :

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Exécutez la commande suivante pour récupérer le mot de passe :

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Exécutez la commande suivante pour récupérer le nom d’utilisateur :

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Exécutez la commande suivante pour récupérer le mot de passe :

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Étapes suivantes

Essayer d’autres [scénarios](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
