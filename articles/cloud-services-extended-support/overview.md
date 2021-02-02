---
title: À propos d’Azure Cloud Services (support étendu)
description: Découvrez les éléments enfants de l’élément NetworkConfiguration du fichier de configuration de service, qui spécifie les valeurs de réseau virtuel et DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 969f60cd92e8c1cbe93f1646cccd08c942ad9923
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762826"
---
# <a name="about-azure-cloud-services-extended-support"></a>À propos d’Azure Cloud Services (support étendu)

> [!IMPORTANT]
> Azure Cloud Services (support étendu) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cloud Services (support étendu) est un nouveau modèle de déploiement basé sur  [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) pour le produit  [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/). Il est actuellement disponible en préversion publique. Azure Cloud Services (support étendu) a pour principal avantage de fournir une résilience régionale ainsi qu’une parité des fonctionnalités avec Azure Cloud Services déployé à l’aide d’Azure Service Manager. Il offre également certaines capacités ARM telles que l’accès et le contrôle en fonction du rôle (RBAC), les balises, la stratégie et prend en charge les modèles de déploiement.  

Avec cette modification, le modèle de déploiement basé sur Azure Service Manager pour Azure Cloud Services est renommé [Azure Cloud Services (classique)](../cloud-services/cloud-services-choose-me.md). Vous conserverez la capacité de créer et de déployer rapidement vos applications et services cloud et web. Vous serez en mesure de mettre à l’échelle votre infrastructure de services cloud en fonction de la demande actuelle et de vous assurer que les performances de vos applications puissent suivre le rythme tout en réduisant les coûts.  

## <a name="what-does-not-change"></a>Ce qui ne change pas 
- Vous créez le code, définissez les configurations et le déployez sur Azure. Azure configure l’environnement Compute, exécute votre code, puis le supervise le gère à votre place.
- Azure Cloud Services (support étendu) prend également en charge deux types de rôles, [web et Worker](../cloud-services/cloud-services-choose-me.md). Il n’y a pas de changement dans la conception, l’architecture ou les composants des rôles Worker et web. 
- Les trois composants d’un service cloud, à savoir la définition de service (.csdef), la configuration de service (.cscfg) et le package de services (.cspkg), sont reportés et leur [format](cloud-services-model-and-package.md) n’est pas modifié. 
- Aucune modification n’est requise pour le code du runtime, car le plan de données est identique et seul le plan de contrôle change. 
- Les versions du système d’exploitation invité d’Azure et les mises à jour associées sont alignées sur Azure Cloud Services (classique).
- Le processus de mise à jour sous-jacent en ce qui concerne les domaines de mise à jour, la procédure de mise à niveau, la restauration et les changements de service autorisés pendant une mise à jour ne changent pas.

## <a name="changes-in-deployment-model"></a>Modifications du modèle de déploiement

Des modifications minimes sont requises dans les fichiers de configuration de service (.cscfg) et de définition de service (.csdef) pour déployer Azure Cloud Services (support étendu). Aucune modification n’est requise pour le code du runtime. Toutefois, les scripts de déploiement devront être mis à jour pour appeler les nouvelles API basées sur Azure Resource Manager. 

:::image type="content" source="media/overview-image-1.png" alt-text="L’image montre la configuration classique d’un service cloud avec l’ajout d’une section de modèle.":::

Les principales différences entre Azure Cloud Services (classique) et Azure Cloud Services (support étendu) par rapport au déploiement sont les suivantes : 

- Les déploiements Azure Resource Manager utilisent des [modèles ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) qui sont un fichier JavaScript Object Notation (JSON) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative, qui vous permet d’indiquer ce que vous envisagez de déployer sans avoir à écrire la séquence de commandes de programmation pour le créer. La configuration du service et le fichier de définition de service doivent être cohérents avec le [modèle ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) lors du déploiement d’Azure Cloud Services (support étendu). Pour ce faire, vous pouvez [créer manuellement le modèle ARM](deploy-template.md) ou le créer à l’aide de [PowerShell](deploy-powershell.md), [Portail Azure](deploy-portal.md) et [Visual Studio](deploy-visual-studio.md).  

- Les clients doivent utiliser [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) pour [gérer les certificats dans Azure Cloud Services (support étendu)](certificates-and-key-vault.md). Azure Key Vault vous permet de stocker et de gérer en toute sécurité les informations d’identification de l’application telles que les secrets, les clés et les certificats dans un référentiel cloud central et sécurisé. Vos applications peuvent s’authentifier auprès de Key Vault au moment de l’exécution pour récupérer les informations d’identification. 

- Toutes les ressources déployées via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) doivent se trouver à l’intérieur d’un réseau virtuel. Les réseaux virtuels et les sous-réseaux sont créés dans Azure Resource Manager à l’aide des API Azure Resource Manager existantes et doivent être référencés dans la section NetworkConfiguration du fichier .cscfg lors du déploiement d’Azure Cloud Services (support étendu).   

- Chaque service cloud (support étendu) est un déploiement indépendant unique. Azure Cloud Services (support étendu) ne prend pas en charge plusieurs emplacements dans un même service cloud.  
    - La capacité d’échange d’adresses IP virtuelles<sup>*</sup> peut être utilisée pour effectuer un échange entre deux services cloud (support étendu). Pour tester et mettre en place une nouvelle version d’un service cloud, déployez un service cloud (support étendu) et marquez-le pour indiquer que son adresse IP virtuelle peut être échangée avec celle d’un autre service cloud (support étendu).  

- L’étiquette DNS (Domain Name Service) est facultative pour un service cloud (support étendu). Dans Azure Resource Manager, l’étiquette DNS est une propriété de la ressource IP publique associée au service cloud. 


<sup>*</sup> L’échange d’adresses IP virtuelles pour Azure Cloud Services (support étendu) n’est pas disponible pendant la Préversion publique.  

## <a name="migration-to-azure-resource-manager"></a>Migration vers Azure Resource Manager

Azure Cloud Services (support étendu) vous offre deux possibilités pour migrer d’[Azure Service Manager](https://docs.microsoft.com/powershell/azure/servicemanagement/overview?view=azuresmps-4.0.0&preserve-view=true ) vers [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 
1) Les clients déploient les services cloud directement dans Azure Resource Manager, puis suppriment l’ancien service cloud dans Azure Service Manager. 
2) La migration sur place permet de migrer Azure Cloud Services (classique) avec un temps d’arrêt minimal ou nul vers Azure Cloud Services (support étendu). 

### <a name="additional-migration-options"></a>Options de migration supplémentaires

Lors de l’évaluation des plans de migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu), vous pouvez examiner d’autres services Azure, tels que : [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview), [App Service](https://docs.microsoft.com/azure/app-service/overview), [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes) et [Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview). Ces services continueront à offrir des capacités supplémentaires, tandis qu’Azure Cloud Services (support étendu) conservera principalement la parité des fonctionnalités avec Azure Cloud Services (classique). 

En fonction de l’application, Azure Cloud Services (support étendu) peut nécessiter beaucoup moins d’efforts pour passer à Azure Resource Manager par rapport à d’autres options. Si votre application n’évolue pas, Azure Cloud Services (support étendu) est une option viable à prendre en compte, car elle fournit un chemin de migration rapide. À l’inverse, si votre application évolue en permanence et a besoin d’un ensemble de fonctionnalités plus moderne, explorez d’autres services Azure pour mieux répondre à vos besoins actuels et futurs. 

## <a name="next-steps"></a>Étapes suivantes
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) d’Azure Cloud Services (support étendu).
- Déployez une instance d’Azure Cloud Services (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
