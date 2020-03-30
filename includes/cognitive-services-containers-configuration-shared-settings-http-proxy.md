---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320536"
---
Si vous devez configurer un proxy HTTP pour effectuer des requêtes sortantes, utilisez les deux arguments suivants :

| Name | Type de données | Description |
|--|--|--|
|HTTP_PROXY|string|Le proxy à utiliser, par exemple, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Les informations d’identification nécessaires pour s’authentifier auprès du proxy, par exemple, username:password.|
|`<proxy-user>`|string|L’utilisateur pour le proxy.|
|`<proxy-password>`|string|Le mot de passe associé à `<proxy-user>` pour le proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
