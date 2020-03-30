---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229256"
---
Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services Vision](https://aka.ms/VisionContainersPreview) pour demander un accès au conteneur. Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois que vous avez envoyé le formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> Vous devez utiliser une adresse e-mail associée à un compte Microsoft (MSA) ou à un compte Azure Active Directory (Azure AD) dans le formulaire.

Si votre demande est approuvée, vous recevrez un e-mail contenant des instructions pour obtenir vos informations d’identification et un accès au registre de conteneurs privé.

## <a name="log-in-to-the-private-container-registry"></a>Se connecter au registre de conteneurs privé

Il existe plusieurs façons de s’authentifier auprès du registre de conteneurs privé pour les conteneurs Cognitive Services. Nous vous recommandons d’utiliser la méthode de ligne de commande avec l’[interface de ligne de commande Docker (CLI)](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), comme dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, qui est le registre de conteneurs privé pour les conteneurs Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez concaténer le contenu de ce fichier texte dans la commande `docker login`. Utilisez la commande `cat`, comme dans l’exemple suivant. Remplacez *\<passwordFile\>* par le chemin et le nom du fichier texte qui contient le mot de passe. Remplacez *\<username\>* par le nom d’utilisateur indiqué dans vos informations d’identification.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

