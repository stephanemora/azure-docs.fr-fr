---
title: Sécuriser votre cluster dans Azure Data Explorer
description: Cet article explique comment sécuriser votre cluster dans Azure Data Explorer dans le Portail Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172590"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Sécuriser votre cluster dans Azure Data Explorer

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il fournit un chiffrement de volume pour le système d’exploitation et les disques de données de vos machines virtuelles de cluster. Il est également intégré à [Azure Key Vault](/azure/key-vault/), ce qui vous permet de contrôler et de gérer les secrets et les clés de chiffrement de disque, et de garantir que toutes les données figurant sur les disques des machines virtuelles sont chiffrées au repos pendant qu’elles résident dans le stockage Azure. 

Les paramètres de sécurité de votre cluster vous permettent d’activer le chiffrement de disque sur votre cluster.
  
## <a name="enable-encryption-at-rest"></a>Activer le chiffrement des données au repos
  
Activer le [chiffrement des données au repos](/azure/security/fundamentals/encryption-atrest) sur votre cluster offre une protection des données pour les données stockées (au repos). 

1. Dans le portail Azure, accédez à votre ressource de cluster Azure Data Explorer. Sous l’en-tête **Paramètres**, sélectionnez **Sécurité**. 

    ![Activer le chiffrement au repos](media/manage-cluster-security/security-encryption-at-rest.png)

1. Dans la fenêtre **Sécurité**, sélectionnez **Activé** pour le paramètre sécurité du **Chiffrement de disque**. 

1. Sélectionnez **Enregistrer**.
 
> [!NOTE]
> Sélectionnez **Désactivé** pour désactiver le chiffrement une fois qu’il a été activé.

## <a name="next-steps"></a>Étapes suivantes

[Vérifier l’intégrité des clusters](/azure/data-explorer/check-cluster-health)
