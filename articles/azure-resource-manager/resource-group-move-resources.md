---
title: Déplacer des ressources Azure vers un nouveau groupe de ressources ou abonnement | Microsoft Docs
description: Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: tomfitz
ms.openlocfilehash: a5350befd8d0fb1582606554314d909f7fec04c5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272289"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cet article vous montre comment déplacer des ressources Azure vers un autre abonnement Azure ou un autre groupe de ressources sous le même abonnement. Vous pouvez utiliser le portail Azure, Azure PowerShell, Azure CLI ou l’API REST pour déplacer des ressources. Pour suivre un tutoriel, consultez [Tutoriel : Déplacer des ressources Azure vers un autre groupe de ressources ou un autre abonnement](./resource-manager-tutorial-move-resources.md).

Le groupe source et le groupe cible sont verrouillés pendant l’opération de déplacement. Les opérations d’écriture et de suppression sont bloquées sur les groupes de ressources tant que le déplacement n’est pas terminé. Ce verrou signifie que vous ne pouvez pas ajouter, mettre à jour ou supprimer des ressources dans les groupes de ressources, mais il ne signifie pas que les ressources sont figées. Par exemple, si vous déplacez un serveur SQL Server et sa base de données vers un nouveau groupe de ressources, une application qui utilise la base de données ne rencontre aucune interruption de service. Elle peut toujours lire et écrire dans la base de données.

Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. L’opération de déplacement ne peut pas modifier l’emplacement de la ressource. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.

