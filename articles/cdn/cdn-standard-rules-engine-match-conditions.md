---
title: Conditions de correspondance du moteur de règles standard Azure CDN de Microsoft | Microsoft Docs
description: Documentation de référence sur les conditions de correspondance du moteur de règles standard Azure Content Delivery Network (CDN) de Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615840"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Conditions de correspondance du moteur de règles standard Azure CDN de Microsoft

Cet article fournit les descriptions détaillées des conditions de correspondance disponibles pour le [moteur de règles standard](cdn-standard-rules-engine.md) Azure Content Delivery Network (CDN) de Microsoft.

La première partie d’une règle est un ensemble de conditions de correspondance. Chaque règle peut avoir jusqu’à 4 conditions de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles les actions définies dans la règle seront exécutées. Si vous utilisez plusieurs conditions de correspondance, elles seront regroupées à l’aide de la logique AND.

Par exemple, vous pouvez utiliser une condition de correspondance pour les tâches suivantes :

- Filtrer les requêtes générées à partir d’une adresse IP ou d’un pays/d'une région en particulier.
- Filtrer les requêtes d’après les informations d’en-tête.
- Filtrez les requêtes provenant d’appareils mobiles ou de bureau.

## <a name="match-conditions"></a>Conditions de correspondance

Les conditions de correspondance suivantes peuvent être utilisées. 

### <a name="device-type"></a>Type d’appareil 

Cette condition de correspondance identifie les requêtes effectuées à partir d’un appareil mobile ou de bureau, selon ses propriétés.  

**Champs obligatoires**

Operator | Valeur prise en charge
---------|----------------
Égal à, Non égal à | Mobile et Bureau


### <a name="http-version"></a>Version HTTP

Cette condition de correspondance identifie les requêtes en fonction de la version HTTP avec laquelle la requête arrive.

**Champs obligatoires**

Operator | Valeur prise en charge
---------|----------------
Égal à, Non égal à | 2.0, 1.1, 1.0, 0.9, Tout


### <a name="request-cookies"></a>Cookies de requête

Cette condition de correspondance identifie les requêtes en fonction des informations des cookies figurant dans la requête entrante.

**Champs obligatoires**

