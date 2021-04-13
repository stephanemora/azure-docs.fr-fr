---
title: Échec de la commande d’importation/exportation de Sqoop pour certains utilisateurs dans les clusters ESP – Azure HDInsight
description: 'La commande d’importation/exportation d’Apache Sqoop échoue avec l’erreur « Import Failed: java.io.IOException: The ownership on the staging directory /user/yourusername/.staging is not as expected » pour certains utilisateurs dans le cluster Azure HDInsight ESP'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387166"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scénario : la commande d’importation/exportation de Sqoop échoue pour les utilisateurs dont les noms comptent plus de 20 caractères dans les clusters Azure HDInsight ESP

Cet article décrit un problème connu et une solution de contournement lors de l’utilisation de clusters prenant en charge Azure HDInsight ESP (Enterprise Security Pack) à l’aide d’un compte de stockage ADLS Gen2 (ABFS).

## <a name="issue"></a>Problème

L’exécution de la commande d’importation/exportation de Sqoop échoue avec l’erreur ci-dessous pour certains utilisateurs :

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

Dans l’exemple ci-dessus, `/user/yourlongdomainuserna/.staging` affiche le nom d’utilisateur de 20 caractères tronqué pour le nom d’utilisateur `yourlongdomainusername`.

## <a name="cause"></a>Cause

La longueur du nom d’utilisateur dépasse 20 caractères. 

Pour plus de détails, consultez [Synchronisation des objets et des informations d’identification dans un domaine managé Azure Active Directory Domain Services](../active-directory-domain-services/synchronization.md).

## <a name="workaround"></a>Solution de contournement

Utilisez un nom d’utilisateur d’une longueur inférieure ou égale à 20 caractères.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
