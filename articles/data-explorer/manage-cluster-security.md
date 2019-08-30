---
title: Sécuriser votre cluster dans Azure Data Explorer
description: Cet article explique comment sécuriser votre cluster dans Azure Data Explorer dans le Portail Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: ad08bb19f96aadad42e973eebb8adce6875e07b1
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876594"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Sécuriser votre cluster dans Azure Data Explorer

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il fournit un chiffrement de volume pour le système d’exploitation et les disques de données de vos machines virtuelles de cluster. Il est également intégré à [Azure Key Vault](/azure/key-vault/) pour vous aider à contrôler et à gérer les secrets et les clés de chiffrement de disque, et garantit que toutes les données des disques des machines virtuelles sont chiffrées au repos pendant qu’elles résident dans le Stockage Azure. Les paramètres de sécurité de votre cluster vous permettent d’activer le chiffrement de disque sur votre cluster.
  
## <a name="enable-encryption-at-rest"></a>Activer le chiffrement des données au repos
  
Activer le [chiffrement des données au repos](/azure/security/fundamentals/encryption-atrest) sur votre cluster offre une protection des données pour les données stockées (au repos). 

1. Dans le portail Azure, accédez à votre ressource de cluster Azure Data Explorer. Sous l’en-tête **Paramètres**, sélectionnez **Sécurité**. 

    ![Activer le chiffrement au repos](media/manage-cluster-security/security-encryption-at-rest.png)

1. Dans la fenêtre **Sécurité**, sélectionnez **Activé** pour le paramètre sécurité du **Chiffrement de disque**. 

1. Sélectionnez **Enregistrer**.
 
> [!NOTE]
> Une fois le chiffrement activé, il est toujours possible de le désactiver.

## <a name="next-steps"></a>Étapes suivantes

[Vérifier l’intégrité des clusters](/azure/data-explorer/check-cluster-health)
