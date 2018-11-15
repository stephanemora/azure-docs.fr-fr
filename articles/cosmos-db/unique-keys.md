---
title: Clés uniques dans Azure Cosmos DB
description: Apprenez à utiliser des clés uniques dans votre base de données Azure Cosmos DB
author: aliuy
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 006d0ef28d82a7648a56b3bf871c5a3afd6a55a6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624418"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Clés uniques dans Azure Cosmos DB

Les clés uniques vous permettent d'ajouter une couche d'intégrité des données à un conteneur Cosmos. Vous créez une stratégie de clé unique lors de la création d'un conteneur Cosmos. Avec les clés uniques, vous garantissez l'unicité d'une ou plusieurs valeurs au sein d'une partition logique (vous pouvez garantir l'unicité par [clé de partition](partition-data.md)). Une fois qu'un conteneur a été créé avec une stratégie de clé unique, il empêche la création (ou la mise à jour) de doublons au sein d'une partition logique, comme spécifié par la contrainte de clé unique. La clé de partition associée à la clé unique garantit l'unicité d'un élément au sein d'un conteneur.

Penons l'exemple d'un conteneur Cosmos avec une adresse e-mail en guise de contrainte de clé unique et `CompanyID` en guise de clé de partition. En configurant l'adresse e-mail de l'utilisateur comme clé unique, vous veillez à ce que chaque élément possède une adresse e-mail unique dans un `CompanyID` donné. Il est impossible de créer deux éléments avec des adresses e-mail en double et avec la même valeur de clé de partition.  

Si vous souhaitez permettre aux utilisateurs de créer plusieurs éléments avec la même adresse e-mail, mais pas avec la même combinaison de prénom, nom et adresse e-mail, vous pouvez ajouter d’autres chemins d’accès à la stratégie de clé unique. Au lieu de créer une clé unique basée sur l'adresse e-mail, vous pouvez créer une clé unique combinant à la fois le prénom, le nom et l’adresse e-mail (clé unique composite). Dans ce cas, chaque combinaison unique des trois valeurs est autorisée dans un `CompanyID` donné. Par exemple, le conteneur peut inclure des éléments présentant les valeurs suivantes, chaque élément respectant la contrainte de clé unique.

|CompanyID|Prénom|Nom|Adresse de messagerie|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Si vous tentez d’ajouter un autre élément avec les combinaisons répertoriées dans le tableau ci-dessus, vous recevrez une erreur indiquant que la contrainte de clé unique n’est pas respectée. L'un de messages suivants s'affichera : « Une ressource existante possède déjà l'ID ou le nom spécifié » ou « Une ressource existante possède déjà l'ID, le nom ou l'index unique spécifié ».  

## <a name="defining-a-unique-key"></a>Définir une clé unique

Les clés uniques ne peuvent être définies qu'au moment de la création d'un conteneur Cosmos. Une clé unique est associée à une partition logique. Dans l'exemple précédent, si vous partitionnez le conteneur en fonction du code postal, vous aurez des doublons dans chaque partition logique. Lors de la création de clés uniques, tenez compte des propriétés suivantes :

* Vous ne pouvez pas mettre à jour un conteneur existant pour qu'il utilise une autre clé unique. Autrement dit, une fois qu'un conteneur a été créé avec une stratégie de clé unique, la stratégie n'est pas modifiable.

* Si vous souhaitez définir une clé unique pour un conteneur existant, vous devez créer un nouveau conteneur avec la contrainte de clé unique et utiliser l'outil de migration de données approprié pour déplacer les données du conteneur existant vers le nouveau conteneur. Sur les conteneurs SQL, utilisez l'[outil de migration de données](import-data.md) pour déplacer des données. Sur les conteneurs MongoDB, utilisez [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md) pour déplacer des données.

* Une stratégie de clé unique peut inclure un maximum de 16 valeurs de chemin d’accès (par exemple : /firstName, /lastName, /address/zipCode). Chaque stratégie de clé unique peut inclure jusqu’à 10 contraintes ou combinaisons de clé unique, et les chemins d’accès combinés de chaque contrainte d’index unique ne doivent pas dépasser 60 octets. Dans l’exemple précédent, la combinaison prénom, nom et adresse e-mail représente une seule contrainte et utilise trois des 16 chemins d’accès possibles.

* Lorsqu'un conteneur dispose d'une stratégie de clé unique, le coût en unités de requête de la création, de la mise à jour et de la suppression d'un élément est légèrement supérieur.

* Les clés uniques partiellement allouées ne sont pas prises en charge. Si les valeurs de certains chemins d’accès uniques sont manquantes, ces dernières sont traitées comme des valeurs null, qui font partie intégrante de la contrainte d’unicité. Par conséquent, il ne peut y avoir qu'un seul élément avec une valeur null pour satisfaire cette contrainte.

* Les noms de clés uniques sont sensibles à la casse. Prenons l'exemple d'un conteneur doté d'une contrainte de clé unique définie sur /address/zipcode. Si vos données comportent un champ intitulé ZipCode, Cosmos DB insère « null » comme clé unique car « zipcode » n’est pas identique à « ZipCode ». En raison de ce respect de la casse, aucun autre enregistrement contenant ZipCode ne peut être inséré car la valeur « null » dupliquée violera la contrainte de clé unique.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [partitions logiques](partition-data.md)
