---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/06/2020
ms.author: larryfr
ms.openlocfilehash: 83898744610cd27989e6fa0d1c6b1cca6286cb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103020833"
---
Pour empêcher que les fichiers inutiles soient inclus dans l’instantané, créez un fichier « ignore» (`.gitignore` ou `.amlignore`) dans le répertoire. Ajoutez dans ce fichier les fichiers et répertoires à exclure. Pour plus d’informations sur la syntaxe à utiliser dans ce fichier, consultez [syntaxe et modèles](https://git-scm.com/docs/gitignore) pour `.gitignore`. Le fichier `.amlignore` utilise la même syntaxe. _Si les deux fichiers existent, le fichier `.amlignore` est utilisé, et non le fichier `.gitignore`._
