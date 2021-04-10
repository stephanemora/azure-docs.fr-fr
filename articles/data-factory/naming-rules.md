---
title: Règles d’affectation des noms des entités Azure Data Factory
description: Décrit les règles d'affectation de noms pour les entités Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a1a0622c0736bad5f6c205fab01f4405577ed67a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783351"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Règles d’affectation des noms

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le tableau suivant fournit des règles d'affectation de noms pour les artefacts Data Factory.

| Nom | Unicité du nom | Contrôles de validation |
|:--- |:--- |:--- |
| Fabrique de données | Unique sur Microsoft Azure. Les noms ne respectent pas la casse, c’est-à-dire que `MyDF` et `mydf` font référence à la même fabrique de données. |<ul><li>Chaque fabrique de données est liée à un seul abonnement Azure.</li><li>Les noms d’objet doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).</li><li>Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.</li><li>Le nom doit contenir entre 3 et 63 caractères.</li></ul> |
| Services, jeux de données, pipelines et flux de données liés | Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Les noms d’objets doivent commencer par une lettre.</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », «  ? », « / », « < », « > », « * », « % », « & », «  : », « \\ »</li><li>Les tirets (« - ») ne sont pas autorisés dans les noms de services liés, les flux de données et les jeux de données.</li></ul>  |
| Runtime d’intégration |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Le nom du runtime d’intégration ne peut contenir que des lettres, des chiffres et des tirets (-).</li><li>Le premier et le dernier caractères doivent être une lettre ou un chiffre. Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre.</li><li>Les tirets consécutifs ne sont pas autorisés dans un nom de runtime d’intégration. </li></ul> |
| Transformations de flux de données | Unique dans un flux de données. Les noms sont sensibles à la casse | <ul><li>Les noms de transformation de flux de données ne peuvent contenir que des lettres et des chiffres</li><li>Le premier caractère doit être une lettre. </li></ul> |
| Groupe de ressources |Unique sur Microsoft Azure. Les noms sont sensibles à la casse. | Pour plus d’informations, consultez la page [Règles d’affectation de noms Azure et restrictions](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |
| Paramètres et variables du pipeline  |Unique dans le pipeline. Les noms sont sensibles à la casse. | <ul><li>Le contrôle de validation sur les noms de paramètres et de variables est limité à l’unicité par souci de compatibilité descendante.</li><li>Quand vous utilisez des paramètres ou des variables pour référencer des noms d’entités, par exemple un service lié, les règles de nommage d’entité s’appliquent.</li><li>Une bonne pratique consiste à suivre les règles de nommage relatives à la transformation de flux de données pour nommer les paramètres et variables de votre pipeline.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des fabriques de données en suivant les instructions pas à pas de l’article [Démarrage rapide : créer une fabrique de données](quickstart-create-data-factory-powershell.md). 
