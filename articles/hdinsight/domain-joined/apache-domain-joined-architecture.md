---
title: Architecture de cluster Azure HDInsight joint à un domaine | Microsoft Docs
description: Découvrez comment planifier les clusters HDInsight joints à un domaine.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 1f51a1fbb38bc27d15b7a45ca4783508d863fee5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112625"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster Azure HDInsight standard est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. Chaque utilisateur peut créer un cluster dédié à la demande, et le détruire quand il n’est plus nécessaire. 

Nombre d’entreprises se sont orientées vers un modèle dans lequel les clusters sont gérés par les équipes informatiques, et partagés par plusieurs équipes d’application. Ces entreprises de plus grande taille ont besoin d’un accès multi-utilisateur à chaque cluster dans Azure HDInsight.

HDInsight s’appuie sur le fournisseur d’identité répandu, Active Directory, de façon managée. En intégrant HDInsight à [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), vous pouvez accéder aux clusters en utilisant vos informations d’identification de domaine. 

Les machines virtuelles dans HDInsight sont joints à votre domaine fourni. Ainsi, tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié. Les administrateurs peuvent ensuite créer des stratégies d’autorisation strictes à l’aide d’Apache Ranger, de façon à fournir un contrôle de l’accès en fonction du rôle pour les ressources du cluster.


## <a name="integrate-hdinsight-with-active-directory"></a>Intégrer HDInsight avec Active Directory

Hadoop open source s’appuie sur Kerberos pour l’authentification et la sécurité. Ainsi, les nœuds du cluster HDInsight sont joints à un domaine qui est managé par Azure AD DS. La sécurité Kerberos est configurée pour les composants Hadoop sur le cluster. 

Pour chaque composant Hadoop, un principal du service est créé automatiquement. Un principal de machine correspondant est également créé pour chaque machine jointe au domaine. Afin de stocker ces principaux du service et de la machine, vous devez fournir une unité d’organisation dans le contrôleur de domaine (Azure AD DS) où ces principaux sont placés. 

Pour résumer, vous devez configurer un environnement avec :

- Un domaine Active Directory (managé par Azure AD DS).
- LDAP sécurisé (LDAPS) activé dans Azure AD DS.
- Une connectivité réseau appropriée, du réseau virtuel HDInsight au réseau virtuel Azure AD DS, si vous choisissez des réseaux virtuels distincts pour ces services. Une machine virtuelle à l’intérieur du réseau virtuel HDInsight doit avoir une visibilité directe sur Azure AD DS via le peering de réseau virtuel. Si HDInsight et Azure AD DS sont déployés dans le même réseau virtuel, la connectivité est automatiquement fournie et aucune action supplémentaire n’est nécessaire.
- Une unité d’organisation [créée sur Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).
- Un compte de service qui dispose des autorisations pour :
    - créer des principaux de service dans l’unité d’organisation ;
    - joindre des machines au domaine et créer des principaux de machine dans l’unité d’organisation.

La capture d’écran suivante montre une unité d’organisation créée dans contoso.com. Il montre également quelques principaux de service et principaux de machine.

![Unité d’organisation des clusters HDInsight joints à un domaine](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>Configurer différents contrôleurs de domaine
Pour l’instant, HDInsight prend uniquement en charge Azure AD DS comme étant le contrôleur de domaine principal que le cluster utilise pour la communication Kerberos. Par contre, d’autres configurations Active Directory complexes sont possibles, tant que ce type de configuration aboutit à l’activation d’Azure AD DS pour l’accès de HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) fournit un domaine managé qui est entièrement compatible avec Windows Server Active Directory. Microsoft effectue la gestion, les mises à jour correctives et la surveillance du domaine dans une configuration à haute disponibilité. Vous pouvez déployer votre cluster sans vous préoccuper de la maintenance des contrôleurs de domaine. 

Les utilisateurs, groupes et mots de passe sont synchronisés à partir d’Azure Active Directory (Azure AD). La synchronisation unidirectionnelle à partir de votre instance Azure AD dans Azure AD DS permet aux utilisateurs de se connecter au cluster en utilisant les mêmes informations d’identification d’entreprise. 

Pour plus d’informations, consultez [Configurer des clusters HDInsight joints à un domaine avec Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Service Active Directory local ou Active Directory sur des machines virtuelles IaaS

Si vous disposez d’une instance Active Directory locale ou de configurations Active Directory plus complexes pour votre domaine, vous pouvez synchroniser ces identités sur Azure AD au moyen d’Azure AD Connect. Vous pouvez ensuite activer Azure AD DS sur ce locataire Active Directory. 

Étant donné que Kerberos s’appuie sur les hachages de mot de passe, vous devez [activer la synchronisation du hachage de mot de passe sur Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Si vous utilisez la fédération avec Active Directory Federation Services (AD FS), vous avez la possibilité de configurer la synchronisation de hachage de mot de passe comme dispositif de secours en cas de défaillance de votre infrastructure AD FS. Pour plus d’informations, consultez [Activer la synchronisation du hachage de mot de passe avec la synchronisation Azure AD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). 

La seule utilisation du service Active Directory local ou d’Active Directory sur des machines virtuelles IaaS, sans Azure AD ni Azure AD DS, n’est pas une configuration prise en charge pour les clusters HDInsight joints à un domaine.

## <a name="next-steps"></a>Étapes suivantes
* [Configurer des clusters HDInsight joints à un domaine](apache-domain-joined-configure-using-azure-adds.md)
* [Configurer des stratégies Hive pour des clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md)
* [Gérer des clusters HDInsight joints à un domaine](apache-domain-joined-manage.md) 