Nom du cookie | Operator | Valeur du cookie | Transformation de cas
------------|----------|--------------|---------------
Chaîne | [Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

Informations essentielles
- Les valeurs de caractère générique, y compris les astérisques (*), ne sont pas prises en charge lorsque vous spécifiez un nom de cookie, seules les correspondances de nom de cookie exactes sont éligibles pour la comparaison.
- Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
- Les comparaisons de noms de cookie respectent la casse.
- Spécifiez plusieurs valeurs de cookie en les séparant par un espace. 
- Les valeurs de cookie peuvent utiliser des valeurs de caractère générique.
- Si aucune valeur de caractère générique n’est spécifiée, seule une correspondance exacte satisfait cette condition de correspondance. Par exemple, « Valeur » correspond à « Valeur », mais pas à « Valeur1 ». 

### <a name="post-argument"></a>Argument Post

**Champs obligatoires**

Nom de l’argument | Operator | Valeur de l’argument | Transformation de cas
--------------|----------|----------------|---------------
Chaîne | [Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

### <a name="query-string"></a>Chaîne de requête

Cette condition de correspondance identifie les requêtes contenant un paramètre de chaîne de requête spécifié. Ce paramètre est défini sur une valeur qui correspond à un modèle spécifié. Les paramètres de chaîne de requête (par exemple, paramètre = valeur) dans l’URL de requête détermine si cette condition est remplie. Cette condition de correspondance identifie un paramètre de chaîne de requête par son nom et accepte une ou plusieurs valeurs pour la valeur du paramètre.

**Champs obligatoires**

Operator | Chaîne de requête | Transformation de cas
---------|--------------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

### <a name="remote-address"></a>Adresse distante

Cette condition de correspondance identifie les requêtes selon l’adresse IP ou l’emplacement du demandeur.

**Champs obligatoires**

Operator | Valeurs prises en charge
---------|-----------------
Quelconque | N/A
Géocorrespondance | Codes de pays
Correspondance IP | Adresses IP (séparées par des espaces)
Aucun | N/A
Sans géocorrespondance | Codes de pays
Sans correspondance IP | Adresses IP (séparées par des espaces)

Informations essentielles :

- Utilisez la notation CIDR.
- Spécifiez plusieurs adresses IP et/ou blocs d’adresses IP en les séparant par un espace. Par exemple :
  - **Exemple IPv4** : 1.2.3.4 10.20.30.40 correspond aux requêtes qui proviennent de l’adresse 1.2.3.4 ou 10.20.30.40.
  - **Exemple IPv6** : 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 correspond aux requêtes qui proviennent de l’adresse 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- La syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe. Par exemple :
  - **Exemple IPv4** : 5.5.5.64/26 correspond aux requêtes qui proviennent des adresses 5.5.5.64 à 5.5.5.127.
  - **Exemple IPv6** : 1:2:3:/48 correspond aux requêtes qui proviennent des adresses 1:2:3:0:0:0:0:0 à 1:2:3:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Corps de la requête

**Champs obligatoires**

Operator | Corps de la requête | Transformation de cas
---------|--------------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

### <a name="request-header"></a>En-tête de requête

**Champs obligatoires**
Nom de l’en-tête | Operator | Valeur de l’en-tête | Transformation de cas
------------|----------|--------------|---------------
Chaîne | [Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

### <a name="request-method"></a>Méthode de requête

**Champs obligatoires**

Operator | Valeur prise en charge
---------|----------------
Égal à, Non égal à | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

Informations essentielles :

- Par défaut, seule la méthode de requête GET peut générer le contenu mis en cache sur le CDN. Toutes les autres méthodes de requête sont traitées par proxy sur le réseau. 

### <a name="request-protocol"></a>Protocole de requête

**Champs obligatoires**

Operator | Valeur prise en charge
---------|----------------
Égal à, Non égal à | HTTP, HTTPS

### <a name="request-url"></a>URL de la demande

**Champs obligatoires**

Operator | URL de la demande | Transformation de cas
---------|-------------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

Informations essentielles :

- Lorsque vous saisissez l’URL d’une requête, veillez à inclure les informations sur le protocole. Par exemple : « https://www.[votre_domaine].com »

### <a name="url-file-extension"></a>Extension de fichier d’URL

**Champs obligatoires**

Operator | Extension | Transformation de cas
---------|-----------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

Informations essentielles :

- Pour l’extension, n’incluez pas de point de début ; par exemple, utilisez htm au lieu de .html.

### <a name="url-file-name"></a>Nom de fichier d’URL

**Champs obligatoires**

Operator | Nom de fichier | Transformation de cas
---------|-----------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

Informations essentielles :

- Si vous spécifiez plusieurs noms de fichier, séparez chaque nom par une espace. 

### <a name="url-path"></a>Chemin d’URL

**Champs obligatoires**

Operator | Valeur | Transformation de cas
---------|-------|---------------
[Liste des opérateurs standard](#standard-operator-list) | Chaîne, Int | Aucune transformation, en majuscules, en minuscules

Informations essentielles :

- Une valeur de nom de fichier peut utiliser des valeurs de caractère générique. Par exemple, chaque modèle de nom de fichier peut contenir un ou plusieurs astérisques (*), où chaque astérisque correspond à une séquence d’un ou de plusieurs caractères.

## <a name="reference-for-rules-engine-match-conditions"></a>Informations de référence des conditions de correspondance du moteur de règles

### <a name="standard-operator-list"></a>Liste des opérateurs standard

Pour les règles qui contiennent la liste d’opérateurs standard, les opérateurs suivants sont valides :

- Quelconque
- Égal à 
- Contains 
- Commence par 
- Ends with (Se termine par) 
- Inférieur à
- Inférieur ou égal à
- Supérieur à
- Supérieur ou égal à
- Aucun
- Ne contient pas
- Ne commence pas par 
- Ne se termine pas par 
- N’est pas inférieur à
- N’est pas inférieur ou égal à
- N’est pas supérieur à
- N’est pas supérieur ou égal à

Pour les opérateurs numériques, comme « Inférieur à » ou « Supérieur ou égal à », la comparaison utilisée est basée sur la longueur. Dans ce cas, la valeur de la condition de correspondance doit être un entier égal à la longueur que vous souhaitez comparer. 

---

[Revenir en haut](#match-conditions)

</br>

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md)
- [Informations de référence du moteur de règles](cdn-standard-rules-engine-reference.md)
- [Actions du moteur de règles](cdn-standard-rules-engine-actions.md)
- [Appliquer HTTPS à l’aide du moteur de règles standard](cdn-standard-rules-engine.md)
