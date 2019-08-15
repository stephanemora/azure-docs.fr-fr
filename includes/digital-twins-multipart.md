---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: f9b1b32775393196a77bef3aa2257f6fb66afdcb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012118"
---
> [!NOTE]
> Les demandes en plusieurs parties nécessitent généralement trois éléments :
> * Un en-tête **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Un élément **Content-Disposition** :
>   * `form-data; name="metadata"`
> * Le contenu du fichier à charger
>
> L’en-tête **Content-Type** et l’élément **Content-Disposition** varient en fonction du scénario d’utilisation.

Des demandes en plusieurs parties peuvent être effectuées par programme (via C#), à l’aide d’un client REST ou d’un outil tel que [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Les outils clients REST peuvent avoir différents niveaux de support pour les demandes en plusieurs parties complexes. Les paramètres de configuration peuvent aussi légèrement varier d'un outil à un autre. Vérifiez quel outil est le mieux adapté à vos besoins.

> [!IMPORTANT]
> Les demandes en plusieurs parties faites aux API de gestion Azure Digital Twins comprennent généralement deux parties :
> * Des métadonnées blob (par exemple, un type MIME associé) déclarées par l’en-tête **Content-Type** et/ou l’élément **Content-Disposition**
> * Du contenu d’objet blob incluant le contenu non structuré d’un fichier à charger
>
> Aucune des deux parties n’est obligatoire pour les demandes **PATCH**. Les deux sont requises pour **POST** ou pour créer des opérations.

Le [code source de démarrage rapide d’Occupation](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contient des exemples en C# complets, montrant comment adresser des demandes en plusieurs parties aux API de gestion Azure Digital Twins.