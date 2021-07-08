---
title: Changements importants à venir sur Azure Security Center
description: Changements à venir sur Azure Security Center dont vous devez peut-être tenir compte et pour lesquels une planification peut être nécessaire
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/13/2021
ms.author: memildin
ms.openlocfilehash: a490a08946a7357af41cce04051ef01765c8fbe5
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062241"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Changements importants à venir sur Azure Security Center

> [!IMPORTANT]
> Les informations de cette page concernent des produits ou fonctionnalités en préversion, qui peuvent être modifiés de façon substantielle avant d’être commercialisés, le cas échéant. Microsoft n’offre aucun engagement ni garantie, formel ou implicite, concernant les informations contenues dans ce document.

Dans cette page, vous allez découvrir les changements planifiés pour Security Center. Elle décrit les changements planifiés du produit qui peuvent avoir un impact sur votre degré de sécurisation ou vos workflows.

Si vous recherchez les notes de publication les plus récentes, vous les trouverez dans [Nouveautés d’Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Changements planifiés

| Modifications planifiées                                                                                                                                                        | Date estimée de la modification |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Juin 2021                 |
| [Améliorations apportées à la recommandation de classification des données SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | T3 2021                   |
| [Activer le contrôle de sécurité Azure Defender à inclure au degré de sécurisation](#enable-azure-defender-security-control-to-be-included-in-secure-score)                       | T3 2021                   |
|                                                                                                                                                                       |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>L’implémentation héritée de la norme ISO 27001 est remplacée par la nouvelle norme ISO 27001:2013

L’implémentation héritée de la norme ISO 27001 sera supprimée du tableau de bord de conformité réglementaire de Security Center. Si vous effectuez le suivi de la conformité ISO 27001 avec Security Center, intégrez la nouvelle norme ISO 27001:2013 pour tous les groupes d’administration ou abonnements appropriés, car la norme ISO 27001 actuelle sera bientôt supprimée du tableau de bord.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Tableau de bord de conformité réglementaire Security Center présentant le message relatif à la suppression de l’implémentation héritée de la norme ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="enhancements-to-sql-data-classification-recommendation"></a>Améliorations apportées à la recommandation de classification des données SQL

**Date estimée de la modification :** T3 2021

La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** du contrôle de sécurité **Appliquer la classification des données** va être remplacée par une nouvelle version mieux alignée sur la stratégie de classification des données de Microsoft. L’ID de la recommandation sera donc également modifié (actuellement, il s’agit de b0df6f56-862d-4730-8597-38c0fd4ebd59).

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>Activer le contrôle de sécurité Azure Defender à inclure au degré de sécurisation

**Date estimée de la modification :** T3 2021

Les recommandations de renforcement de Security Center sont regroupées en contrôles de sécurité. Chaque contrôle est un groupe logique de recommandations de sécurité associées et reflète les surfaces d'attaque vulnérables. La contribution de chaque contrôle de sécurité au degré de sécurisation global est indiquée clairement sur la page de recommandations, ainsi que dans la liste des contrôles dans [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).

Depuis son introduction, le contrôle **Activer Azure Defender** a obtenu un score maximal possible de 0 point. **Avec cette modification, le contrôle contribuera à votre degré de sécurisation**.

Lorsque vous activez Azure Defender, vous étendez les fonctionnalités du mode Gratuit de Security Center à vos charges de travail exécutées dans des clouds privés et dans d’autres clouds publics, pour une gestion unifiée de la sécurité et une protection contre les menaces sur l’ensemble des charges de travail de cloud hybride. Voici quelques-unes des principales fonctionnalités d’Azure Defender : licences intégrées Microsoft Defender pour point de terminaison pour vos serveurs, analyse des vulnérabilités pour les machines virtuelles et les registres de conteneurs, alertes de sécurité basées sur les analyses comportementales avancées et Machine Learning, ainsi que les fonctionnalités de sécurité des conteneurs. Pour obtenir la liste exhaustive, consultez [Azure Security Center gratuit et Azure Defender activé](security-center-pricing.md).

Cette modification aura un impact sur le degré de sécurisation de tous les abonnements qui ne sont pas protégés par Azure Defender. Nous vous suggérons d’activer Azure Defender avant cette modification pour vous assurer qu’il n’y a aucun impact sur le vôtre. 

Pour plus d’informations, consultez [Démarrage rapide : Activer Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Étapes suivantes

Pour tous les changements récents du produit, consultez [Nouveautés d’Azure Security Center](release-notes.md).