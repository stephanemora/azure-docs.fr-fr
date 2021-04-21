---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629997"
---
Si vous devez configurer un proxy HTTP pour effectuer des requêtes sortantes, utilisez les deux arguments suivants :

| Name | Type de données | Description |
|--|--|--|
|HTTP_PROXY|string|Le proxy à utiliser, par exemple, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Toutes les informations d’identification nécessaires pour s’authentifier auprès du proxy, par exemple `username:password`. Cette valeur **doit être en minuscules**. |
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
