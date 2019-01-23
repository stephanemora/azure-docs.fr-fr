---
title: Créer un cluster WSFC, un écouteur, puis configurer un équilibreur de charge interne pour un groupe de disponibilité Always On sur une machine virtuelle SQL Server à l’aide d’un modèle de démarrage rapide Azure
description: Utilisez les modèles de démarrage rapide Azure pour créer facilement des groupes de disponibilité pour des machines virtuelles SQL Server dans Azure. Un tel modèle vous permet de créer le cluster, joindre des machines virtuelles SQL au cluster, créer l’écouteur et configurer l’équilibreur de charge interne.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360483"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Créer un cluster WSFC, un écouteur, puis configurer un équilibreur de charge interne pour un groupe de disponibilité Always On sur une machine virtuelle SQL Server à l’aide d’un modèle de démarrage rapide Azure
Cet article décrit comment utiliser les modèles de démarrage rapide Azure pour automatiser partiellement le déploiement d’une configuration de groupe de disponibilité Always On pour des machines virtuelles SQL Server dans Azure. Deux modèles de démarrage rapide Azure sont utilisés dans ce processus. 

   | Modèle | Description |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crée le cluster de basculement Windows et joint les machines virtuelles SQL Server à ce dernier. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crée l’écouteur de groupe de disponibilité et configure l’équilibreur de charge interne. |
   | &nbsp; | &nbsp; |

D’autres étapes de la configuration du groupe de disponibilité sont obligatoirement manuelles, notamment la création du groupe de disponibilité et celle de l’équilibreur de charge interne. Cet article décrit la séquence des étapes automatisées et manuelles.
 

