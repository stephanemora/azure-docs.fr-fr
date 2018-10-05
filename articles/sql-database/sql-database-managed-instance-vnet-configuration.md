---
title: Configuration d’un réseau virtuel Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique décrit les options de configuration d’un réseau virtuel avec Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 9d3f867dad40017e8e97ec4f5e370533b018263c
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181162"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel pour Azure SQL Database Managed Instance

Azure SQL Database Managed Instance doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Ce déploiement permet les scénarios suivants : 
- Connexion directe à une instance gérée à partir d’un réseau local 
- Connexion de Managed Instance à un serveur lié ou un autre magasin de données local 
- Connexion de Managed Instance à des ressources Azure  

## <a name="plan"></a>Planification

Planifiez votre déploiement de Managed Instance dans un réseau virtuel en utilisant vos réponses aux questions suivantes : 
- Envisagez-vous de déployer une seule ou plusieurs options Managed Instance ? 

  Le nombre d’options Managed Instance détermine la taille minimale du sous-réseau à allouer à vos instances gérées. Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour Managed Instance](#determine-the-size-of-subnet-for-managed-instances). 
- Avez-vous besoin de déployer votre option Managed Instance sur un réseau virtuel existant ou créez-vous un réseau ? 

   Si vous envisagez d’utiliser un réseau virtuel existant, vous devez modifier sa configuration pour prendre en compte Managed Instance. Pour plus d’informations, consultez [Modifier un réseau virtuel existant pour Managed Instance](#modify-an-existing-virtual-network-for-managed-instances). 

   Si vous envisagez de créer un réseau virtuel, consultez [Créer un réseau virtuel pour Managed Instance](#create-a-new-virtual-network-for-a-managed-instance).

## <a name="requirements"></a>Configuration requise

Pour créer une instance managée, créez un sous-réseau dédié (le sous-réseau Instance managée) à l’intérieur du réseau virtuel qui respecte les exigences suivantes :
- **Un sous-réseau dédié** : le sous-réseau Instance managée ne doit contenir aucun autre service cloud et ne doit pas être un sous-réseau de passerelle. Vous ne pouvez pas créer d’instance managée dans un sous-réseau qui contient des ressources autres qu’une instance managée, ni ajouter ultérieurement d’autres ressources à l’intérieur du sous-réseau.
- **Un groupe de sécurité réseau (NSG) compatible** : un groupe de sécurité réseau associé à un sous-réseau Instance managée doit contenir les règles répertoriées dans les tableaux suivants (règles de sécurité de trafic entrant obligatoires et règles de sécurité de trafic sortant obligatoires), qui doivent être prioritaires par rapport aux autres règles. Vous pouvez utiliser un groupe de sécurité réseau pour contrôler entièrement l’accès au point de terminaison de données de l’instance managée en filtrant le trafic sur le port 1433. 
- **Une table de routage défini par l'utilisateur compatible** : le sous-réseau Instance managée doit comprendre une table UDR à laquelle est affecté le routage UDR obligatoire **0.0.0.0/0 Next Hop Internet**. En outre, vous pouvez utiliser la passerelle de réseau virtuel ou une appliance de réseau virtuel pour ajouter en tant que destination un routage UDR qui achemine le trafic comprenant des plages d’adresses IP privées locales. 
- **Un DNS personnalisé facultatif** : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP du programme de résolution récursif d’Azure (par exemple 168.63.129.16) à la liste. Pour plus d’informations, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md). Le serveur DNS personnalisé doit pouvoir résoudre les noms d’hôtes dans les domaines suivants et leurs sous-domaines : *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* et *microsoftonline-p.com*. 
- **Aucun point de terminaison de service** : le sous-réseau Instance managée ne doit pas être associé à un point de terminaison de service. Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
- **Un nombre d’adresses IP suffisant** : le sous-réseau Instance managée doit avoir 16 adresses IP au minimum (le minimum recommandé est de 32 adresses IP). Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour les options Managed Instance](#determine-the-size-of-subnet-for-managed-instances).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une instance managée si le sous-réseau de destination ne répond pas à toutes les exigences précédentes. Lorsqu’une instance managée est créée, une *stratégie d’intention réseau* est appliquée au sous-réseau pour empêcher toute modification non conforme de la configuration réseau. Lorsque la dernière instance restante est supprimée du sous-réseau, la *stratégie d’intention réseau* est elle aussi supprimée.

### <a name="mandatory-inbound-security-rules"></a>Règles de sécurité du trafic entrant obligatoires 

| NOM       |PORT                        |PROTOCOLE|SOURCE           |DESTINATION|ACTION|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|TCP     |Quelconque              |Quelconque        |AUTORISER |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |Quelconque        |AUTORISER |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|Quelconque        |AUTORISER |

### <a name="mandatory-outbound-security-rules"></a>Règles de sécurité du trafic sortant obligatoires 

| NOM       |PORT          |PROTOCOLE|SOURCE           |DESTINATION|ACTION|
|------------|--------------|--------|-----------------|-----------|------|
|gestion  |80, 443, 12000|TCP     |Quelconque              |Quelconque        |AUTORISER |
|mi_subnet   |Quelconque           |Quelconque     |Quelconque              |SOUS-RÉSEAU MI  |AUTORISER |

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

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>Créer un réseau virtuel pour une instance managée

Pour créer et configurer le réseau virtuel, le plus simple consiste à utiliser le modèle de déploiement Azure Resource Manager.

1. Connectez-vous au portail Azure.

2. Utilisez le bouton **Déployer dans Azure** pour déployer un réseau virtuel dans le cloud Azure :

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Ce bouton ouvre un formulaire que vous pouvez utiliser pour configurer l’environnement réseau où vous pouvez déployer Managed Instance.

  > [!Note]
  > Ce modèle Azure Resource Manager déploie un réseau virtuel avec deux sous-réseaux. Un sous-réseau appelé **ManagedInstances** est réservé pour les Managed Instances et a une table d’itinéraires préconfigurée, tandis que le second sous-réseau appelé **Default** est utilisé pour les autres ressources qui doivent accéder à Managed Instance (par exemple, les machines virtuelles Azure). Vous pouvez supprimer le sous-réseau **Default** si vous n’en avez pas besoin.

3. Configurez l’environnement réseau. Dans le formulaire suivant, vous pouvez configurer les paramètres de votre environnement réseau :

![Configurer le réseau Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

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

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour un tutoriel qui montre comment créer un réseau virtuel, créer une option Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Création d’une option Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
