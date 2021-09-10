---
title: Mettre à jour la clé d’accès du compte de stockage Azure dans Azure HDInsight
description: Découvrez comment mettre à jour la clé d’accès du compte de stockage Azure dans un cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562218"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>Mettre à jour les clés d’accès du compte de stockage Azure dans un cluster HDInsight

Dans cet article, découvrez comment effectuer la rotation des clés d’accès du compte de stockage Azure pour les comptes de stockage principal ou secondaire dans Azure HDInsight.

>[!CAUTION]
> Le fait d’effectuer directement la rotation de la clé d’accès côté stockage rend le cluster HDInsight inaccessible.

## <a name="prerequisites"></a>Configuration requise

* Nous allons utiliser une approche pour effectuer la rotation des clés d’accès principales et secondaires du compte de stockage de manière échelonnée et en alternance pour garantir que le cluster HDInsight est accessible tout au long du processus.

    Voici un exemple d’utilisation des clés d’accès principales et secondaires du stockage, ainsi que de la définition de stratégies de rotation sur celles-ci :
    1. Utilisez la clé d’accès 1 sur le compte de stockage lors de la création du cluster HDInsight.
    1. Configurez la stratégie de rotation pour la clé d’accès 2 tous les N jours. Dans le cadre de cette rotation, mettez à jour HDInsight pour utiliser la clé d’accès 1, puis effectuez la rotation de la clé d’accès 2 sur le compte de stockage.
    1. Configurez la stratégie de rotation pour la clé d’accès 1 tous les N/2 jours. Dans le cadre de cette rotation, mettez à jour HDInsight pour utiliser la clé d’accès 2, puis effectuez la rotation de la clé d’accès 1 sur le compte de stockage.
    1. Avec l’approche ci-dessus, la clé d’accès 1 fait l’objet d’une rotation les N/2, 3N/2, etc. jours et la clé d’accès 2 fait l’objet d’une rotation les N, 2N, 3N, etc. jours.

* Pour configurer la rotation périodique des clés de compte de stockage, consultez [Automatiser la rotation d’un secret](../key-vault/secrets/tutorial-rotation-dual.md).

## <a name="update-storage-account-access-keys"></a>Mettre à jour les clés d’accès du compte de stockage

Utilisez une [action de script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) pour appliquer les modifications en tenant compte des considérations suivantes :

|Propriété | Valeur |
|---|---|
|URI de script bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|Type(s) de nœud|Head|
|Paramètres|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (facultatif)|

* `ACCOUNTNAME` est le nom du compte de stockage sur le cluster HDInsight.
* `ACCOUNTKEY` est la clé d’accès pour `ACCOUNTNAME`.
* `-p` est facultatif. si ce paramètre est spécifié, la clé n’est pas chiffrée et est stockée en texte brut dans le fichier core-site.xml.

## <a name="known-issues"></a>Problèmes connus

Le script précédent met directement à jour la clé d’accès côté cluster uniquement et ne renouvelle pas une copie côté fournisseur de ressources HDInsight. Par conséquent, l’action de script hébergée dans le compte de stockage échoue après la rotation de la clé d’accès.

Solution de contournement : utilisez des [URI SAS](hdinsight-storage-sharedaccesssignature-permissions.md) pour les actions de script ou rendez les scripts accessibles publiquement.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des comptes de stockage](hdinsight-hadoop-add-storage.md)