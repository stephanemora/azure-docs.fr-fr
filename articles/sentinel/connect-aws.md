---
title: Connecter des données Symantec AWS à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Symantec AWS à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674112"
---
# <a name="connect-azure-sentinel-to-aws"></a>Connecter Azure Sentinel à AWS

Utilisez le connecteur AWS pour diffuser tous vos événements AWS CloudTrail vers Azure Sentinel. Ce processus de connexion délègue l’accès pour Azure Sentinel à vos journaux de ressources AWS, créant une relation d’approbation entre AWS CloudTrail et Azure Sentinel. Pour ce faire, un rôle est créé sur AWS, qui autorise Azure Sentinel à accéder à vos journaux d’activité AWS.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de l’autorisation d’écriture sur l’espace de travail Azure Sentinel.

## <a name="connect-aws"></a>Connecter AWS 
 
1.  Dans votre console Amazon Web Services, sous **Security, Identity & Compliance** (Sécurité, identité & conformité), cliquez sur **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Choisissez **Roles** et cliquez sur **Create role** (Créer un rôle).

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Choisissez **Another AWS account** (Autre compte AWS). Dans le champ **Account ID** (ID de compte), entrez l’**ID de compte Microsoft** (**123412341234**) que vous trouverez dans la page du connecteur AWS dans le portail Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Assurez-vous que l’option **Require External ID** (Exiger un ID externe) est sélectionnée, puis entrez l’ID externe (ID d’espace de travail) que vous trouverez dans la page du connecteur AWS dans le portail Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  Sous **Attach permissions policy** (Attacher une stratégie d’autorisation), sélectionnez **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Entrez une étiquette (facultatif).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Ensuite, entrez un **Role name** (Nom de rôle) et cliquez sur le bouton **Create role**.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  Dans la liste Roles, choisissez le rôle que vous avez créé.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Copiez le **Role ARN** (numéro ARN du rôle) et collez-le dans le champ **Role to add** (Rôle à ajouter) dans le portail Azure Sentinel.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Pour utiliser le schéma pertinent dans Log Analytics pour les événements AWS, recherchez **AWSCloudTrail**.



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter AWS CloudTrail à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

