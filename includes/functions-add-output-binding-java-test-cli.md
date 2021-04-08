---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673271"
---
## <a name="update-the-tests"></a>Mettre à jour les tests

Étant donné que l’archétype crée également un ensemble de tests, vous devez mettre à jour ces tests pour gérer le nouveau paramètre `msg` dans la signature de méthode `run`.  

Accédez à l’emplacement de votre code de test sous _src/test/Java_, ouvrez le fichier projet *Function.java* et remplacez la ligne de code sous `//Invoke` par le code suivant.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::