---
title: Sécuriser votre espace de travail Synapse (préversion)
description: Cet article vous apprend à utiliser des rôles et un contrôle d’accès pour contrôler les activités et l’accès aux données dans l’espace de travail Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 35fb8adaa5f7c0fff1c6d967f0136736b8071ce4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260153"
---
# <a name="secure-your-synapse-workspace-preview"></a>Sécuriser votre espace de travail Synapse (préversion)

Cet article vous apprend à utiliser des rôles et un contrôle d’accès pour contrôler les activités et l’accès aux données. Ces instructions simplifient le contrôle d’accès dans Azure Synapse Analytics. Vous devez uniquement ajouter et supprimer des utilisateurs dans l’un des trois groupes de sécurité.

## <a name="overview"></a>Vue d’ensemble

Pour sécuriser un espace de travail Synapse (préversion), vous devez suivre un modèle de configuration des éléments suivants :

- Rôles Azure (tels que les rôles intégrés Propriétaire, Contributeur, etc.)
- Rôles Synapse (non basés sur des rôles Azure). Il en existe trois :
  - Administrateur d’espace de travail Synapse
  - Administrateur SQL Synapse
  - Administrateur Apache Spark pour Azure Synapse Analytics
- Contrôle d’accès pour les données dans Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Contrôle d’accès pour les bases de données SQL et Spark Synapse.

## <a name="steps-to-secure-a-synapse-workspace"></a>Procédure de sécurisation d’un espace de travail Synapse

Ce document utilise des noms standard pour simplifier les instructions. Remplacez-les par les noms de votre choix.

|Paramètre | Valeur d'exemple | Description |
| :------ | :-------------- | :---------- |
| **Espace de travail Synapse** | WS1 |  Nom de l’espace de travail Synapse. |
| **Compte ADLSGEN2** | STG1 | Compte ADLS à utiliser avec votre espace de travail. |
| **Conteneur** | CNT1 | Conteneur dans STG1 que l’espace de travail utilisera par défaut. |
| **Locataire Active directory** | contoso | Nom du locataire Azure Active actif.|
||||

## <a name="step-1-set-up-security-groups"></a>ÉTAPE 1 : Configurer des groupes de sécurité

Créez et remplissez trois groupes de sécurité pour votre espace de travail :

- **WS1\_WSAdmins** : pour les utilisateurs qui ont besoin d’un contrôle total sur l’espace de travail.
- **WS1\_SparkAdmins** : pour les utilisateurs qui ont besoin d’un contrôle total sur les aspects Spark de l’espace de travail.
- **WS1\_SQLAdmins** : pour les utilisateurs qui ont besoin d’un contrôle total sur les aspects SQL de l’espace de travail.
- Ajoutez **WS1\_WSAdmins** à **WS1\_SQLAdmins**.
- Ajoutez **WS1\_WSAdmins** à **WS1\_SparkAdmins**.

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>ÉTAPE 2 : Préparer votre compte Azure Data Lake Storage Gen2.

Identifiez les informations ci-après sur votre stockage :

- Compte ADLSGEN2 à utiliser pour votre espace de travail. Ce document l’appelle STG1.  STG1 est considéré comme le compte de stockage « principal » pour votre espace de travail.
- Conteneur à l’intérieur de WS1 que votre espace de travail Synapse utilisera par défaut. Ce document l’appelle CNT1.  Ce conteneur est utilisé aux fins suivantes :
  - Stockage des fichiers de données de stockage pour les tables Spark.
  - Journaux d’exécution pour les travaux Spark.

- Sur le portail Azure, affectez les groupes de sécurité aux rôles suivants sur CNT1 :

  - Affectez **WS1\_WSAdmins** au rôle **Contributeur de données du blob de stockage**.
  - Affectez **WS1\_SparkAdmins** au rôle **Contributeur de données du blob de stockage**.
  - Affectez **WS1\_SQLAdmins** au rôle **Contributeur de données du blob de stockage**.

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>ÉTAPE 3 : Créer et configurer votre espace de travail Synapse

