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
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715239"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster HDInsight standard est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. Chaque utilisateur peut avoir un autre cluster dédié pour lui-même à la demande et le détruire quand il n’est plus nécessaire. Cependant, nombre d’entreprises ont commencé la transition vers un modèle dans lequel les clusters sont gérés par les équipes informatiques et partagés par plusieurs équipes d’application. Ainsi, l’accès multi-utilisateur au cluster est nécessaire dans Azure HDInsight pour ces entreprises de plus grande taille.

HDInsight s’appuie sur le fournisseur d’identité le plus répandu, Active Directory (AD) de façon managée. En intégrant HDInsight à [Azure Active Directory Domain Services (AAD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), vous pouvez accéder aux clusters en utilisant vos informations d’identification de domaine. Les machines virtuelles dans HDInsight sont jointes au domaine que vous fournissez : tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent sans problème pour l’utilisateur authentifié. Les administrateurs peuvent ensuite créer des stratégies d’autorisation strictes à l’aide d’Apache Ranger, de façon à contrôler l’accès en fonction du rôle pour les ressources du cluster.


## <a name="integrate-hdinsight-with-active-directory"></a>Intégrer HDInsight avec Active Directory

Hadoop open source s’appuie sur Kerberos pour fournir l’authentification et la sécurité. Ainsi, les nœuds du cluster HDInsight sont joints à un domaine managé par AAD-DS. La sécurité Kerberos est configurée pour les composants Hadoop sur le cluster. Pour chacun des composants Hadoop, un principal du service est créé automatiquement. Un principal de machine correspondant est également créé pour chaque machine jointe au domaine. Afin de stocker ces principaux du service et de l’ordinateur, il est nécessaire de fournir une unité d’organisation dans le contrôleur de domaine (AAD-DS) où ces principaux sont placés. 

Pour résumer, vous devez configurer un environnement avec :

- Un domaine Active Directory (managé par AAD-DS)
- LDAP sécurisé (LDAPS) activé dans AAD-DS.
- Une connectivité réseau appropriée du réseau virtuel HDInsight vers le réseau virtuel AAD-DS, si vous choisissez des réseaux virtuels distincts pour ceux-ci. Une machine virtuelle à l’intérieur du réseau virtuel HDI doit avoir AAD-DS en ligne de mire avec l’appairage du réseau virtuel. Si HDI et AAD-DS sont tous deux déployés dans le même réseau virtuel, la connectivité est automatiquement fournie et aucune action supplémentaire n’est nécessaire.
- Une unité d’organisation [créée sur AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).
- Un compte de service qui dispose des autorisations pour :
    - Créer des principaux de service dans l’unité d’organisation.
    - Joindre des machines au domaine et créer des principaux de machine dans l’unité d’organisation.

La capture d’écran suivante montre une unité d’organisation créée dans contoso.com. Certains des principaux de service et de machine figurent également sur la capture d’écran.

![Unité d’organisation des clusters HDInsight joints à un domaine](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Configuration des différents contrôleurs de domaine :
Actuellement, HDInsight prend en charge seulement AAD-DS comme contrôleur de domaine principal avec lequel le cluster communiquera pour en faire un cluster Kerberos. Cependant, d’autres configurations AD complexes sont également possibles dès lors qu’elles permettent à AAD-DS d’accéder à HDI.

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)** : ce service fournit un domaine managé qui est entièrement compatible avec Windows Server Active Directory. Microsoft effectue la gestion, les mises à jour correctives et la surveillance du domaine dans une configuration à haute disponibilité. Vous pouvez déployer votre cluster sans vous préoccuper de la maintenance des contrôleurs de domaine. Les utilisateurs, les groupes et les mots de passe sont synchronisés à partir de votre annuaire Azure Active Directory [synchronisation unidirectionnelle de AAD vers AAD-DS], ce qui permet aux utilisateurs de se connecter au cluster en utilisant les mêmes informations d’identification d’entreprise. Pour plus d’informations, consultez [Guide pratique pour configurer des clusters HDInsight joints à un domaine avec AAD-DS](./apache-domain-joined-configure-using-azure-adds.md).
- **AD local ou AD sur des machines virtuelles IaaS** : si vous avez un annuaire AD local ou d’autres configurations AD plus complexes pour votre domaine, vous pouvez synchroniser les identités vers AAD via AD Connect, puis activer AAD-DS sur ce locataire AD. Comme Kerberos s’appuie sur les hachages de mot de passe, vous devez [activer la synchronisation du hachage de mot de passe sur AAD-DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Si vous utilisez la fédération avec les services Active Directory Federation Services (AD FS), vous pouvez choisir de configurer la synchronisation du hachage de mot de passe comme alternative de secours en cas de défaillance de votre infrastructure AD FS. Pour plus d’informations, consultez [Activer la synchronisation du hachage de mot de passe avec la synchronisation AAD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). L’utilisation d’AD local ou d’AD sur des machines virtuelles IaaS uniquement, sans AAD et AAD-DS, n’est pas une configuration prise en charge pour la jonction de domaine d’un cluster HDI.

## <a name="next-steps"></a>Étapes suivantes
* [Configurer des clusters HDInsight joints à un domaine](apache-domain-joined-configure-using-azure-adds.md).
* [Configurer des stratégies Hive pour des clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* [Gérer des clusters HDInsight joints à un domaine](apache-domain-joined-manage.md). 
