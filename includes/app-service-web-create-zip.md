---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133102"
---
## <a name="create-a-project-zip-file"></a>Créer un fichier ZIP de projet

Vérifiez que vous êtes toujours dans le répertoire racine de l’exemple de projet. Créez une archive ZIP contenant tous les éléments de votre projet. La commande suivante utilise l’outil par défaut dans votre terminal :

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Par la suite, vous chargez ce fichier ZIP dans Azure et le déployer dans App Service.