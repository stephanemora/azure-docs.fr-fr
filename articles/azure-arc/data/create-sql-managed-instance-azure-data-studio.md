---
title: Créer un service Azure SQL Managed Instance à l’aide d’Azure Data Studio
description: Créer un service Azure SQL Managed Instance à l’aide d’Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 15d4ba669f736fd1322c137a16d8b79f40cea6d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562966"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Créer une instance gérée SQL – Azure Arc utilisant Azure Data Studio

Ce document vous guide au fil des étapes d’installation d’un service Azure SQL Managed Instance – Azure Arc en utilisant Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Créer un service Azure SQL Managed Instance sur Azure Arc

- Lancer Azure Data Studio
- Sous l’onglet Connexions, cliquez sur les trois points situés en haut à gauche, puis choisissez « Nouveau déploiement »
- Dans les options de déploiement, sélectionnez **Azure SQL Managed Instance – Azure Arc**. 
  > [!NOTE]
  > Vous pouvez ici être invité à installer l’interface CLI qui convient, si elle n’est pas installée.
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

- Affichez tous les services Azure SQL Managed Instance approvisionnés en utilisant les commandes suivantes :

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
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