## <a name="prerequisites"></a>Prérequis 
Pour automatiser la configuration d’un groupe de disponibilité Always On à l’aide de modèles de démarrage rapide, vous devez déjà avoir les prérequis suivants : 
- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un groupe de ressources avec un [contrôleur de domaine](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Une ou plusieurs [machines virtuelles jointes à un domaine dans Azure qui exécutent SQL Server 2016 (ou version supérieure) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) dans le même groupe à haute disponibilité ou la même zone de disponibilité et qui sont [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Inscrire une machine virtuelle SQL existante auprès du nouveau fournisseur de ressources
Étant donné que ces modèles de démarrage rapide Azure s’appuient sur le fournisseur de ressources de machine virtuelle SQL (Microsoft.SqlVirtualMachine), les machines virtuelles SQL Server existantes doivent être inscrites auprès de ce dernier. Ignorez cette étape si vous avez créé votre machine virtuelle SQL Server après le mois de décembre 2018, puisque toutes les machines virtuelles SQL Server créées après cette date sont automatiquement inscrites. Cette section explique comment effectuer l’inscription auprès du fournisseur en utilisant le portail Azure, mais vous pouvez aussi utiliser [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Si vous supprimez votre ressource de machine virtuelle SQL Server, vous revenez au paramètre de licence codé en dur de l’image. 

1. Ouvrez le portail Azure et accédez à **Tous les services**. 
1. Accédez à **Abonnements** et sélectionnez l’abonnement qui vous intéresse.  
1. Dans le panneau **Abonnements**, accédez à **Fournisseur de ressources**. 
1. Tapez `sql` dans le filtre pour afficher les fournisseurs de ressources liés à SQL. 
1. Sélectionnez *Inscrire*, *Réinscrire* ou *Désinscrire* pour le fournisseur **Microsoft.SqlVirtualMachine**, en fonction de l’action souhaitée. 

  ![Modifier le fournisseur](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Étape 1 : Créer le cluster WSFC et joindre les machines virtuelles SQL Server au cluster à l’aide du modèle de démarrage rapide 
Une fois que vos machines virtuelles SQL Server sont inscrites auprès du nouveau fournisseur de ressources de machine virtuelle SQL, vous pouvez joindre vos machines virtuelles SQL Server dans *SqlVirtualMachineGroup*. Cette ressource définit les métadonnées du cluster de basculement Windows, notamment la version, l’édition, le nom de domaine complet, les comptes Active Directory nécessaires pour gérer le cluster et le compte de stockage en tant que témoin cloud. L’ajout de la machine virtuelle SQL Server au *SqlVirtualMachineGroup* amorce le service de cluster de basculement Windows et joint les machines virtuelles SQL Server au cluster. Cette étape est automatisée avec le modèle de démarrage rapide **101-sql-vm-ag-setup** et peut être implémentée avec les étapes suivantes :

1. Accédez au modèle de démarrage rapide [ **101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) et sélectionnez **Déployer sur Azure** pour le lancer au sein du portail Azure.
1. Remplissez les champs obligatoires pour configurer les métadonnées du cluster de basculement Windows. Vous pouvez laisser vides les champs facultatifs.

    Le tableau suivant montre les valeurs nécessaires au modèle : 

   | **Champ** | Valeur |
   | --- | --- |
   | **Abonnement** |  Abonnement dans lequel vos machines virtuelles SQL Server existent. |
   |**Groupe de ressources** | Groupe de ressources où résident vos machines virtuelles SQL Server. | 
   |**Nom du cluster de basculement** | Nom souhaité pour votre nouveau cluster de basculement Windows. |
   | **Liste des machines virtuelles existantes** | Machines virtuelles SQL Server que vous voulez faire participer au groupe de disponibilité et, de ce fait, intégrer à ce nouveau cluster. Séparez ces valeurs par une virgule et un espace (par ex. : SQLVM1, SQLVM2). |
   | **Version de SQL Server** | Dans la liste déroulante, sélectionnez la version de SQL Server utilisée par vos machines virtuelles SQL Server. Actuellement, seules les images SQL 2016 et SQL 2017 sont prises en charge. |
   | **Nom de domaine complet existant** | FQDN existant du domaine dans lequel se trouvent vos machines virtuelles SQL Server. |
   | **Compte de domaine existant** | Compte de domaine existant qui dispose d’un accès sysadmin à SQL Server. | 
   | **Mot de passe du compte de domaine** | Mot de passe du compte de domaine mentionné précédemment. | 
   | **Compte de service SQL existant** | Compte d’utilisateur de domaine utilisé pour contrôler le service SQL Server. Vous pouvez trouver ces informations à l’aide du [**Gestionnaire de configuration SQL Server**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Mot de passe du service SQL** | Mot de passe utilisé par le compte d’utilisateur de domaine qui contrôle le service SQL Server. |
   | &nbsp; | &nbsp; |

1. Si vous acceptez les termes et conditions, cochez la case en regard de **J’accepte les termes et conditions mentionnés ci-dessus** et sélectionnez **Acheter** pour finaliser le déploiement du modèle de démarrage rapide. 
1. Pour superviser votre déploiement, sélectionnez-le à partir de l’icône en forme de cloche **Notifications** située dans votre bannière de navigation supérieure ou accédez à votre **groupe de ressources** dans le portail Azure, sélectionnez  **Déploiements** dans le champ **Paramètres**, puis choisissez le déploiement « Microsoft.Template ». 

## <a name="step-2---manually-create-the-availability-group"></a>Étape 2 : Créer manuellement le groupe de disponibilité 
Créez manuellement le groupe de disponibilité, comme vous le feriez normalement, à l’aide de [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) ou [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > Ne créez **pas** d’écouteur pour l’instant, car cette opération est automatisée par le modèle de démarrage rapide **101-sql-vm-aglistener-setup** à l’étape 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Étape 3 : Créer manuellement l’équilibreur de charge interne
L’écouteur de groupe de disponibilité Always On a besoin d’un équilibreur de charge Azure interne. Ce dernier fournit une adresse IP « flottante » à l’écouteur de groupe de disponibilité qui permet des opérations de basculement et de reconnexion plus rapides. Si les machines virtuelles SQL Server d’un groupe de disponibilité font partie du même groupe à haute disponibilité, alors vous pouvez utiliser un équilibreur de charge basique ; dans le cas contraire, vous devez utiliser un équilibreur de charge standard.  **L’équilibreur de charge interne doit se trouver dans le même réseau virtuel que les instances de machine virtuelle SQL Server.** Vous avez juste besoin de créer l’équilibreur de charge interne, car le reste de la configuration (notamment le pool back-end, la sonde d’intégrité et les règles d’équilibrage de charge) est géré par le modèle de démarrage rapide **101-sql-vm-aglistener-setup** à l’étape 4. 

1. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles SQL Server. 
2. Dans le groupe de ressources, cliquez sur **Ajouter**.
3. Recherchez **équilibreur de charge** puis, dans les résultats de la recherche, sélectionnez **Équilibreur de charge**, publié par **Microsoft**.
4. Dans le panneau **Équilibrage de charge**, cliquez sur **Créer**.
5. Dans la boîte de dialogue **Créer l’équilibreur de charge**, configurez l’équilibreur de charge comme suit :

   | Paramètre | Valeur |
   | --- | --- |
   | **Nom** |Nom de l’équilibrage de charge. Par exemple, **sqlLB**. |
   | **Type** |**Interne** : La plupart des implémentations utilisent un équilibreur de charge interne, ce qui permet aux applications du même réseau virtuel de se connecter au groupe de disponibilité.  </br> **Externe** : Permet aux applications de se connecter au groupe de disponibilité par le biais d’une connexion Internet publique. |
   | **Réseau virtuel** |Sélectionnez le réseau virtuel contenant les instances de SQL Server. |
   | **Sous-réseau** |Sélectionnez le sous-réseau contenant les instances de SQL Server. |
   | **Affectation d’adresses IP** |**Statique** |
   | **Adresse IP privée** |Spécifiez une adresse IP disponible à partir du sous-réseau. Vous allez l’utiliser pour créer un écouteur sur le cluster.|
   | **Abonnement** |Si vous avez plusieurs abonnements, ce champ doit normalement apparaître. Sélectionnez l’abonnement que vous voulez associer à cette ressource. Normalement, il s’agit du même abonnement que pour toutes les ressources du groupe de disponibilité. |
   | **Groupe de ressources** |Sélectionnez le groupe de ressources contenant les instances de SQL Server. |
   | **Lieu** |Sélectionnez l’emplacement Azure contenant les instances de SQL Server. |
   | &nbsp; | &nbsp; |

6. Sélectionnez **Créer**. 


  >[!NOTE]
  > La ressource d’adresse IP publique de chaque machine virtuelle SQL Server doit avoir une référence SKU standard compatible avec l’équilibreur de charge standard. Pour déterminer la référence SKU de la ressource d’adresse IP publique de votre machine virtuelle, accédez à votre **groupe de ressources**, sélectionnez votre ressources d’**adresse IP publique** pour la machine virtuelle SQL Server souhaitée et recherchez la valeur sous **Référence SKU** dans le volet **Vue d’ensemble**. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Étape 4 : Créer l’écouteur de groupe de disponibilité et configurer l’équilibreur de charge interne avec le modèle de démarrage rapide

Créez l’écouteur de groupe de disponibilité et configurez l’équilibreur de charge interne automatiquement avec le modèle de démarrage rapide **101-sql-vm-aglistener-setup** puisqu’il provisionne la ressource Microsoft.SqlVirtualMachine/Sql Virtual Machine Groups/Availability Group Listener. Le modèle de démarrage rapide **101-sql-vm-aglistener-setup** effectue les actions suivantes par le biais du fournisseur de ressources de machine virtuelle SQL :

 - Il configure les paramètres réseau du cluster et de l’équilibreur de charge interne. 
 - Il configure le pool back-end, la sonde d’intégrité et les règles d’équilibrage de charge de l’équilibreur de charge interne.
 - Il crée l’écouteur de groupe de disponibilité avec le nom et l’adresse IP donnés.

Pour configurer l’équilibreur de charge interne et créer l’écouteur de groupe de disponibilité, effectuez les étapes suivantes :
1. Accédez au modèle de démarrage rapide [ **101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) et sélectionnez **Déployer sur Azure** pour le lancer au sein du portail Azure.
1. Remplissez les champs obligatoires pour configurer l’équilibreur de charge interne, puis créez l’écouteur de groupe de disponibilité. Vous pouvez laisser vides les champs facultatifs. 

    Le tableau suivant montre les valeurs nécessaires au modèle : 

   | **Champ** | Valeur |
   | --- | --- |
   |**Groupe de ressources** | Groupe de ressources dans lequel vos machines virtuelles SQL Server et votre groupe de disponibilité existent. | 
   |**Nom du cluster de basculement existant** | Nom du cluster auquel vos machines virtuelles SQL Server sont jointes. |
   | **Groupe de disponibilité SQL existant**| Nom du groupe de disponibilité duquel vos machines virtuelles SQL Server font partie. |
   | **Liste des machines virtuelles existantes** | Noms des machines virtuelles SQL Server qui font partie du groupe de disponibilité précédemment mentionné. Les noms doivent être séparés par une virgule et un espace (par ex. : SQLVM1, SQLVM2). |
   | **Nom de domaine complet existant** | FQDN existant du domaine dans lequel se trouvent vos machines virtuelles SQL Server. |
   | **Écouteur** | Nom DNS que vous voulez attribuer à l’écouteur. Par défaut, ce modèle spécifie le nom « aglistener », mais vous pouvez le modifier. |
   | **Port de l’écouteur** | Port que vous voulez que l’écouteur utilise. En règle générale, ce port doit être le port par défaut 1433, et de fait, il s’agit du numéro de port spécifié par ce modèle. Toutefois, si votre port par défaut a été modifié, alors le port de l’écouteur doit plutôt utiliser cette valeur modifiée. | 
   | **Réseau virtuel existant** | Nom du réseau virtuel où se trouvent vos machines virtuelles SQL Server et l’équilibreur de charge interne. |
   | **Sous-réseau existant** | *Nom* du sous-réseau interne de vos machines virtuelles SQL Server (par ex. : default). Pour déterminer cette valeur, vous pouvez accéder à votre **groupe de ressources**, sélectionner votre **réseau virtuel**, sélectionner vos **sous-réseaux** dans le volet **Paramètres**, puis copiez la valeur figurant sous **Nom**. |
   | **Équilibreur de charge interne existant** | Nom de l’équilibreur de charge interne que vous avez créé à l’étape 3. |
   | **Port de la sonde** | Port de la sonde que vous voulez que l’équilibreur de charge interne utilise. Le modèle utilise 59999 par défaut, mais vous pouvez modifier cette valeur. |
   | &nbsp; | &nbsp; |

1. Si vous acceptez les termes et conditions, cochez la case en regard de **J’accepte les termes et conditions mentionnés ci-dessus** et sélectionnez **Acheter** pour finaliser le déploiement du modèle de démarrage rapide. 
1. Pour superviser votre déploiement, sélectionnez-le à partir de l’icône en forme de cloche **Notifications** située dans votre bannière de navigation supérieure ou accédez à votre **groupe de ressources** dans le portail Azure, sélectionnez  **Déploiements** dans le champ **Paramètres**, puis choisissez le déploiement « Microsoft.Template ». 

  >[!NOTE]
  >Si votre déploiement échoue en cours de route, vous devrez [supprimer l’écouteur tout juste créé](#remove-availability-group-listener) manuellement à l’aide de PowerShell avant de redéployer le modèle de démarrage rapide **101-sql-vm-aglistener-setup**. 

## <a name="remove-availability-group-listener"></a>Supprimer l’écouteur de groupe de disponibilité
Si, plus tard, vous avez besoin de supprimer l’écouteur de groupe de disponibilité configuré par le modèle, vous devez passer par le fournisseur de ressources de machine virtuelle SQL. Dans la mesure où l’écouteur est inscrit par le biais du fournisseur de ressources de machine virtuelle SQL, le supprimer par le biais de SQL Server Management Studio ne suffit pas. Vous devez en fait le supprimer par le biais du fournisseur de ressources de machine virtuelle SQL à l’aide de PowerShell. Ainsi, vous supprimez du fournisseur de ressources de machine virtuelle SQL les métadonnées de l’écouteur de groupe de disponibilité et vous supprimez physiquement l’écouteur du groupe de disponibilité. 

L’extrait de code suivant supprime l’écouteur de groupe de disponibilité SQL à la fois du fournisseur de ressources SQL et de votre groupe de disponibilité : 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Erreurs et problèmes connus
Cette section décrit certains problèmes connus et leurs éventuelles solutions. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>L’écouteur de groupe de disponibilité existe déjà pour le groupe de disponibilité « \<Nom-Groupe-Disponibilité> »
Le groupe de disponibilité sélectionné utilisé dans le modèle de démarrage rapide Azure de l’écouteur de groupe de disponibilité contient déjà un écouteur, soit physiquement au sein du groupe de disponibilité, soit sous forme de métadonnées au sein du fournisseur de ressources de machine virtuelle SQL.  Supprimez l’écouteur à l’aide de [PowerShell](#remove-availability-group-listener) avant de redéployer le modèle de démarrage rapide **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>La connexion fonctionne uniquement à partir du réplica principal
Ce comportement vient probablement d’échec de déploiement du modèle **101-sql-vm-aglistener-setup** qui a laissé la configuration de l’équilibreur de charge interne dans un état incohérent. Vérifiez que le pool back-end liste le groupe à haute disponibilité et qu’il existe des règles pour la sonde d’intégrité et l’équilibreur de charge. S’il manque quelque chose, alors la configuration de l’équilibreur de charge interne est un état incohérent. 

Pour corriger ce comportement, supprimez l’écouteur à l’aide de [PowerShell](#remove-availability-group-listener), supprimez l’équilibreur de charge interne par le biais du portail Azure et recommencez à l’[étape 3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest : seule la liste des machines virtuelles SQL peut être mise à jour
Cette erreur peut se produire lorsque vous déployez le modèle **101-sql-vm-aglistener-setup** si l’écouteur a été supprimé par le biais de SQL Server Management Studio (SSMS), mais qu’il n’a pas été supprimé du fournisseur de ressources de machine virtuelle SQL. La suppression de l’écouteur par le biais de SSMS ne supprime pas les métadonnées de l’écouteur dans le fournisseur de ressources de machine virtuelle SQL. Vous devez supprimer l’écouteur du fournisseur de ressources à l’aide de [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Forum aux questions sur la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs des machines virtuelles SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de la machine virtuelle SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Changement de modèles de licence pour une machine virtuelle SQL Server](virtual-machines-windows-sql-ahb.md)



