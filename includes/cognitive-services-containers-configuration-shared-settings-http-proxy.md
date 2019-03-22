---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 0745e4987ca602a41f2d93ddc0a93cb061ce127d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58052000"
---
Si vous devez configurer un proxy HTTP pour effectuer des requêtes sortantes, utilisez les deux arguments suivants :

| Nom | Type de données | Description |
|--|--|--|
|HTTP_PROXY|string|proxy à utiliser, par exemple `http://proxy:8888`|
|HTTP_PROXY_CREDS|string|informations d’identification nécessaires à l’authentification auprès du proxy, par exemple nom_utilisateur:mot_de_passe.|

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=http://190.169.1.6:3128 \
HTTP_PROXY_CREDS=jerry:123456 \
Logging:Disk:LogLevel=Debug Logging:Disk:Format=json
```