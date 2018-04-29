---
title: Développer des composants personnalisés payants, ou sous licence, pour le runtime d’intégration Azure-SSIS| Microsoft Docs
description: Cet article explique comment un éditeur de logiciels indépendant peut développer et installer des composants personnalisés payants ou sous licence pour le runtime d’intégration Azure-SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Développer des composants personnalisés payants, ou sous licence, pour le runtime d’intégration Azure-SSIS

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problème : le runtime d’intégration Azure-SSIS nécessite une approche différente

La nature du runtime d’intégration Azure-SSIS présente plusieurs difficultés, qui rendent les méthodes standard de gestion des licences utilisées pour l’installation en local des composants personnalisés inappropriées.

-   Les nœuds du runtime d’intégration Azure-SSIS sont volatils et peuvent être alloués ou libérés à tout moment. Par exemple, vous pouvez démarrer ou arrêter des nœuds pour gérer le coût ou procéder à une montée ou une descente en puissance sur différentes tailles de nœuds. Par conséquent, la liaison d’une licence de composants tiers à un nœud donné à l’aide d’informations spécifiques à la machine comme l’adresse MAC ou l’ID du processeur n’est plus viable.

-   Vous pouvez également augmenter ou diminuer la taille des instances du runtime d’intégration Azure-SSIS, ce qui vous permet de réduire ou développer le nombre de nœuds à tout moment.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Solution : les variables d’environnement Windows et du système SSIS dédiés à la liaison et la validation des licences

En raison des restrictions des méthodes traditionnelles de gestion des licences décrites dans la section précédente, le runtime d’intégration Azure-SSIS fournit les variables d’environnement Windows et du système SSIS dédiées à la liaison et la validation des licences des composants tiers. Les éditeurs de logiciels indépendants peuvent utiliser ces variables pour obtenir des données uniques et persistantes associées à un runtime d’intégration Azure-SSIS, comme l’ID de cluster et le nombre de nœuds de cluster. En s’appuyant sur ces données, les éditeurs de logiciels indépendants sont en mesure de lier la licence de leur composant à un runtime d’intégration Azure-SSIS *en tant que cluster*, avec un ID qui n’est jamais modifié, ni pendant les montées et descentes en puissance, ni pendant l’augmentation et la diminution du nombre d’instances, ni même pendant la reconfiguration du runtime d’intégration Azure-SSIS.

Le schéma suivant représente l’installation typique, l’activation et la liaison des licences, ainsi que les flux de validation pour les composants tiers qui utilisent ces nouvelles variables :

![Installation des composants sous licence](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instructions
1. Les éditeurs de logiciels indépendants peuvent proposer leurs composants sous licence sous diverses références ou niveaux (par exemple, nœud unique, jusqu’à 5 nœuds, jusqu’à 10 nœuds, etc.). L’éditeur fournit la clé de produit correspondante lorsque les clients acquièrent un produit. Il peut également octroyer le conteneur d’objets blob Stockage Azure, qui comporte le script d’installation éditeur et les fichiers associés. Les clients peuvent copier ces fichiers dans leur propre conteneur de stockage et les modifier avec leur propre clé de produit (par exemple, en exécutant `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Ils sont ensuite en mesure de provisionner ou de reconfigurer le runtime d’intégration Azure-SSIS avec l’URI SAS de leur conteneur en tant que paramètre. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS).

2. Lors du provisionnement ou de la reconfiguration du runtime d’intégration Azure-SSIS, le script d’installation éditeur est exécuté sur chaque nœud afin d’interroger les variables d’environnement Windows, `SSIS_CLUSTERID` et `SSIS_CLUSTERNODECOUNT`. Ensuite, le runtime d’intégration Azure-SSIS transmet son ID de cluster et la clé du produit sous licence au serveur d’activation éditeur, afin de générer une clé d’activation.

3. Une fois la clé d’activation reçue, le script d’installation éditeur peut la stocker en local sur chaque nœud (par exemple, dans le Registre).

4. Lorsque les clients exécutent un package utilisant le composant sous licence de l’éditeur sur un nœud du runtime d’intégration Azure-SSIS, le package lit la clé d’activation stockée en local et la valide en la comparant à l’ID de cluster du nœud. Le package peut également, éventuellement, transmettre le nombre de nœuds du cluster au serveur d’activation éditeur.

    Voici un exemple de code qui valide la clé d’activation et transmet le nombre de nœuds du cluster :

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

-   [Installation personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)