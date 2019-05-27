---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124586"
---
Remplir et soumettre le [formulaire de demande de conteneurs de Vision Services cognitifs](https://aka.ms/VisionContainersPreview) pour demander l’accès au conteneur. Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois que vous envoyez le formulaire, l’équipe Azure Cognitive Services passe en revue pour vous assurer que vous remplissez les critères pour l’accès au Registre de conteneurs privé.

> [!IMPORTANT]
> Vous devez utiliser une adresse de messagerie associée à un compte Microsoft (MSA) ou un compte Azure Active Directory (Azure AD) dans le formulaire.

Si votre demande est approuvée, vous recevez un e-mail contenant des instructions qui expliquent comment obtenir vos informations d’identification et d’accéder au Registre de conteneurs privé.

## <a name="log-in-to-the-private-container-registry"></a>Se connecter au registre de conteneurs privé

Il existe plusieurs façons de s’authentifier auprès du Registre de conteneurs privé pour les conteneurs de Cognitive Services. Nous vous recommandons d’utiliser la méthode de ligne de commande à l’aide de la [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez le [connexion docker](https://docs.docker.com/engine/reference/commandline/login/) commande, comme indiqué dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, c'est-à-dire le Registre de conteneurs privé pour les conteneurs de Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez concaténer le contenu du fichier texte dans le `docker login` commande. Utilisez le `cat` commande, comme indiqué dans l’exemple suivant. Remplacez *\<passwordFile\>* avec le chemin d’accès et le nom du fichier texte qui contient le mot de passe. Remplacez *\<nom d’utilisateur\>* avec le nom d’utilisateur fourni dans vos informations d’identification.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

