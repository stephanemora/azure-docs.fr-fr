---
title: Azure Defender pour le stockage - avantages et fonctionnalités
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour le stockage.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3d14a4a569d59b8bc9cac1334da1486960288412
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003527"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Présentation d’Azure Defender pour le stockage

**Azure Defender pour Stockage** est une couche de sécurité intelligente native Azure qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos comptes de stockage. Il utilise les fonctionnalités avancées de l’IA de sécurité et de [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) pour fournir des recommandations et des alertes de sécurité contextuelles.

Les alertes de sécurité sont déclenchées lorsque des anomalies se produisent dans l’activité. Ces alertes sont intégrées à Azure Security Center et envoyées par e-mail aux administrateurs d’abonnement avec les détails des activités suspectes et des recommandations sur la façon d’examiner et de corriger les menaces.

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|**Azure Defender pour Stockage** est facturé conformément à la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Types de stockage protégés :|[Stockage Blob](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) Gouvernement des États-Unis<br>![Non](./media/icons/no-icon.png) Azure Chine|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Quels sont les avantages d’Azure Defender pour le stockage ?

Azure Defender pour le stockage offre les avantages suivants :

- **Sécurité native Azure** : l’activation en un clic permet à Defender pour le stockage de protéger les données stockées dans les objets blob Azure, les fichiers Azure et les lacs de données. En tant que service natif Azure, Defender pour le stockage offre une sécurité centralisée sur l’ensemble des ressources de données gérées par Azure et est intégré à d’autres services de sécurité Azure comme Azure Sentinel.
- **Suite de détection riche** : la technologie Microsoft Threat Intelligence permet aux détections dans Defender pour le stockage de couvrir les principales menaces de stockage telles que l’accès anonyme, les informations d’identification compromises, l’ingénierie sociale, les abus de privilèges et le contenu malveillant.
- **Réponse à grande échelle** : les outils d’automatisation de Security Center facilitent la prévention et la réponse par rapport aux menaces identifiées. Pour en savoir plus, consultez [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Présentation générale des fonctionnalités d’Azure Defender pour le stockage.":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quels sont les types d’alertes fournis par Azure Defender pour le stockage ?

Les alertes de sécurité sont déclenchées dans les cas suivants :

- **Modèles d’accès suspects** – tels qu’un accès réussi à partir d’un nœud de sortie Tor ou d’une adresse IP considérée comme suspecte par Microsoft Threat Intelligence
- **Activités suspectes** – telles que l’extraction de données anormales ou un changement inhabituel d’autorisations d’accès
- **Téléchargement de contenu malveillant** – tel que des fichiers de logiciels malveillants potentiels (basés sur l’analyse de réputation du code de hachage) ou l’hébergement de contenu d’hameçonnage

Les alertes fournissent des détails sur l’incident qui les a déclenchées, ainsi que des suggestions pour enquêter et contrer les menaces. Les alertes peuvent être exportées vers Azure Sentinel ou toute autre solution SIEM tierce ou tout autre outil externe.

> [!TIP]
> Une bonne pratique consiste à [configurer Azure Defender pour Stockage](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) au niveau de l’abonnement, mais vous pouvez également [le configurer sur des comptes de stockage individuels](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Qu’est-ce que l’analyse de réputation du code de hachage pour les programmes malveillants ?

Pour déterminer si un fichier téléchargé est suspect, Azure Defender pour le stockage utilise l’analyse de réputation de hachage prise en charge par [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Les outils de protection contre les menaces n’analysent pas les fichiers téléchargés, mais ils examinent les journaux de stockage et comparent le code de hachage des fichiers récemment téléchargés avec ceux des virus, chevaux de Troie, logiciels espions et ransomware connus. 

Lorsqu’un fichier est supposé contenir un logiciel malveillant, Security Center affiche une alerte et peut éventuellement envoyer un e-mail au propriétaire du stockage pour approbation afin de supprimer le fichier suspect. Pour configurer cette suppression automatique des fichiers signalée par l’analyse du code de hachage, déployez une [automatisation du flux de travail pour déclencher des alertes « Logiciel malveillant potentiel chargé sur un compte de stockage »](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Pour activer les fonctionnalités de protection contre les menaces de Security Center, vous devez activer Azure Defender sur l’abonnement contenant les charges de travail applicables.
>
> Vous pouvez activer **Azure Defender pour le stockage** au niveau de l’abonnement ou de la ressource.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Déclencher une alerte de test liée à Azure Defender pour le stockage

Pour tester les alertes de sécurité depuis Azure Defender pour le stockage dans votre environnement, générez l’alerte « Accès à partir d’un nœud de sortie Tor dans un compte de stockage » en effectuant les actions suivantes :

1. Ouvrez un compte de stockage bénéficiant de la protection Azure Defender pour le stockage activée.
1. Dans la barre latérale, sélectionnez « Conteneurs », puis ouvrez un conteneur existant ou créez-en un nouveau.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Ouverture d’un conteneur d’objets blob à partir d’un compte de stockage Azure." lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Chargez un fichier dans ce conteneur.

    > [!CAUTION]
    > Ne chargez pas de fichier contenant des données sensibles.

1. Utilisez le menu contextuel du fichier chargé pour sélectionner « Générer une signature d’accès partagé ».

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="L’option Générer une signature d’accès partagé d’un fichier dans un conteneur d’objets blob.":::

1. Laissez les options par défaut et sélectionnez **Générer une URL et un jeton SAS**.

1. Copiez l’URL SAS générée.

1. Sur votre machine locale, ouvrez le navigateur Tor.

    > [!TIP]
    > Vous pouvez télécharger Tor depuis le site du projet Tor [https://www.torproject.org/download/](https://www.torproject.org/download/).

1. Dans le navigateur Tor, accédez à l’URL SAS.

1. Téléchargez le fichier que vous avez chargé à l’étape 3.

    En moins de deux heures, vous obtenez l’alerte de sécurité suivante à partir de Security Center :

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Alerte de sécurité concernant l’accès à partir d’un nœud de sortie Tor.":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à propos d’Azure Defender pour le stockage.

Pour des informations connexes, consultez les articles suivants : 

- Qu’une alerte soit générée par Security Center ou reçue par Security Center à partir d’un autre produit de sécurité, vous pouvez l’exporter. Pour exporter vos alertes vers Azure Sentinel (ou un système SIEM tiers) ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers SIEM](continuous-export.md).
- [Comment activer Advanced Defender pour le stockage](../storage/common/azure-defender-storage-configure.md)
- [Liste des alertes Azure Defender pour le stockage](alerts-reference.md#alerts-azurestorage)
- [Fonctionnalités de renseignement sur les menaces de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)