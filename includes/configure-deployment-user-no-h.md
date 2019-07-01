---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ebea8bfd69a4df605142ab82f3efbc7d97d34529
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143969"
---
Dans Azure Cloud Shell, configurez des informations d’identification de déploiement avec la commande [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Vous utilisez cet utilisateur de déploiement pour les déploiements FTP et Git en local sur une application web. Le nom d’utilisateur et le mot de passe sont tous les deux au niveau du compte. _Ils sont différents des informations d’identification de votre abonnement Azure._

Dans l’exemple suivant, remplacez *\<username>* et *\<password>* , parenthèses comprises, par le nom et le mot de passe du nouvel utilisateur. Le nom d’utilisateur doit être unique au sein de Azure. Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Vous obtenez une sortie JSON, avec le mot de passe indiqué comme étant `null`. Si vous obtenez une erreur `'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur `'Bad Request'. Details: 400`, utilisez un mot de passe plus fort. Le nom d’utilisateur de déploiement ne doit pas contenir le symbole « @ » pour les opérations push Git locales.

Vous ne configurez cet utilisateur de déploiement qu’une seule fois. Vous pouvez l’utiliser pour tous vos déploiements Azure.

> [!NOTE]
> Enregistrez le nom d’utilisateur et le mot de passe. Vous en aurez besoin pour déployer ultérieurement l’application web.
>
>
