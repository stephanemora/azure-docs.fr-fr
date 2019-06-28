---
title: Migrer des ressources de calcul Azure depuis Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de calcul Azure depuis Azure Germany vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: c48c757a0c1a4ff0bf0bffbd49c50efbc2c9b93b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033749"
---
# <a name="migrate-compute-resources-to-global-azure"></a>Migrer des ressources de calcul vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources de calcul Azure depuis Azure Germany vers Azure global.

## <a name="compute-iaas"></a>IaaS de calcul

Vous ne pouvez pas migrer directement l’infrastructure de calcul Azure vers des ressources d’infrastructure as a service (IaaS) depuis Azure Germany vers Azure global. Toutefois, il existe différentes façons de « dupliquer » vos machines virtuelles.

### <a name="duplicate-by-using-site-recovery"></a>Duplication via Site Recovery

Azure Site Recovery peut vous aider à migrer votre machines virtuelles depuis Azure Germany vers Azure global. Comme la source et la cible se trouvent sur des locataires différents lors d’une migration depuis Azure Germany vers Azure global, vous ne pouvez pas utiliser l’option de récupération d’urgence normale proposée par Azure pour les machines virtuelles. L’astuce consiste à configurer un coffre Site Recovery sur l’environnement cible (Azure global) et à procéder comme si vous déplaciez un serveur physique vers Azure. Sur le Portail Microsoft Azure, sélectionnez un chemin de réplication indiqué comme **non virtualisé**. Une fois la réplication terminée, effectuez un basculement.

> [!NOTE]
> Les étapes suivantes sont celles que vous pouvez suivre pour migrer un serveur physique en cours d’exécution en local sur Azure.

Pour en savoir plus, passez en revue ce [didacticiel très utile sur Site Recovery](../site-recovery/physical-azure-disaster-recovery.md). Pour une vue d’ensemble rapide, voici une version plus courte et légèrement modifiée du processus :

Installez un serveur de configuration/processus dans votre environnement source pour générer les images de serveur. Ensuite, répliquez les images dans le coffre Azure Recovery Services au sein de votre environnement cible. Le travail est entièrement effectué par le serveur de configuration. Vous n’avez pas besoin de modifier les serveurs individuels.

1. Connectez-vous au Portail Azure Germany.
1. Comparez les versions de système d’exploitation des machines virtuelles que vous souhaitez migrer avec la [matrice de prise en charge](../site-recovery/vmware-physical-secondary-support-matrix.md).
1. Configurez une nouvelle machine virtuelle sur votre instance de Réseau virtuel Microsoft Azure source, afin qu’elle agisse en tant que serveur de configuration :
   1. Sélectionnez **DS4v3** ou version supérieure (4 à 8 cœurs, 16 Go de mémoire).
   1. Connectez un disque supplémentaire, disposant d’au moins 1 To d’espace disponible (pour les images de machine virtuelle).
   1. Utilisez Windows Server 2012 R2 ou version ultérieure.
1. Assurez-vous que les ports 443 et 9443 sont ouverts pour le sous-réseau, dans les deux sens.
1. Connectez-vous à la nouvelle machine virtuelle (ConfigurationServer).
1. Lors de votre session Bureau à distance, connectez-vous au Portail Azure global à l’aide des informations d’identification correspondantes.
1. Configurez le réseau virtuel dans lequel les machines virtuelles répliquées seront exécutées.
1. Création d’un compte Azure Storage.
1. Configurez le coffre Recovery Services.
1. Définissez le champ **Objectif de protection** (**Vers Azure** > **Non virtualisé/Autre**).
1. Téléchargez le fichier d’installation unifiée de Site Recovery (**Préparer l’infrastructure** > **Source**). Lorsque vous ouvrez l’URL du portail à partir de ConfigurationServer, le fichier est téléchargé sur le serveur approprié. Depuis un emplacement autre que ConfigurationServer, chargez le fichier d’installation sur ConfigurationServer.
1. Téléchargez la clé d’inscription du coffre (chargement sur ConfigurationServer, tel qu’indiqué à l’étape précédente, si nécessaire).
1. Exécutez l’installation unifiée de Site Recovery sur ConfigurationServer.
1. Configurez l’environnement cible (vérifiez que vous êtes toujours connecté au portail cible).
1. Définissez la stratégie de réplication.
1. Démarrez la réplication.

