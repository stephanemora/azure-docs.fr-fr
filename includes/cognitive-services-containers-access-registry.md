---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063941"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Utiliser l’interface CLI Docker pour authentifier le registre de conteneurs privé

Vous pouvez vous authentifier auprès du registre de conteneurs privé pour les conteneurs Cognitive Services de plusieurs façons, mais la méthode recommandée à partir de la ligne de commande est l’utilisation de l’[interface CLI Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez la [commande `docker login`](https://docs.docker.com/engine/reference/commandline/login/), comme indiqué dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, le registre de conteneurs privé pour les conteneurs Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez utiliser la commande `cat` pour concaténer son contenu à la commande `docker login`, comme indiqué dans l’exemple suivant. Remplacez *\<passwordFile\>* par le chemin et le nom du fichier texte qui contient le mot de passe, et *\<username\>* par le nom d’utilisateur fourni dans vos informations d’identification.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
