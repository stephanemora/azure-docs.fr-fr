---
title: Dépannage Azure Blockchain Workbench
description: Guide de dépannage d’une application Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8a2715666c4fff490f5184b7b8719b412952b9bf
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Dépannage Azure Blockchain Workbench

Un script PowerShell est disponible pour aider le développeur avec les activités de débogage ou de support technique. Le script génère un résumé et collecte les journaux détaillés pour le dépannage. Les journaux détaillés sont relatifs aux éléments suivants :

* Réseau Blockchain, tels qu’Ethereum
* Microservices Blockchain Workbench
* Application Insights
* Surveillance Azure (OMS)

Vous pouvez utiliser les informations pour identifier la procédure à suivre et déterminer la cause racine des problèmes. 

## <a name="troubleshooting-script"></a>Script de dépannage

Le script de dépannage PowerShell est disponible sur GitHub. [Téléchargez un fichier zip ](https://github.com/Azure-Samples/blockchain/archive/master.zip) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Exécutez le script
[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

Exécutez le script `collectBlockchainWorkbenchTroubleshooting.ps1` pour collecter des journaux et créer un fichier ZIP contenant un dossier des données de dépannage. Par exemple : 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Le script accepte les paramètres suivants :

| Paramètre  | Description | Obligatoire |
|---------|---------|----|
| SubscriptionID | ID d’abonnement associé à la création ou l’emplacement de l’ensemble des ressources. | OUI |
| ResourceGroupName | Nom du groupe de ressources Azure dans lequel a été déployé Blockchain Workbench. | OUI |
| OutputDirectory | Chemin d’accès utilisé pour la création du fichier ZIP de sortie. S’il n’est pas spécifié, il est défini par défaut sur le répertoire actuel. | Non 
| OmsSubscriptionId | L’ID de l’abonnement dans lequel l’instance OMS est déployée. Transmettez ce paramètre uniquement si l’instance OMS associée au réseau blockchain est déployée à l’extérieur du groupe de ressources Blockchain Workbench.| Non  |
| OmsResourceGroup |Le groupe de ressources dans lequel est déployée l’instance OMS. Transmettez ce paramètre uniquement si l’instance OMS associée au réseau blockchain est déployée à l’extérieur du groupe de ressources Blockchain Workbench.| Non  |
| OmsWorkspaceName | Le nom de l’espace de travail OMS. Transmettez ce paramètre uniquement si l’instance OMS associée au réseau blockchain est déployée à l’extérieur du groupe de ressources Blockchain Workbench. | Non  |

## <a name="what-is-collected"></a>Quels sont les éléments collectés ?

Le fichier ZIP de sortie contient la structure de dossier suivante :

| Dossier \ Fichier | Description  |
|---------|---------|
| \Summary.txt | Résumé du système |
| \metrics\blockchain | Mesures relatives à l’application blockchain |
| \metrics\workbench | Mesures relatives à l’application Workbench |
| \details\blockchain | Journaux détaillés relatifs à l’application blockchain |
| \details\workbench | Journaux détaillés relatifs à l’application workbench |

Le fichier de résumé vous octroie un aperçu sur l’état global de l’application et sur l’intégrité de l’application. Le résumé communique les actions recommandées, met en évidence les erreurs principales et les métadonnées sur les services en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Architecture Azure Blockchain Workbench](blockchain-workbench-architecture.md)