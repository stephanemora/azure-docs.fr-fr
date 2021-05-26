---
author: baanders
description: Fichier include pour le nettoyage d’une instance Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0783f25813fa8e4a65a33975017e2b2d03a4dd2c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110075474"
---
* **Si vous n’avez pas besoin des ressources que vous avez créées dans ce tutoriel**, vous pouvez supprimer l’instance Azure Digital Twins et toutes les autres ressources de cet article à l’aide de la commande [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete). Cette opération supprime toutes les ressources Azure d’un groupe de ressources ainsi que ce dernier.
    
    > [!IMPORTANT]
    > La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement les mauvaises ressources ou le mauvais groupe de ressources.
    
    Ouvrez [Azure Cloud Shell](https://shell.azure.com) et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```