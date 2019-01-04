---
title: Utiliser les API Video Indexer pour personnaliser un modèle linguistique - Azure
titlesuffix: Azure Media Services
description: Cet article explique comment personnaliser un modèle linguistique avec les API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 95334ac326b346da23f17d3a9d494120235abace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283725"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personnaliser un modèle linguistique avec les API Video Indexer

Video Indexer vous permet de créer des modèles linguistiques personnalisés pour la reconnaissance vocale en chargeant un texte d’adaptation, à savoir un texte extrait du domaine de vocabulaire auquel vous souhaitez que le moteur s’adapte. Une fois votre modèle formé, les nouveaux mots qui apparaissent dans le texte d’adaptation seront reconnus. 

Pour obtenir une présentation détaillée et les meilleures pratiques pour les modèles linguistiques personnalisés, consultez [Personnaliser un modèle linguistique avec Video Indexer](customize-language-model-overview.md).

Vous pouvez utiliser les API Video Indexer pour créer et modifier des modèles linguistiques personnalisés dans votre compte, comme décrit dans cette rubrique. Vous pouvez également utiliser le site web comme décrit dans [Personnaliser un modèle linguistique avec le site web Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Créer un modèle linguistique

La commande suivante crée un nouveau modèle linguistique personnalisé dans le compte spécifié. Vous pouvez charger des fichiers pour le modèle linguistique dans cet appel. Vous pouvez également créer ici le modèle linguistique et charger ultérieurement des fichiers en mettant à jour le modèle linguistique.

> [!NOTE]
> Vous devez néanmoins former le modèle avec ses fichiers pour lui permettre d’en apprendre le contenu. La section suivante fournit des instructions sur la formation d’une langue.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>Paramètres de la demande

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountId|chaîne|Oui|GUID pour ce compte.|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|
|modelName|chaîne|Oui|Nom du modèle linguistique|
|Langage|chaîne|Oui|Langue du modèle linguistique. <br/>Le paramètre **langage** doit afficher une langue au format BCP-47 « balise de langue-région' (par exemple : « en-US »). Les langues prises en charge sont les suivantes : Anglais (en-US), Allemand (de-DE), Espagnol (es-SP), Arabe (ar-EG), Français (fr-FR), Hindi (hi-HI), Italien (it-IT), Japonais (ja-JP), Portugais (pt-BR), Russe (ru-RU) et Chinois (zh-CN).  |

### <a name="request-body"></a>Corps de la demande

Pour charger des fichiers à ajouter au modèle linguistique, vous devez charger les fichiers dans le corps à l’aide des données de formulaire en plus de fournir les valeurs des paramètres requis ci-dessus. Il existe deux façons d'effectuer cette opération : 

1. La clé sera le nom de fichier et la valeur sera le fichier txt
2. La clé sera le nom de fichier et la valeur sera une URL vers le fichier txt

### <a name="response"></a>response

La réponse fournit des métadonnées sur le modèle linguistique qui vient d’être créé ainsi que des métadonnées sur chacun des fichiers du modèle au format de l’exemple de sortie JSON.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Former un modèle linguistique

La commande suivante forme un modèle linguistique personnalisé dans le compte spécifié, avec le contenu des fichiers qui ont été chargés et activés dans le modèle linguistique. 

> [!NOTE]
> Vous devez d’abord créer le modèle linguistique et charger ses fichiers. Vous pouvez charger les fichiers lors de la création ou de la mise à jour du modèle linguistique. 

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?).

### <a name="request-parameters"></a>Paramètres de la demande

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique (généré lorsque le modèle linguistique est créé)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse fournit des métadonnées sur le modèle linguistique qui vient d’être formé ainsi que des métadonnées sur chacun des fichiers du modèle au format de l’exemple de sortie JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Vous devez ensuite utiliser la valeur **id** du modèle linguistique pour le paramètre **linguisticModelId** lorsque vous [chargez une vidéo dans un index](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), et pour le paramètre **languageModelId** lorsque vous [réindexez une vidéo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Supprimer un modèle linguistique

La commande suivante supprime un modèle linguistique personnalisé du compte spécifié. Toute vidéo qui utilisait le modèle linguistique supprimé conservera le même index jusqu'à la réindexation de la vidéo. Si vous réindexez la vidéo, vous pouvez lui affecter un nouveau modèle linguistique. Sinon, Video Indexer utilisera son modèle par défaut pour réindexer la vidéo.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête DELETE.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique (généré lorsque le modèle linguistique est créé)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

Aucun contenu n’est retourné en cas de suppression effective du modèle linguistique.

## <a name="update-a-language-model"></a>Mettre à jour un modèle linguistique

La commande suivante met à jour un modèle linguistique personnalisé dans le compte spécifié.

> [!NOTE]
> Vous devez avoir déjà créé le modèle linguistique. Vous pouvez utiliser cet appel pour activer ou désactiver tous les fichiers du modèle, mettre à jour le nom du modèle linguistique, et charger des fichiers à ajouter au modèle linguistique.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Voici la requête dans Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique (généré lorsque le modèle linguistique est créé)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|
|modelName|chaîne|Non |Nouveau nom que vous pouvez donner au modèle|
|enable|booléenne|Non |Choisissez si tous les fichiers de ce modèle sont activés (true) ou désactivés (false)|

### <a name="request-body"></a>Corps de la demande

Pour charger des fichiers à ajouter au modèle linguistique, vous devez charger les fichiers dans le corps à l’aide des données de formulaire en plus de fournir les valeurs des paramètres requis ci-dessus. Il existe deux façons d'effectuer cette opération : 

1. La clé sera le nom de fichier et la valeur sera le fichier txt
2. La clé sera le nom de fichier et la valeur sera une URL vers le fichier txt

### <a name="response"></a>response

La réponse fournit des métadonnées sur le modèle linguistique qui vient d’être formé ainsi que des métadonnées sur chacun des fichiers du modèle au format de l’exemple de sortie JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Vous pouvez utiliser la valeur **id** des fichiers retournée ici pour charger le contenu du fichier.

## <a name="update-a-file-from-a-language-model"></a>Mettre à jour un fichier d’un modèle linguistique

La commande suivante vous permet de mettre à jour le nom et d’**activer** un fichier d’un modèle linguistique personnalisé dans le compte spécifié.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Voici la requête dans Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountId|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique contenant le fichier (généré lorsque le modèle linguistique est créé)|
|fileId|chaîne|Oui|ID du fichier en cours de mise à jour (généré quand le fichier est chargé lors de la création ou de la mise à jour du modèle linguistique)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|
|fileName|chaîne|Non |Nom du fichier à mettre à jour|
|enable|booléenne|Non |Mettre à jour si ce fichier est activé (true) ou désactivé (false) dans le modèle linguistique||

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse fournit des métadonnées sur le fichier que vous avez mis à jour, au format de l’exemple de sortie JSON ci-dessous.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Vous pouvez utiliser la valeur **id** du fichier retournée ici pour charger le contenu du fichier.

## <a name="get-a-specific-language-model"></a>Obtenir un modèle linguistique spécifique

La commande suivante retourne des informations sur le modèle linguistique utilisé comme langue dans le compte spécifié ainsi que sur les fichiers que ce modèle contient. 

### <a name="request-url"></a>URL de la demande

Il s’agit d’une demande GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16).

