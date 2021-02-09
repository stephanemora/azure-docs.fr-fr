---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494008"
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

   > [!NOTE]
   > RedHat OpenShift 4.5 ou version ultérieure modifie la façon dont la contrainte de contexte de sécurité est appliquée au compte de service.
   > Utilisez le même espace de noms ici et dans la commande `azdata arc dc create` ci-dessous. Par exemple, `arc`. 
   > 
   > Si vous utilisez RedHat OpenShift 4.5 ou une version ultérieure, exécutez : 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
