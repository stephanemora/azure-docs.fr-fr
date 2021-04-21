---
author: baanders
description: Fichier include pour le nettoyage d’une instance Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800187"
---
* **Si vous n’avez pas besoin des ressources que vous avez créées dans ce tutoriel**, vous pouvez supprimer l’instance Azure Digital Twins et toutes les autres ressources de cet article à l’aide de la commande [az group delete](/cli/azure/group#az_group_delete). Cette opération supprime toutes les ressources Azure d’un groupe de ressources ainsi que ce dernier.
    
    > [!IMPORTANT]
    > La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement les mauvaises ressources ou le mauvais groupe de ressources.
    
    Ouvrez [Azure Cloud Shell](https://shell.azure.com) et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```