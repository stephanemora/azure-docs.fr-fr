---
title: Azure Defender pour le stockage - avantages et fonctionnalités
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour le stockage.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 31d1bde1555f98164ccba32d4615ba51837c5ef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930759"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Présentation d’Azure Defender pour le stockage

**Azure Defender pour Stockage** détecte les activités potentiellement dangereuses sur vos comptes de stockage Azure. Vos données peuvent être protégées, qu’elles soient stockées en tant que conteneurs blob, de partages de fichiers ou de lacs de données.

Cette couche de protection vous permet de traiter efficacement les menaces *sans* pour autant être un expert en sécurité, et vous aide à gérer des systèmes de supervision de la sécurité.


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour le stockage** est facturé comme indiqué sur [la page de tarification](security-center-pricing.md)|
|Types de stockage protégés|[Stockage Blob](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) Gouvernement des États-Unis<br>![Non](./media/icons/no-icon.png) Chine Gov, autres Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quels sont les types d’alertes fournis par Azure Defender pour le stockage ?

Les alertes de sécurité sont déclenchées dans les cas suivants :

- **Activité suspecte** : par exemple, le compte de stockage a fait l’objet d’un accès à partir d’une adresse IP connue comme étant un nœud de sortie actif de Tor
- **Comportement anormal** : par exemple, inclut les modifications apportées au modèle d’accès à un compte de stockage
- **Programmes malveillants potentiels chargés** : analyse de réputation du code de hachage qui indique qu’un fichier chargé contient des programmes malveillants

Les alertes fournissent des détails sur l’incident qui les a déclenchées, ainsi que des suggestions pour enquêter et contrer les menaces.

> [!TIP]
> Vous pouvez simuler des alertes de stockage en suivant les instructions données dans [ce billet de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Qu’est-ce que l’analyse de réputation du code de hachage pour les programmes malveillants ?

Pour déterminer si un fichier téléchargé est suspect, Azure Defender pour le stockage utilise l’analyse de réputation de hachage prise en charge par [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Les outils de protection contre les menaces n’analysent pas les fichiers téléchargés, mais ils examinent les journaux de stockage et comparent le code de hachage des fichiers récemment téléchargés avec ceux des virus, chevaux de Troie, logiciels espions et ransomware connus. 

Lorsqu’un fichier est supposé contenir un logiciel malveillant, Security Center affiche une alerte et peut éventuellement envoyer un e-mail au propriétaire du stockage pour approbation afin de supprimer le fichier suspect. Pour configurer cette suppression automatique des fichiers signalée par l’analyse du code de hachage, déployez une [automatisation du flux de travail pour déclencher des alertes « Logiciel malveillant potentiel chargé sur un compte de stockage »](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez activer Azure Defender sur l’abonnement contenant les charges de travail applicables.
>
> Vous pouvez activer **Azure Defender pour le stockage** au niveau de l’abonnement ou de la ressource.



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à propos d’Azure Defender pour le stockage.

Pour des informations connexes, consultez les articles suivants : 

- Qu’une alerte soit générée par Security Center ou reçue par Security Center à partir d’un autre produit de sécurité, vous pouvez l’exporter. Pour exporter vos alertes vers Azure Sentinel (ou un système SIEM tiers) ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers SIEM](continuous-export.md).
- [Comment activer Advanced Defender pour le stockage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Liste des alertes Azure Defender pour le stockage](alerts-reference.md#alerts-azurestorage)
- [Fonctionnalités de renseignement sur les menaces de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)