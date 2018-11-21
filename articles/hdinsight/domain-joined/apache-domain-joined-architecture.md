---
title: Architecture Azure HDInsight avec le pack Sécurité Entreprise
description: Apprenez à planifier la sécurité HDInsight avec le pack Sécurité Entreprise.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 35768aed3509d6ec5b73c64c7a8a6b132957ace1
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632728"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Utiliser le pack Sécurité Entreprise dans HDInsight

Le cluster Azure HDInsight standard est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. Chaque utilisateur peut créer un cluster dédié à la demande, et le détruire quand il n’est plus nécessaire. 

Nombre d’entreprises se sont orientées vers un modèle dans lequel les clusters sont gérés par les équipes informatiques, et partagés par plusieurs équipes d’application. Ces entreprises de plus grande taille ont besoin d’un accès multi-utilisateur à chaque cluster dans Azure HDInsight.

HDInsight s’appuie sur le fournisseur d’identité répandu, Active Directory, de façon managée. En intégrant HDInsight à [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), vous pouvez accéder aux clusters en utilisant vos informations d’identification de domaine. 

Les machines virtuelles dans HDInsight sont joints à votre domaine fourni. Ainsi, tous les services exécutés sur HDInsight (Apache Ambari, serveur Apache Hive, Apache Ranger, serveur Apache Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié. Les administrateurs peuvent ensuite créer des stratégies d’autorisation strictes à l’aide d’Apache Ranger, de façon à fournir un contrôle de l’accès en fonction du rôle pour les ressources du cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Intégrer HDInsight avec Active Directory

Apache Hadoop open source s’appuie sur Kerberos pour l’authentification et la sécurité. Ainsi, les nœuds de cluster HDInsight avec le pack Sécurité Entreprise (ESP) sont joints à un domaine qui est géré par Azure AD DS. La sécurité Kerberos est configurée pour les composants Hadoop sur le cluster. 

Les éléments suivants sont créés automatiquement :
- Un principal de service pour chaque composant Hadoop 
- Un principal de machine pour chaque machine jointe au domaine
- Une unité d’organisation pour chaque cluster afin de stocker ces principaux de service et de machine 

Pour résumer, vous devez configurer un environnement avec :

- Un domaine Active Directory (managé par Azure AD DS).
- LDAP sécurisé (LDAPS) activé dans Azure AD DS.
- Une connectivité réseau appropriée, du réseau virtuel HDInsight au réseau virtuel Azure AD DS, si vous choisissez des réseaux virtuels distincts pour ces services. Une machine virtuelle à l’intérieur du réseau virtuel HDInsight doit avoir une visibilité directe sur Azure AD DS via le peering de réseau virtuel. Si HDInsight et Azure AD DS sont déployés dans le même réseau virtuel, la connectivité est automatiquement fournie et aucune action supplémentaire n’est nécessaire.

## <a name="set-up-different-domain-controllers"></a>Configurer différents contrôleurs de domaine
Pour l’instant, HDInsight prend uniquement en charge Azure AD DS comme étant le contrôleur de domaine principal que le cluster utilise pour la communication Kerberos. Par contre, d’autres configurations Active Directory complexes sont possibles, tant que ce type de configuration aboutit à l’activation d’Azure AD DS pour l’accès de HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) fournit un domaine managé qui est entièrement compatible avec Windows Server Active Directory. Microsoft effectue la gestion, les mises à jour correctives et la surveillance du domaine dans une configuration à haute disponibilité. Vous pouvez déployer votre cluster sans vous préoccuper de la maintenance des contrôleurs de domaine. 

Les utilisateurs, groupes et mots de passe sont synchronisés à partir d’Azure Active Directory (Azure AD). La synchronisation unidirectionnelle à partir de votre instance Azure AD dans Azure AD DS permet aux utilisateurs de se connecter au cluster en utilisant les mêmes informations d’identification d’entreprise. 

Pour plus d’informations, consultez [Configurer des clusters HDInsight avec ESP en utilisant Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Service Active Directory local ou Active Directory sur des machines virtuelles IaaS

Si vous disposez d’une instance Active Directory locale ou de configurations Active Directory plus complexes pour votre domaine, vous pouvez synchroniser ces identités sur Azure AD au moyen d’Azure AD Connect. Vous pouvez ensuite activer Azure AD DS sur ce locataire Active Directory. 

Étant donné que Kerberos s’appuie sur les hachages de mot de passe, vous devez [activer la synchronisation du hachage de mot de passe sur Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Si vous utilisez la fédération avec Active Directory Federation Services (AD FS), vous avez la possibilité de configurer la synchronisation de hachage de mot de passe comme dispositif de secours en cas de défaillance de votre infrastructure AD FS. Pour plus d’informations, consultez [Activer la synchronisation du hachage de mot de passe avec la synchronisation Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

La seule utilisation du service Active Directory local ou d’Active Directory sur des machines virtuelles IaaS, sans Azure AD ni Azure AD DS, n’est pas une configuration prise en charge pour les clusters HDInsight avec ESP.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des clusters HDInsight avec ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Configurer des stratégies Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md)
* [Gérer des clusters HDInsight avec ESP](apache-domain-joined-manage.md) 
