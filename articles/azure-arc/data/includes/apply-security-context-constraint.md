---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687586"
---
Cette section explique comment appliquer une contrainte de contexte de sécurité (SCC). Pour la préversion, ces dernières assouplissent les contraintes de sécurité. 

1. Téléchargez la contrainte de contexte de sécurité (SCC) personnalisée. Utilisez l’une des valeurs suivantes : 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Brut](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      La commande  suivante télécharge arc-data-scc.yaml :

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

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
