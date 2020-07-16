---
title: Règles d’affectation des noms des entités Azure Data Factory
description: Décrit les règles d'affectation de noms pour les entités Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: fb8c25a49aa4cacc09ba6cd51cc859c4db036ec6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670002"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Règles d’affectation des noms

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le tableau suivant fournit des règles d'affectation de noms pour les artefacts Data Factory.

| Nom | Unicité du nom | Contrôles de validation |
|:--- |:--- |:--- |
| Data Factory |Unique sur Microsoft Azure. Les noms ne respectent pas la casse, c’est-à-dire que `MyDF` et `mydf` font référence à la même fabrique de données. |<ul><li>Chaque fabrique de données est liée à un seul abonnement Azure.</li><li>Les noms d’objet doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).</li><li>Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.</li><li>Le nom doit contenir entre 3 et 63 caractères.</li></ul> |
| Services, jeux de données et pipelines liés |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Les noms d’objet doivent commencer par une lettre, un chiffre ou un trait de soulignement (_).</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li><li>Les tirets (« - ») ne sont pas autorisés dans les noms de services liés et de jeux de données uniquement.</li></ul>  |
| Runtime d’intégration |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Le nom du runtime d’intégration ne peut contenir que des lettres, des chiffres et des tirets (-).</li><li>Le premier et le dernier caractères doivent être une lettre ou un chiffre. Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre.</li><li>Les tirets consécutifs ne sont pas autorisés dans un nom de runtime d’intégration. </li></ul> |
| Groupe de ressources |Unique sur Microsoft Azure. Les noms sont sensibles à la casse. | Pour plus d’informations, consultez la page [Règles d’affectation de noms Azure et restrictions](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment créer des fabriques de données en suivant les instructions pas à pas de l’article [Démarrage rapide : créer une fabrique de données](quickstart-create-data-factory-powershell.md). 
