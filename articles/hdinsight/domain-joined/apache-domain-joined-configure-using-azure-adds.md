---
title: Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure AD-DS
description: Découvrez comment configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408352"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services

Les clusters Pack Sécurité Entreprise (ESP) offrent un accès multi-utilisateur sur les clusters Azure HDInsight. Comme les clusters HDInsight avec ESP sont connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. 

Dans cet article, vous allez apprendre à configurer un cluster HDInsight avec ESP en utilisant Azure Active Directory Domain Services (Azure AD-DS).

>[!NOTE]
>ESP est en disponibilité générale dans HDI 3.6 pour Spark, Interactive et Hadoop. Les types de clusters ESP pour HBase et Kafka sont en préversion.

## <a name="enable-azure-ad-ds"></a>Activer Azure AD-DS

L’activation d’Azure AD-DS est une condition préalable à la création d’un cluster HDInsight avec ESP. Pour plus d’informations, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Quand Azure AD-DS est activé, tous les utilisateurs et les objets commencent la synchronisation d’Azure Active Directory (AAD) avec Azure AD-DS par défaut. La longueur de l’opération de synchronisation varie selon le nombre d’objets dans AAD. La synchronisation pourrait prendre quelques jours pour des centaines de milliers d’objets. 

Les clients peuvent choisir de synchroniser uniquement les groupes qui ont besoin d’accéder aux clusters HDInsight. Cette option de synchroniser uniquement certains groupes est appelée *synchronisation délimitée*. Pour des instructions, consultez [Configurer une synchronisation délimitée entre Azure AD et votre domaine managé](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization).

> [!NOTE]
> Seuls les administrateurs des locataires disposent des privilèges pour créer une instance Azure AD-DS. L’authentification multifacteur doit être désactivée uniquement pour les utilisateurs qui ont accès au cluster.

Au moment d’activer le protocole LDAP sécurisé, placez le nom de domaine dans le nom de l’objet ou dans l’autre nom de l’objet du certificat. Par exemple, si votre nom de domaine est *contoso.com*, assurez-vous que ce nom exact existe dans le nom de l’objet ou dans l’autre nom de l’objet de votre certificat. Pour plus d’informations, consultez [Configurer le protocole LDAP sécurisé pour un domaine managé Azure AD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Vérifier l’état d’intégrité d’AAD-DS

Affichez l’état d’intégrité de vos services AAD-DS en sélectionnant **Intégrité** sous la catégorie **Gérer**. Assurez-vous que l’état d’AAD-DS est vert (en cours d’exécution) et que la synchronisation est terminée.

![Intégrité d’Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Ajouter une identité managée

Après avoir activé Azure AD-DS, créez une identité managée affecté par l’utilisateur et affectez-la au rôle **Contributeur des services de domaine HDInsight** dans le contrôle d’accès Azure AD-DS.

![Contrôle d’accès Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

L’affectation d’une identité managée au rôle **Contributeur des services de domaine HDInsight** permet de s’assurer que l’identité a un accès approprié pour effectuer certaines opérations de services de domaine sur le domaine AAD-DS. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Créer un cluster HDInsight avec ESP

L’étape suivante consiste à créer le cluster HDInsight avec ESP activé en utilisant Azure AD-DS.

Il est plus facile de placer l’instance Azure AD-DS et le cluster HDInsight dans le même réseau virtuel Azure. Si vous avez choisi de les placer dans des réseaux virtuels différents, vous devez appairer ces réseaux virtuels afin que les machines virtuelles HDInsight aient le contrôleur de domaine dans leur ligne de visée pour joindre les machines virtuelles. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md). Pour vérifier si l’appairage a été effectué correctement, joignez une machine virtuelle au réseau virtuel/sous-réseau HDInsight et effectuez un test ping sur le nom de domaine, ou exécutez **ldp.exe** pour accéder au domaine AAD-DS.

Au moment de créer un cluster HDInsight, vous avez la possibilité d’activer le pack Sécurité Entreprise sous l’onglet personnalisé. 

![Sécurité et réseau Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Dès lors que vous activez ESP, les erreurs de configuration courantes en rapport avec Azure AD-DS sont automatiquement détectées et validées.

![Pack Sécurité Entreprise Azure HDInsight - Validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

La détection précoce vous fait gagner du temps en vous permettant de corriger les erreurs avant de créer le cluster.

![Pack Sécurité Entreprise Azure HDInsight - Échec de validation de domaine](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quand vous créez un cluster HDInsight avec ESP, vous devez fournir les paramètres suivants :

- **Utilisateur administrateur du cluster** : choisissez un administrateur pour votre cluster à partir de votre instance Azure AD-DS synchronisée. Ce compte doit être déjà synchronisé et disponible dans AAD-DS.

- **Groupes d’accès au cluster** : les groupes de sécurité dont vous voulez synchroniser les utilisateurs avec le cluster doivent être disponibles dans Azure AD-DS. Par exemple, le groupe HiveUsers. Pour plus d’informations, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS** : ldaps://contoso.com:636 est un exemple.

La capture d’écran suivante montre les configurations réussies dans le portail Azure :

![Configuration Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

Vous pouvez choisir l’identité managée que vous avez créée dans la liste déroulante des identités managées affectées par l’utilisateur lors de la création d’un cluster.

![Configuration Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md).
* Pour vous connecter à des clusters HDInsight avec ESP à l’aide de SSH, consultez [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).