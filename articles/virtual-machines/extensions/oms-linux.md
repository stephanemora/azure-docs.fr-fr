---
title: Extension de machine virtuelle Log Analytics pour Linux
description: Déployez l’agent Log Analytics sur une machine virtuelle Linux avec une extension de machine virtuelle.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: d24463834a49cdfbdb4599770caf579ae94582d8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999816"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle Log Analytics pour Linux

## <a name="overview"></a>Vue d’ensemble

Les journaux Azure Monitor fournissent des fonctionnalités de supervision, d’alerte et de correction d’alertes pour les ressources cloud et locales. L’extension de machine virtuelle Log Analytics pour Linux est publiée et prise en charge par Microsoft. L’extension installe l’agent Log Analytics sur les machines virtuelles Azure et inscrit les machines virtuelles dans un espace de travail Log Analytics existant. Ce document présente les plateformes, configurations et options de déploiement prises en charge pour l’extension de machine virtuelle Log Analytics pour Linux.

>[!NOTE]
>Dans le cadre de la transition en cours entre Microsoft Operations Management Suite (OMS) et Azure Monitor, l’agent OMS pour Windows ou Linux sera appelé l’agent Log Analytics pour Windows et l’agent Log Analytics pour Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

Pour plus d’informations sur les distributions Linux prises en charge, consultez l’article [Vue d’ensemble des agents Azure Monitor](../../azure-monitor/platform/agents-overview.md#supported-operating-systems).

### <a name="agent-and-vm-extension-version"></a>Version de l’agent et de l’extension de machine virtuelle
Le tableau ci-après mappe la version de l’extension de machine virtuelle Log Analytics sur la version du bundle de l’agent Log Analytics pour chaque mise en production. Un lien vers les notes de publication pour la version du bundle de l’Agent Log Analytics est inclus. Les notes de version fournissent des détails sur les correctifs de bogues et les nouvelles fonctionnalités disponibles pour une version spécifique de l’agent.  

| Version d’extension de machine virtuelle Linux Log Analytics | Version du bundle de l’Agent Log Analytics | 
|--------------------------------|--------------------------|
| 1.13.13 | [1.13.7-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.7-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center configure automatiquement l’agent Log Analytics et le connecte à l’espace de travail Log Analytics par défaut créé par ASC dans votre abonnement Azure. Si vous utilisez Azure Security Center, ne suivez pas la procédure de ce document. Si vous le faites, vous écrasez l’espace de travail configuré et interrompez la connexion à Azure Security Center.

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension de l’agent Log Analytics pour Linux nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension d’agent Log Analytics. L’extension nécessite l’ID et la clé de l’espace de travail Log Analytics cible ; Ces valeurs peuvent être [trouvées dans votre espace de travail Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) dans le portail Azure. La clé de l’espace de travail devant être traitée comme une donnée sensible, elle est stockée dans une configuration protégée. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible. Notez que **workspaceId** et **workspaceKey** respectent la casse.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Le schéma ci-dessus repose sur le principe qu’il est placé au niveau racine du modèle. Si vous le placez à l’intérieur de la ressource de machine virtuelle dans le modèle, vous devez modifier les propriétés `type` et `name`, comme décrit [dans la suite de cet article](#template-deployment).

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (par exemple) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (par exemple) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Les modèles sont particulièrement adaptés lorsque vous déployez une ou plusieurs machines virtuelles nécessitant une configuration post-déploiement, comme l’intégration aux journaux Azure Monitor. Vous trouverez un exemple de modèle Resource Manager qui inclut l’extension de machine virtuelle Agent Log Analytics dans la [Galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

La configuration JSON pour une extension de machine virtuelle peut être imbriquée à l’intérieur de la ressource de machine virtuelle ou placée à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement de la configuration JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/templates/child-resource-name-type.md). 

L’exemple suivant suppose que l’extension de machine virtuelle est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente, et le type reflète la configuration imbriquée.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle d’agent Log Analytics sur une machine virtuelle existante. Remplacez la valeur de *myWorkspaceKey* ci-dessous par votre clé d’espace de travail et la valeur de *myWorkspaceId* par votre ID d’espace de travail. Ces valeurs réside dans votre espace de travail Log Analytics dans le Portail Azure, sous *Paramètres avancés*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Dépanner

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie de l’exécution de l’extension est enregistrée dans le fichier suivant :

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Codes d’erreur et signification

| Code d'erreur | Signification | Action possible |
| :---: | --- | --- |
| 9 | Activation appelée prématurément | [Mettez à jour l’agent Azure Linux](./update-linux-agent.md) à la dernière version disponible. |
| 10 | La machine virtuelle est déjà connectée à un espace de travail Log Analytics | Pour connecter la machine virtuelle à l’espace de travail spécifié dans le schéma d’extension, définissez stopOnMultipleConnections sur la valeur false dans les paramètres publics ou supprimez cette propriété. Cette machine virtuelle est facturée une fois pour chaque espace de travail auquel elle est connectée. |
| 11 | Configuration non valide fournie à l’extension | Suivez les exemples précédents pour définir toutes les valeurs de propriété du déploiement. |
| 17 | Échec d’installation du package Log Analytics | 
| 19 | Échec d’installation du package OMI | 
| 20 | Échec d’installation du package SCX |
| 51 | Cette extension n’est pas prise en charge sur le système d’exploitation de la machine virtuelle | |
| 55 | Impossible de se connecter au service Azure Monitor, car des packages nécessaires sont manquants ou le gestionnaire de package dpkg est verrouillé| Vérifiez que le système dispose d’un accès Internet ou qu’un proxy HTTP valide a été fourni. Ensuite, vérifiez que le bon ID d’espace de travail est utilisé, puis vérifiez que les utilitaires curl et tar sont installés. |

Pour plus d’informations sur la résolution des problèmes, consultez le [guide de résolution des problèmes de l’agent Log Analytics pour Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