### <a name="request-parameters-and-request-body"></a>Paramètres et corps de la requête

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique (généré lorsque le modèle linguistique est créé)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse fournit des métadonnées sur le modèle linguistique spécifié ainsi que des métadonnées sur chacun des fichiers du modèle au format de l’exemple de sortie JSON ci-dessous.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Vous pouvez utiliser la valeur **id** du fichier retournée ici pour charger le contenu du fichier.

## <a name="get-all-the-language-models"></a>Obtenir tous les modèles linguistiques

La commande suivante dresse la liste de tous les modèles linguistiques personnalisés dans le compte spécifié.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?).

### <a name="request-parameters"></a>Paramètres de la demande

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse renvoie une liste de tous les modèles linguistiques dans votre compte, en précisant chaque métadonnée et fichier, au format de l’exemple de sortie JSON ci-dessous.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Supprimer un fichier d’un modèle linguistique

La commande suivante supprime le fichier spécifié à partir du modèle linguistique spécifié dans le compte spécifié. 

### <a name="request-url"></a>URL de la demande

Il s’agit d’une requête DELETE.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique contenant le fichier (généré lorsque le modèle linguistique est créé)|
|fileId|chaîne|Oui|ID du fichier en cours de mise à jour (généré quand le fichier est chargé lors de la création ou de la mise à jour du modèle linguistique)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

Aucun contenu n’est retourné en cas de suppression effective du fichier du modèle linguistique.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obtenir les métadonnées d’un fichier à partir d’un modèle linguistique

Cette commande retourne le contenu et les métadonnées du fichier spécifié à partir du modèle linguistique choisi dans votre compte.

### <a name="request-url"></a>URL de la demande

Il s’agit d’une demande GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Voici la requête dans Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique contenant le fichier (généré lorsque le modèle linguistique est créé)|
|fileId|chaîne|Oui|ID du fichier en cours de mise à jour (généré quand le fichier est chargé lors de la création ou de la mise à jour du modèle linguistique)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse fournit le contenu et les métadonnées du fichier au format JSON, sous une forme similaire à ce qui suit :

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Cet exemple de fichier contient les mots « hello » et « world » dans deux lignes distinctes.

## <a name="download-a-file-from-a-language-model"></a>Télécharger un fichier à partir d’un modèle linguistique

La commande suivante télécharge un fichier texte avec le contenu du fichier provenant du modèle linguistique dans le compte spécifié. Ce fichier texte doit correspondre au contenu du fichier texte chargé à l’origine.

### <a name="request-url"></a>URL de la demande
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Voici la requête dans Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Consultez les paramètres requis et effectuez des tests à partir du portail des développeurs Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122).

### <a name="request-parameters"></a>Paramètres de la demande 

|**Nom**|**Type**|**Obligatoire**|**Description**|
|---|---|---|---|
|location|chaîne|Oui|La région Azure vers laquelle l’appel doit être routé. Pour plus d’informations, consultez [Régions Azure et Video Indexer](regions.md).|
|accountID|chaîne|Oui|GUID pour ce compte.|
|modelId|chaîne|Oui|ID du modèle linguistique contenant le fichier (généré lorsque le modèle linguistique est créé)|
|fileId|chaîne|Oui|ID du fichier en cours de mise à jour (généré quand le fichier est chargé lors de la création ou de la mise à jour du modèle linguistique)|
|accessToken|chaîne|Oui|Jeton d’accès (doit être du type [Jeton d’accès de compte](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pour s’authentifier auprès de l’appel. Les jetons d’accès expirent au bout d’une heure.|

### <a name="request-body"></a>Corps de la demande 

Aucun autre corps de demande n’est requis pour cet appel.

### <a name="response"></a>response

La réponse entraînera le téléchargement d’un fichier texte avec le contenu du fichier au format JSON. 

## <a name="next-steps"></a>Étapes suivantes

[Personnaliser un modèle linguistique à l’aide d’un site web](customize-language-model-with-website.md)
