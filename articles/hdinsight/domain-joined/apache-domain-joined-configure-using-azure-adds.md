---
title: Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure AD-DS
description: Découvrir comment configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968371"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services

Les clusters Pack Sécurité Entreprise (ESP) offrent un accès multi-utilisateur sur les clusters Azure HDInsight. Comme les clusters HDInsight avec ESP sont connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. 

Dans cet article, vous allez apprendre à configurer un cluster HDInsight avec ESP en utilisant Azure Active Directory Domain Services (Azure AD-DS).

>[!NOTE]
>ESP est disponible dans HDI 3.6 et + pour Spark, Interactive et Hadoop. ESP pour les types de cluster HBase est disponible en préversion.


## <a name="enable-azure-ad-ds"></a>Activer Azure AD-DS

L’activation d’Azure AD-DS est une condition préalable à la création d’un cluster HDInsight avec ESP. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Seuls les administrateurs des locataires disposent des privilèges pour créer une instance Azure AD-DS. Si vous utilisez Azure Data Lake Storage Gen1 comme stockage par défaut pour HDInsight, vérifiez que le locataire Azure AD par défaut pour Azure Data Lake Storage Gen1 est identique au domaine pour le cluster HDInsight. Étant donné que Hadoop s’appuie sur Kerberos et l’authentification de base, l’authentification multifacteur doit être désactivée pour les utilisateurs qui accèdent au cluster.

Le protocole LDAP sécurisé s’adresse à un domaine managé Azure AD-DS. Au moment d’activer le protocole LDAPS, placez le nom de domaine dans le nom du sujet ou dans l’autre nom du sujet du certificat. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé pour un domaine managé Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Ajouter une identité managée

Après avoir activé Azure AD-DS, créez une identité managée et affectez-la au rôle **Contributeur des services de domaine HDInsight** dans le contrôle d’accès Azure AD-DS.

![Contrôle d’accès Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Créer un cluster HDInsight avec ESP

L’étape suivante consiste à créer le cluster HDInsight avec ESP activé en utilisant Azure AD-DS.

Il est plus facile de placer l’instance Azure AD-DS et le cluster HDInsight dans le même réseau virtuel Azure. Si vous avez choisi de les placer dans des réseaux virtuels différents, vous devez appairer ces réseaux virtuels afin que les machines virtuelles HDInsight aient le contrôleur de domaine dans leur ligne de visée pour joindre les machines virtuelles. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Au moment de créer un cluster HDInsight, vous avez la possibilité d’activer le pack Sécurité Entreprise pour connecter votre cluster à Azure AD-DS. 

![Sécurité et réseau Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Dès lors que vous activez ESP, les erreurs de configuration courantes en rapport avec Azure AD-DS sont automatiquement détectées et validées.

![Pack Sécurité Entreprise Azure HDInsight - Validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

La détection précoce vous fait gagner du temps en vous permettant de corriger les erreurs avant de créer le cluster.

![Pack Sécurité Entreprise Azure HDInsight - Échec de validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quand vous créez un cluster HDInsight avec ESP, vous devez fournir les paramètres suivants :

- **Utilisateur administrateur du cluster** : choisissez un administrateur pour votre cluster à partir de votre instance Azure AD-DS synchronisée.

- **Groupes d’accès au cluster** : les groupes de sécurité dont vous voulez synchroniser les utilisateurs avec le cluster doivent être synchronisés et disponibles dans Azure AD-DS. Par exemple, HiveUsers. Si vous souhaitez spécifier plusieurs groupes d’utilisateurs, séparez-les par des points-virgules (« ; »). Le ou les groupes doivent exister dans le répertoire avant le provisionnement. Pour plus d’informations, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Si le groupe n’existe pas, une erreur se produit : « Groupe HiveUsers introuvable dans Active Directory ».

- **URL LDAPS** : ldaps://contoso.onmicrosoft.com:636 est un exemple.

    > [!IMPORTANT]
    > Indiquez l’URL complète, y compris « ldaps:// » et le numéro de port (:636).

La capture d’écran suivante montre les configurations dans le portail Azure :

   ![Configuration Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md).
* Pour vous connecter à des clusters HDInsight avec ESP à l’aide de SSH, consultez [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

