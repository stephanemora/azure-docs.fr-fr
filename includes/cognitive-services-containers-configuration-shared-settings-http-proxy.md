---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639217"
---
Si vous devez configurer un proxy HTTP pour effectuer des requêtes sortantes, utilisez les deux arguments suivants :

| Name | Type de données | Description |
|--|--|--|
|HTTPS_PROXY|string|Le proxy à utiliser, par exemple, `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|string|Les informations d’identification nécessaires pour s’authentifier auprès du proxy, par exemple, username:password.|
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
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