> [!NOTE]
> Cet article décrit le déplacement de ressources entre abonnements Azure existants. Vous devez convertir votre abonnement Azure si vous souhaitez le mettre à niveau (par exemple, passer d’une offre gratuite au paiement à l’utilisation).
> * Pour mettre à niveau votre essai gratuit, consultez [Passer d’un essai gratuit ou d’un abonnement Azure Microsoft Imagine au paiement à l’utilisation](..//billing/billing-upgrade-azure-subscription.md).
> * Pour changer un compte de paiement à l’utilisation, consultez [Remplacer votre abonnement Paiement à l’utilisation Azure par une autre offre](../billing/billing-how-to-switch-azure-offer.md).
> * Si vous ne pouvez pas convertir l’abonnement, [créez une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md). Sélectionnez **Gestion des abonnements** comme type de problème.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>Quand contacter le support Azure

Vous pouvez déplacer la plupart des ressources via les opérations en libre-service présentées dans cet article. Utilisez les opérations en libre-service pour :

* Déplacer des ressources Resource Manager.
* Déplacer des ressources classiques conformément aux [limitations du déploiement classique](#classic-deployment-limitations).

Contactez le [support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) quand vous devez :

* Déplacer vos ressources vers un nouveau compte Azure (et client Azure Active Directory) et que vous avez besoin d’aide concernant les instructions de la section précédente.
* Déplacer des ressources classiques, mais que vous rencontrez des problèmes avec les limitations.

## <a name="services-that-can-be-moved"></a>Services pouvant être déplacés

La liste suivante fournit une synthèse générale des services Azure qui peuvent être déplacés vers un nouveau groupe de ressources et un nouvel abonnement. Pour obtenir la liste de quelle ressource types prennent en charge de déplacement, consultez [déplacer prise en charge de l’opération pour les ressources](move-support-resources.md).

* Analysis Services
* Gestion des API
* Applications App Service (applications web) : consultez [Limitations d’App Service](#app-service-limitations)
* App Service Certificates - [Limitations d’App Service Certificate](#app-service-certificate-limitations)
* Automation - Les runbooks doivent exister dans le même groupe de ressources que le compte Automation.
* Azure Active Directory B2C
* Cache Azure pour Redis : si l’instance du Cache Azure pour Redis est configurée avec un réseau virtuel, l’instance ne peut pas être déplacée vers un autre abonnement. Consultez [Limitations des réseaux virtuels](#virtual-networks-limitations).
* Azure Cosmos DB
* Explorateur de données Azure
* Azure Database for MariaDB
* Azure Database pour MySQL
* Azure Database pour PostgreSQL
* Azure DevOps : les organisations Azure DevOps ayant acheté des extensions non-Microsoft doivent [annuler leurs achats](https://go.microsoft.com/fwlink/?linkid=871160) avant de pouvoir déplacer le compte entre des abonnements.
* Azure Maps
* Journaux Azure Monitor
* Azure Relay
* Azure Stack - Inscriptions
* Batch
* BizTalk Services
* Service de robot
* CDN
* Cloud Services : consultez [Limitations relatives au déploiement Classic](#classic-deployment-limitations)
* Cognitive Services
* Registre de conteneurs : un registre de conteneurs ne peut pas être déplacé quand la géoréplication est activée.
* Content Moderator
* Cost Management
* Insights client
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Front Door
* Clusters HDInsight - voir [Limitations de HDInsight](#hdinsight-limitations)
* Iot Central
* IoT Hubs
* Key Vault : les coffres de clés utilisés pour le chiffrement de disques ne peuvent pas être déplacés vers des groupes de ressources situés dans le même abonnement ni d’un abonnement à l’autre.
* Équilibreurs de charge : l’équilibrage de charge de la référence SKU de base peut être déplacé. L’équilibrage de charge de la référence SKU standard ne peut pas être déplacé.
* Logic Apps
* Machine Learning : les services web Machine Learning Studio peuvent être déplacés uniquement vers un groupe de ressources d’un même abonnement. Les autres ressources Machine Learning peuvent être déplacées entre les abonnements.
* Des disques gérés - managées disques dans les Zones de disponibilité ne peut pas être déplacés vers un autre abonnement
* Identité managée (affectée par l’utilisateur)
* Media Services
* Moniteur : vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](../azure-subscription-service-limits.md#monitor-limits).
* Notification Hubs
* Operational Insights
* Operations Management
* Tableaux de bord du portail
* Power BI : Power BI Embedded et Collection d’espaces de travail Power BI
* IP publique : l’IP publique de la référence SKU de base peut être déplacée. L’IP publique de la référence SKU standard ne peut pas être déplacée.
* Coffre Recovery Services : inscrivez-vous à une [préversion](#recovery-services-limitations).
* SAP HANA sur Azure
* Scheduler
* Recherche : vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche dans des régions différentes. Déplacez-les plutôt dans des opérations distinctes.
* Service Bus
* Service Fabric
* Service Fabric Mesh
* Service SignalR
* Stockage : les comptes de stockage dans des régions différentes ne peuvent pas être déplacés dans la même opération. Utilisez à la place des opérations distinctes pour chaque région.
* Storage (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Stream Analytics - Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.
* Serveur SQL Database : la base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées. Ce comportement s’applique aux bases de données Azure SQL Database et Azure SQL Data Warehouse.
* Time Series Insights
* Traffic Manager
* Machines virtuelles - voir [limitations de Machines virtuelles](#virtual-machines-limitations)
* Virtual Machines (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Groupes identiques de machines virtuelles : consultez [Limitations relatives aux machines virtuelles](#virtual-machines-limitations)
* Réseaux virtuels : consultez [Limitations relatives aux réseaux virtuels](#virtual-networks-limitations)
* Passerelle VPN

### <a name="services-that-cannot-be-moved"></a>Services ne pouvant pas être déplacés

La liste suivante fournit une synthèse générale des services Azure qui ne peuvent pas être déplacés vers un nouveau groupe de ressources et un nouvel abonnement. Pour plus d’informations, consultez [Prise en charge de l’opération de déplacement pour les ressources](move-support-resources.md).

* Services de domaine AD
* Service de contrôle d’intégrité hybride Active Directory
* Application Gateway
* Azure Database Migration
* Azure Databricks
* Pare-feu Azure
* Service Azure Kubernetes (AKS)
* Azure Migrate
* Azure NetApp Files
* Certificats : les certificats App Service Certificates peuvent être déplacés, mais les certificats chargés ont des [limitations](#app-service-limitations).
* Applications classiques
* Container Instances
* Service de conteneur
* Data Box
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* Lab Services - laboratoires de salle de classe ne peut pas être déplacés vers un nouveau groupe de ressources ou d’un abonnement. Dev/test peut être déplacé vers un nouveau groupe de ressources dans le même abonnement, mais pas entre abonnements.
* Applications gérées
* Microsoft Genomics
* Sécurité
* Site Recovery
* StorSimple Device Manager
* Réseaux virtuels (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)

## <a name="limitations"></a>Limites

La section décrit comment gérer des scénarios compliqués de déplacement des ressources. Les limitations sont les suivantes :

* [Limitations relatives aux machines virtuelles](#virtual-machines-limitations)
* [Limitations de réseaux virtuels](#virtual-networks-limitations)
* [limitations d’App Service](#app-service-limitations)
* [Limitations d’App Service Certificate](#app-service-certificate-limitations)
* [Limitations relatives au déploiement Classic](#classic-deployment-limitations)
* [Limitations de Recovery Services](#recovery-services-limitations)
* [Limitations de HDInsight](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Limitations relatives aux machines virtuelles

Vous pouvez déplacer des machines virtuelles avec disques gérés, des images gérés, des captures instantanées gérées et des groupes à haute disponibilité avec machines virtuelles qui utilisent des disques gérés. Disques gérés dans les Zones de disponibilité ne peut pas être déplacés vers un autre abonnement.

Les scénarios suivants ne sont pas encore pris en charge :

* Les machines virtuelles avec un certificat stocké dans Key Vault peuvent être déplacées vers un nouveau groupe de ressources dans le même abonnement, mais pas entre abonnements.
* Impossible de déplacer les machines virtuelles identiques avec équilibrage de charge de référence (SKU) Standard ou IP publique de référence (SKU) Standard.
* Les machines virtuelles auxquelles des plans sont associés créées à partir de ressources de la Place de marché ne peuvent pas être déplacées entre des groupes de ressources ou des abonnements. Déprovisionnez la machine virtuelle dans l’abonnement actuel, puis redéployez-la dans le nouvel abonnement.

Pour déplacer des machines virtuelles configurées avec Sauvegarde Azure, utilisez la solution de contournement suivante :

* Recherchez l’emplacement de votre machine virtuelle.
* Recherchez un groupe de ressources dont le modèle de nommage est le suivant : `AzureBackupRG_<location of your VM>_1`, par exemple, AzureBackupRG_westus2_1
* Si vous utilisez le portail Azure, cochez « Afficher les types masqués »
* Si vous utilisez PowerShell, utilisez l’applet de commande `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Si dans l’interface CLI, utilisez le `az resource list -g AzureBackupRG_<location of your VM>_1`
* Rechercher la ressource avec le type `Microsoft.Compute/restorePointCollections` qui présente le modèle d’affectation de noms `AzureBackup_<name of your VM that you're trying to move>_###########`
* Supprimez cette ressource. Cette opération supprime uniquement les points de récupération instantanée, et non les données sauvegardées dans le coffre.
* Une fois la suppression terminée, vous pouvez déplacer votre machine virtuelle. Vous pouvez déplacer la machine virtuelle et le coffre vers l’abonnement cible. Après le déplacement, vous pouvez poursuivre les sauvegardes sans perte de données.
* Pour plus d’informations sur le déplacement des coffres Recovery Services pour la sauvegarde, consultez [Limitations de Recovery Services](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Limitations de réseaux virtuels

Lors de la migration d’un réseau virtuel, vous devez également migrer ses ressources dépendantes. Pour les passerelles VPN, vous devez déplacer les adresses IP, les passerelles de réseau virtuel et toutes les ressources de connexion associées. Les passerelles de réseau locales peuvent se trouver dans un autre groupe de ressources.

Pour déplacer une machine virtuelle avec une carte d’interface réseau, vous devez déplacer toutes les ressources dépendantes. Vous devez déplacer le réseau virtuel pour la carte d’interface réseau, tous les autres cartes d’interface réseau pour le réseau virtuel et les passerelles VPN.

Pour déplacer un réseau virtuel homologué, vous devez d’abord désactiver l’homologation du réseau virtuel. Une fois l’homologation désactivée, vous pouvez déplacer le réseau virtuel. Après le déplacement, réactivez l’homologation du réseau virtuel.

Vous ne pouvez pas déplacer un réseau virtuel vers un autre abonnement s’il contient un sous-réseau avec des liens de navigation dans les ressources. Par exemple, si une ressource de Cache Azure pour Redis est déployée dans un sous-réseau, ce sous-réseau possède un lien de navigation dans les ressources.

### <a name="app-service-limitations"></a>limitations d’App Service

Les limitations imposées diffèrent selon que les ressources App Service sont déplacées au sein d’un abonnement ou vers un nouvel abonnement. Si votre application web utilise un App Service Certificate, voir [Limitations d’App Service Certificate](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>Déplacement au sein d’un même abonnement

Lorsque vous déplacez une application web _au sein du même abonnement_, vous ne pouvez pas déplacer les certificats SSL tiers. Toutefois, vous pouvez déplacer une application web vers le nouveau groupe de ressources sans emporter son certificat tiers, et la fonctionnalité SSL de votre application peut continuer de fonctionner.

Si vous souhaitez déplacer le certificat SSL avec l’application web, suivez ces étapes :

1. Supprimer le certificat tiers de l’application web, mais en conserver une copie
2. Déplacer l’application web
3. Charger le certificat tiers sur l’application web déplacée.

#### <a name="moving-across-subscriptions"></a>Déplacement entre différents abonnements

Lors du déplacement d’une application Web _entre des abonnements_, les limites suivantes s’appliquent :

- Le groupe de ressources de destination ne doit avoir aucune ressource App Service existante. Les ressources App Service comprennent :
    - Web Apps
    - Plans App Service
    - Certificats SSL chargés ou importés
    - Environnements App Service
- Toutes les ressources App Service du groupe de ressources doivent être déplacées simultanément.
- Les ressources App Service ne peuvent être déplacées qu’à partir du groupe de ressources dans lequel elles ont été créées à l’origine. Si une ressource App Service n’est plus dans son groupe de ressources d’origine, elle doit d’abord réintégrer ce groupe avant de pouvoir être déplacée entre les abonnements.

### <a name="app-service-certificate-limitations"></a>Limitations d’App Service Certificate

Vous pouvez déplacer votre App Service Certificate vers un nouveau groupe de ressources ou abonnement. Si votre App Service Certificate est lié à une application web, vous devez prendre certaines mesures avant de déplacer les ressources vers un nouvel abonnement. Supprimez la liaison SSL et le certificat privé de l’application web avant de déplacer les ressources. L’App Service Certificate n’a pas besoin d’être supprimé, seulement le certificat privé de l’application web.

### <a name="classic-deployment-limitations"></a>Limitations relatives au déploiement Classic

Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplaciez les ressources au sein d’un abonnement ou vers un nouvel abonnement.

#### <a name="same-subscription"></a>Même abonnement

Lors du déplacement de ressources d’un groupe de ressources vers un autre au sein du même abonnement, les restrictions suivantes s’appliquent :

* Les réseaux virtuels (classiques) ne peuvent pas être déplacés.
* Les machines virtuelles (classiques) doivent être déplacées avec le service cloud.
* Le service cloud ne peut être déplacé que lorsque le déplacement comprend toutes ses machines virtuelles.
* Un seul service cloud peut être déplacé à la fois.
* Un seul compte de stockage (classique) peut être déplacé à la fois.
* Vous ne pouvez pas déplacer un compte de stockage (classique) dans la même opération avec une machine virtuelle ou un service cloud.

Pour déplacer des ressources classiques vers un nouveau groupe de ressources dans le même abonnement, utilisez les opérations de déplacement standard via le [portail](#use-portal), Azure PowerShell, l’interface CLI Azure ou l’API REST. Vous utilisez les mêmes opérations que vous celles que vous utilisez pour déplacer des ressources Resource Manager.

#### <a name="new-subscription"></a>Nouvel abonnement

Lors du déplacement de ressources vers un nouvel abonnement, les restrictions suivantes s’appliquent :

* Toutes les ressources classiques de l’abonnement doivent être déplacées au cours de la même opération.
* L’abonnement cible ne doit pas contenir d’autres ressources classiques.
* Le déplacement peut uniquement être demandé par le biais d’une API REST distincte pour les déplacements classiques. Les commandes de déplacement standard de Resource Manager ne fonctionnent pas lors du déplacement de ressources classiques vers un nouvel abonnement.

Pour déplacer des ressources classiques vers un nouvel abonnement, utilisez des opérations REST spécifiques aux ressources classiques. Pour utiliser REST, procédez comme suit :

1. Vérifiez si l’abonnement source peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Dans le corps de la demande, spécifiez :

   ```json
   {
    "role": "source"
   }
   ```

     La réponse pour l’opération de validation est au format suivant :

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

2. Vérifiez si l’abonnement de destination peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Dans le corps de la demande, spécifiez :

   ```json
   {
    "role": "target"
   }
   ```

     La réponse est dans le même format que la validation de l’abonnement source.
3. Si les deux abonnements sont validés, déplacez toutes les ressources classiques d’un abonnement à l’autre via l’opération suivante :

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    Dans le corps de la demande, spécifiez :

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Cette opération peut prendre plusieurs minutes.

### <a name="recovery-services-limitations"></a>Limitations de Recovery Services

 Pour déplacer un coffre Recovery Services, vous devez être inscrit à une [préversion publique limitée](../backup/backup-azure-move-recovery-services-vault.md).

Actuellement, vous pouvez déplacer un coffre Recovery Services par région à la fois. Vous ne pouvez pas déplacer les coffres qui sauvegardent les données Azure Files, Azure File Sync ou SQL dans des machines virtuelles IaaS.

Si une machine virtuelle ne se déplace pas avec le coffre, les points de récupération actuels de la machine virtuelle restent dans le coffre jusqu’à leur expiration. Que la machine virtuelle soit déplacée avec le coffre ou non, vous pouvez la restaurer à partir de l’historique de sauvegarde dans le coffre.

Le coffre Recovery Services ne prend pas en charge les sauvegardes lors du passage d’un abonnement à un autre. Si vous déplacez un coffre contenant des données de sauvegarde de machine virtuelle entre des abonnements, vous devez déplacer vos machines virtuelles dans le même abonnement et utiliser le même groupe de ressources cible pour continuer les sauvegardes.

Les stratégies de sauvegarde définies pour le coffre sont conservées après son déplacement. La création de rapports et la surveillance doivent être configurées à nouveau pour le coffre après son déplacement.

Pour déplacer une machine virtuelle vers un nouvel abonnement sans déplacer le coffre Recovery Services :

 1. Arrêter temporairement la sauvegarde
 1. [Supprimez le point de restauration](#virtual-machines-limitations). Cette opération supprime uniquement les points de récupération instantanée, et non les données sauvegardées dans le coffre.
 1. Déplacer les machines virtuelles vers le nouvel abonnement
 1. Réactiver la protection dans un nouveau coffre sur cet abonnement

Le déplacement n’est pas possible pour les ressources de stockage, de réseau ou de calcul utilisées pour configurer la récupération d’urgence avec Azure Site Recovery. Par exemple, supposons que vous avez configuré la réplication de vos machines locales vers un compte de stockage (Storage1) et que vous souhaitez que la machine protégée apparaisse après le basculement vers Azure comme une machine virtuelle (VM1) connectée à un réseau virtuel (Network1). Vous ne pouvez pas déplacer ces ressources Azure (Storage1, VM1 et Network1) sur différents groupes de ressources dans le même abonnement ou sur différents abonnements.

### <a name="hdinsight-limitations"></a>Limitations de HDInsight

Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.

Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="checklist-before-moving-resources"></a>Liste de contrôle avant le déplacement de ressources

Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Vérifiez ces conditions pour prévenir d'éventuelles erreurs.

1. Les abonnements source et de destination doivent être actifs. Si vous rencontrez des problèmes lors de l’activation d’un compte qui a été désactivé, [créez une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md). Sélectionnez **Gestion des abonnements** comme type de problème.

1. Les abonnements source et de destination doivent exister dans le même [client Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Pour vérifier que les deux abonnements ont le même ID client, utilisez Azure PowerShell ou Azure CLI.

   Pour Azure PowerShell, utilisez :

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Pour l’interface de ligne de commande Azure, consultez :

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Si les ID client pour les abonnements source et de destination ne sont pas identiques, utilisez les méthodes suivantes pour rapprocher les ID client :

   * [Transfert de la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
   * [Comment associer ou ajouter un abonnement Azure à Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. L’abonnement de destination doit être inscrit pour le fournisseur de la ressource déplacée. Sinon, vous recevez une erreur indiquant que **l’abonnement n’est pas inscrit pour un type de ressource**. Vous pouvez rencontrer cette erreur lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource.

   Pour PowerShell, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Pour inscrire un fournisseur de ressources, utilisez :

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Pour l’interface CLI d’Azure, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Pour inscrire un fournisseur de ressources, utilisez :

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Le compte déplaçant les ressources doit avoir au moins les autorisations suivantes :

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** sur le groupe de ressources source.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** sur le groupe de ressources de destination.

1. Avant de déplacer les ressources, vérifiez les quotas d’abonnement pour l’abonnement vers lequel vous souhaitez déplacer les ressources. Si le déplacement des ressources signifie que l’abonnement dépassera ses limites, vous devez vérifier si vous pouvez demander une augmentation du quota. Pour connaître la liste des limites et savoir comment demander une augmentation, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

1. Quand c’est possible, divisez les grands déplacements en opérations de déplacement distinctes. Resource Manager retourne immédiatement une erreur en cas de tentative de déplacement de plus de 800 ressources en une seule opération. Cependant, un déplacement de moins de 800 ressources peut également échouer en raison d’un dépassement du délai d’expiration.

1. Le service doit activer la possibilité de déplacer des ressources. Pour déterminer si le déplacement sera effectué, [valider votre demande de déplacement](#validate-move). Consultez les sections ci-dessous dans cet article pour connaître les [services permettant de déplacer des ressources](#services-that-can-be-moved) et les [services qui ne le permettent pas](#services-that-cannot-be-moved).

## <a name="validate-move"></a>Valider le déplacement

[L’opération de validation du déplacement](/rest/api/resources/resources/validatemoveresources) vous permet de tester votre scénario de déplacement sans réellement déplacer les ressources. Utilisez cette opération pour déterminer si le déplacement sera effectué. Pour exécuter cette opération, vous avez besoin des éléments suivants :

* Nom du groupe de ressources source
* ID de ressource du groupe de ressources cible
* ID de ressource de chaque ressource à déplacer
* [Jeton d’accès](/rest/api/azure/#acquire-an-access-token) pour votre compte

Envoyez la requête suivante :

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Avec le corps de la demande :

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Si la demande est correctement mise en forme, l’opération retourne :

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Le code d’état 202 indique que la demande de validation a été acceptée, mais la réussite de l’opération de déplacement n’est pas encore déterminée. La valeur de `location` contient une URL que vous utilisez pour vérifier l’état de l’opération longue.  

Pour vérifier l’état, envoyez la demande suivante :

```
GET <location-url>
Authorization: Bearer <access-token>
```

Pendant l’exécution de l’opération, vous continuez à recevoir le code d’état 202. Attendez le nombre de secondes indiqué dans la valeur `retry-after` avant de réessayer. Si l’opération de déplacement est validée, vous recevez le code d’état 204. Si la validation de déplacement échoue, vous recevez un message d’erreur, par exemple :

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>Déplacer des ressources

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />À l’aide du portail Azure

Pour déplacer des ressources, sélectionnez le groupe de ressources qui les contient, puis sélectionnez le bouton **Déplacer**.

![Déplacer des ressources](./media/resource-group-move-resources/select-move.png)

Indiquez si vous déplacez les ressources vers un nouveau groupe de ressources ou vers un nouvel abonnement.

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Confirmez que vous devez mettre à jour les scripts de ces ressources et sélectionnez **OK**. Si vous avez sélectionné l’icône Modifier l’abonnement à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

Dans **Notifications**, vous voyez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsque l’opération est terminée, vous êtes informé du résultat.

![afficher les résultats du déplacement](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>En utilisant Azure PowerShell

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande [Move-AzResource](/powershell/module/az.resources/move-azresource). L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre `DestinationSubscriptionId`.

### <a name="by-using-azure-cli"></a>À l’aide d’Azure CLI

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) . Fournissez les ID des ressources à déplacer. L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources. Dans le paramètre `--ids`, spécifiez une liste séparée par des espaces des ID des ressources à déplacer.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

### <a name="by-using-rest-api"></a>À l’aide de l’API REST

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les applets de commande PowerShell pour gérer vos ressources, consultez [utilisation d’Azure PowerShell avec Resource Manager](manage-resources-powershell.md).
* Pour en savoir plus sur les commandes Azure CLI pour gérer vos ressources, consultez [à l’aide de l’interface CLI Azure avec Resource Manager](manage-resources-cli.md).
* Pour plus d’informations sur les fonctionnalités du portail permettant de gérer votre abonnement, consultez [Utilisation du Portail Azure pour gérer les ressources](resource-group-portal.md).
* Pour plus d’informations sur l’application d’une organisation logique à vos ressources, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
