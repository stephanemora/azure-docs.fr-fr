---
title: Configurer des clusters HDInsight joints à un domaine avec AAD-DS
description: Découvrez comment configurer les clusters HDInsight joints à un domaine à l’aide d’Azure Active Directory Domain Services.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849824"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurer les clusters HDInsight joints à un domaine à l’aide d’Azure Active Directory Domain Services

Les clusters joints à un domaine fournissent l’accès multi-utilisateur sur les clusters HDInsight. Les clusters HDInsight joints à un domaine sont connectés à un domaine. De cette façon, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. 

Dans cet article, vous allez voir comment configurer un cluster HDInsight joint à un domaine à l’aide d’Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Créer une instance d’Azure AD DS

L’activation d’Azure AD Domain Services (AAD-DS) est un prérequis avant de pouvoir créer un cluster HDInsight joint à un domaine. Pour activer une instance AAD-DS, consultez [Comment activer AAD-DS à l’aide du portail Azure ](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Seuls les administrateurs des locataires disposent des autorisations pour créer une instance AAD-DS. Si vous utilisez Azure Data Lake Storage (ADLS) comme stockage par défaut pour HDInsight, vérifiez que le locataire Azure AD par défaut pour ADLS est identique au domaine pour le cluster HDInsight. Étant donné que Hadoop s’appuie sur Kerberos et l’authentification de base, l’authentification multifacteur doit être désactivée pour les utilisateurs qui auront accès au cluster.

Une fois que l’instance AAD-DS a été provisionnée, vous devez créer un compte de service dans AAD (qui est ensuite synchronisé avec AAD-DS) avec les autorisations nécessaires. Si ce compte de service existe déjà, vous devez réinitialiser son mot de passe et attendre qu’il soit synchronisé avec AAD-DS (cette réinitialisation entraîne la création du hachage de mot de passe Kerberos, qui peut prendre jusqu’à 30 minutes pour effectuer la synchronisation avec AAD-DS). Ce compte de service doit pouvoir effectuer ce qui suit :

- Joindre des machines au domaine et placer les principaux de machine dans l’unité d’organisation que vous spécifiez lors de la création du cluster
- Créer des principaux de service au sein de l’unité d’organisation que vous spécifiez lors de la création du cluster.

> [!NOTE]
> Étant donné qu’Apache Zeppelin utilise le nom de domaine pour authentifier le compte de service d’administration, le compte de service DOIT porter le même nom de domaine que son suffixe UPN pour qu’Apache Zeppelin fonctionne correctement.

Pour plus d’informations sur les unités d’organisation et savoir comment les gérer, consultez [Créer une unité d’organisation sur un AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Un LDAP sécurisé pour le domaine managé AAD-DS est requis. Pour activer le protocole LDAPS, consultez [Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine managé AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Créer un cluster HDInsight joint à un domaine

L’étape suivante consiste à créer le cluster HDInsight à l’aide d’AAD DS et du compte de service créé dans la section précédente.

Il est plus facile de placer AAD DS et le cluster HDInsight dans le même réseau virtuel Azure (VNet). Si vous avez choisi de les placer dans des réseaux virtuels différents, vous devez appairer ces réseaux virtuels afin que les machines virtuelles HDI aient le contrôleur de domaine en ligne de vue pour joindre les machines virtuelles. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Lorsque vous créez un cluster HDInsight joint à un domaine, vous devez fournir les paramètres suivants :

- **Nom de domaine** : nom de domaine associé à AAD-DS. Par exemple, contoso.onmicrosoft.com.
- **Nom d’utilisateur du domaine** : compte de service dans le domaine managé qui a été créé dans la section précédente. Par exemple : hdiadmin@contoso.onmicrosoft.com. Cet utilisateur de domaine devient l’administrateur de ce cluster HDInsight.
- **Mot de passe du domaine** : mot de passe du compte de service.
- **Unité de l’organisation** : nom unique de l’unité d’organisation à utiliser avec le cluster HDInsight. Par exemple : OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Si cette unité d’organisation n’existe pas, le cluster HDInsight tente de créer l’unité d’organisation à l’aide des privilèges dont dispose le compte de service. (Par exemple, si le compte de service est dans le groupe d’administrateurs AAD-DS, il dispose des autorisations appropriées pour créer une unité d’organisation. Sinon, vous devrez peut-être créer d’abord l’unité d’organisation et donner au compte du service le contrôle total sur cette unité d’organisation. Reportez à [Créer une unité d’organisation sur un AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).)

    > [!IMPORTANT]
    > Il est important d’inclure tous les contrôleurs de domaine séparés par une virgule après l’unité d’organisation (par exemple, OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com).

- **URL LDAPS** : par exemple, ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Indiquez l’url complète, y compris le protocole ldaps:// et le numéro de port (:636).

- **Accès au groupe d’utilisateurs** : groupes de sécurité dont vous souhaitez synchroniser les utilisateurs avec le cluster. Par exemple, HiveUsers. Si vous souhaitez spécifier plusieurs groupes d’utilisateurs, séparez-les par des virgules (« , »).
 
La capture d’écran suivante montre les configurations dans le portail Azure :

![Configuration d’un cluster HDInsight joint à un domaine avec Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour utiliser des clusters HDInsight joints à un domaine, consultez [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

