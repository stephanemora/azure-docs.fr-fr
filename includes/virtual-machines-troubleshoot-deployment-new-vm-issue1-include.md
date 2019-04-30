---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123829"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problème : Image personnalisée ; erreurs d’approvisionnement
Les erreurs de configuration se produisent lorsque vous téléchargez ou capturez une image de machine virtuelle généralisée en tant qu’image de machine virtuelle spécialisée, ou inversement. Une erreur de délai d’attente de configuration survient dans le premier cas, tandis que c’est un échec de configuration dans le second. Pour déployer votre image personnalisée sans erreur, vous devez vous assurer que le type de l’image ne change pas pendant le processus de capture.

Le tableau suivant répertorie les combinaisons possibles d’images généralisées et spécialisés, le type d’erreur que vous pouvez rencontrer et ce qu’il faut faire pour corriger ces erreurs.

<!--Update_Description: wording update, update link-->