Dans le portail Azure, créez un espace de travail Synapse :

- Nommez l’espace de travail WS1.
- Choisissez STG1 pour le compte de stockage.
- Choisissez CNT1 pour le conteneur utilisé comme « système de fichiers ».
- Ouvrez WS1 dans Synapse Studio.
- Sélectionnez **Gérer** > **Contrôle d’accès** et affectez les groupes de sécurité aux rôles Synapse suivants.
  - Affectez **WS1\_WSAdmins** aux administrateurs d’espace de travail Synapse.
  - Affectez **WS1\_SparkAdmins** aux administrateurs Spark Synapse.
  - Affectez **WS1\_SQLAdmins** aux administrateurs SQL Synapse.

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>ÉTAPE 4 : Configurer Data Lake Storage Gen2 pour une utilisation par l’espace de travail Synapse

L’espace de travail Synapse a besoin d’accéder à STG1 et CNT1 pour pouvoir exécuter des pipelines et effectuer des tâches système.

- Ouvrez le portail Azure
- Localisez STG1.
- Accédez à CNT1.
- Assurez-vous que le MSI (Managed Service Identity) pour WS1 est attribué au rôle **Contributeur de données du blob de stockage** sur CNT1
  - Si vous ne le voyez pas affecté, affectez-le.
  - Le MSI porte le même nom que l’espace de travail. Dans ce cas, il s’agit de &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>ÉTAPE 5 : Configurer l’accès administrateur pour les pools SQL

- Ouvrez le portail Azure
- Accédez à WS1.
- Sous **Paramètres**, sélectionnez **Administrateur SQL Active Directory**.
- Sélectionnez **Définir l’administrateur**, puis choisissez WS1\_SQLAdmins.

## <a name="step-6-maintain-access-control"></a>ÉTAPE 6 : Gérer le contrôle d’accès

La configuration est terminée.

Maintenant, pour gérer l’accès des utilisateurs, vous pouvez ajouter et supprimer des utilisateurs dans les trois groupes de sécurité.

Vous pouvez affecter manuellement des utilisateurs à des rôles Synapse, mais cela n’a pas pour effet de configurer les éléments de manière cohérente. Au lieu de cela, limitez-vous à ajouter ou supprimer des utilisateurs dans les groupes de sécurité.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>ÉTAPE 7 : Vérifier l’accès pour les utilisateurs dans les rôles

Les utilisateurs de chaque rôle doivent suivre les étapes suivantes :

| Number | Étape | Administrateurs d'espace de travail | Administrateurs Spark | Administrateurs SQL |
| --- | --- | --- | --- | --- |
| 1 | Charger un fichier Parquet dans CNT1 | YES | YES | YES |
| 2 | Lire le fichier parquet à l’aide de SQL à la demande | YES | Non | YES |
| 3 | Créer un pool Spark | OUI [1] | OUI [1] | Non  |
| 4 | Lire le fichier Parquet avec un bloc-notes | YES | YES | Non |
| 5 | Créer un pipeline à partir du bloc-notes et déclencher l’exécution immédiate du pipeline | YES | Non | Non |
| 6 | Créer un pool SQL et exécuter un script SQL tel que &quot;SELECT 1&quot; | OUI [1] | Non | OUI [1] |

> [!NOTE]
> [1] Pour créer des pools SQL ou Spark, l’utilisateur doit avoir au moins le rôle Contributeur sur l’espace de travail Synapse.
>
 
>[!TIP]
> - Certaines étapes ne seront délibérément pas autorisées en fonction du rôle.
> - N’oubliez pas que certaines tâches peuvent échouer si la sécurité n’a pas été entièrement configurée. Ces tâches sont indiquées dans le tableau.

## <a name="step-8-network-security"></a>ÉTAPE 8 : Sécurité réseau

Pour configurer le pare-feu de l’espace de travail, le réseau virtuel et la [Liaison privée](../../azure-sql/database/private-endpoint-overview.md).

