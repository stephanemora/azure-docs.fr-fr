---
title: Résolution des problèmes du micro-agent Defender pour IoT (préversion)
titleSuffix: Azure Defender for IoT
description: Découvrez comment gérer les erreurs inattendues ou inexpliquées.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809585"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Résolution des problèmes du micro-agent Defender pour IoT (préversion)

Si vous avez des erreurs inattendues ou inexpliquées, utilisez les méthodes de dépannage suivantes pour tenter de résoudre vos problèmes. Vous pouvez également contacter l’équipe produit d’Azure Defender pour IoT afin d’obtenir de l’aide si nécessaire.   

## <a name="service-status"></a>État du service 

Pour voir l’état du service : 

1. Exécutez la commande suivante :

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Vérifiez que le service est stable en contrôlant qu’il est `active` et que la durée de bon fonctionnement dans le processus est appropriée.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Vérifiez que le service est stable en contrôlant qu’il est actif et que la durée de bon fonctionnement est appropriée.":::

Si le service apparaît comme étant `inactive`, exécutez la commande suivante pour le démarrer :

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Vous savez que le service plante si le temps de bon fonctionnement du processus est trop court. Pour résoudre ce problème, vous devez examiner les journaux.

## <a name="review-logs"></a>Consulter les journaux 

Utilisez la commande suivante pour vérifier que le service du micro-agent Defender IoT s’exécute avec des privilèges racines.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Vérifiez que le service de l’agent Defender pour IoT s’exécute avec des privilèges racines.":::

Pour visualiser les journaux, utilisez la commande suivante :  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Pour redémarrer le service, utilisez la commande suivante : 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Étapes suivantes

Consultez [Prise en charge et mise hors service des fonctionnalités](edge-security-module-deprecation.md).
