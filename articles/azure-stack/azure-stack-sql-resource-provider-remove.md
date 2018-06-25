---
title: Suppression du fournisseur de ressources SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment vous pouvez supprimer le fournisseur de ressources SQL de votre déploiement Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294778"
---
# <a name="removing-the-mysql-resource-provider"></a>Suppression du fournisseur de ressources MySQL  
Avant de supprimer le fournisseur de ressources SQL, il est essentiel de supprimer toutes les dépendances.

## <a name="remove-the-mysql-resource-provider"></a>Supprimer le fournisseur de ressources MySQL 

1. Vérifiez que vous avez supprimé toutes les dépendances du fournisseur de ressources SQL existantes.

  > [!NOTE]
  > La désinstallation du fournisseur de ressources SQL est effectuée même si des ressources dépendantes utilisent le fournisseur de ressources. 
  
2. Vérifiez que vous disposez du package de déploiement d’origine que vous avez téléchargé pour cette version de l’adaptateur du fournisseur de ressources SQL.
3. Réexécutez le script de déploiement à l’aide des paramètres suivants :
    - Paramètre -Uninstall
    - Adresse IP ou nom DNS du point de terminaison privilégié.
    - Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié.
    - Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack.

## <a name="next-steps"></a>Étapes suivantes
[Offrir App Services en tant que PaaS](azure-stack-app-service-overview.md)
