---
title: Résoudre les problèmes de gestion du runtime d’intégration SSIS dans Azure Data Factory | Microsoft Docs
description: Cet article explique comment résoudre les problèmes liés à la gestion du runtime d’intégration SSIS (SSIS IR).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253016"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Résoudre les problèmes de gestion du runtime d’intégration SSIS dans Azure Data Factory

Ce document explique comment résoudre les problèmes de gestion du runtime d’intégration SSIS (SSIS IR).

## <a name="overview"></a>Vue d'ensemble

En cas de problème avec le provisionnement ou le déprovisionnement d’un runtime d’intégration SSIS, un message d’erreur s’affiche dans le portail ADF ou est retourné par une applet de commande PowerShell. L’erreur est toujours présentée sous la forme d’un code d’erreur avec un message d’erreur détaillé.

Si le code d’erreur est InternalServerError, cela signifie que le service rencontre des problèmes temporaires. Vous pouvez retenter l’opération plus tard. Si la nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.

Le plus souvent, les trois dépendances externes qui provoquent des erreurs sont les suivantes : le serveur Azure SQL Database ou Azure SQL Database Managed Instance, les scripts d’installation personnalisés et la configuration du réseau virtuel (si le code d’erreur n’est pas InternalServerError).

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problèmes relatifs au serveur Azure SQL Database ou à Azure SQL Database Managed Instance

Si vous provisionnez le runtime d’intégration SSIS avec la base de données du catalogue SSIS, vous aurez besoin d’un serveur Azure SQL Database ou d’Azure SQL Database Managed Instance. Ceux-ci doivent être accessibles par le runtime d’intégration SSIS. Le compte du serveur Azure SQL Database ou d’Azure SQL Database Managed Instance doit avoir l’autorisation de créer une base de données de catalogue SSIS (SSISDB). En cas d’erreur, un code d’erreur avec un message d’exception SQL détaillé s’affiche dans le portail ADF. Suivez les étapes ci-dessous pour résoudre les codes d’erreur.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Ce message d’erreur peut s’afficher lors du provisionnement d’un nouveau runtime d’intégration SSIS ou lors de l’exécution d’un runtime d’intégration.

Pendant le provisionnement du runtime d’intégration, cette erreur peut se produire pour les raisons suivantes. Dans ce cas, le message d’erreur comprend un message SqlException détaillé.

* Problème de connexion réseau. Vérifiez que le nom d’hôte SQL Server ou Managed Instance est accessible, et qu’il n’existe aucun pare-feu ou groupe de sécurité réseau qui empêchent le runtime d’intégration SSIS d’accéder au serveur.
* La connexion a échoué et l’authentification SQL a été utilisée. Cela signifie que le compte fourni ne peut pas se connecter à SQL Server. Vérifiez que le bon compte d’utilisateur a été fourni.
* La connexion a échoué et l’authentification AAD (identité managée) a été utilisée. Ajoutez l’identité managée de votre fabrique à un groupe AAD et autorisez l’identité à accéder à votre serveur de base de données de catalogue.
* Le délai de connexion a expiré (toujours causé par la configuration de la sécurité). Il est recommandé de créer une machine virtuelle, de la joindre au même réseau virtuel que le runtime d’intégration (si celui-ci se trouve sur un réseau virtuel), puis d’installer SSMS et de vérifier l’état du serveur Azure SQL Database ou d’Azure SQL Database Managed Instance.

Pour les autres problèmes, reportez-vous aux informations détaillées du message d’erreur d’exception SQL et corrigez le problème signalé dans le message d’erreur. Si vous rencontrez encore des problèmes, contactez l’équipe du support Azure SQL Database Managed Instance ou celle chargée des serveurs Azure SQL Database.

Si cette erreur se produit pendant l’exécution du runtime d’intégration, il est très probable que des modifications aient été apportées au groupe de sécurité réseau ou au pare-feu. En effet, ces modifications empêchent le nœud Worker du runtime d’intégration SSIS d’accéder au serveur Azure SQL Database ou à Azure SQL Database Managed Instance. Débloquez le nœud Worker du runtime d’intégration SSIS pour lui permettre d’accéder au serveur Azure SQL Database ou à Azure SQL Database Managed Instance.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Le message d’erreur ressemble à celui-ci : « La base de données 'SSISDB' a atteint son quota de taille ». Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles. Voici les solutions possibles :
* Augmentez le quota de taille de votre base de données SSIS.
* Modifiez la configuration de la base de données SSIS pour réduire la taille, par exemple :
   * En raccourcissant la période de conservation et en réduisant le nombre de versions des projets
   * En raccourcissant la période de conservation des journaux
   * En modifiant le niveau par défaut des journaux, etc.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Cette erreur signifie que le serveur Azure SQL Database ou qu’Azure SQL Database Managed Instance comprend déjà une base de données SSIS qui est utilisée par un autre runtime d’intégration. Vous devez fournir un autre serveur Azure SQL Database ou une autre instance d’Azure SQL Database Managed Instance, ou sinon, supprimer la base de données SSIS existante et redémarrer le nouveau runtime d’intégration.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Cette erreur peut se produire pour deux raisons :

