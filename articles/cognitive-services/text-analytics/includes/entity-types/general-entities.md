---
title: Entités nommées générales
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211384"
---
## <a name="general-entity-types"></a>Types d’entités générales

### <a name="person"></a>Person

Reconnaître les noms de personnes dans le texte.

Langages :
* Préversion publique : `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` et `Turkish`

| Nom de sous-type | Description                                                      | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/A          | Noms de personnes reconnus, par exemple `Bill Gates`, `Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Type de travail ou rôle d’une personne.

Langages :
* Préversion publique : `English`

| Nom de sous-type | Description                                                                                | Disponible à partir de la version du modèle |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Types de travaux, par exemple `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Location

Points de repère, structures et caractéristiques géographiques et entités géopolitiques naturels et créés par l’homme.

Langages :

* Préversion publique : `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` et `Turkish`

| Nom de sous-type              | Description                                                                              | Disponible à partir de la version du modèle |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/A                       | emplacements, par exemple `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty`  | `2019-10-01`                           |
| Entité géopolitique (GPE) – En anglais uniquement| Villes, pays, États, par exemple, `Seattle`, `Pennsylvania`, `South Africa`, `Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organization  

Organisations, entreprises, agences et autres groupes de personnes reconnus. Par exemple : sociétés, groupes politiques, bandes musicales, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité. 

Langages : 

* Préversion publique : `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian`, `Spanish`, `Swedish` et `Turkish`

| Nom de sous-type | Description                                                                                             | Disponible à partir de la version du modèle |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | organisations, par exemple `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration`, `VOA` | `2019-10-01`                           |

### <a name="event"></a>Événement  

Événements historiques, sociaux et naturels.  

Langages : 

* Préversion publique : `English`

| Nom de sous-type | Description                                                            | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/A          | Événements tels que `wedding`, `hurricane`, `car accident`, `solar eclipse`, `American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produit  

Objets physiques de différentes catégories.  

Langages : 

* Préversion publique : `English`

| Nom de sous-type | Description                                                                        | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/A          | Par exemple, `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag`, `Xbox` | `2020-02-01`                           |
| Informatique    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Compétence  

Entité décrivant une capacité ou une expertise.  

Langages : 

* Préversion publique : `English`

| Nom de sous-type | Description                                                                 | Disponible à partir de la version du modèle |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/A          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Numéro de téléphone

Numéros de téléphone (Numéros de téléphone américains uniquement). 

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                    | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | Numéros de téléphone américains, par exemple `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Adresse de messagerie. 

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                      | Disponible à partir de la version du modèle |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | Adresse e-mail, par exemple `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

URL Internet

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                          | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | URL de sites web, par exemple `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Adresse IP

Adresse de protocole Internet

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                              | Disponible à partir de la version du modèle |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | Adresse réseau, par exemple `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Entités de date et d’heure. 

* Disponible à partir de la version `2019-10-01` du modèle

Langages :

* Préversion publique : `Chinese-Simplified`, `English`, `French`, `German` et `Spanish`

| Nom de sous-type    | Exemples                     |
|-------------|------------------------------|
| N/A         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Temps     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| Définissez | `every Saturday`         |

###  <a name="quantity"></a>Quantité

Nombres et quantités numériques. 

* Disponible à partir de la version `2019-10-01` du modèle

Langages :

* Préversion publique : `Chinese-Simplified`, `English`, `French`, `German` et `Spanish`

| Nom de sous-type    | Exemples                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| Pourcentage  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| Devise    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Température | `32 degrees`, `10°C`         |
