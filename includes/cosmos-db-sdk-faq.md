---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515268"
---
**1. Comment les clients vont-ils être informés du retrait du kit de développement logiciel ?**

Microsoft avertira 12 mois à l’avance de la fin de la prise en charge du Kit de développement logiciel (SDK) mis hors service afin de faciliter une transition en douceur vers un Kit de développement logiciel (SDK) pris en charge. En outre, les clients seront informés par différents canaux de communication : Portail Azure, Azure Updates et communication directe avec les administrateurs de services concernés.

**2. Les clients peuvent-ils créer des applications à l’aide d’un Kit de développement logiciel (SDK) Azure Cosmos DB destiné à être mis hors service pendant la période des 12 mois ?** 

Oui, les clients ont un accès complet permettant de créer, déployer et modifier des applications à l’aide du Kit de développement logiciel (SDK) Azure Cosmos DB qui sera mis hors service pendant la période de préavis de 12 mois. Pendant cette période de préavis de 12 mois, il est conseillé aux clients de migrer vers une version prise en charge plus récente du Kit de développement logiciel (SDK) d’Azure Cosmos DB, le cas échéant. 

**3. Après la date de mise hors service, qu’advient-il des applications qui utilisent le Kit de développement logiciel (SDK) Azure Cosmos DB non pris en charge ?** 

Après la date de mise hors service, Azure Cosmos DB n’apportera plus de correctifs de bogues, n’ajoutera plus de nouvelles fonctionnalités et ne fournira plus de support aux versions mises hors service du Kit de développement logiciel (SDK). Si vous préférez ne pas effectuer la mise à niveau, les requêtes envoyées depuis les versions mises hors service du Kit de développement logiciel (SDK) continueront d’être traitées par le service Azure Cosmos DB. 

**4. Quelles sont les versions du Kit de développement logiciel (SDK) qui disposent des dernières fonctionnalités et mises à jour ?**

Les nouvelles fonctionnalités et mises à jour ne seront ajoutées qu’à la dernière version mineure de la dernière version majeure prise en charge du Kit de développement logiciel (SDK). Nous vous recommandons de toujours utiliser la dernière version pour tirer parti des nouvelles fonctionnalités, des améliorations des performances et des correctifs de bogues. Si vous utilisez une ancienne version du Kit de développement logiciel (SDK) qui n’a pas été mise hors service, vos requêtes vers Azure Cosmos DB fonctionneront toujours, mais vous n’aurez accès à aucune des nouvelles fonctionnalités.  

**5. Que dois-je faire si je ne parviens pas à mettre à jour mon application avant la date limite ?**

Nous vous recommandons de mettre à niveau vers la dernière version du kit de développement logiciel dès que possible. Une fois le Kit de développement logiciel (SDK) marqué pour la mise hors service, vous aurez 12 mois pour mettre à jour votre application. Si vous n’êtes pas en mesure de procéder à une mise à jour avant la date de mise hors service, les requêtes envoyées à partir des versions mises hors service du Kit de développement logiciel (SDK) continueront d’être traitées par Azure Cosmos DB. Vos applications continueront donc à fonctionner. Toutefois, Azure Cosmos DB n’apportera plus de correctifs de bogues, n’ajoutera plus de nouvelles fonctionnalités et ne fournira plus de support aux versions mises hors service du Kit de développement logiciel (SDK). 

Si vous avez un plan de support et que vous avez besoin d’un support technique, veuillez [nous contacter](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) en remplissant un ticket de support.
    


