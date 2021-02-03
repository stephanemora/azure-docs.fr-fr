---
title: Chiffrement Azure HDInsight en transit
description: En savoir plus sur les fonctionnalités de sécurité permettant d’assurer le chiffrement en transit de votre cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946851"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Chiffrement IPSec en transit pour Azure HDInsight

Cet article traite de l’implémentation du chiffrement en transit pour les communications entre les nœuds de cluster Azure HDInsight.

## <a name="background"></a>Arrière-plan

Azure HDInsight offre un large éventail de fonctionnalités de sécurité pour sécuriser les données de votre entreprise. Ces solutions sont regroupées sous les piliers que sont la sécurité du périmètre, l’authentification, l’autorisation, l’audit, le chiffrement et la conformité. Le chiffrement peut être appliqué aux données au repos et en transit.

Le chiffrement au repos est couvert par le chiffrement côté serveur sur les comptes de stockage Azure, ainsi que par le chiffrement de disque sur les machines virtuelles Azure qui font partie de votre cluster HDInsight.

Le chiffrement des données en transit sur HDInsight est obtenu avec le protocole [TLS (Transport Layer Security)](../transport-layer-security.md) pour accéder aux passerelles de cluster et le protocole [IPSec (Internet Protocol Security)](https://wikipedia.org/wiki/IPsec) entre les nœuds de cluster. Le protocole IPSec peut éventuellement être activé entre tous les nœuds principaux, nœuds Worker, nœuds de périphérie, nœuds Zookeeper, ainsi que les nœuds de passerelle et [broker d’ID](./identity-broker.md).

## <a name="enable-encryption-in-transit"></a>Activer le chiffrement en transit

### <a name="azure-portal"></a>Portail Azure

Pour créer un nouveau cluster avec le chiffrement en transit activé à l’aide du portail Azure, procédez comme suit :

1. Commencez le processus normal de création d’un cluster. Pour connaître les étapes initiales de création d’un cluster, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).
1. Renseignez les onglets **Général** et **Stockage**. Continuez sur l’onglet **Sécurité + mise en réseau**.

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Créer un cluster – Onglet sécurité et mise en réseau.":::

1. Sous l’onglet **Sécurité + mise en réseau**, cochez la case **Activer le chiffrement en transit**.

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Créer un cluster – Activer le chiffrement en transit.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Créer un cluster avec chiffrement en transit activé à l’aide de l’interface de ligne de commande Azure

Le chiffrement en transit est activé à l’aide de la propriété `isEncryptionInTransitEnabled`.

Vous pouvez [télécharger un exemple de fichier des paramètres et des modèles](https://github.com/Azure-Samples/hdinsight-enterprise-security). Avant d’utiliser le modèle et l’extrait de code Azure CLI ci-dessous, remplacez les espaces réservés suivants par leurs valeurs correctes :

| Espace réservé | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | L’ID de votre abonnement Azure |
| `<RESOURCE_GROUP>` | Le groupe de ressources dans lequel vous souhaitez créer le cluster et le compte de stockage. |
| `<STORAGEACCOUNTNAME>` | Le compte de stockage qui doit être utilisé avec le cluster. Le nom doit être au format `ACCOUNTNAME.blob.core.windows.net`. |
| `<CLUSTERNAME>` | Nom de votre cluster HDInsight |
| `<PASSWORD>` | Le mot de passe que vous avez choisi pour vous connecter au cluster à l’aide de SSH et du tableau de bord Ambari. |
| `<VNET_NAME>` | Le réseau virtuel sur lequel le cluster sera déployé. |

L’extrait de code ci-dessous effectue les étapes initiales suivantes :

1. Il établit la connexion à votre compte Azure.
1. Il définit l’abonnement actif où les opérations de création seront effectuées.
1. Il crée un groupe de ressources pour les nouvelles activités de déploiement.
1. Il déploie le modèle pour créer un nouveau cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la sécurité d’entreprise dans Azure HDInsight](hdinsight-security-overview.md)
* [Synchroniser des utilisateurs Azure Active Directory avec un cluster HDInsight](../disk-encryption.md)
