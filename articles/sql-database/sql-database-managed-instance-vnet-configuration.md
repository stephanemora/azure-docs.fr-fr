---
title: Configuration d’un réseau virtuel Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique décrit les options de configuration d’un réseau virtuel avec Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 748489785241c0eab6022e3585164974f330d6f9
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049671"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (préversion) doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Ce déploiement permet les scénarios suivants : 
- Connexion directe à une instance gérée à partir d’un réseau local 
- Connexion de Managed Instance à un serveur lié ou un autre magasin de données local 
- Connexion de Managed Instance à des ressources Azure  

## <a name="plan"></a>Planification

Planifiez votre déploiement de Managed Instance dans un réseau virtuel en utilisant vos réponses aux questions suivantes : 
- Envisagez-vous de déployer une seule ou plusieurs options Managed Instance ? 

  Le nombre d’options Managed Instance détermine la taille minimale du sous-réseau à allouer à vos instances gérées. Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour Managed Instance](#determine-the-size-of-subnet-for-managed-instances). 
- Avez-vous besoin de déployer votre option Managed Instance sur un réseau virtuel existant ou créez-vous un réseau ? 

   Si vous envisagez d’utiliser un réseau virtuel existant, vous devez modifier sa configuration pour prendre en compte Managed Instance. Pour plus d’informations, consultez [Modifier un réseau virtuel existant pour Managed Instance](#modify-an-existing-virtual-network-for-managed-instances). 

   Si vous envisagez de créer un réseau virtuel, consultez [Créer un réseau virtuel pour Managed Instance](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Configuration requise

Pour la création d’une option Managed Instance, vous avez besoin d’un sous-réseau dédié conforme aux exigences suivantes dans le réseau virtuel :
- **Sous-réseau dédié** : le sous-réseau ne doit contenir aucun autre service cloud associé et ne doit pas être un sous-réseau de passerelle. Vous ne pouvez pas créer d’option Managed Instance dans un sous-réseau qui contient des ressources autres qu’une instance managée ni ajouter ultérieurement d’autres ressources à l’intérieur du sous-réseau.
- **Pas de groupe de sécurité réseau** : aucun groupe de sécurité réseau ne doit être associé au sous-réseau. 
- **Table de routage spécifique** : le sous-réseau doit avoir une table de routage utilisateur (UDR) avec un itinéraire Internet de tronçon suivant 0.0.0.0/0 comme seul itinéraire affecté. Pour plus d’informations, consultez [Créer la table de routage nécessaire et l’associer](#create-the-required-route-table-and-associate-it).
3. **DNS personnalisé éventuel** : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) à la liste. Pour plus d’informations, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
4. **Pas de point de terminaison de service** : le sous-réseau ne doit pas avoir de point de terminaison de service associé. Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
5. **Nombre d’adresses IP suffisant** : le sous-réseau doit absolument avoir 16 adresses IP au minimum (le minimum recommandé est de 32 adresses IP). Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une nouvelle option Managed Instance si le sous-réseau de destination n’est pas compatible avec toutes les exigences précédentes. La réseau virtuel de destination et le sous-réseau doivent constamment respecter ces exigences (avant et après le déploiement), car toute violation peut entraîner l’entrée de l’instance dans un état défectueux et la rendre indisponible. Pour récupérer de cet état, vous devez créer une instance dans un réseau virtuel avec les stratégies réseau conformes, recréer des données de niveau d’instance et restaurer vos bases de données. Cela induit un temps d’arrêt important pour vos applications.

Avec l’introduction de la _stratégie d’intention de réseau_, vous pouvez ajouter un groupe de sécurité réseau (NSG) sur un sous-réseau Managed Instance une fois que l’option Managed Instance est créée.

Vous pouvez désormais utiliser un groupe de sécurité réseau pour affiner les plages d’adresses IP à partir desquelles les applications et les utilisateurs peuvent interroger et gérer les données en filtrant le trafic réseau acheminé vers le port 1433. 

> [!IMPORTANT]
> Lorsque vous configurez les règles du groupe de sécurité réseau qui limiteront l’accès au port 1433, vous devez également insérer les règles de trafic entrant à la priorité la plus haute affichées dans le tableau ci-dessous. Sinon, la stratégie d’intention de réseau bloque la modification comme étant non conforme.

| NOM       |PORT                        |PROTOCOLE|SOURCE           |DESTINATION|ACTION|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|Quelconque     |Quelconque              |Quelconque        |AUTORISER |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |Quelconque        |AUTORISER |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|Quelconque        |AUTORISER |

L’expérience de routage a également été améliorée : en plus de l’itinéraire Internet type de tronçon suivant 0.0.0.0/0, vous pouvez maintenant ajouter un itinéraire défini par l’utilisateur (UDR) pour acheminer le trafic vers vos plages d’adresses IP privées sur site via la passerelle de réseau virtuel ou une appliance de réseau virtuel (NVA).

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Déterminer la taille du sous-réseau pour les options Managed Instance

Quand vous créez une option Managed Instance, Azure alloue plusieurs machines virtuelles en fonction du niveau sélectionné pendant l’approvisionnement. Étant donné que ces machines virtuelles sont associées à votre sous-réseau, elles nécessitent des adresses IP. Pour garantir la haute disponibilité pendant les opérations normales et la maintenance du service, Azure peut allouer des machines virtuelles supplémentaires. Par conséquent, le nombre d’adresses IP nécessaires dans un sous-réseau est supérieur au nombre d’options Managed Instance dans ce sous-réseau. 

Par défaut, une option Managed Instance a besoin d’un minimum de 16 adresses IP dans un sous-réseau et peut en utiliser jusqu’à 256. Ainsi, vous pouvez utiliser des masques de sous-réseau /28 à /24 lors de la définition de vos plages d’adresses IP de sous-réseau. 

> [!IMPORTANT]
> Une taille de sous-réseau avec 16 adresses IP est le minimum absolu avec un potentiel limité pour la montée en puissance ultérieure de Managed Instance. Le choix d'un sous-réseau avec le préfixe /27 ou un préfixe inférieur est fortement recommandé. 

Si vous envisagez de déployer plusieurs options Managed Instance à l’intérieur du sous-réseau et avez besoin d’optimiser la taille de ce dernier, utilisez ces paramètres pour former un calcul : 

- Azure utilise cinq adresses IP dans le sous-réseau pour ses besoins propres. 
- Chaque instance à usage général nécessite deux adresses. 
- Chaque instance Critique pour l’entreprise a besoin de quatre adresses

**Exemple** : vous prévoyez d’avoir trois Managed Instances de type Usage général et deux de type Critique pour l’entreprise. Cela signifie que vous avez besoin de 5 + 3 * 2 + 2 * 4 = 19 adresses IP. Comme les plages d’adresses IP sont définies par puissance de 2, vous avez besoin d’une plage de 32 (2 ^ 5) adresses IP. Ainsi, vous devez réserver le sous-réseau avec un masque de sous-réseau de /27. 

> [!IMPORTANT]
> Le calcul affiché ci-dessus deviendra obsolète au fur et à mesure des améliorations. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Créer un réseau virtuel pour Managed Instance à l’aide du déploiement Azure Resource Manager

Pour créer et configurer le réseau virtuel, le plus simple consiste à utiliser le modèle de déploiement Azure Resource Manager.

1. Connectez-vous au portail Azure.

2. Utilisez le bouton **Déployer dans Azure** pour déployer un réseau virtuel dans le cloud Azure :

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Ce bouton ouvre un formulaire que vous pouvez utiliser pour configurer l’environnement réseau où vous pouvez déployer Managed Instance.

  > [!Note]
  > Ce modèle Azure Resource Manager déploie un réseau virtuel avec deux sous-réseaux. Un sous-réseau appelé **ManagedInstances** est réservé pour les Managed Instances et a une table d’itinéraires préconfigurée, tandis que le second sous-réseau appelé **Default** est utilisé pour les autres ressources qui doivent accéder à Managed Instance (par exemple, les machines virtuelles Azure). Vous pouvez supprimer le sous-réseau **Default** si vous n’en avez pas besoin.

3. Configurez l’environnement réseau. Dans le formulaire suivant, vous pouvez configurer les paramètres de votre environnement réseau :

![Configurer le réseau Azure](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

Vous pouvez modifier les noms du réseau virtuel et des sous-réseaux et ajustez les plages d’adresses IP associées à vos ressources réseau. Lorsque vous appuyez sur le bouton « Acheter », ce formulaire crée et configure votre environnement. Si vous n’avez pas besoin de deux sous-réseaux, vous pouvez supprimer celui par défaut. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modifier un réseau virtuel existant pour des options Managed Instance 

Les questions et réponses figurant dans cette section vous montrent comment ajouter une option Managed Instance à un réseau virtuel existant. 

**Utilisez-vous un modèle de déploiement classique ou Resource Manager pour le réseau virtuel existant ?** 

Vous pouvez uniquement créer une option Managed Instance dans des réseaux virtuels Resource Manager. 

**Voulez-vous créer un sous-réseau pour une instance gérée SQL ou en utilisez un existant ?**

Si vous voulez en créer un : 

- Calculez la taille du sous-réseau en suivant les instructions de la section [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances).
- Suivez les étapes décrites dans [Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel](../virtual-network/virtual-network-manage-subnet.md). 
- Créez une table de routage qui contient une seule entrée, **0.0.0.0/0**, en tant qu’itinéraire Internet de tronçon suivant et associez-la au sous-réseau de l’option Managed Instance.  

Si vous souhaitez créer une option Managed Instance à l’intérieur d’un sous-réseau existant, nous recommandons le script PowerShell suivant pour préparer le sous-réseau.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
La préparation du sous-réseau s’effectue en trois étapes simples :

- Valider : le réseau virtuel et le sous-réseau sont validés pour les exigences de mise en réseau de Managed Instance.
- Confirmer : l’utilisateur voit un ensemble de modifications à effectuer pour préparer le sous-réseau au déploiement de Managed Instance et est invité à donner son consentement.
- Préparer : le réseau virtuel et le sous-réseau sont configurés correctement.

**Avez-vous configuré un serveur DNS personnalisé ?** 

Dans l’affirmative, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md). 

- Créez la table de routage nécessaire et associez-la : consultez [Créer la table de routage nécessaire et l’associer](#create-the-required-route-table-and-associate-it).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour un tutoriel qui montre comment créer un réseau virtuel, créer une option Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Création d’une option Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
