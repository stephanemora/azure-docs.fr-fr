---
title: Créer un service Azure SQL Managed Instance à l’aide d’Azure Data Studio
description: Créer un service Azure SQL Managed Instance à l’aide d’Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3223ec1a9538228c1b3d9f2cdcb8ea1c051e0e10
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110495961"
---
# <a name="create-sql-managed-iinstance---azure-arc-using-azure-data-studio"></a>Créer un service SQL Managed Instance – Azure Arc en utilisant Azure Data Studio

Ce document vous guide au fil des étapes d’installation d’un service Azure SQL Managed Instance – Azure Arc en utilisant Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Se connecter au contrôleur de données Azure Arc

Avant de pouvoir créer une instance, connectez-vous au contrôleur de données Azure Arc si vous ne l’avez pas encore fait.

```console
azdata login
```

Vous êtes ensuite invité à entrer l’espace de noms dans lequel le contrôleur de données est créé, le nom d’utilisateur et le mot de passe pour vous connecter au contrôleur.  

> Si vous devez valider l’espace de noms, vous pouvez exécuter ```kubectl get pods -A``` pour obtenir une liste de tous les espaces de noms sur le cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Créer un service Azure SQL Managed Instance sur Azure Arc

- Lancer Azure Data Studio
- Sous l’onglet Connexions, cliquez sur les trois points situés en haut à gauche, puis choisissez « Nouveau déploiement »
- Dans les options de déploiement, sélectionnez **Azure SQL Managed Instance – Azure Arc**. 
  > [!NOTE]
  > Vous pouvez être invité à installer l’outil [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ici s’il n’est pas encore installé.
- Acceptez la déclaration de confidentialité et les termes du contrat de licence, puis cliquez dans le bas sur **Sélectionner**



- Dans le panneau Déployer Azure SQL Managed Instance – Azure Arc, entrez les informations suivantes :
  - Entrez un nom pour l’instance SQL Server.
  - Entrez et confirmer un mot de passe pour l’instance SQL Server.
  - Sélectionnez la classe de stockage appropriée pour les données.
  - Sélectionnez la classe de stockage appropriée pour les journaux.

- Cliquez sur le bouton **Déployer**.

- Cette action doit lancer la création du service Azure SQL Managed Instance – Azure Arc sur le contrôleur de données.

- Votre création ne devrait prendre que quelques minutes.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Connecter un service Azure SQL Managed Instance – Azure Arc à partir d’Azure Data Studio

- Connectez-vous au contrôleur de données Azure Arc en spécifiant l’espace de noms, le nom d’utilisateur et le mot de passe pour le contrôleur de données : 
```console
azdata login
```

- Affichez tous les services Azure SQL Managed Instance approvisionnés en utilisant les commandes suivantes :

```console
azdata arc sql mi list
```

La sortie doit ressembler à ceci ; copiez l’élément ServerEndpoint (y compris le numéro de port) à partir d’ici.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Dans Azure Data Studio, sous l’onglet **Connexions**, cliquez sur **Nouvelle connexion** dans la vue **Serveurs**.
- Dans le panneau **Connexion**, collez l’élément ServerEndpoint dans la zone de texte Serveur.
- Sélectionnez **Connexion SQL** comme type d’authentification.
- Entrez *sa* comme nom d’utilisateur.
- Entrez le mot de passe du compte `sa`.
- Si vous le souhaitez, entrez le nom de la base de données spécifique à laquelle se connecter.
- Si vous le souhaitez, sélectionnez Ajouter un nouveau groupe de serveurs, selon vos besoins.
- Sélectionnez **Se connecter** pour vous connecter au service Azure SQL Managed Instance – Azure Arc.




## <a name="next-steps"></a>Étapes suivantes

Essayez à présent de [superviser votre instance SQL](monitor-grafana-kibana.md)