Une fois la première réplication réussie, testez un scénario de basculement. Vérifiez, puis supprimez le test. L’étape finale consiste à effectuer le basculement réel.

> [!CAUTION]
> Aucune synchronisation n’est effectuée avec la machine virtuelle source. Si vous souhaitez effectuer une migration à nouveau, nettoyez tout et recommencez depuis le début !

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>Duplication à l’aide d’une importation/exportation du modèle Resource Manager

Vous pouvez exporter le modèle Azure Resource Manager que vous utilisez pour le déploiement sur votre ordinateur local. Modifiez le modèle afin de changer l’emplacement et d’autres paramètres ou variables. Ensuite, effectuez à nouveau le déploiement dans Azure global. 

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Exportez le modèle Resource Manager vers le portail en sélectionnant le groupe de ressources. Sélectionnez **Déploiements**, puis choisissez le déploiement le plus récent. Sélectionnez **Modèle** dans le menu de gauche, puis téléchargez le modèle.

Un fichier .zip incluant plusieurs fichiers se télécharge. Les scripts PowerShell, Azure CLI, Ruby ou .NET vous aident à déployer votre modèle. Le fichier *parameters.json* inclut toutes les entrées effectuées depuis le dernier déploiement. Il est probable que vous deviez modifier certains paramètres dans ce fichier. Modifiez le fichier *template.json* si vous souhaitez redéployer uniquement un sous-ensemble des ressources.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [didacticiels sur Site Recovery](https://docs.microsoft.com/azure/site-recovery/#step-by-step-tutorials).
- Apprenez comment [exporter des modèles Microsoft Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou lisez la présentation de [Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Découvrez plus d’informations sur la [récupération d’urgence depuis un site physique vers Azure à l’aide de Site Recovery](../site-recovery/physical-azure-disaster-recovery.md).
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="cloud-services"></a>Cloud Services

Vous pouvez redéployer des ressources Azure Cloud Services en fournissant les définitions `.cspkg` et `.cscfg` une nouvelle fois.

### <a name="azure-portal"></a>Portail Azure

Pour redéployer des services Cloud sur le Portail Microsoft Azure, procédez comme suit :

1. [Créez un service cloud](../cloud-services/cloud-services-how-to-create-deploy-portal.md) en utilisant vos définitions `.cspkg` et `.cscfg`.
1. Mettez à jour [l’enregistrement CNAME ou A](../cloud-services/cloud-services-custom-domain-name-portal.md) pour faire pointer le trafic vers le nouveau service cloud.
1. Lorsque le trafic pointe vers le nouveau service cloud, supprimez l’ancien service cloud dans Azure Germany.

### <a name="powershell"></a>PowerShell

Pour redéployer des services cloud à l’aide de PowerShell, procédez comme suit :

1. [Créez un service cloud](/powershell/module/servicemanagement/azure/new-azureservice) à l’aide de vos définitions `.cspkg` et `.cscfg`.

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. [Créez un déploiement](/powershell/module/servicemanagement/azure/new-azuredeployment) à l’aide de vos définitions `.cspkg` et `.cscfg`.

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. Mettez à jour [l’enregistrement CNAME ou A](../cloud-services/cloud-services-custom-domain-name-portal.md) pour faire pointer le trafic vers le nouveau service cloud.
1. Lorsque le trafic pointe vers le nouveau service cloud, [supprimez l’ancien service cloud](/powershell/module/servicemanagement/azure/remove-azureservice) dans Azure Germany.

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>API REST

Pour redéployer des services cloud à l’aide de l’API REST, procédez comme suit :

1. [Créez un service cloud](/rest/api/compute/cloudservices/rest-create-cloud-service) dans l’environnement cible.

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. Créez un déploiement à l’aide de [l’API de création de déploiement](/previous-versions/azure/reference/ee460813(v=azure.100)). Pour rechercher vos définitions `.cspkg` et `.cscfg`, vous pouvez appeler [l’API d’obtention de package](/previous-versions/azure/reference/jj154121(v=azure.100)).

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. Lorsque le trafic pointe vers le nouveau service cloud, [supprimez l’ancien service cloud](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-delete-cloud-service) dans Azure Germany.

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

Pour plus d'informations :

- Consultez la [vue d’ensemble des services Microsoft Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

## <a name="service-fabric"></a>Service Fabric

Vous ne pouvez pas migrer des ressources Microsoft Azure Service Fabric depuis Azure Germany vers Azure global. Vous devez redéployer les ressources Service Fabric dans le nouvel environnement.

Vous pouvez obtenir des informations relatives à votre environnement Service Fabric actuel à l’aide des cmdlets PowerShell. Accédez à toutes les cmdlets associées à Service Fabric en saisissant `Get-Help *ServiceFabric*` dans PowerShell.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [didacticiels sur Service Fabric](https://docs.microsoft.com/azure/service-fabric/#step-by-step-tutorials).
- Découvrez comment [créer un cluster](../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Consultez la [vue d’ensemble de Service Fabric](../service-fabric/service-fabric-overview.md).

## <a name="batch"></a>Batch

Vous ne pouvez pas migrer les données de compte Azure Batch d’une région à une autre. En effet, le compte peut être associé à des machines virtuelles en cours d’exécution et interagir fréquemment avec les données des comptes de stockage, bases de données ou autres systèmes de stockage.

Redéployez vos scripts de déploiement, vos modèles ou votre code dans la nouvelle région. Le redéploiement inclut les tâches suivantes :

1. [Création d’un compte Batch](../batch/batch-account-create-portal.md).
1. [Augmentation du quota associé à votre compte Batch](../batch/batch-quota-limit.md).
1. Création de pools Batch.
1. Création de comptes de stockage, de bases de données et d’autres services utilisés pour conserver les données d’entrée et de sortie.
1. Mise à jour de votre configuration et de votre code afin qu’ils pointent vers le nouveau compte Batch et pour utiliser de nouvelles informations d’identification.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [tutoriels sur Azure Batch](https://docs.microsoft.com/azure/batch/#step-by-step-tutorials).
- Consultez la [vue d’ensemble d’Azure Batch](../batch/batch-technical-overview.md).

## <a name="functions"></a>Fonctions

Actuellement, il n’est pas possible de migrer des ressources Azure Functions depuis Azure Germany vers Azure global. Nous vous recommandons d’exporter le modèle Resource Manager, de modifier l’emplacement et de le redéployer vers la région cible.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats, les paramètres d’application et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [didacticiels sur Azure Functions](https://docs.microsoft.com/azure/azure-functions/#step-by-step-tutorials).
- Apprenez comment [exporter des modèles Microsoft Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou lisez une présentation de [Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Consultez la [vue d’ensemble d’Azure Functions](../azure-functions/functions-overview.md)
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="virtual-machine-scale-sets"></a>Groupes identiques de machines virtuelles

Pour migrer des groupes de machines virtuelles identiques vers Azure global, exportez le modèle Resource Manager, adaptez-le au nouvel environnement, puis redéployez-le dans la région cible. Exportez uniquement le modèle de base, puis redéployez le modèle dans le nouvel environnement. Les instances de groupes groupe de machines virtuelles identiques doivent toutes être identiques.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [didacticiels sur les groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/#step-by-step-tutorials).
- Découvrez comment [exporter des modèles Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Consultez la [présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Accédez à une présentation des [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/overview.md).
- Lisez une [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="web-apps"></a>Web Apps

Actuellement, les applications que vous avez créées à l’aide de la fonctionnalité Web Apps d’Azure App Service ne peuvent pas être migrées à partir d’Azure Germany vers Azure global. Nous vous recommandons d’exporter une application Web en tant que modèle Resource Manager, puis de la redéployer lorsque vous avez modifié la propriété d’emplacement pour qu’elle porte sur la nouvelle région.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [didacticiels sur App Service](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials).
- Apprenez comment [exporter des modèles Microsoft Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou lisez une présentation de [Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Consultez la [présentation d’App Service](../app-service/overview.md).
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