* Le compte d’utilisateur configuré pour le runtime d’intégration SSIS n’est pas autorisé à créer la base de données. Vous pouvez autoriser l’utilisateur à créer la base de données.
* Créez un délai d’expiration pour la base de données, comme un délai d’expiration d’exécution, un délai d’expiration d’opération de base de données, etc. Vous pouvez réessayer plus tard pour voir si le problème est résolu. Si la nouvelle tentative a échoué, contactez l’équipe du support Azure SQL Database Managed Instance ou celle chargée des serveurs Azure SQL Database.

Pour les autres problèmes, reportez-vous aux informations détaillées du message d’erreur d’exception SQL et corrigez le problème signalé dans le message d’erreur. Si vous rencontrez encore des problèmes, contactez l’équipe du support Azure SQL Database Managed Instance ou celle chargée des serveurs Azure SQL Database.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Un message d’erreur du type « Le nom d’objet 'catalog.catalog_properties' n’est pas valide » signifie soit que vous avez déjà une base de données nommée SSISDB, mais qu’elle n’a pas été créée par le runtime d’intégration SSIS, soit que l’état de la base de données n’est pas valide, en raison d’erreurs survenues lors du dernier provisionnement du runtime d’intégration SSIS. Vous pouvez supprimer une base de données existante portant le nom de SSISDB, ou configurer un nouveau serveur Azure SQL Database ou une nouvelle instance Azure SQL Database Managed Instance pour le runtime d’intégration.

## <a name="custom-setup"></a>Installation personnalisée

