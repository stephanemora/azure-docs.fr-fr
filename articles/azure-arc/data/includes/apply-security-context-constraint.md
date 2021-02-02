---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696060"
---
Cette section explique comment appliquer une contrainte de contexte de sécurité (SCC). Pour la préversion, ces dernières assouplissent les contraintes de sécurité. 

1. Téléchargez la contrainte de contexte de sécurité (SCC) personnalisée. Utilisez l’une des valeurs suivantes : 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Brut](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - La commande `curl` suivante télécharge arc-data-scc.yaml :

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Créez la contrainte de contexte de sécurité.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Appliquez la contrainte de contexte de sécurité au compte de service.

   > [!NOTE]
   > Utilisez le même espace de noms ici et dans la commande `azdata arc dc create` ci-dessous. Par exemple, `arc`.

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```