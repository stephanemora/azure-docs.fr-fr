---
title: Configurer un cluster HDInsight joint à un domaine avec Azure Active Directory Domain Services (Azure AD DS)
description: Découvrir comment configurer un cluster HDInsight joint à un domaine avec Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 17924b0a00f4605d41492768b0124c583664aca6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042139"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Configurer un cluster HDInsight joint à un domaine avec Azure Active Directory Domain Services

Les clusters joints à un domaine fournissent l’accès multi-utilisateur sur les clusters Azure HDInsight. Les clusters HDInsight joints à un domaine sont connectés à un domaine. De cette façon, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. 

Dans cet article, vous apprenez à configurer un cluster HDInsight joint à un domaine au moyen d’Azure Active Directory Domain Services (Azure AD DS).

## <a name="enable-azure-ad-ds"></a>Activer Azure AD DS

L’activation d’Azure AD-DS est un prérequis avant de pouvoir créer un cluster HDInsight joint à un domaine. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Seuls les administrateurs des locataires disposent des privilèges pour créer une instance Azure AD DS. Si vous utilisez Azure Data Lake Storage Gen1 comme stockage par défaut pour HDInsight, vérifiez que le locataire Azure AD par défaut pour Azure Data Lake Storage Gen1 est identique au domaine pour le cluster HDInsight. Étant donné que Hadoop s’appuie sur Kerberos et l’authentification de base, l’authentification multifacteur doit être désactivée pour les utilisateurs qui accèdent au cluster.

Après avoir provisionné l’instance Azure AD DS, créez un compte de service dans Azure Active Directory (Azure AD) avec les autorisations appropriées. Si ce compte de service existe déjà, réinitialisez son mot de passe et attendez qu’il se synchronise avec Azure AD DS. Cette réinitialisation entraîne la création du hachage de mot de passe Kerberos, et peut prendre jusqu’à 30 minutes pour se synchroniser avec Azure AD DS. 

Le compte de service doit pouvoir effectuer ce qui suit :

- Joindre des machines au domaine et placer les principaux de machine dans l’unité d’organisation que vous spécifiez lors de la création du cluster
- Créer des principaux de service au sein de l’unité d’organisation que vous spécifiez lors de la création du cluster.

> [!NOTE]
> Étant donné qu’Apache Zeppelin utilise le nom de domaine pour authentifier le compte de service d’administration, le compte de service *doit* porter le même nom de domaine que son suffixe UPN pour qu’Apache Zeppelin fonctionne correctement.

Pour en apprendre davantage sur les unités d’organisation et savoir comment les gérer, consultez [Créer une unité d’organisation sur un domaine managé Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Le protocole LDAP sécurisé sert pour un domaine managé Azure AD DS. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé pour un domaine managé Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Créer un cluster HDInsight joint à un domaine

L’étape suivante consiste à créer le cluster HDInsight avec Azure AD DS et le compte de service que vous avez créé à la section précédente.

Il est plus facile de placer l’instance Azure AD DS et le cluster HDInsight dans le même réseau virtuel Azure. Si vous avez choisi de les placer dans des réseaux virtuels différents, vous devez appairer ces réseaux virtuels afin que les machines virtuelles HDInsight aient le contrôleur de domaine dans leur ligne de visée pour joindre les machines virtuelles. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Lorsque vous créez un cluster HDInsight joint à un domaine, vous devez fournir les paramètres suivants :

- **Nom de domaine** : nom de domaine associé à l’instance d’Azure AD DS. contoso.onmicrosoft.com est un exemple.

- **Nom d’utilisateur de domaine** : compte de service dans le domaine managé du contrôleur de domaine Azure AD DS que vous avez créé à la section précédente. Par exemple hdiadmin@contoso.onmicrosoft.com. Cet utilisateur de domaine devient l’administrateur de ce cluster HDInsight.

- **Mot de passe du domaine** : mot de passe du compte de service.

- **Unité d’organisation** : nom unique de l’unité d’organisation à utiliser avec le cluster HDInsight. UO=HDInsightOU,CD=contoso,CD=onmicrosoft,CD=com en est un exemple. Si cette unité d’organisation n’existe pas, le cluster HDInsight tente de créer l’unité d’organisation en utilisant les privilèges dont dispose le compte de service. Par exemple, si le compte de service est dans le groupe Administrateurs Azure AD DS, il détient les autorisations appropriées pour créer une unité d’organisation. Sinon, vous devez créer d’abord l’unité d’organisation pour donner le contrôle total au compte de service sur cette unité d’organisation. Pour plus d’informations, consultez [Créer une unité d’organisation sur un domaine managé Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Incluez tous les contrôleurs de domaine, séparés par une virgule, après l’unité d’organisation (par exemple, OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com).

- **URL LDAPS** : ldaps://contoso.onmicrosoft.com:636 est un exemple.

    > [!IMPORTANT]
    > Indiquez l’URL complète, y compris « ldaps:// » et le numéro de port (:636).

- **Accès au groupe d’utilisateurs** : groupes de sécurité dont vous souhaitez synchroniser les utilisateurs avec le cluster. Par exemple, HiveUsers. Si vous souhaitez spécifier plusieurs groupes d’utilisateurs, séparez-les par des points-virgules (« ; »). Le ou les groupes doivent exister dans le répertoire avant le provisionnement. Pour plus d’informations, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Si le groupe n’existe pas, une erreur se produit : « Groupe HiveUsers introuvable dans Active Directory ».

La capture d’écran suivante montre les configurations dans le portail Azure :

   ![Configuration d’un cluster HDInsight joint à un domaine avec Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour se connecter à des clusters HDInsight joints à un domaine à l’aide de SSH, consultez [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