L’installation personnalisée fournit une interface permettant d’ajouter vos propres étapes d’installation lors du provisionnement ou de la reconfiguration de votre runtime d’intégration SSIS. Pour plus d’informations, consultez [Installation personnalisée du runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Vérifiez que votre conteneur comprend uniquement les fichiers nécessaires à l’installation personnalisée, car tous les fichiers du conteneur seront téléchargés sur le nœud Worker du runtime d’intégration SSIS. Il est recommandé de tester le script d’installation personnalisée sur un ordinateur local pour résoudre les problèmes d’exécution du script avant d’exécuter celui-ci dans le runtime d’intégration SSIS.

Le conteneur du script d’installation personnalisée fait également l’objet d’une vérification pendant l’exécution du runtime d’intégration SSIS, car celui-ci est régulièrement mis à jour, ce qui nécessite d’accéder une nouvelle fois au conteneur pour télécharger le script d’installation personnalisée, puis de le réinstaller. Lors de la vérification, il est également vérifié que le conteneur est accessible et que le fichier main.cmd existe.

Si une erreur se produit lors de l’installation personnalisée, vous verrez un message d’erreur avec le code CustomSetupScriptFailure. Consultez le message d’erreur qui contient un sous-code d’erreur.  Suivez les étapes ci-dessous pour résoudre les sous-codes d’erreur.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Cela signifie que le runtime d’intégration SSIS ne peut pas accéder à votre conteneur d’objets Blob Azure pour l’installation personnalisée. Vérifiez que l’URI SAS du conteneur est accessible et qu’il n’a pas expiré.

Tout d’abord, arrêtez le runtime d’intégration s’il est en cours d’exécution. Reconfigurez le runtime d’intégration avec le nouvel URI SAS du conteneur d’installation personnalisée, puis redémarrez le runtime d’intégration.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Cela signifie que le runtime d’intégration SSIS ne trouve pas le script d’installation personnalisée (main. cmd) dans votre conteneur d’objets Blob. Vérifiez que le fichier main.cmd se trouve dans le conteneur, qui est le point d’entrée de l’installation personnalisée.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Cela signifie que l’exécution du script d’installation personnalisée (main.cmd) a échoué. Vous pouvez d’abord essayer le script sur votre ordinateur local ou vérifier les journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets Blob.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Cela signifie que le délai d’exécution du script d’installation personnalisée a expiré. Vérifiez que votre conteneur d’objets Blob contient uniquement les fichiers nécessaires à l’installation personnalisée. Vous pouvez également vérifier les journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets Blob. Le délai maximal pour l’installation personnalisée est actuellement fixé à 45 minutes. Ce délai comprend le temps nécessaire au téléchargement de tous les fichiers de votre conteneur, ainsi que le temps nécessaire à leur installation sur le runtime d’intégration SSIS. Si un délai plus long est nécessaire, envoyez-nous un ticket de support.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Cela signifie que le chargement des journaux d’exécution de l’installation personnalisée dans votre conteneur d’objets Blob a échoué, soit parce que le runtime d’intégration SSIS n’a pas l’autorisation d’écriture sur votre conteneur d’objets Blob, soit à cause de problèmes liés au stockage ou au réseau. Si l’installation personnalisée réussit, cette erreur n’impacte pas le fonctionnement de SSIS, toutefois, les journaux seront manquants. Si l’installation personnalisée a échoué avec une autre erreur et si le chargement du journal échoue, nous signalerons cette erreur en premier pour que le journal puisse être chargé à des fins d’analyse. Ensuite, nous signalerons l’autre problème. Si ce problème n’est pas résolu après une nouvelle tentative, contactez l’équipe du support technique Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuration du réseau virtuel

Quand vous joignez le runtime d’intégration SSIS à un réseau virtuel, c’est le réseau virtuel de l’abonnement utilisateur qui est utilisé. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

En cas de problème lié au réseau virtuel, l’erreur ci-dessous s’affiche.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Cela peut être dû à différentes raisons. Suivez les étapes ci-dessous pour résoudre les sous-codes d’erreur.

### <a name="forbidden"></a>Interdit

Le message d’erreur ressemble à celui-ci : « subnetId n’est pas activé pour le compte actuel. Le fournisseur de ressources Microsoft.Batch n’est pas inscrit dans le même abonnement de réseau virtuel ».

Cela signifie qu’Azure Batch ne peut pas accéder à votre réseau virtuel. Inscrivez le fournisseur de ressources Microsoft.Batch dans le même abonnement de réseau virtuel.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Le message d’erreur ressemble à ceci : « Le réseau virtuel spécifié n’existe pas ou le service Batch ne peut pas y accéder » ou « Le sous-réseau spécifié xxx n’existe pas ».

Cela signifie que le réseau virtuel n’existe pas, que le service Azure Batch ne peut pas y accéder ou que le sous-réseau fourni n’existe pas. Vérifiez que le réseau virtuel et le sous-réseau existent et qu’Azure Batch peut y accéder.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Le message d’erreur ressemble à ceci : « Échec du provisionnement du runtime d’intégration dans le réseau virtuel. Si les paramètres du serveur DNS ou du groupe de sécurité réseau sont configurés, vérifiez que le serveur DNS est accessible et que le groupe de sécurité réseau est correctement configuré ».

Il est probable que les paramètres du serveur DNS ou du groupe de sécurité réseau aient une configuration personnalisée qui empêche la résolution du nom du serveur Azure nécessaire au runtime d’intégration SSIS ou qui empêche son accès. Pour plus d’informations, consultez le document [Configuration du réseau virtuel d’un runtime d’intégration SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous rencontrez toujours des problèmes, contactez l’équipe du support technique Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

Le runtime d’intégration SSIS sera mis à jour régulièrement de façon automatique. Un nouveau pool Azure Batch sera créé lors de la mise à niveau et l’ancien pool Azure Batch sera supprimé. La ressource associée au réseau virtuel de l’ancien pool sera supprimée et une ressource associée au nouveau réseau virtuel sera créée dans votre abonnement. Cette erreur signifie que la suppression de la ressource liée au réseau virtuel de l’ancien pool a échoué en raison du verrou de suppression qui est appliqué au niveau de l’abonnement ou du groupe de ressources. Supprimez le verrou de suppression.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Le provisionnement du runtime d’intégration SSIS peut échouer pour différentes raisons. En cas d’échec, toutes les ressources créées sont supprimées. Toutefois, les ressources du réseau virtuel ne peuvent pas être supprimées en raison d’un verrou de suppression de ressources appliqué au niveau de l’abonnement ou du groupe de ressources. Supprimez le verrou de suppression et redémarrez le runtime d’intégration.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Lorsque vous arrêtez le runtime d’intégration SSIS, toutes les ressources liées au réseau virtuel sont supprimées, mais la suppression échoue en raison d’un verrou de suppression de ressources appliqué au niveau de l’abonnement ou du groupe de ressources. Supprimez le verrou de suppression, puis réessayez d’arrêter le runtime.

### <a name="nodeunavailable"></a>NodeUnavailable

Cette erreur se produit pendant l’exécution du runtime d’intégration. Cela signifie que le runtime d’intégration est passé de l’état sain à l’état non sain. Ceci est toujours dû au fait que la configuration du serveur DNS ou du groupe de sécurité réseau a été modifiée, ce qui empêche le runtime d’intégration SSIS de se connecter au service dépendant. Vous devez corriger les problèmes de configuration du serveur DNS ou du groupe de sécurité réseau. Pour plus d’informations, consultez [Configuration du réseau virtuel pour un runtime d’intégration SSIS](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous rencontrez toujours des problèmes, contactez l’équipe du support technique Azure Data Factory.
