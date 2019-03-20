---
title: Vue d'ensemble des balises de service de Pare-feu Azure
description: Cet article offre une vue d'ensemble des balises de service de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805618"
---
# <a name="azure-firewall-service-tags"></a>Balises de service de Pare-feu Azure

Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Les balises de service de Pare-feu Azure peuvent être utilisées dans le champ de destination des règles réseau. Vous pouvez les utiliser à la place d'adresses IP spécifiques.

## <a name="supported-service-tags"></a>Balises de service prises en charge

Vous pouvez utiliser les balises de service suivantes dans les règles réseau de Pare-feu Azure :

* **AzureCloud** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour Azure, notamment l’ensemble des [adresses IP publiques du centre de données](https://www.microsoft.com/download/details.aspx?id=41653). Si vous spécifiez *AzureCloud* comme valeur, le trafic est autorisé ou refusé pour les adresses IP publiques Azure. Si vous souhaitez uniquement autoriser l’accès à AzureCloud dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région. Par exemple, pour autoriser l’accès uniquement à Azure AzureCloud dans la région USA Est, vous pouvez spécifier *AzureCloud.EastUS* en tant que balise de service. 
* **AzureTrafficManager** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour les adresses IP de sondage Azure Traffic Manager. Vous trouverez plus d’informations sur les adresses IP de sondage Traffic Manager dans les [Questions fréquentes (FAQ) sur Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Stockage** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour le service Stockage Azure. Si vous spécifiez *Storage* pour la valeur, le trafic est autorisé ou refusé vers le stockage. Si vous souhaitez uniquement autoriser l’accès au stockage dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure Storage uniquement dans la région USA Est, vous pouvez spécifier *Storage.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique.
* **Sql** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresses des services Azure SQL Database et Azure SQL Data Warehouse. Si vous spécifiez *Sql* pour la valeur, le trafic vers Sql est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à Sql dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure SQL Database uniquement dans la région USA Est, vous pouvez spécifier *Sql.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique.
* **AzureCosmosDB** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Cosmos DB. Si vous spécifiez *AzureCosmosDB* comme valeur, le trafic vers AzureCosmosDB est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à AzureCosmosDB dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format AzureCosmosDB.[nom_région].
* **AzureKeyVault** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Key Vault. Si vous spécifiez *AzureKeyVault* comme valeur, le trafic vers AzureKeyVault est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à AzureKeyVault dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format AzureKeyVault.[nom_région].
* **EventHub** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure EventHub. Si vous spécifiez *EventHub* comme valeur, le trafic vers EventHub est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à EventHub dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format EventHub.[nom_région]. 
* **ServiceBus** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure ServiceBus. Si vous spécifiez *ServiceBus* comme valeur, le trafic vers ServiceBus est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à ServiceBus dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format ServiceBus.[nom_région].
* **MicrosoftContainerRegistry** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Microsoft Container Registry. Si vous spécifiez *MicrosoftContainerRegistry* comme valeur, le trafic vers MicrosoftContainerRegistry est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à MicrosoftContainerRegistry dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format MicrosoftContainerRegistry.[nom_région].
* **AzureContainerRegistry** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Container Registry. Si vous spécifiez *AzureContainerRegistry* comme valeur, le trafic vers AzureContainerRegistry est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à AzureContainerRegistry dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format AzureContainerRegistry.[nom_région]. 
* **AppService** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure AppService. Si vous spécifiez *AppService* comme valeur, le trafic vers AppService est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à AppService dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format AppService.[nom_région]. 
* **AppServiceManagement** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure AppService Management. Si vous spécifiez *AppServiceManagement* comme valeur, le trafic vers AppServiceManagement est autorisé ou refusé. 
* **ApiManagement** (Resource Manager uniquement) : Cette balise indique les préfixes d'adresse du service Gestion des API Azure. Si vous spécifiez *ApiManagement* comme valeur, le trafic vers ApiManagement est autorisé ou refusé.  
* **AzureConnectors** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Connectors. Si vous spécifiez *AzureConnectors* comme valeur, le trafic vers AzureConnectors est autorisé ou refusé. Si vous souhaitez simplement autoriser l'accès à AzureConnectors dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format AzureConnectors.[nom_région].
* **AzureDataLake** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Data Lake. Si vous spécifiez *AzureDataLake* comme valeur, le trafic vers AzureDataLake est autorisé ou refusé.
* **AzureActiveDirectory** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureActiveDirectory. Si vous spécifiez *AzureActiveDirectory* comme valeur, le trafic vers AzureActiveDirectory est autorisé ou refusé.
* **AzureMonitor** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureMonitor. Si vous spécifiez *AzureMonitor* comme valeur, le trafic vers AzureMonitor est autorisé ou refusé.
* **ServiceFabric** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service ServiceFabric. Si vous spécifiez *ServiceFabric* comme valeur, le trafic vers ServiceFabric est autorisé ou refusé.
* **AzureMachineLearning** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureMachineLearning. Si vous spécifiez *AzureMachineLearning* comme valeur, le trafic est autorisé ou refusé à AzureMachineLearning.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les règles du service Pare-feu Azure, consultez [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).