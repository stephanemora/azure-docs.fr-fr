---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344684"
---
Dans Cloud Shell, configurez des informations d’identification de déploiement avec la commande [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Cet utilisateur de déploiement est requis pour les déploiements FTP et Git en local sur une application web. Le nom d’utilisateur et le mot de passe par défaut sont tous les deux au niveau du compte. _Ils sont différents des informations d’identification de votre abonnement Azure._

Dans l’exemple suivant, remplacez *\<username>* et *\<password>* (parenthèses comprises) par le nom et le mot de passe du nouvel utilisateur. Le nom d’utilisateur doit être unique au sein de Azure. Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Vous devez obtenir une sortie JSON, avec le mot de passe indiqué comme étant `null`. Si vous obtenez une erreur `'Conflict'. Details: 409`, modifiez le nom d’utilisateur. Si vous obtenez une erreur ` 'Bad Request'. Details: 400`, utilisez un mot de passe plus fort.

Vous devez configurer cet utilisateur de déploiement une seule fois. Vous pouvez l’utiliser pour tous vos déploiements Azure.

> [!NOTE]
> Notez le nom d’utilisateur et le mot de passe. Vous en aurez besoin pour déployer ultérieurement l’application web.
>
>
