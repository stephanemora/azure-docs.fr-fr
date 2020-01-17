---
title: Utiliser le broker d’ID (préversion) pour la gestion des informations d’identification - Azure HDInsight
description: Découvrez le broker d’ID HDInsight pour simplifier l’authentification pour les clusters Apache Hadoop joints à un domaine.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475915"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Utiliser un broker d’ID pour la gestion des informations d’identification

Cet article explique comment configurer et utiliser la fonctionnalité de broker d’ID dans Azure HDInsight. Vous pouvez utiliser cette fonctionnalité pour vous connecter à Apache Ambari à l’aide de l’authentification multifacteur Azure et d’accéder aux tickets Kerberos requis sans avoir besoin de hachages de Mot de passe dans Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Vue d’ensemble

Le broker d’ID simplifie les configurations d’authentification complexes dans les scénarios suivants :

* Votre organisation s’appuie sur une fédération pour authentifier les utilisateurs afin d’accéder aux ressources du cloud. Auparavant, pour utiliser des clusters de pack Sécurité Entreprise (ESP) HDInsight, vous deviez activer la synchronisation de hachage de Mot de passe à partir de votre environnement local pour Azure Active Directory. Cette exigence peut être difficile ou indésirable pour certaines organisations.

* Vous créez des solutions qui utilisent des technologies reposant sur des mécanismes d’authentification différents. Par exemple, Apache Hadoop et Apache Ranger s’appuient sur Kerberos, tandis qu’Azure Data Lake Storage s’appuie sur OAuth.

Le broker d’ID fournit une infrastructure d’authentification unifiée et supprime la nécessité de synchroniser les hachages de Mot de passe pour Azure AD DS. Le broker d’ID est constitué de composants s’exécutant sur une machine virtuelle Windows Server (nœud du broker d’ID), ainsi que des nœuds de passerelle de cluster. 

Le diagramme suivant illustre le déroulement de l’authentification pour tous les utilisateurs, y compris les utilisateurs fédérés, après l’activation du broker d’ID :

![Flux d’authentification avec le broker d’ID](./media/identity-broker/identity-broker-architecture.png)

Le broker d’ID vous permet de vous connecter à des clusters ESP à l’aide d’une authentification multifacteur, sans fournir de Mot de passe. Si vous êtes déjà connecté à d’autres services Azure, tels que le portail Azure, vous pouvez vous connecter à votre cluster HDInsight avec une expérience d’authentification unique (SSO).

## <a name="enable-hdinsight-id-broker"></a>Activer le broker d’ID HDInsight

Pour créer un cluster ESP avec le broker d’ID activé, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Suivez les étapes de création de base pour un cluster ESP. Pour plus d’informations, consultez [Créer un cluster HDInsight avec ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Sélectionnez **Activer le broker d’ID HDInsight**.

La fonctionnalité de broker d’ID ajoute une machine virtuelle supplémentaire au cluster. Cette machine virtuelle est le nœud du broker d’ID et comprend des composants serveur pour prendre en charge l’authentification. Le nœud du broker d’ID est joint au domaine Azure AD DS.

![Option permettant d’activer le broker d’ID](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Intégration d’outils

Le plug-in [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) HDInsight est mis à jour pour prendre en charge OAuth. Vous pouvez utiliser ce plug-in pour vous connecter au cluster et envoyer des travaux.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Accès SSH sans hachage de Mot de passe dans Azure AD DS

Une fois le broker d’ID activé, vous avez toujours besoin d’un hachage de Mot de passe stocké dans Azure AD DS pour les scénarios SSH avec des comptes de domaine. Pour utiliser SSH avec une machine virtuelle jointe à un domaine, ou pour exécuter la commande `kinit`, vous devez fournir un Mot de passe. 

L’authentification SSH requiert que le hachage soit disponible dans Azure AD DS. Si vous souhaitez utiliser SSH uniquement pour les scénarios d’administration, vous pouvez créer un compte cloud seulement et l’utiliser pour utiliser SSH pour le cluster. Les autres utilisateurs peuvent toujours utiliser les outils Ambari ou HDInsight (par exemple, le plug-in IntelliJ) sans avoir le hachage de Mot de passe disponible dans Azure AD DS.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchroniser des utilisateurs Azure Active Directory vers un cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Surveiller les performances du cluster](../hdinsight-key-scenarios-to-monitor.md)
