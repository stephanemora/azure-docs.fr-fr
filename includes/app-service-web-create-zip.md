---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65914289"
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