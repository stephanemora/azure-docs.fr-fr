---
title: Configurer des conditions de correspondance d’ensemble de règles Azure Front Door Standard/Premium
description: Cet article fournit la liste des différentes conditions de correspondance disponibles avec un ensemble de règles d’Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097721"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Conditions de correspondance d’un ensemble de règles Azure Front Door Standard/Premium

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Ce tutoriel vous montre comment créer un ensemble de règles avec votre premier ensemble de règles dans le portail Azure. Dans un [ensemble de règles](concept-rule-set.md) Azure Front Door Standard/Premium, une règle se compose de zéro, une ou plusieurs conditions de correspondance, et d’une action. Cet article fournit des descriptions détaillées des conditions de correspondance que vous pouvez utiliser dans un ensemble de règles Azure Front Door Standard/Premium.

La première partie d’une règle est une condition de correspondance ou un ensemble de conditions de correspondance. Une règle peut comporter jusqu’à 10 conditions de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles des actions définies sont effectuées. Si vous utilisez plusieurs conditions de correspondance, elles seront regroupées à l’aide de la logique AND. Pour toutes les conditions de correspondance qui prennent en charge plusieurs valeurs (indiquées comme « séparées par des espaces »), c’est l’opérateur « OR » qui est appliqué.

Par exemple, vous pouvez utiliser une condition de correspondance pour les tâches suivantes :

* Filtrer les requêtes en fonction d’une adresse IP, d’un pays ou d’une région spécifique.
* Filtrer les requêtes d’après les informations d’en-tête.
* Filtrer les requêtes provenant d’appareils mobiles ou de bureau.
* Filtrer les demandes à partir du nom de fichier de la demande et de l’extension de fichier.
* Filtrer les demandes provenant de l’URL de requête, du protocole, du chemin d’accès, de la chaîne de requête, des arguments de publication, etc.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les conditions de correspondance suivantes sont disponibles pour être utilisées dans un ensemble de règles Azure Front Door Standard/Premium :

## <a name="device-type"></a>Type d’appareil

Identifie les requêtes effectuées à partir d’un appareil mobile ou de bureau.  

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Valeurs prises en charge
---------|----------------
Égal à, Non égal à | Mobile et Bureau

## <a name="post-argument"></a>Argument Post

Identifie les requêtes en fonction des arguments définis pour la méthode de demande POST utilisée dans la requête.

#### <a name="required-fields"></a>Champs obligatoires

