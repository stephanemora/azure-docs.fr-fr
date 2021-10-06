---
title: Normalisation et modèle ASIM (Azure Sentinel Information Model) | Microsoft Docs
description: Cet article explique comment Azure Sentinel normalise les données de nombreuses sources différentes à l’aide d’Azure Sentinel Information Model (ASIM)
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: e90171faf59daf70bde2150036fdf0a757e53979
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621392"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>Normalisation et modèle ASIM (Azure Sentinel Information Model)(préversion publique)

Azure Sentinel ingère les données de nombreuses sources. Pour travailler avec différents types de données et de tableaux, vous devez comprendre chacun d’eux, et écrire et utiliser des ensembles de données uniques pour les règles d’analytique, les classeurs et les requêtes de chasse pour chaque type ou schéma.


Parfois, vous aurez besoin de règles, de classeurs et de requêtes distincts, même lorsque les types de données partagent des éléments communs, comme les périphériques de pare-feu. La corrélation entre les différents types de données pendant une investigation et la chasse peut également être difficile.

Cet article fournit une vue d’ensemble du modèle ASIM (Azure Sentinel Information Model), qui fournit une solution pour relever les défis liés à la gestion de plusieurs types de données.

> [!TIP]
> Regardez également le [webinaire ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) ou passez en revue les [diapositives du webinaire](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG). Pour plus d’informations, consultez [Étapes suivantes](#next-steps).
>

> [!IMPORTANT]
> ASIM n’est actuellement disponible qu’en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="common-asim-usage"></a>Utilisation courante d’ASIM

Le modèle ASIM (Azure Sentinel Information Model) offre une expérience transparente pour gérer diverses sources dans des affichages uniformes et normalisés, en fournissant les fonctionnalités suivantes :

- **Détection de source croisée**. Les règles d’analytique normalisées fonctionnent dans les sources, localement et dans le Cloud, et elles détectent les attaques telles que la force brute ou le déplacement impossible entre systèmes, notamment Okta, AWS et Azure.

- **Contenu indépendant de la source**. La couverture du contenu intégré et personnalisé à l’aide d’ASIM s’étend automatiquement à toute source qui prend en charge ASIM, même si la source a été ajoutée après la création du contenu. Par exemple, l’analytique des événements de processus prend en charge toute source qu’un client peut utiliser pour apporter les données, comme Microsoft Defender for Endpoint, Windows Events et Sysmon.

- **Prise en charge de vos sources personnalisées** dans l’analytique intégrée

- **Simplicité d’utilisation**. Une fois qu’un analyste a appris à utiliser ASIM, l’écriture des requêtes est beaucoup plus simple car les noms des champs sont toujours identiques.

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM et métadonnées des événements de sécurité Open source

Azure Sentinel Information Model est aligné sur le modèle CIM de [métadonnées des événements de sécurité Open source (OSSEM)](https://ossemproject.com/intro.html), ce qui permet d’établir une corrélation entre les entités prévisibles et les tables normalisées.

OSSEM est un projet de la communauté qui se concentre principalement sur la documentation et la normalisation des journaux d’événements de sécurité provenant de diverses sources de données et de divers systèmes d’exploitation. Ce projet fournit également un modèle CIM (Common Information Model) qui peut être utilisé par les ingénieurs de données lors des procédures de normalisation des données pour permettre aux analystes de sécurité d’interroger et d’analyser les données dans diverses sources de données.

Pour plus d’informations, consultez la [documentation de référence OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).

## <a name="asim-components"></a>Composants ASIM

L’illustration suivante montre comment les données non normalisées peuvent être traduites en contenu normalisé et utilisées dans Azure Sentinel. Par exemple, vous pouvez commencer avec une table personnalisée, spécifique à un produit et non normalisée, et utiliser un analyseur et un schéma de normalisation pour convertir cette table en données normalisées. Utilisez vos données normalisées à la fois dans les analyses, les règles, les classeurs et les requêtes Microsoft et personnalisées.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Utilisation et distribution des données non normalisées pour la conversion de données normalisée dans Azure Sentinel":::

Le modèle d’informations Azure Sentinel comprend les composants suivants :

|Composant  |Description  |
|---------|---------|
|**Schémas normalisés**     |   Couvre les ensembles standard de types d’événements prévisibles que vous pouvez utiliser lors de la création de fonctionnalités unifiées. <br><br>Chaque schéma définit les champs qui représentent un événement, une convention d’affectation de noms de colonne normalisée et un format standard pour les valeurs de champ. <br><br> ASIM définit actuellement les schémas suivants :<br> - [Session réseau](normalization-schema.md)<br> - [Activité DNS](dns-normalization-schema.md)<br> - [Événement Processus](process-events-normalization-schema.md)<br> - [Événement d’authentification](authentication-normalization-schema.md)<br> - [Événement du Registre](registry-event-normalization-schema.md)<br> - [Activité de fichier](file-event-normalization-schema.md)  <br><br>Pour plus d’informations, consultez [Schémas du modèle Azure Sentinel Information Model](normalization-about-schemas.md).  |
|**Analyseurs**     |  Mappez les données existantes aux schémas normalisés à l’aide des [fonctions KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions). <br><br>Déployez les analyseurs de normalisation développés par Microsoft à partir du [dossier des analyseurs Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers). Les analyseurs normalisés se trouvent dans des sous-dossiers dont le nom commence par **ASim***.  <br><br>Pour plus d’informations, consultez [Analyseurs du modèle Azure Sentinel Information Model](normalization-about-parsers.md).     |
|**Contenu pour chaque schéma normalisé**     |    Comprend des règles d’analytique, des classeurs, des requêtes de chasse et bien plus encore. Le contenu de chaque schéma normalisé fonctionne sur toutes les données normalisées sans qu’il soit nécessaire de créer du contenu propre à la source. <br><br>Pour plus d’informations, consultez [Contenu du modèle Azure Sentinel Information Model](normalization-content.md).   |
| | |

### <a name="asim-terminology"></a>Terminologie ASIM

Le modèle ASIM (Azure Sentinel Information Model) utilise les termes suivants :

|Terme  |Description  |
|---------|---------|
|**Périphérique de création de rapport**     |   Système envoyant les enregistrements à Azure Sentinel. Ce système peut ne pas être le système de sujet pour l’enregistrement qui est envoyé.      |
|**Enregistrement**     |Unité de données envoyée à partir du périphérique de création de rapport. Cet enregistrement est souvent appelé `log`, `event` ou `alert`, mais peut également avoir d’autres types de données.         |
|**Contenu** ou **Élément de contenu**     |Les différents artefacts, personnalisables ou créés par l’utilisateur, que vous pouvez utiliser avec Azure Sentinel. Ces artefacts incluent, par exemple, des règles Analytics, des requêtes de chasse et des classeurs. Un élément de contenu est l’un de ces artefacts.|
| | |

<br>

## <a name="getting-started-with-asim"></a>Prise en main d’ASIM

Pour commencer à utiliser ASIM :

1. Déployer tous les analyseurs ASIM à partir du [référentiel GitHub Azure Sentinel](https://aka.ms/AzSentinelASim).

1. Activez les modèles de règle d’analytique qui utilisent ASIM. Pour plus d’informations, consultez [Liste de contenu du modèle ASIM (Azure Sentinel Information Model)](normalization-content.md#builtin).

1. Utilisez ASIM dans votre espace de travail, à l’aide des méthodes suivantes :

    - Utilisez les requêtes de chasse ASIM du référentiel GitHub Azure Sentinel, lors de l’interrogation des journaux dans KQL, dans la page **Journaux** d’Azure Sentinel. Pour plus d’informations, consultez [Liste de contenu du modèle ASIM (Azure Sentinel Information Model)](normalization-content.md#builtin).

    - Écrivez vos propres règles d’analytique à l’aide de ASIM ou [convertissez des règles existantes](normalization-content.md#builtin).

    - Permettez à vos données personnalisées d’utiliser des analyses intégrées en [écrivant des analyseurs](normalization-about-parsers.md) pour vos sources personnalisées et en les [ajoutant](normalization-about-parsers.md#include) à l’analyseur source agnostique approprié.

## <a name="next-steps"></a><a name="next-steps"></a>Étapes suivantes

Cet article fournit une vue d’ensemble de la normalisation dans Azure Sentinel et dans le modèle Azure Sentinel Information Model.

Pour plus d'informations, consultez les pages suivantes :

- Regardez le [webinaire sur ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Schémas du modèle Azure Sentinel Information Model](normalization-about-schemas.md)
- [Analyseurs du modèle Azure Sentinel Information Model](normalization-about-parsers.md)
- [Contenu du modèle Azure Sentinel Information Model](normalization-content.md)
