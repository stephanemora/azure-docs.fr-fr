---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 11a336bbcf75c6c4de61f1bb681ab6ee7aa05650
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741347"
---
Vous devez d'abord compléter et envoyer le [formulaire de demande de conteneurs Vision Cognitive Services](https://aka.ms/VisionContainersPreview) pour demander l'accès au conteneur. Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. À la réception du formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> Vous devez utiliser une adresse e-mail associée à un compte Microsoft (MSA) ou à un compte Azure Active Directory (Azure AD) dans le formulaire.

Si votre demande est approuvée, vous recevez un e-mail contenant des instructions relatives à l’obtention de vos informations d’identification et à l’accès au registre de conteneurs privé.

## <a name="log-in-to-the-private-container-registry"></a>Se connecter au registre de conteneurs privé

Il y a plusieurs façons de s’authentifier auprès du registre de conteneurs privé pour les conteneurs Cognitive Services, mais la méthode recommandée à partir de la ligne de commande est l’utilisation de la [CLI Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), comme indiqué dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, le registre de conteneurs privé pour les conteneurs Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez utiliser la commande `cat` pour concaténer son contenu à la commande `docker login`, comme indiqué dans l’exemple suivant. Remplacez *\<passwordFile\>* par le chemin et le nom du fichier texte contenant le mot de passe, et *\<username\>* par le nom d’utilisateur fourni dans vos informations d’identification.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