Nom de l’argument | Opérateur | Valeur de l’argument | Transformation de cas
--------------|----------|----------------|---------------
String | [Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

## <a name="query-string"></a>Chaîne de requête

Identifie les requêtes qui contiennent un paramètre de chaîne de requête spécifique. Ce paramètre est défini sur une valeur qui correspond à un modèle spécifique. Les paramètres de chaîne de requête (par exemple, **paramètre = valeur**) dans l’URL de requête déterminent si cette condition est remplie. Cette condition de correspondance identifie un paramètre de chaîne de requête par son nom et accepte une ou plusieurs valeurs pour la valeur du paramètre.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Chaîne de requête | Transformation de cas
---------|--------------|---------------
[Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

## <a name="remote-address"></a>Adresse distante

Identifie les requêtes en fonction de l’emplacement ou de l’adresse IP du demandeur.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Valeurs prises en charge
---------|-----------------
Géocorrespondance | Code pays
Correspondance IP | Adresse IP (séparée par des espaces)
Sans géocorrespondance | Code pays
Sans correspondance IP | Adresse IP (séparée par des espaces)

#### <a name="key-information"></a>Informations essentielles

* Utilisez la notation CIDR.
* Pour plusieurs adresses IP et des blocs d’adresses IP, une logique « OU » est utilisée.
    * **Exemple IPv4** : Si vous ajoutez deux adresses IP *1.2.3.4* et *10.20.30.40*, la condition est remplie si des demandes arrivent de l’adresse 1.2.3.4 ou de l’adresse 10.20.30.40.
    * **Exemple IPv6** : Si vous ajoutez deux adresses IP *1:2:3:4:5:6:7:8* et *10:20:30:40:50:60:70:80*, la condition est remplie si des demandes arrivent de l’adresse 1:2:3:4:5:6:7:8 ou de l’adresse 10:20:30:40:50:60:70:80.
* La syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe. Par exemple :
    * **Exemple IPv4** : *5.5.5.64/26* correspond aux requêtes qui proviennent des adresses 5.5.5.64 à 5.5.5.127.
    * **Exemple IPv6** : *1:2:3:/48* correspond aux requêtes qui proviennent des adresses 1:2:3:0:0:0:0:0 à 1:2:3: ffff:ffff:ffff:ffff:ffff.

## <a name="request-body"></a>Corps de la demande

Identifie les requêtes en fonction d’un texte spécifique qui apparaît dans le corps de la requête.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Corps de la demande | Transformation de cas
---------|--------------|---------------
[Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

## <a name="request-header"></a>En-tête de requête

Identifie les requêtes qui utilisent un en-tête spécifique dans la requête.

#### <a name="required-fields"></a>Champs obligatoires

Nom de l’en-tête | Opérateur | Valeur de l’en-tête | Transformation de cas
------------|----------|--------------|---------------
String | [Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

## <a name="request-method"></a>Méthode de requête

Identifie les requêtes qui utilisent la méthode de demande spécifiée.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Valeurs prises en charge
---------|----------------
Égal à, Non égal à | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Informations essentielles

Seule la méthode de demande GET peut générer le contenu mis en cache dans Azure Front Door. Toutes les autres méthodes de requête sont traitées par proxy sur le réseau.

## <a name="request-protocol"></a>Protocole de requête

Identifie les requêtes qui utilisent le protocole spécifié.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Valeurs prises en charge
---------|----------------
Égal à, Non égal à | HTTP, HTTPS

## <a name="request-url"></a>URL de la demande

Identifie les requêtes qui correspondent à l’URL spécifiée.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | URL de la demande | Transformation de cas
---------|-------------|---------------
[Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

#### <a name="key-information"></a>Informations essentielles

Lorsque vous utilisez cette condition de règle, veillez à inclure les informations de protocole. Par exemple : *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Extension de fichier de la requête

Identifie les requêtes qui incluent l’extension de fichier spécifiée dans le nom de fichier dans l’URL de la requête.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Extension | Transformation de cas
---------|-----------|---------------
[Liste d'opérateurs](#operator-list)  | Chaîne, Int | Minuscule, Majuscule

#### <a name="key-information"></a>Informations essentielles

Pour l’extension, n’incluez pas de point de début ; par exemple, utilisez *html* au lieu de *.html*.

## <a name="request-file-name"></a>Nom de fichier de la requête

Identifie les requêtes qui incluent le nom de fichier spécifié dans l’URL de la requête.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Nom de fichier | Transformation de cas
---------|-----------|---------------
[Liste d'opérateurs](#operator-list)| Chaîne, Int | Minuscule, Majuscule

## <a name="request-path"></a>Chemin de la requête

Identifie les requêtes qui incluent le chemin d’accès spécifié dans l’URL de la requête.

#### <a name="required-fields"></a>Champs obligatoires

Opérateur | Valeur | Transformation de cas
---------|-------|---------------
[Liste d'opérateurs](#operator-list) | Chaîne, Int | Minuscule, Majuscule

## <a name="operator-list"></a><a name = "operator-list"></a>Liste des opérateurs

Pour les règles qui acceptent les valeurs de la liste des opérateurs standard, les opérateurs suivants sont valides :

* Quelconque
* Égal à
* Contient
* Commence par
* Se termine par
* Inférieur à
* Inférieur ou égal à
* Supérieur à
* Supérieur ou égal à
* Aucun
* Ne contient pas
* Ne commence pas par
* Ne se termine pas par
* N’est pas inférieur à
* N’est pas inférieur ou égal à
* N’est pas supérieur à
* N’est pas supérieur ou égal à
* Expression régulière

Pour les opérateurs numériques tels que *Inférieur à* et *Supérieur ou égal à*, la comparaison utilisée est basée sur la longueur. La valeur de la condition de correspondance doit être un entier égal à la longueur que vous voulez comparer.

## <a name="regular-expression"></a>Expression régulière

Expression régulière ne prend pas en charge les opérations suivantes :

* Références arrière et capture de sous-expressions
* Assertions arbitraires de largeur nulle
* Références de sous-routines et modèles récursifs
* Modèles conditionnels
* Verbes de contrôle de retours sur trace
* La directive \C mono-octet
* La directive de correspondance de saut de ligne \R
* La directive de début de correspondance de réinitialisation \K
* Légendes et code incorporé
* Regroupement atomique et quantificateurs de possession

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [ensembles de règles](concept-rule-set.md).
* Découvrez comment [configurer votre premier ensemble de règles](how-to-configure-rule-set.md).
* En savoir plus sur les [actions d’un ensemble de règles](concept-rule-set-actions.md).
