---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116767"
---
Si vous devez configurer un proxy HTTP pour effectuer des requêtes sortantes, utilisez les deux arguments suivants :

| Nom | Type de données | Description |
|--|--|--|
|HTTP_PROXY|chaîne|Le proxy à utiliser, par exemple, `http://proxy:8888`<br><proxy-url>|
|HTTP_PROXY_CREDS|chaîne|Les informations d’identification nécessaires pour s’authentifier auprès du proxy, par exemple, username:password.|
|`<proxy-user>`|chaîne|L’utilisateur pour le proxy.|
|`proxy-password`|chaîne|Le mot de passe associé à `<proxy-user>` pour le proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```