## <a name="step-9-completion"></a>ÉTAPE 9 : Completion

Votre espace de travail est maintenant entièrement configuré et sécurisé.

## <a name="how-roles-interact-with-synapse-studio"></a>Interaction des rôles avec Synapse Studio

Synapse Studio se comporte différemment en fonction des rôles d’utilisateur. Certains éléments peuvent être masqués ou désactivés si un utilisateur n’est pas affecté à des rôles donnant l’accès approprié. Le tableau suivant résume l’effet sur Synapse Studio.

| Tâche | Administrateurs d'espace de travail | Administrateurs Spark | Administrateurs SQL |
| --- | --- | --- | --- |
| Ouvrir Synapse Studio | YES | YES | YES |
| Afficher le hub d’accueil | YES | YES | YES |
| Afficher le hub de données | YES | YES | YES |
| Hub de données / Afficher les comptes et conteneurs ADLS Gen2 liés | OUI [1] | OUI [1] | OUI [1] |
| Hub de données / Voir les bases de données | YES | YES | YES |
| Hub de données / Voir les objets dans les bases de données | YES | YES | YES |
| Hub de données / Accéder aux données dans les bases de données de pool SQL | YES   | Non   | YES   |
| Hub de données / Accéder aux données dans les bases de données SQL à la demande | OUI [2]  | Non  | OUI [2]  |
| Hub de données / Accéder aux données dans les bases de données Spark | OUI [2] | OUI [2] | OUI [2] |
| Utiliser le hub de développement | YES | YES | YES |
| Hub de développement / Créer des scripts SQL | YES | Non | YES |
| Hub de développement / Créer des définitions de travaux Spark | YES | YES | Non |
| Hub de développement / Créer des blocs-notes | YES | YES | Non |
| Hub de développement / Créer des flux de données | YES | Non | Non |
| Utiliser le hub d’orchestration | YES | YES | YES |
| Hub d’orchestration / Utiliser des pipelines | YES | Non | Non |
| Utiliser le hub de gestion | YES | YES | YES |
| Hub de gestion / Pools SQL | YES | Non | YES |
| Hub de gestion / Pools Spark | YES | YES | Non |
| Hub de gestion / Déclencheurs | YES | Non | Non |
| Hub de gestion / Services liés | YES | YES | YES |
| Hub de gestion / Contrôle d’accès (affecter des utilisateurs aux rôles de l’espace de travail Synapse) | YES | Non | Non |
| Hub de gestion / Runtimes d’intégration | YES | YES | YES |
| Utiliser le hub Monitor | YES | YES | YES |
| Hub Monitor / Orchestration / Exécutions de pipeline  | YES | Non | Non |
| Hub Monitor / Orchestration / Exécutions de déclencheur  | YES | Non | Non |
| Hub Monitor / Orchestration / Runtimes d’intégration  | YES | YES | YES |
| Hub Monitor / Activités / Applications Spark | YES | YES | Non  |
| Hub Monitor / Activités / Requêtes SQL | YES | Non | YES |
| Hub Monitor / Activités / Pools Spark | YES | YES | Non  |
| Hub Monitor / Déclencheurs | YES | Non | Non |
| Hub de gestion / Services liés | YES | YES | YES |
| Hub de gestion / Contrôle d’accès (affecter des utilisateurs aux rôles de l’espace de travail Synapse) | YES | Non | Non |
| Hub de gestion / Runtimes d’intégration | YES | YES | YES |


> [!NOTE]
> [1] l’accès aux données dans les conteneurs dépend du contrôle d’accès dans ADLS Gen2. </br>
> [2] Les tables SQL OD et Spark stockent leurs données dans ADLS Gen2, et l’accès nécessite de disposer des autorisations appropriées sur ADLS Gen2.

## <a name="next-steps"></a>Étapes suivantes

Créer un [espace de travail Synapse](../quickstart-create-workspace.md)
