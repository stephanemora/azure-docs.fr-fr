---
title: Analyser les images de registre avec Azure Defender
description: Découvrez comment analyser les images de vos registres de conteneurs Azure avec Azure Defender pour les registres de conteneurs.
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: d00e23ffa7e3bd2fc1084ba6253274d14031d624
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553016"
---
# <a name="scan-registry-images-with-azure-defender"></a>Analyser les images de registre avec Azure Defender

Pour analyser les vulnérabilités des images de vos registres de conteneurs Azure, vous pouvez intégrer l’une des solutions disponibles de la Place de Marché Azure. Si vous souhaitez utiliser Azure Security Center, vous pouvez éventuellement activer **Azure Defender pour les registres de conteneurs** au niveau de l’abonnement. 

* Apprenez-en davantage sur [Azure Defender pour les registres de conteneurs](../security-center/defender-for-container-registries-introduction.md)
* Apprenez-en davantage sur la [sécurité des conteneurs dans Azure Security Center](../security-center/container-security.md)

## <a name="registry-operations-by-azure-defender"></a>Opérations de registre d’Azure Defender

Azure Defender analyse les images envoyées (push) ou importées dans un registre ou toutes les images tirées (pull) au cours des 30 derniers jours. Si des vulnérabilités sont détectées, les [corrections recommandées](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings) s’affichent dans Azure Security Center.

 Après avoir effectué les étapes recommandées pour corriger le problème de sécurité, remplacez l’image dans votre registre. Azure Defender analyse de nouveau l’image pour vérifier que les vulnérabilités sont corrigées. 

Pour obtenir des informations détaillées, consultez [Utiliser Azure Defender pour des registres de conteneurs](../security-center/defender-for-container-registries-usage.md).

> [!TIP]
> Azure Defender s’authentifie auprès du registre pour tirer les images en vue d’une analyse des vulnérabilités. Si les [journaux de ressources](monitor-service-reference.md#resource-logs) sont collectés pour votre registre, vous verrez les événements de connexion au registre et les événements de tirage d’image générés par Azure Defender. Ces événements sont associés à un ID alphanumérique comme `b21cb118-5a59-4628-bab0-3c3f0e434cg6`.

## <a name="scanning-a-network-restricted-registry"></a>Analyse d’un registre avec accès réseau restreint

Azure Defender peut analyser des images dans un registre de conteneurs accessible publiquement ou un registre protégé par des règles d’accès réseau. Si des règles de réseau sont configurées (si vous désactivez l’accès public au registre, configurez des règles d’accès IP ou créez des points de terminaison privés), veillez à activer le paramètre réseau pour [**autoriser les services Microsoft approuvés**](allow-access-trusted-services.md) à accéder au registre. Ce paramètre est activé par défaut dans un nouveau registre de conteneurs.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur l’accès au registre pour les [services approuvés](allow-access-trusted-services.md).
* Pour restreindre l’accès à un registre à l’aide d’un point de terminaison privé dans un réseau virtuel, consultez [Configurer Azure Private Link pour un registre de conteneurs Azure](container-registry-private-link.md).
* Pour configurer des règles de pare-feu pour le registre, consultez [Configurer des règles de réseau IP public](container-registry-access-selected-networks.md).
