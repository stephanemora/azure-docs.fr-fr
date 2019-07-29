---
title: Conditions de correspondance du moteur de règles Azure CDN de Verizon Premium | Microsoft Docs
description: Documentation de référence des conditions de correspondance du moteur de règles Azure Content Delivery Network de Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593201"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Conditions de correspondance du moteur de règles Azure CDN de Verizon Premium

Cet article fournit les descriptions détaillées des conditions de correspondance disponibles pour le [moteur de règles](cdn-verizon-premium-rules-engine.md) Azure Content Delivery Network (CDN) de Verizon Premium.

La deuxième partie d’une règle est la condition de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles un ensemble de fonctionnalités est exécuté.

Par exemple, vous pouvez utiliser une condition de correspondance pour les tâches suivantes :

- Filtrer les requêtes ciblant le contenu dans un emplacement particulier.
- Filtrer les requêtes générées à partir d’une adresse IP ou d’un pays/d'une région en particulier.
- Filtrer les requêtes d’après les informations d’en-tête.

## <a name="always-match-condition"></a>Condition de correspondance Toujours

La condition de correspondance Toujours applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

Nom | Objectif
-----|--------
[Toujours](#always) | Applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

## <a name="device-match-condition"></a>Condition de correspondance Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.  

Nom | Objectif
-----|--------
[Appareil](#device) | Identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.

## <a name="location-match-conditions"></a>Conditions de correspondance Emplacement

Les conditions de correspondance Emplacement identifient les requêtes selon l’emplacement du demandeur.

Nom | Objectif
-----|--------
[Numéro AS](#as-number) | Identifie les requêtes issues d’un réseau particulier.
[Pays](#country) | Identifie les requêtes provenant des pays/régions spécifiés.

## <a name="origin-match-conditions"></a>Conditions de correspondance Origine

Les conditions de correspondance Origine identifient les requêtes qui pointent vers le stockage Content Delivery Network ou le serveur d’origine d’un client.

Nom | Objectif
-----|--------
[Origine CDN](#cdn-origin) | Identifie les requêtes de contenu stocké dans le stockage Content Delivery Network.
[Origine client](#customer-origin) | Identifie les requêtes de contenu stocké sur le serveur d’origine d’un client spécifique.

## <a name="request-match-conditions"></a>Conditions de correspondance Requête

Les conditions de correspondance Requête identifient les requêtes selon leurs propriétés.

Nom | Objectif
-----|--------
[Adresse IP du client](#client-ip-address) | Identifie les requêtes issues d’une adresse IP particulière.
[Paramètre de cookie](#cookie-parameter) | Recherche la valeur spécifiée dans les cookies associés à chaque requête.
[Expression régulière de paramètre de cookie](#cookie-parameter-regex) | Recherche l’expression régulière spécifiée dans les cookies associés à chaque requête.
[CNAME de périmètre](#edge-cname) | Identifie les requêtes qui pointent vers un CNAME de périphérie spécifique.
[Domaine de référence](#referring-domain) | Identifie les requêtes qui ont été référencées à partir des noms d’hôte spécifiés.
[Littéral d’en-tête de requête](#request-header-literal) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur spécifiée.
[Expression régulière d’en-tête de requête](#request-header-regex) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
[Caractère générique d’en-tête de requête](#request-header-wildcard) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond au modèle spécifié.
[Méthode de requête](#request-method) | Identifie les requêtes par leur méthode HTTP.
[Modèle de requête](#request-scheme) | Identifie les requêtes par leur protocole HTTP.

## <a name="url-match-conditions"></a>Conditions de correspondance URL

Les conditions de correspondance URL identifient les requêtes selon leurs URL.

Nom | Objectif
-----|--------
[Répertoire du chemin d’URL](#url-path-directory) | Identifie les requêtes par leur chemin relatif.
[Extension du chemin d’URL](#url-path-extension) | Identifie les requêtes par leur extension de nom de fichier.
[Nom de fichier du chemin d’URL](#url-path-filename) | Identifie les requêtes par leur nom de fichier.
[Littéral du chemin d’URL](#url-path-literal) | Compare le chemin relatif d’une requête à la valeur spécifiée.
[Expression régulière du chemin d’URL](#url-path-regex) | Compare le chemin relatif d’une requête à l’expression régulière spécifiée.
[Caractère générique du chemin d’URL](#url-path-wildcard) | Compare le chemin relatif d’une requête au modèle spécifié.
[Littéral de requête d’URL](#url-query-literal) | Compare la chaîne de requête d’une requête à la valeur spécifiée.
[Paramètre de requête d’URL](#url-query-parameter) | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond au modèle spécifié.
[Expression régulière de requête d’URL](#url-query-regex) | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
[Caractère générique de requête d’URL](#url-query-wildcard) | Compare les valeurs spécifiées à la chaîne de requête de la requête.

## <a name="reference-for-rules-engine-match-conditions"></a>Informations de référence des conditions de correspondance du moteur de règles

---

### <a name="always"></a>Toujours

La condition de correspondance Toujours applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Numéro AS

Le réseau Numéro AS est défini par son numéro de système autonome (ASN). 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Numéro AS est remplie :

- **Correspond** : nécessite que l’ASN du réseau client corresponde à l’un des ASN spécifiés. 
- **Ne correspond pas** : nécessite que l’ASN du réseau client ne corresponde pas aux ASN spécifiés.

Informations essentielles :

- Spécifiez plusieurs ASN en les séparant par un espace. Par exemple, 64514 64515 correspond aux requêtes qui proviennent de 64514 ou 64515.
- Certaines requêtes peuvent ne pas retourner d’ASN valide. Un point d’interrogation (?) correspond aux requêtes pour lesquelles un ASN valide ne peut pas être déterminé.
- Spécifiez l’ASN entier du réseau souhaité. Les valeurs partielles ne sont pas prises en compte.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Origine CDN

La condition de correspondance Origine CDN est remplie quand les deux conditions suivantes sont remplies :

- Le contenu du stockage CDN a été demandé.
- L’URI de la requête utilise le type de point d’accès de contenu (par exemple, /000001) qui est défini dans cette condition de correspondance :
  - URL CDN : l’URI de la requête doit contenir le point d’accès de contenu sélectionné.
  - Edge CNAME URL (URL du CNAME de périmètre) : la configuration du CNAME de périmètre correspondant doit pointer vers le point d’accès de contenu sélectionné.
  
Informations essentielles :

- Le point d’accès de contenu identifie le service qui doit traiter le contenu demandé.
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine CDN avec une condition de correspondance Origine client crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine CDN ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Adresse IP du client

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Adresse IP du client est remplie :

- **Correspond** : nécessite que l’adresse IP du client corresponde à l’une des adresses IP spécifiées. 
- **Ne correspond pas** : nécessite que l’adresse IP du client ne corresponde pas aux adresses IP spécifiées. 

Informations essentielles :

- Utilisez la notation CIDR.
- Spécifiez plusieurs adresses IP et/ou blocs d’adresses IP en les séparant par un espace. Par exemple :
  - **Exemple IPv4** : 1.2.3.4 10.20.30.40 correspond aux requêtes qui proviennent de l’adresse 1.2.3.4 ou 10.20.30.40.
  - **Exemple IPv6** : 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 correspond aux requêtes qui proviennent de l’adresse 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- La syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe. Par exemple :
  - **Exemple IPv4** : 5.5.5.64/26 correspond aux requêtes qui proviennent des adresses 5.5.5.64 à 5.5.5.127.
  - **Exemple IPv6** : 1:2:3:/48 correspond aux requêtes qui proviennent des adresses 1:2:3:0:0:0:0:0 à 1:2:3:ffff:ffff:ffff:ffff:ffff.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Paramètre de cookie

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Paramètre de cookie est remplie.

- **Correspond** : nécessite qu’une requête contienne le cookie spécifié avec une valeur qui correspond à au moins une des valeurs définies dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :

- Nom du cookie :
  - Étant donné que des valeurs de caractère générique, y compris les astérisques (*), ne sont pas prises en charge lorsque vous spécifiez un nom de cookie, seules les correspondances de nom de cookie exactes sont éligibles pour la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie :
  - Spécifiez plusieurs valeurs de cookie en les séparant par un espace.
  - Une valeur de cookie peut utiliser des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Si aucune valeur de caractère générique n’a été spécifiée, seule une correspondance exacte satisfait cette condition de correspondance. Par exemple, « Valeur » correspond à « Valeur », mais pas à « Valeur1 » ni « Valeur2 ».
  - Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Expression régulière de paramètre de cookie

La condition de correspondance Expression régulière de paramètre de cookie définit une valeur et un nom de cookie. Vous pouvez utiliser des [expressions régulières](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) pour définir la valeur de cookie souhaitée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière de paramètre de cookie est remplie.

- **Correspond** : nécessite que la requête contienne le cookie spécifié avec une valeur qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.
  
Informations essentielles :

- Nom du cookie :
  - Étant donné que des expressions régulières et des valeurs de caractère générique, y compris les astérisques (*), ne sont pas prises en charge lorsque vous spécifiez un nom de cookie, seules les correspondances de nom de cookie exactes sont éligibles pour la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie :
  - Une valeur de cookie peut contenir des expressions régulières.
  - Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Vous pouvez spécifier un pays à l’aide de son code de pays. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Pays est remplie :

- **Correspond** : nécessite que la requête contienne les codets de pays spécifiés. 
- **Ne correspond pas** : nécessite que la requête ne contienne pas les codets de pays spécifiés.

Informations essentielles :

- Spécifiez plusieurs codes de pays en les séparant par un espace.
- Les caractères génériques ne sont pas pris en charge dans la spécification d’un code de pays.
- Les codes de pays « EU » et « AP » n’incluent pas toutes les adresses IP de ces régions.
- Certaines requêtes peuvent ne pas retourner de code de pays valide. Un point d’interrogation (?) correspond aux requêtes pour lesquelles un code de pays valide ne peut pas être déterminé.
- Les codes de pays respectent la casse.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implémentation du filtrage par pays à l’aide du moteur de règles

Cette condition de correspondance vous permet d’effectuer une multitude de personnalisations en fonction de l’emplacement d’origine d’une requête. Par exemple, le comportement de la fonctionnalité de filtrage par pays peut être répliqué via la configuration suivante :

- Correspondance de caractère générique du chemin d’URL : définissez la [condition de correspondance Caractère générique du chemin d’URL](#url-path-wildcard) sur le répertoire qui sera sécurisé. 
    Ajoutez un astérisque à la fin du chemin d’accès relatif pour vous assurer que l’accès à tous ses enfants sera limité par cette règle.

- Correspondance de pays : définissez la condition de correspondance Pays sur l’ensemble de pays souhaité.
  - Autoriser : définissez la condition de correspondance Pays sur **Ne correspond pas** pour autoriser uniquement l’accès des pays spécifiés au contenu stocké dans l’emplacement défini par la condition de correspondance Caractère générique du chemin d’URL.
  - Bloquer : définissez la condition de correspondance Pays sur **Correspond** pour bloquer l’accès des pays spécifiés au contenu stocké dans l’emplacement défini par la condition de correspondance Caractère générique du chemin d’URL.

- Deny Access (403) Feature (Fonctionnalité Refuser l’accès (403)) : activez [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) (Fonctionnalité Refuser l’accès (403)) pour répliquer la partie autorisée ou bloquée de la fonctionnalité de filtrage par pays.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Origine client

Informations essentielles :

- La condition de correspondance Origine client est satisfaite que le contenu soit demandé à l’aide d’une URL CDN ou d’une URL CNAME de périmètre qui pointe vers l’origine client sélectionnée.
- Une configuration d’origine client référencée par une règle ne peut pas être supprimée à partir de la page Origine client. Avant de supprimer une configuration d’origine client, vérifiez que les configurations suivantes ne la référencent pas :
  - Condition de correspondance Origine client
  - Configuration de CNAME de périmètre
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine client avec une condition de correspondance Origine CDN crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine client ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés. La détection de périphérique mobile est obtenue par le biais de [WURFL](http://wurfl.sourceforge.net/). 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Appareil est remplie :

- **Correspond** : nécessite que l’appareil du demandeur corresponde à la valeur spécifiée. 
- **Ne correspond pas** : nécessite que l’appareil du demandeur ne corresponde pas à la valeur spécifiée.

Informations essentielles :

- Utilisez l’option **Ignorer la casse** pour spécifier si la valeur indiquée respecte la casse.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

#### <a name="string-type"></a>Type de chaîne

En général, une fonctionnalité WURFL accepte n’importe quelle combinaison de chiffres, de lettres et de symboles. En raison de la nature flexible de cette fonctionnalité, vous devez choisir la façon dont la valeur associée à cette condition de correspondance est interprétée. Le tableau suivant décrit l’ensemble d’options disponibles :

Type     | Description
---------|------------
Littéral  | Sélectionnez cette option pour empêcher la plupart des caractères de prendre une signification particulière à l’aide de leur [valeur littérale](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Caractère générique | Sélectionnez cette option pour utiliser tous les [caractères génériques] ([valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)).
Expression régulière    | Sélectionnez cette option pour utiliser des [expressions régulières](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Les expressions régulières sont utiles pour la définition d’un modèle de caractères.

#### <a name="wurfl-capabilities"></a>Fonctionnalités WURFL

Une fonctionnalité WURFL fait référence à une catégorie qui décrit les appareils mobiles. La fonctionnalité sélectionnée détermine le type de description de l’appareil mobile qui est utilisé pour identifier les requêtes.

Le tableau suivant répertorie les fonctionnalités WURFL et leurs variables pour le moteur de règles.

> [!NOTE]
> Les variables suivantes sont prises en charge dans les fonctionnalités **Modifier l’en-tête de requête client** et **Modifier l’en-tête de réponse client**.

Fonctionnalité | Variable | Description | Exemples de valeurs
-----------|----------|-------------|----------------
Nom de la marque | %{wurfl_cap_brand_name} | Chaîne qui indique le nom de la marque de l’appareil. | Samsung
Système d’exploitation de l’appareil | %{wurfl_cap_device_os} | Chaîne qui indique le système d’exploitation installé sur l’appareil. | IOS
Version du système d’exploitation de l’appareil | %{wurfl_cap_device_os_version} | Chaîne qui indique le numéro de version du système d’exploitation installé sur l’appareil. | 1.0.1
Orientation double | %{wurfl_cap_dual_orientation} | Valeur booléenne qui indique si l’appareil prend en charge l’orientation double. | true
DTD HTML par défaut | %{wurfl_cap_html_preferred_dtd} | Chaîne qui indique la définition de type de document (DTD) par défaut du périphérique mobile pour le contenu HTML. | Aucun<br/>xhtml_basic<br/>html5
Incorporation d’images | %{wurfl_cap_image_inlining} | Valeur booléenne qui indique si l’appareil prend en charge les images codées en Base64. | false
Est Android | %{wurfl_vcap_is_android} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation Android. | true
Est IOS | %{wurfl_vcap_is_ios} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation iOS. | false
Est une télévision intelligente | %{wurfl_cap_is_smarttv} | Valeur booléenne qui indique si l’appareil est une télévision intelligente. | false
Est un smartphone | %{wurfl_vcap_is_smartphone} | Valeur booléenne qui indique si l’appareil est un smartphone. | true
Est une tablette | %{wurfl_cap_is_tablet} | Valeur booléenne qui indique si l’appareil est une tablette. Cette description est indépendante du système d’exploitation. | true
Est un appareil sans fil | %{wurfl_cap_is_wireless_device} | Valeur booléenne qui indique si l’appareil est considéré comme un appareil sans fil. | true
Nom marketing | %{wurfl_cap_marketing_name} | Chaîne qui indique le nom marketing de l’appareil. | BlackBerry 8100 Pearl
Navigateur mobile | %{wurfl_cap_mobile_browser} | Chaîne qui indique le navigateur utilisé pour demander du contenu à partir de l’appareil. | Chrome
Version du navigateur mobile | %{wurfl_cap_mobile_browser_version} | Chaîne qui indique la version du navigateur utilisé pour demander du contenu à partir de l’appareil. | 31
Nom du modèle | %{wurfl_cap_model_name} | Chaîne qui indique le nom du modèle de l’appareil. | s3
Téléchargement progressif | %{wurfl_cap_progressive_download} | Valeur booléenne qui indique si l’appareil prend en charge la lecture d’un fichier audio/vidéo pendant son téléchargement. | true
Date de lancement | %{wurfl_cap_release_date} | Chaîne qui indique l’année et le mois de l’ajout de l’appareil à la base de données WURFL.<br/><br/>Format : `yyyy_mm` | 2013_december
Hauteur de résolution | %{wurfl_cap_resolution_height} | Entier qui indique la hauteur de l’appareil en pixels. | 768
Largeur de résolution | %{wurfl_cap_resolution_width} | Entier qui indique la largeur de l’appareil en pixels. | 1 024

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Cname Edge

Informations essentielles :

- La liste des CNAME de périmètre disponibles est limitée à ceux qui ont été configurés sur la page des CNAME de périmètre pour la plateforme sur laquelle le moteur de règles est configuré.
- Avant de supprimer une configuration de CNAME de périmètre, vérifiez qu’une condition de correspondance CNAME de périmètre ne la référence pas. Les configurations de CNAME de périmètre qui ont été définies dans une règle ne peuvent pas être supprimées à partir de la page Enregistrements CNAME de périmètre.
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance CNAME de périmètre avec une condition de correspondance Origine client crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance CNAME de périmètre ne peuvent pas être combinées à l’aide d’une instruction AND IF.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domaine de référence

Le nom d’hôte associé au point d’accès par lequel le contenu a été demandé détermine si la condition Domaine de référence est remplie.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Domaine de référence est remplie :

- **Correspond** : nécessite que le nom d’hôte de référence corresponde aux valeurs spécifiées. 
- **Ne correspond pas** : nécessite que le nom d’hôte de référence ne corresponde pas à la valeur spécifiée.

Informations essentielles :

- Spécifiez plusieurs noms d’hôte en les séparant par un espace.
- Cette condition de correspondance prend en charge les [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Si la valeur spécifiée ne contient pas d’astérisque, elle doit correspondre à la valeur exacte du nom d’hôte du point d’accès. Par exemple, « mydomain.com » ne correspond pas à « www.mydomain.com ».
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Littéral d’en-tête de requête

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral d’en-tête de requête est remplie.

- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre à celle définie dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :

- Les comparaisons de noms d’en-tête respectent toujours la casse. Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Expression régulière d’en-tête de requête

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière d’en-tête de requête est remplie.

- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre au modèle défini dans [l’expression régulière](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) spécifiée.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :

- Nom de l’en-tête :
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Remplacez les espaces dans le nom d’en-tête par « % 20 ».
- Valeur de l’en-tête :
  - Une valeur d’en-tête peut contenir des expressions régulières.
  - Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - La condition de correspondance est remplie uniquement quand une valeur d’en-tête correspond exactement à au moins un des modèles spécifiés.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Caractère générique d’en-tête de requête

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique d’en-tête de requête est remplie.

- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre à au moins une valeur définie dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur spécifiée.
  
Informations essentielles :

- Nom de l’en-tête :
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Les espaces dans le nom d’en-tête doivent être remplacés par « %20 ». Vous pouvez aussi utiliser cette valeur pour spécifier des espaces dans une valeur d’en-tête.
- Valeur de l’en-tête :
  - Une valeur d’en-tête peut utiliser des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - Cette condition de correspondance est remplie quand une valeur d’en-tête correspond exactement à au moins un des modèles spécifiés.
  - Spécifiez plusieurs valeurs en les séparant par un espace.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Méthode de requête

La condition de correspondance Méthode de requête est respectée uniquement quand les ressources sont demandées par la méthode de requête sélectionnée. Les méthodes de requête disponibles sont :

- GET
- HEAD
- POST
- OPTIONS
- PUT
- SUPPRIMER
- TRACE
- CONNECT

Informations essentielles :

- Par défaut, seule la méthode de requête GET peut générer le contenu mis en cache sur le réseau. Toutes les autres méthodes de requête sont traitées par proxy sur le réseau.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Modèle de requête

La condition de correspondance Modèle de requête est respectée uniquement quand les ressources sont demandées par le protocole sélectionné. Les protocoles disponibles sont :

- HTTP
- HTTPS

Informations essentielles :

- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Répertoire du chemin d’URL

Identifie une requête par son chemin d’accès relatif, qui exclut le nom de fichier de la ressource demandée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Répertoire du chemin d’URL est remplie.

- **Correspond** : nécessite que la requête contienne un chemin d’URL relative, sans le nom de fichier, qui correspond au modèle d’URL spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL relative, sans le nom de fichier, qui ne correspond pas au modèle d’URL spécifié.

Informations essentielles :

- Utilisez l’option **Relative to** (Relatif à) pour spécifier si la comparaison de l’URL commence avant ou après le point d’accès au contenu. Le point d’accès au contenu est la partie du chemin d’accès qui s’affiche entre le nom d’hôte CDN Verizon et le chemin d’accès relatif à la ressource demandée (par exemple, /800001/CustomerOrigin). Il identifie un emplacement par type de serveur (par exemple, CDN ou origine du client) et votre numéro de compte client.

   Les valeurs suivantes sont disponibles pour l’option **Relative to** (Relatif à) :
  - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN. 

  Par exemple : http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

  Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  Cette URL pointe vers le nom d’hôte CDN Verizon suivant: http:\//wpc.0001.&lt;domaine&gt;/800001/myorigin/**myfolder/index.htm**

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL.

    Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL.
  - CDN URL: http:\//wpc.0001.&lt;domaine&gt;/800001/CustomerOrigin/path/asset.htm
    
  - URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informations supplémentaires :
  - Domaine personnalisé : https:\//my.domain.com/path/asset.htm
    
    - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/
    
    - Chemin d’URL (relatif à l’origine) : /path/

- La partie de l’URL qui est utilisée pour la comparaison d’URL s’arrête juste avant le nom de fichier de la ressource demandée. Une barre oblique de fin est le dernier caractère de ce type de chemin.

- Remplacez les espaces dans le modèle de chemin d’URL par « %20 ».

- Chaque modèle de chemin d’URL peut contenir un ou plusieurs astérisques (*), où chaque astérisque correspond à une séquence d’un ou de plusieurs caractères.

- Spécifiez plusieurs chemins d’URL dans le modèle en les séparant par une espace.

    Par exemple : */sales/ */marketing/

- Une spécification de chemin d’URL peut utiliser des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Extension du chemin d’URL

Identifie les requêtes par l’extension de fichier de la ressource demandée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Extension du chemin d’URL est remplie.

- **Correspond** : nécessite que l’URL de la requête contienne une extension de fichier qui correspond exactement au modèle spécifié.

   Par exemple, si vous spécifiez « htm », les ressources « htm » correspondent, mais pas les ressources « html ».  

- **Ne correspond pas** : nécessite que l’URL de la requête contienne une extension de fichier qui ne correspond pas au modèle spécifié.

Informations essentielles :

- Spécifiez les extensions de fichier à mettre en correspondance dans le champ **Valeur**. N’incluez pas de point de début ; par exemple, utilisez htm au lieu de .htm.

- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

- Spécifiez plusieurs extensions de fichier en délimitant chaque extension avec une espace. 

    Par exemple : htm html

- Par exemple, spécifier « htm » correspond aux ressources « htm », mais pas aux ressources « html ».

#### <a name="sample-scenario"></a>Exemple de scénario

L’exemple de configuration suivant part du principe que cette condition de correspondance est remplie quand une requête correspond à l’une des extensions spécifiées.

Spécification de valeur : asp aspx php html

Cette condition de correspondance est remplie quand elle trouve des URL qui se terminent par les extensions suivantes :

- .asp
- .aspx
- .php
- .html

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nom de fichier du chemin d’URL

Identifie les requêtes par le nom de fichier de la ressource demandée. Dans le cadre de cette condition de correspondance, un nom de fichier se compose du nom de la ressource demandée, d’un point et de l’extension de fichier (par exemple, index.html).

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Nom de fichier du chemin d’URL est remplie.

- **Correspond** : nécessite que la requête contienne un nom de fichier dans son chemin d’URL qui correspond au schéma spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un nom de fichier dans son chemin d’URL qui ne correspond pas au schéma spécifié.

Informations essentielles :

- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

- Pour spécifier plusieurs extensions de fichier, séparez chaque extension avec une espace.

    Par exemple : index.htm index.html

- Remplacez les espaces dans une valeur de nom de fichier par « % 20 ».

- Une valeur de nom de fichier peut utiliser des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Par exemple, chaque modèle de nom de fichier peut contenir un ou plusieurs astérisques (*), où chaque astérisque correspond à une séquence d’un ou de plusieurs caractères.

- Si aucun caractère générique n’est spécifié, seule une correspondance exacte satisfait cette condition de correspondance.

    Par exemple, spécifier « presentation.ppt » correspond à une ressource nommée « presentation.ppt », mais pas à une ressource nommée « presentation.pptx ».

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Littéral du chemin d’URL

Compare le chemin d’URL d’une requête, y compris le nom de fichier, à la valeur spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral du chemin d’URL est remplie.

- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond au schéma spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas au schéma spécifié.

Informations essentielles :

- Utilisez l’option **Relative to** (Relatif à) pour spécifier si le point de comparaison de l’URL commence avant ou après le point d’accès au contenu. 

    Les valeurs suivantes sont disponibles pour l’option **Relative to** (Relatif à) :
  - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN.

    Par exemple : http:\//wpc.0001.&lt;domaine&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

    Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  Cette URL pointe vers le nom d’hôte CDN Verizon suivant: http:\//wpc.0001.&lt;domaine&gt;/800001/myorigin/**myfolder/index.htm**

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant une comparaison d’URL.

Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL :

- CDN URL: http:\//wpc.0001.&lt;domaine&gt;/800001/CustomerOrigin/path/asset.htm
- URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Informations supplémentaires :
    
    - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/asset.htm
   
    - Chemin d’URL (relatif à l’origine) : /path/asset.htm

- Les chaînes de requête dans l’URL sont ignorées.
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
- La valeur spécifiée pour cette condition de correspondance est comparée au chemin d’accès relatif de la requête exacte effectuée par le client.

- Pour faire correspondre toutes les demandes effectuées vers un répertoire particulier, utilisez la condition de correspondance [Répertoire du chemin d’URL](#url-path-directory) ou [Caractère générique du chemin d’URL](#url-path-wildcard).

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Expression régulière du chemin d’URL

Compare le chemin d’URL d’une requête à [l’expression régulière](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière du chemin d’URL est remplie.

- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL.

    Par exemple, deux URL pointent vers la même ressource et ont donc le même chemin d’URL.

     - CDN URL: http:\//wpc.0001.&lt;domaine&gt;/800001/CustomerOrigin/path/asset.htm

     - URL CNAME de périmètre : http:\//my.domain.com/path/asset.htm

    Informations supplémentaires :
    
     - Chemin d’URL : /800001/CustomerOrigin/path/asset.htm

- Les chaînes de requête dans l’URL sont ignorées.
    
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
    
- Les espaces dans le chemin d’URL doivent être remplacées par « %20 ».

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Caractère générique du chemin d’URL

Compare le chemin d’URL relatif d’une requête au modèle de caractère générique spécifié.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique du chemin d’URL est remplie.

- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond au schéma de caractère générique spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas au schéma de caractère générique spécifié.

Informations essentielles :

- Option **Relative to** (Relatif à) : cette option détermine si le point de comparaison de l’URL commence avant ou après le point d’accès au contenu.

   Cette option peut avoir les valeurs suivantes :
     - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN.

       Par exemple : http:\//wpc.0001.&lt;domaine&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

       Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Cette URL pointe vers le nom d’hôte CDN Verizon suivant: http:\//wpc.0001.&lt;domaine&gt;/800001/myorigin/**myfolder/index.htm**

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL.

    Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL :
     - CDN URL: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informations supplémentaires :
    
     - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/asset.htm
    
     - Chemin d’URL (relatif à l’origine) : /path/asset.htm
    
- Spécifiez plusieurs chemins d’URL en les séparant par une espace.

   Par exemple : /marketing/asset.* /sales/\*.htm

- Les chaînes de requête dans l’URL sont ignorées.
    
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
    
- Remplacez les espaces dans le chemin d’URL par « % 20 ».
    
- La valeur spécifiée pour un chemin d’URL peut utiliser des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Chaque modèle de chemin d’URL peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.

#### <a name="sample-scenarios"></a>Exemples de scénarios

Les exemples de configuration dans le tableau suivant supposent que cette condition de correspondance est remplie quand une requête correspond au modèle d’URL spécifié :

Valeur                   | Par rapport à    | Résultat 
------------------------|----------------|-------
\*/test.html \*/test.php  | Racine ou Origine | Ce modèle correspond aux requêtes de ressources nommées « test.html » ou « test.php » dans les dossiers.
/80ABCD/origin/text/*   | Racine           | Ce modèle correspond lorsque la ressource demandée répond aux critères suivants : <br />- Il doit résider sur une origine client appelée « origin ». <br />- Le chemin d’accès relatif doit commencer par un dossier appelé « text ». Autrement dit, la ressource demandée peut résider dans le dossier « text » ou l’un de ses sous-dossiers récursifs.
*/css/* */js/*          | Racine ou Origine | Ce modèle correspond à toutes les URL CDN ou CNAME de périmètre qui contiennent un dossier css ou js.
*.jpg *.gif *.png       | Racine ou Origine | Ce modèle correspond à toutes les URL CDN ou CNAME de périmètre se terminant par .jpg, .gif ou .png. Une autre façon de spécifier ce modèle consiste à utiliser la [condition de correspondance Extension du chemin d’URL](#url-path-extension).
/images/\* /media/\*      | Origine         | Ce modèle est mis en correspondance par les URL CDN ou CNAME de périmètre dont le chemin d’accès relatif commence par un dossier « images » ou « media ». <br />- CDN URL: http:\//wpc.0001.&lt;domaine&gt;/800001/myorigin/images/sales/event1.png<br />- Exemple d’URL CNAME de périmètre : http:\//cdn.mydomain.com/images/sales/event1.png

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Littéral de requête d’URL

Compare la chaîne de requête d’une requête à la valeur spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral de requête d’URL est remplie.

- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à la chaîne spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à la chaîne spécifiée.

Informations essentielles :

- Seule une chaîne de requête exacte répond à cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- N’incluez pas de point d’interrogation (?) au début du texte de valeur de chaîne de requête.
    
- Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

   Caractère | Encodage par URL
   ----------|---------
   Espace     | %20
   &         | %25

- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Paramètre de requête d’URL

Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié. Ce paramètre est défini sur une valeur qui correspond à un modèle spécifié. Les paramètres de chaîne de requête (par exemple, paramètre = valeur) dans l’URL de requête détermine si cette condition est remplie. Cette condition de correspondance identifie un paramètre de chaîne de requête par son nom et accepte une ou plusieurs valeurs pour la valeur du paramètre. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Paramètre de requête d’URL est remplie.

- **Correspond** : nécessite qu’une requête contienne le paramètre spécifié avec une valeur qui correspond à au moins une des valeurs définies dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le paramètre spécifié.
  - Elle contient le paramètre spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.

Cette condition de correspondance fournit un moyen simple de spécifier des combinaisons nom/valeur de paramètre. Pour plus de souplesse, si vous faites correspondre un paramètre de chaîne de requête, envisagez d’utiliser la condition de correspondance [Caractère générique de requête d’URL](#url-query-wildcard).

Informations essentielles :

- Seul un nom de paramètre de requête d’URL peut être spécifié par instance de cette condition de correspondance.
    
- Étant donné que les valeurs de caractère générique ne sont pas prises en charge lorsqu’un nom de paramètre est spécifié, seules les correspondances de nom de paramètre exactes sont éligibles pour la comparaison.
- Les valeurs de paramètre peuvent inclure des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Chaque modèle de valeur de paramètre peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.
   - Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

       Caractère | Encodage par URL
       ----------|---------
       Espace     | %20
       &         | %25

- Spécifiez plusieurs valeurs de paramètre de chaîne de requête en les séparant par une espace. Cette condition de correspondance est remplie quand une requête contient l’une des combinaisons nom/valeur spécifiées.

   - Exemple 1 :

     - Configuration :

       ValueA ValueB

     - Cette configuration correspond aux paramètres de chaîne de requête suivants :

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Exemple 2 :

     - Configuration : 

        Value%20A Value%20B

     - Cette configuration correspond aux paramètres de chaîne de requête suivants :

       Parameter1=Value%20A

       Parameter1=Value%20B

- Cette condition de correspondance est remplie uniquement s’il existe une correspondance exacte à au moins une des combinaisons nom/valeur de chaîne de requête spécifiées.

   Par exemple, si vous utilisez la configuration de l’exemple précédent, la combinaison nom/valeur de paramètre « Parameter1 = ValueAdd » ne serait pas considérée comme une correspondance. Toutefois, si vous spécifiez l’une des valeurs suivantes, elle correspondra à la combinaison nom/valeur :

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

#### <a name="sample-scenarios"></a>Exemples de scénarios

L’exemple suivant montre comment cette option fonctionne dans des situations spécifiques :

Nom  | Valeur |  Résultat
------|-------|--------
Utilisateur  | Joe   | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée est « ?user=joe ».
Utilisateur  | *     | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée contient un paramètre Utilisateur.
Email | Joe\* | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée contient un paramètre E-mail qui commence par « Joe ».

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Expression régulière de requête d’URL

Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié. Ce paramètre est défini sur une valeur qui correspond à une [expression régulière](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière de requête d’URL est remplie.

- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :

- Seules les correspondances exactes à l’expression régulière spécifiée satisfont cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- Pour cette option, une chaîne de requête commence par le premier caractère après le délimiteur de point d’interrogation (?) pour la chaîne de requête.
    
- Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

   Caractère | Encodage par URL | Valeur
   ----------|--------------|------
   Espace     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Notez que les symboles de pourcentage doivent être placés dans une séquence d’échappement.

- Placez les caractères d’expression régulière spéciaux dans une double séquence d’échappement (par exemple, \^$. +) pour inclure une barre oblique inverse dans l’expression régulière.

   Par exemple :

   Valeur | Interprété comme 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Caractère générique de requête d’URL

Compare les valeurs spécifiées à la chaîne de requête de la requête.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique de requête d’URL est remplie.

- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à la valeur de caractère générique spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à la valeur de caractère générique spécifiée.

Informations essentielles :

- Pour cette option, une chaîne de requête commence par le premier caractère après le délimiteur de point d’interrogation (?) pour la chaîne de requête.
- Les valeurs de paramètre peuvent inclure des [valeurs de caractère générique](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) :
   - Chaque modèle de valeur de paramètre peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.
   - Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

     Caractère | Encodage par URL
     ----------|---------
     Espace     | %20
     &         | %25

- Spécifiez plusieurs valeurs en les séparant par un espace.

   Par exemple :  *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Seules les correspondances exactes à au moins un des modèles de chaîne de requête spécifiés satisfont cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

#### <a name="sample-scenarios"></a>Exemples de scénarios

L’exemple suivant montre comment cette option fonctionne dans des situations spécifiques :

 Nom                 | Description
 ---------------------|------------
user=joe              | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée est « ?user=joe ».
\*user=\* \*optout=\* | Ce modèle correspond lorsque la requête d’URL CDN contient l’utilisateur ou le paramètre de désabonnement.

[Revenir en haut](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md)
- [Informations de référence du moteur de règles](cdn-verizon-premium-rules-engine-reference.md)
- [Expressions conditionnelles du moteur de règles](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Fonctionnalités du moteur de règles](cdn-verizon-premium-rules-engine-reference-features.md)
- [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-verizon-premium-rules-engine.md)
