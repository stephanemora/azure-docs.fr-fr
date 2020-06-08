---
title: Fonctionnalités du moteur de règles Azure CDN de Verizon Premium | Microsoft Docs
description: Documentation de référence sur les fonctionnalités du moteur de règles Azure CDN de Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872710"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Fonctionnalités du moteur de règles Azure CDN de Verizon Premium

Cet article comprend les descriptions détaillées des fonctionnalités du [moteur de règles](cdn-verizon-premium-rules-engine.md) Azure Content Delivery Network (CDN).

La troisième partie d’une règle est la fonctionnalité. Une fonctionnalité définit le type d’action appliqué au type de requête qui est identifié par un ensemble de conditions de correspondance.


Pour connaître les dernières fonctionnalités, consultez la [Documentation du moteur de règles Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).


## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Informations de référence sur les fonctionnalités du moteur de règles Azure CDN de Verizon Premium

---

### <a name="age-response-header"></a>En-tête de réponse Age

**Objectif** : Détermine si un en-tête de réponse Age est inclus dans la réponse envoyée au demandeur.

Valeur|Résultats
--|--
activé | L’en-tête de réponse Age est inclus dans la réponse envoyée au demandeur.
Désactivé | L’en-tête de réponse Age est exclu de la réponse envoyée au demandeur.

**Comportement par défaut** : Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Paramètres de bande passante

**Objectif :** Détermine si les paramètres de limitation de bande passante (par exemple, ec_rate et ec_prebuf) sont actifs.

Les paramètres de limitation de bande passante déterminent si le taux de transfert de données pour une requête du client est limité à un taux personnalisé.

Valeur|Résultats
--|--
activé|Permet aux points de présence d’honorer les requêtes de limitation de bande passante.
Désactivé|Force les points de présence à ignorer les paramètres de limitation de bande passante. Le contenu demandé est pris en charge normalement (c’est-à-dire, sans limitation de bande passante).

**Comportement par défaut :** activé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Limitation de bande passante

**Objectif :** limite la bande passante pour la réponse fournie par les points de présence.

Les deux options suivantes doivent être définies pour configurer correctement la limitation de bande passante.

Option|Description
--|--
Koctets par seconde|Définissez cette option sur la bande passante maximale (en Ko par seconde) qui peut être utilisée pour fournir la réponse.
Secondes prebuf|Définissez cette option sur le nombre de secondes que doivent attendre les points de présence pour que soit effectuée la limitation de la bande passante. L’objectif de cette période de bande passante illimitée consiste à éviter les problèmes de coupure ou de mise en mémoire tampon pour un lecteur multimédia en raison d’une limitation de bande passante.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Ignorer le cache

**Objectif :** Détermine si la requête doit contourner la mise en cache.

Valeur|Résultats
--|--
activé|Force toutes les requêtes à passer par le serveur d’origine, même si le contenu a été précédemment mis en cache sur les points de présence.
Désactivé|Force les points de présence à mettre les ressources en cache selon la stratégie de cache définie dans ses en-têtes de réponse.

**Comportement par défaut :**

- **HTTP Large :** Désactivé

<!---
- **ADN:** Enabled

--->

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Méthodes HTTP pouvant être mises en cache

**Objectif :** Détermine l’ensemble de méthodes HTTP supplémentaires pouvant être mises en cache sur le réseau.

Informations essentielles :

- Cette fonction suppose que les réponses GET soient toujours mises en cache. Par conséquent, la méthode HTTP GET ne doit pas être incluse lors de la définition de cette fonctionnalité.
- Cette fonctionnalité prend uniquement en charge la méthode HTTP POST. Activez la mise en cache de réponse POST en définissant cette fonctionnalité sur `POST`.
- Par défaut, seules les requêtes dont le corps est inférieur à 14 Ko sont mises en cache. Utilisez la fonctionnalité Taille de corps de requête pouvant être mise en cache pour définir la taille de corps de requête maximale.

**Comportement par défaut :** seules les réponses GET sont mises en cache.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Taille de corps de requête pouvant être mise en cache

**Objectif :** Définit le seuil permettant de déterminer si une réponse POST peut être mise en cache.

Ce seuil est déterminé par la définition d’une taille de corps de requête maximale. Les requêtes qui contiennent un corps de requête plus large ne sont pas mises en cache.

Informations essentielles :

- Cette fonctionnalité n’est applicable que lorsque les réponses POST peuvent prétendre à la mise en cache. Utilisez la fonctionnalité Méthodes HTTP pouvant être mises en cache pour activer la mise en cache de requête POST.
- Le corps de requête est pris en considération pour :
    - les valeurs x-www-form-urlencoded
    - Garantir une clé de cache unique
- La définition d’une taille de corps de requête maximale peut affecter les performances de diffusion de données.
    - **Valeur recommandée :** 14 Ko
    - **Valeur minimale :** 1 Ko

**Comportement par défaut :** 14 Ko

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Traitement d’en-tête de contrôle de cache

**Objectif :** contrôle la génération des en-têtes `Cache-Control` par le point de présence quand la fonctionnalité Obsolescence maximale externe est active.

Pour obtenir ce type de configuration le plus simple consiste à placer les fonctionnalités Âge maximal externe et En-tête de contrôle de cache dans la même instruction.

Valeur|Résultats
--|--
Remplacer|Garantit que les actions suivantes se produisent :<br/> - Remplacement de l’en-tête `Cache-Control` généré par le serveur d’origine. <br/>- Ajout de l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age à la réponse.
Transfert direct|Permet de garantir que l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age ne soit jamais ajouté à la réponse. <br/> Si le serveur d’origine génère un en-tête `Cache-Control`, celui-ci est transmis directement à l’utilisateur final. <br/> Si le serveur d’origine ne génère pas d’en-tête `Cache-Control`, l’en-tête de réponse risque de ne pas contenir d’en-tête `Cache-Control`.
Ajouter si manquant|Si aucun en-tête `Cache-Control` n’a été transmis par le serveur d’origine, cette option ajoute l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age. Cette option est utile pour garantir que toutes les ressources reçoivent un en-tête `Cache-Control`.
Supprimer| Cette option permet de garantir qu’aucun en-tête `Cache-Control` ne soit inclus dans la réponse de l’en-tête. Si un en-tête `Cache-Control` a déjà été attribué, il est supprimé de la réponse de l’en-tête.

**Comportement par défaut :** remplacer.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Chaîne de requête de clé de cache

**Objectif :** Détermine si la clé de cache inclut ou exclut les paramètres de chaîne de requête associés à une requête.

Informations essentielles :

- Spécifiez un ou plusieurs noms de paramètre de chaîne de requête et séparez chaque nom de paramètre par un espace unique.
- Cette fonctionnalité détermine si les paramètres de chaîne de requête sont inclus ou exclus de la clé de cache. Vous trouverez des informations supplémentaires pour chaque option dans le tableau suivant.

Type|Description
--|--
 Inclure|  Indique que chaque paramètre spécifié doit être inclus dans la clé de cache. Une clé de cache unique est générée pour chaque requête contenant une valeur unique pour un paramètre de chaîne de requête défini dans cette fonctionnalité.
 Inclure tout  |Indique qu’une clé de cache unique est créée pour chaque requête dans une ressource qui inclut une chaîne de requête unique. Ce type de configuration n’est généralement pas recommandé, car il peut entraîner un petit pourcentage de correspondances dans le cache. Un faible nombre de correspondances dans le cache augmente la charge sur le serveur d’origine, car il doit traiter plus de requêtes. Cette configuration duplique le comportement de mise en cache appelé « unique-cache » sur la page de mise en cache de la chaîne de requête.
 Exclure | Indique que seuls les paramètres spécifiés sont exclus de la clé de cache. Tous les autres paramètres de chaîne de requête figurent dans la clé de cache.
 Exclure tous  |Indique que tous les paramètres de chaîne de requête sont exclus de la clé de cache. Cette configuration duplique le comportement de mise en cache par défaut « standard-cache » sur la page de mise en cache de la chaîne de requête.  

Le moteur de règles vous permet de personnaliser la manière dont la mise en cache de la chaîne de requête est implémentée. Par exemple, vous pouvez spécifier que la mise en cache de la chaîne de requête est effectuée uniquement sur certains emplacements ou types de fichiers.

Pour dupliquer le comportement de mise en cache de la chaîne de requête « no-cache » sur la page de mise en cache de la chaîne de requête, créez une règle qui contient une condition de correspondance de caractère générique de requête d’URL et une fonctionnalité Ignorer le cache. Définissez la condition de correspondance de caractère générique de requête d’URL sur un astérisque (*).

>[!IMPORTANT]
> Si l’autorisation par jeton est activée pour un chemin d’accès sur ce compte, le mode de cache standard est le seul mode qui puisse être utilisé pour la mise en cache de la chaîne de requête. Pour plus d’informations, consultez [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Exemples de scénarios

L’exemple d’utilisation suivant inclut un exemple de requête et la clé de cache par défaut :

- **Exemple de requête :** http://wpc.0001.&lt;Domain&gt; /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Clé de cache par défaut :** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Inclure

Exemple de configuration :

- **Type :** Inclure
- **Paramètre(s) :** langue

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Inclure tout

Exemple de configuration :

- **Type :** Inclure tout

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclure

Exemple de configuration :

- **Type :** Exclure
- **Paramètre(s) :** sessionid userid

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Exclure tous

Exemple de configuration :

- **Type :** Exclure tous

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Réécriture de la clé de cache

**Objectif :** Réécrit la clé de cache associée à une requête.

Une clé de cache est le chemin d’accès relatif qui identifie une ressource pour les besoins de la mise en cache. En d’autres termes, les serveurs recherchent une version mise en cache d’une ressource en fonction de son chemin, tel que défini par sa clé de cache.

Configurez cette fonctionnalité en définissant les deux options suivantes :

Option|Description
--|--
Chemin d’accès d’origine| Permet de définir le chemin d’accès relatif aux types de requêtes dont la clé de cache est réécrite. Un chemin d’accès relatif peut être défini en sélectionnant un chemin d’accès d’origine de base et en définissant un modèle d’expression régulière.
Nouveau chemin d’accès|Permet de définir le chemin d’accès relatif pour la nouvelle clé de cache. Un chemin d’accès relatif peut être défini en sélectionnant un chemin d’accès d’origine de base et en définissant un modèle d’expression régulière. Ce chemin d’accès relatif peut être construit de manière dynamique au moyen de [variables HTTP](cdn-http-variables.md).

**Comportement par défaut :** la clé de cache d’une requête est déterminée par l’URI de requête.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Commentaire

**Objectif :** Permet d’ajouter une remarque dans une règle.

Une utilisation de cette fonctionnalité consiste à fournir des informations supplémentaires sur l’utilisation générale d’une règle ou sur la raison pour laquelle une condition de correspondance spécifique ou une fonctionnalité spécifique a été ajoutée à la règle.

Informations essentielles :

- Vous pouvez spécifier un maximum de 150 caractères.
- Utilisez uniquement des caractères alphanumériques.
- Cette fonctionnalité n’affecte pas le comportement de la règle. Il est simplement destiné à proposer une zone dans laquelle vous pouvez fournir des informations afin de pouvoir vous y référer ultérieurement, ou qui peuvent vous aider lors du dépannage de la règle.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Remplissage de cache complet

**Objectif :** Détermine ce qui se passe quand une requête génère une absence de cache partielle sur un point de présence.

Une absence de cache partielle décrit l’état du cache pour une ressource qui n’a pas été complètement téléchargée sur un point de présence. Si une ressource n’est que partiellement mise en cache sur un point de présence, la requête suivante de cette ressource est transférée à nouveau sur le serveur d’origine.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Une absence de cache partielle se produit généralement après qu’un utilisateur a abandonné un téléchargement ou pour les ressources qui sont demandées uniquement à l’aide de requêtes de plage HTTP. Cette fonctionnalité est particulièrement utile pour les ressources volumineuses qui ne sont généralement pas téléchargées entièrement (par exemple, les vidéos). Par conséquent, cette fonctionnalité est activée par défaut sur la plateforme HTTP Large. Elle est désactivée sur toutes les autres plateformes.

Conservez la configuration par défaut pour la plateforme HTTP Large, car cela réduit la charge sur le serveur d’origine de votre client et augmente la vitesse à laquelle vos clients téléchargent votre contenu.

Valeur|Résultats
--|--
activé|Restaure le comportement par défaut. Le comportement par défaut consiste à forcer le point de présence à lancer une récupération en arrière-plan de la ressource à partir du serveur d’origine. Après quoi, la ressource se trouvera dans le cache local du point de présence.
Désactivé|Empêche un point de présence d’effectuer une récupération en arrière-plan pour la ressource. Le résultat est tel que la requête suivante pour cette ressource à partir de cette région force un point de présence à la demander à partir du serveur d’origine du client.

**Comportement par défaut :** activé.

#### <a name="compatibility"></a>Compatibilité

En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes :

- Numéro AS
- Adresse IP du client
- Paramètre de cookie
- Expression régulière de paramètre de cookie
- Country
- Appareil
- Cname Microsoft Edge
- Domaine de référence
- Littéral d’en-tête de requête
- Expression régulière d’en-tête de requête
- Caractère générique d’en-tête de requête
- Méthode de requête
- Modèle de requête
- Littéral de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL
- Paramètre de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Compresser les types de fichiers

**Objectif :** Définit les formats de fichier des fichiers qui sont compressés sur le serveur.

Un format de fichier peut être spécifié à l’aide de son type de média Internet (par exemple, Content-Type). Le type de média Internet est constitué de métadonnées indépendantes de la plateforme, permettant aux serveurs d’identifier le format de fichier d’une ressource particulière. Vous trouverez ci-dessous une liste des types de média Internet courants.

Type de média Internet|Description
--|--
texte/brut|Fichiers de texte brut
texte/html| Fichiers HTML
texte/css|Cascading Style Sheets (CSS)
application/x-javascript|JavaScript
application/javascript|JavaScript

Informations essentielles :

- Spécifiez plusieurs types de média Internet en délimitant chacun d’entre eux à l’aide d’un seul espace.
- Cette fonctionnalité compresse uniquement les ressources dont la taille est inférieure à 1 Mo. Les ressources plus volumineuses ne sont pas compressées par les serveurs.
- Certains types de contenu, tels que des images, des vidéos et des éléments multimédias audio (par exemple, JPG, MP3, MP4, etc.), sont déjà compressés. Étant donné que la compression supplémentaire sur ces types de ressources ne diminue pas considérablement la taille du fichier, il est recommandé de ne pas activer la compression sur ceux-ci.
- Les caractères génériques, tels que des astérisques, ne sont pas pris en charge.
- Avant d’ajouter cette fonctionnalité à une règle, veillez à définir l’option de désactivation de la compression sur la page Compression pour la plateforme à laquelle cette règle est appliquée.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Champ de fichier journal personnalisé 1

**Objectif :** Détermine le format et le contenu à attribuer au champ de fichier journal personnalisé dans un fichier journal brut.

Ce champ personnalisé vous permet de déterminer quelles valeurs d’en-tête de réponse et de requête sont stockées dans vos fichiers journaux.

Par défaut, le champ du fichier journal personnalisé est appelé « x-ec_custom-1 ». Le nom de ce champ peut être personnalisé à partir de la page des paramètres de journalisation bruts.

Le format pour spécifier les en-têtes de requête et de réponse est défini comme suit :

Type d’en-tête|Format|Exemples
-|-|-
En-tête de requête|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Authorization}i
En-tête de réponse|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informations essentielles :

- Un champ de fichier journal personnalisé peut contenir n’importe quelle combinaison de champs d’en-tête et de texte brut.
- Les caractères valides pour ce champ sont les suivants : caractères alphanumériques (par exemple, 0 à 9, a à z et A à Z), tirets, deux-points, points-virgules, apostrophes, virgules, points, traits de soulignement, signes égal, parenthèses, crochets et espaces. Le symbole de pourcentage et les accolades sont autorisés uniquement lorsqu’ils sont utilisés pour spécifier un champ d’en-tête.
- L’orthographe de chaque champ d’en-tête spécifié doit correspondre au nom d’en-tête de requête/réponse souhaité.
- Si vous souhaitez spécifier plusieurs en-têtes, utilisez un séparateur pour indiquer chaque en-tête. Par exemple, vous pouvez utiliser une abréviation pour chaque en-tête :
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Valeur par défaut :**  -

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>En-têtes de réponse de cache de débogage

**Objectif :** détermine si une réponse peut inclure des [en-têtes de réponse X-EC-Debug](cdn-http-debug-headers.md) qui fournissent des informations sur la stratégie de cache de la ressource demandée.

Les en-têtes de réponse de cache de débogage seront inclus dans la réponse lorsque les conditions suivantes sont remplies :

- La fonctionnalité En-têtes de réponse de cache de débogage a été activée sur la requête spécifiée.
- La requête spécifiée définit l’ensemble des en-têtes de réponse de cache de débogage qui seront inclus dans la réponse.

Des en-têtes de réponse de cache de débogage peuvent être demandés en incluant l’en-tête suivant et les directives spécifiées dans la requête :

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exemple :**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valeur|Résultats
-|-
activé|Les requêtes des en-têtes de réponse de cache de débogage renvoient une réponse qui inclut l’en-tête X-EC-Debug.
Désactivé|L’en-tête de réponse X-EC-Debug est exclu de la réponse.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Âge maximal interne par défaut

**Objectif :** Détermine l’intervalle d’âge maximal par défaut pour la revalidation du cache entre le point de présence et le serveur d’origine. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un point de présence ne vérifie si une ressource mise en cache correspond à la ressource stockée sur le serveur d’origine.

Informations essentielles :

- Cette action a lieu uniquement pour les réponses issues d’un serveur d’origine qui n’a pas attribué d’indication d’âge maximal (max-age) dans l’en-tête `Cache-Control` ou `Expires`.
- Cette action n’aura pas lieu pour les ressources qui ne sont pas considérées comme susceptibles d’être mises en cache.
- Cette action n’affecte pas les revalidations du cache entre le navigateur et le point de présence. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` ou `Expires` envoyés au navigateur, qui peuvent être personnalisés avec la fonctionnalité External Max-Age.
- Les résultats de cette action n’ont pas d’effet visible sur les en-têtes de réponse et le contenu retourné à partir des points de présence de votre contenu, mais cela peut avoir une incidence sur la quantité de trafic de revalidation envoyé à partir des points de présence vers votre serveur d’origine.
- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel un âge maximal interne par défaut peut être appliqué.
    - spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’intervalle d’âge maximal interne.

- Définissez l’unité de temps sur « Off » (Désactivé) pour affecter un intervalle d’âge maximal interne par défaut (default internal max-age interval) de 7 jours pour les requêtes auxquelles aucune indication d’âge maximal (max-age) n’a été affectée dans l’en-tête `Cache-Control` ou `Expires`.

**Valeur par défaut :** 7 jours

#### <a name="compatibility"></a>Compatibilité

En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes :
- Numéro AS
- Adresse IP du client
- Paramètre de cookie
- Expression régulière de paramètre de cookie
- Country
- Appareil
- Cname Edge
- Domaine de référence
- Littéral d’en-tête de requête
- Expression régulière d’en-tête de requête
- Caractère générique d’en-tête de requête
- Méthode de requête
- Modèle de requête
- Littéral de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL
- Paramètre de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Deny Access (403)

**Objectif** : Détermine si toutes les requêtes sont rejetées avec une réponse 403 Interdit.

Valeur | Résultats
------|-------
activé| Entraîne le rejet de toutes les demandes qui répondent aux critères avec une réponse 403 Interdit.
Désactivé| Restaure le comportement par défaut. Le comportement par défaut consiste à autoriser le serveur d’origine à déterminer le type de réponse qui sera renvoyée.

**Comportement par défaut** : Désactivé

> [!TIP]
   > Une utilisation possible de cette fonctionnalité consiste à l’associer à une condition de correspondance d’en-tête de requête pour bloquer l’accès aux référents HTTP qui utilisent des liens incorporés vers votre contenu.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Expiration du traitement d’en-tête

**Objectif :** Contrôle la génération des en-têtes `Expires` par un point de présence quand la fonctionnalité Obsolescence maximale externe est active.

La manière la plus simple d’atteindre ce type de configuration consiste à placer les fonctionnalités Âge maximal externe et Expiration du traitement d’en-tête dans la même instruction.

Valeur|Résultats
--|--
Remplacer|Garantit que les actions suivantes auront lieu :<br/>- Remplacement de l’en-tête `Expires` généré par le serveur d’origine.<br/>- Ajout de l’en-tête `Expires` généré par la fonctionnalité External Max-Age à la réponse.
Transfert direct|Permet de garantir que l’en-tête `Expires` généré par la fonctionnalité External Max-Age ne soit jamais ajouté à la réponse. <br/> Si le serveur d’origine génère un en-tête `Expires`, celui-ci est transmis directement à l’utilisateur final. <br/>Si le serveur d’origine ne génère pas d’en-tête `Expires`, l’en-tête de réponse risque de ne pas contenir d’en-tête `Expires`.
Ajouter si manquant| Si aucun en-tête `Expires` n’a été transmis par le serveur d’origine, cette option ajoute l’en-tête `Expires` généré par la fonctionnalité External Max-Age. Cette option est utile pour garantir que toutes les ressources reçoivent un en-tête `Expires`.
Supprimer| Garantit qu’aucun en-tête `Expires` n’est inclus dans la réponse de l’en-tête. Si un en-tête `Expires` a déjà été attribué, il est supprimé de la réponse de l’en-tête.

**Comportement par défaut :** Remplacer

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Âge maximal externe

**Objectif :** Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le navigateur et le point de présence. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un navigateur puisse rechercher la nouvelle version d’une ressource à partir d’un point de présence.

L’activation de cette fonctionnalité génère des en-têtes `Cache-Control: max-age` et `Expires` à partir des points de présence, et les envoie au client HTTP. Par défaut, ces en-têtes remplacent ceux créés par le serveur d’origine. Toutefois, les fonctionnalités Traitement d’en-tête de contrôle de cache et Expiration du traitement d’en-tête peuvent servir à modifier ce comportement.

Informations essentielles :

- Cette action n’affecte pas les revalidations du cache entre le point de présence et le serveur d’origine. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` et `Expires` provenant du serveur d’origine, et peuvent être personnalisés avec les fonctionnalités Default Internal Max-Age et Force Internal Max-Age.
- Configurez cette fonctionnalité en spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.).
- Définissez cette fonctionnalité sur une valeur négative pour que les points de présence envoient un `Cache-Control: no-cache`, ainsi qu’une heure `Expires` passée avec chaque réponse envoyée au navigateur. Même si un client HTTP ne met pas en cache la réponse, ce paramètre n’affecte pas la capacité des points de présence à mettre en cache la réponse provenant du serveur d’origine.
- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Les en-têtes `Cache-Control` et `Expires` avec la réponse du serveur d’origine sont transmis directement au navigateur.

**Comportement par défaut :** Off

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Suivre les redirections

**Objectif :** Détermine si les requêtes peuvent être redirigées vers le nom d’hôte défini dans l’en-tête Location retourné par un serveur d’origine du client.

Informations essentielles :

- Les requêtes peuvent uniquement être redirigées vers les URL CNAME Edge qui correspondent à la même plateforme.

Valeur|Résultats
-|-
activé|Les requêtes peuvent être redirigées.
Désactivé|Les requêtes ne seront pas redirigées.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Forcer l’âge maximal interne

**Objectif :** Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le point de présence et le serveur d’origine. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un point de présence ne vérifie si une ressource mise en cache correspond à la ressource stockée sur le serveur d’origine.

Informations essentielles :

- Cette fonctionnalité remplace l’intervalle d’âge maximal (max-age interval) défini dans les en-têtes `Cache-Control` ou `Expires` générés à partir d’un serveur d’origine.
- Cette fonctionnalité n’affecte pas les revalidations du cache entre le navigateur et le point de présence. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` ou `Expires` envoyés au navigateur.
- Cette fonctionnalité n’a pas d’effet visible sur la réponse fournie par un point de présence au demandeur. Toutefois, elle peut avoir une incidence sur la quantité de trafic de revalidation envoyée à partir des points de présence vers le serveur d’origine.
- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel un âge maximal interne sera appliqué.
    - spécifiant une valeur entière et en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’intervalle d’âge maximal de la requête.

- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Un intervalle d’âge maximal interne n’est pas attribué aux ressources demandées. Si l’en-tête d’origine ne contient pas d’instructions de mise en cache, la ressource est mise en cache en fonction du paramètre actif de la fonctionnalité Âge maximal interne par défaut.

**Comportement par défaut :** Off

#### <a name="compatibility"></a>Compatibilité

En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes :
- Numéro AS
- Adresse IP du client
- Paramètre de cookie
- Expression régulière de paramètre de cookie
- Country
- Appareil
- Cname Edge
- Domaine de référence
- Littéral d’en-tête de requête
- Expression régulière d’en-tête de requête
- Caractère générique d’en-tête de requête
- Méthode de requête
- Modèle de requête
- Littéral de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL
- Paramètre de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Prise en charge H.264 (téléchargement progressif HTTP)

**Objectif :** Détermine les types de formats de fichier H.264 qui peuvent être utilisés pour diffuser du contenu.

Informations essentielles :

- Définissez un ensemble d’extensions de noms de fichier H.264 autorisées séparé par un espace dans l’option Extensions de fichier. Cette option remplacera le comportement par défaut. Maintenez la prise en charge de MP4 et F4V en incluant ces extensions de noms de fichier lors de la définition de cette option.
- Incluez un point lorsque vous spécifiez une extension de nom de fichier (par exemple, _.mp4_, _.f4v_).

**Comportement par défaut :** le téléchargement progressif HTTP prend en charge les formats multimédia MP4 et F4V par défaut.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honorer la requête non-cache

**Objectif :** Détermine si les requêtes non-cache d’un client HTTP sont transmises au serveur d’origine.

Une demande non-cache se produit lorsque le client HTTP envoie un en-tête `Cache-Control: no-cache` et/ou `Pragma: no-cache` dans la requête HTTP.

Valeur|Résultats
--|--
activé|Permet de transmettre une requête non cache d’un client HTTP au serveur d’origine. Le serveur d’origine retourne alors les en-têtes et le corps de la réponse via le point de présence au client HTTP.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à éviter la transmission des requêtes non cache au serveur d’origine.

Pour tout le trafic de production, il est fortement recommandé de laisser cette fonctionnalité désactivée par défaut. Dans le cas contraire, les serveurs d’origine ne sont pas protégés des utilisateurs finaux qui peuvent par inadvertance déclencher un grand nombre de requêtes non cache lors de l’actualisation des pages web ou des nombreux lecteurs multimédias populaires codés pour envoyer un en-tête non cache avec chaque requête vidéo. Néanmoins, il peut être utile d’appliquer cette fonctionnalité à certains répertoires de test ou intermédiaires hors production pour autoriser la collecte de contenu actualisé à partir du serveur d’origine à la demande.

L’état du cache qui est signalé pour une requête, dont le transfert vers un serveur d’origine est autorisé en raison de cette fonctionnalité, est `TCP_Client_Refresh_Miss`. Le rapport des états du cache, qui est disponible dans le module de création de rapports de base, fournit des informations statistiques par état de cache. Ce rapport vous permet de suivre le nombre et le pourcentage de requêtes transférées vers un serveur d’origine en raison de cette fonctionnalité.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignorer la requête non-cache d’origine

**Objectif :** détermine si le CDN ignore les directives suivantes remises par un serveur d’origine :

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informations essentielles :

- Configurez cette fonctionnalité en définissant une liste délimitée par un espace des codes d’état pour lequel les directives ci-dessus seront ignorées.
- L’ensemble des codes d’état valides pour cette fonctionnalité sont les suivants : 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 et 505.
- Désactivez cette fonctionnalité en lui attribuant une valeur vide.

**Comportement par défaut :** le comportement par défaut consiste à respecter les directives ci-dessus.

#### <a name="compatibility"></a>Compatibilité

En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes :
- Numéro AS
- Adresse IP du client
- Paramètre de cookie
- Expression régulière de paramètre de cookie
- Country
- Appareil
- Cname Edge
- Domaine de référence
- Littéral d’en-tête de requête
- Expression régulière d’en-tête de requête
- Caractère générique d’en-tête de requête
- Méthode de requête
- Modèle de requête
- Littéral de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL
- Paramètre de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignorer les plages inacceptables

**Objectif :** Détermine la réponse à retourner aux clients quand une requête génère un code d’état 416 Plage demandée non satisfaisante.

Par défaut, ce code d’état est retourné lorsque la requête de plage d’octets spécifiée ne peut pas être satisfaite par un point de présence et qu’un champ d’en-tête de requête If-Range n’a pas été spécifié.

Valeur|Résultats
-|-
activé|Empêche les points de présence de répondre à une requête de plage d’octets non valide avec un code d’état 416 Plage demandée non satisfaisante. Au lieu de cela, les serveurs fournissent la ressource demandée et retournent le code 200 - OK au client.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à respecter le code d’état 416 Plage demandée non satisfaisante.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Obsolescence maximale interne

**Objectif :** Contrôle la durée après l’expiration normale d’une ressource mise en cache pendant laquelle cette ressource peut être remise depuis un point de présence quand ce point de présence ne parvient pas à la revalider avec le serveur d’origine.

Normalement, lorsque l’élément max-age d’une ressource arrive à expiration, le point de présence envoie une requête de revalidation au serveur d’origine. Le serveur d’origine répond ensuite par un 304 Non modifié pour permettre au point de présence d’exploiter la ressource mise en cache, ou un 200 OK pour fournir au point de présence une version mise à jour de la ressource mise en cache.

Si le point de présence n’est pas en mesure d’établir une connexion avec le serveur d’origine lors de la tentative d’une telle revalidation, cette fonctionnalité Obsolescence maximale interne contrôle si et depuis combien de temps, le point de présence peut continuer à traiter la ressource now-stale.

Notez que cet intervalle de temps démarre à compter de l’expiration de l’âge maximal de la ressource et non lors de l’échec d’une revalidation. Par conséquent, la durée maximale pendant laquelle une ressource peut être traitée sans réussir la revalidation correspond à la durée spécifiée par la combinaison max-age/max-stale. Par exemple, si une ressource a été mise en cache à 9 h 00, avec un âge maximal (max-age) de 30 minutes et une obsolescence maximale de 15 minutes, l’échec d’une tentative de revalidation à 9 h 44 entraînerait la réception par l’utilisateur final de la ressource mise en cache obsolète, tandis que l’échec d’une tentative de revalidation à 9 h 46 entraînerait la réception par l’utilisateur final d’un 504 Expiration de la passerelle.

Les valeurs configurées pour cette fonctionnalité sont remplacées par les en-têtes `Cache-Control: must-revalidate` ou `Cache-Control: proxy-revalidate` reçus du serveur d’origine. Si l’un de ces en-têtes issus du serveur d’origine est reçu lors de la mise en cache initiale d’une ressource, le point de présence ne traite pas de ressource mise en cache obsolète. Dans ce cas, si le point de présence n’est pas en mesure de revalider avec le serveur d’origine après expiration de l’intervalle d’âge maximal de la ressource, le point de présence retourne une erreur 504 Expiration de la passerelle.

Informations essentielles :

- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel une obsolescence maximale sera appliquée.
    - spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’obsolescence maximale qui sera appliquée.

- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Une ressource mise en cache ne sera pas traitée au-delà de son délai d’expiration normal.

**Comportement par défaut :** deux minutes

#### <a name="compatibility"></a>Compatibilité

En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes :
- Numéro AS
- Adresse IP du client
- Paramètre de cookie
- Expression régulière de paramètre de cookie
- Country
- Appareil
- Cname Edge
- Domaine de référence
- Littéral d’en-tête de requête
- Expression régulière d’en-tête de requête
- Caractère générique d’en-tête de requête
- Méthode de requête
- Modèle de requête
- Littéral de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL
- Paramètre de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Journaliser la chaîne de requête

**Objectif :** Détermine si une chaîne de requête est stockée avec l’URL dans les journaux d’activité d’accès.

Valeur|Résultats
-|-
activé|Permet le stockage des chaînes de requête lors de l’enregistrement des URL dans un journal d’accès. Si une URL ne contient pas de chaîne de requête, cette option n’aura pas d’effet.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à ignorer les chaînes de requête lors de l’enregistrement des URL dans un journal d’accès.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Nombre maximal de requêtes toujours actives

**Objectif :** Définit le nombre maximal de requêtes pour une connexion toujours active avant sa fermeture.

Définir le nombre maximal de requêtes sur une valeur faible est déconseillé et peut entraîner une dégradation des performances.

Informations essentielles :

- Spécifiez cette valeur sous forme d’entier.
- N’incluez pas de virgule ou de point dans la valeur spécifiée.

**Valeur par défaut :** 10 000 requêtes

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modifier l’en-tête de requête client

**Objectif :** chaque requête contient un ensemble d’en-têtes de requête qui la décrivent. Cette fonctionnalité peut soit :

- Ajouter ou remplacer la valeur affectée à un en-tête de requête. Si l’en-tête de requête spécifié n’existe pas, cette fonctionnalité l’ajoute à la requête.
- Supprimez un en-tête de requête de la requête.

Les requêtes transmises à un serveur d’origine reflètent les modifications apportées par cette fonctionnalité.

Une des actions suivantes peut être effectuée sur un en-tête de requête :

Option|Description|Exemple
-|-|-
Ajouter|La valeur spécifiée sera ajoutée à la fin de la valeur d’en-tête de requête existante.|**Valeur d’en-tête de requête (client) :**<br/>Value1<br/>**Valeur d’en-tête de requête (moteur de règles) :**<br/>Value2 <br/>**Nouvelle valeur d’en-tête de requête :** <br/>Value1Value2
Remplacer|La valeur d’en-tête de requête est définie sur la valeur spécifiée.|**Valeur d’en-tête de requête (client) :**<br/>Value1<br/>**Valeur d’en-tête de requête (moteur de règles) :**<br/>Value2<br/>**Nouvelle valeur d’en-tête de requête :**<br/> Value2 <br/>
DELETE|Supprime l’en-tête de requête spécifié.|**Valeur d’en-tête de requête (client) :**<br/>Value1<br/>**Modifier la configuration de l’en-tête de requête client :**<br/>supprime l’en-tête de requête en question.<br/>**Résultat :**<br/>l’en-tête de requête spécifié ne sera pas transféré vers le serveur d’origine.

Informations essentielles :

- Veillez à ce que la valeur spécifiée dans l’option Nom corresponde exactement à l’en-tête de requête voulu.
- La casse n’est pas prise en compte pour l’identification d’un en-tête. Par exemple, l’une des variantes du nom de l’en-tête `Cache-Control` ci-dessous peut servir à l’identifier :
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Lorsque vous spécifiez un nom d’en-tête, utilisez uniquement des caractères alphanumériques, des tirets ou des traits de soulignement.
- La suppression d’un en-tête évite sa transmission à un serveur d’origine par les points de présence.
- Les en-têtes suivants sont réservés et ne peuvent pas être modifiés par cette fonctionnalité :
    - forwarded
    - host
    - via
    - avertissement
    - x-forwarded-for
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modifier l’en-tête de réponse client

Chaque réponse contient un ensemble d’en-têtes de réponse qui la décrivent. Cette fonctionnalité peut soit :

- Ajouter ou remplacer la valeur affectée à un en-tête de réponse. Si l’en-tête de requête spécifié n’existe pas, cette fonctionnalité l’ajoute à la réponse.
- Supprimez un en-tête de réponse de la réponse.

Par défaut, les valeurs d’en-tête de réponse sont définies par un serveur d’origine et par les points de présence.

Une des actions suivantes peut être effectuée sur un en-tête de réponse :

Option|Description|Exemple
-|-|-
Ajouter|La valeur spécifiée sera ajoutée à la fin de la valeur d’en-tête de réponse existante.|**Valeur d’en-tête de réponse (client) :**<br />Value1<br/>**Valeur d’en-tête de réponse (moteur de règles) :**<br/>Value2<br/>**Nouvelle valeur d’en-tête de réponse :**<br/>Value1Value2
Remplacer|La valeur d’en-tête de réponse est définie sur la valeur spécifiée.|**Valeur d’en-tête de réponse (client) :**<br/>Value1<br/>**Valeur d’en-tête de réponse (moteur de règles) :**<br/>Value2 <br/>**Nouvelle valeur d’en-tête de réponse :**<br/>Value2 <br/>
DELETE|Supprime l’en-tête de réponse spécifiée.|**Valeur d’en-tête de réponse (client) :**<br/>Value1<br/>**Modifier la configuration de l’en-tête de réponse client :**<br/>supprime l’en-tête de réponse en question.<br/>**Résultat :**<br/>l’en-tête de réponse spécifié ne sera pas transféré vers le demandeur.

Informations essentielles :

- Veillez à ce que la valeur spécifiée dans l’option Nom corresponde exactement à l’en-tête de réponse voulu.
- La casse n’est pas prise en compte pour l’identification d’un en-tête. Par exemple, l’une des variantes du nom de l’en-tête `Cache-Control` ci-dessous peut servir à l’identifier :
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- La suppression d’un en-tête évite son transfert au demandeur.
- Les en-têtes suivants sont réservés et ne peuvent pas être modifiés par cette fonctionnalité :
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - Date
    - server
    - trailer
    - transfer-encoding
    - upgrade
    - vary
    - via
    - avertissement
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partage de cache partiel

**Objectif :** Détermine si une requête peut générer du contenu partiellement mis en cache.

Ce cache partiel peut ensuite être utilisé pour répondre aux nouvelles requêtes de ce contenu jusqu’à ce que le contenu demandé soit entièrement mis en cache.

Valeur|Résultats
-|-
activé|Les requêtes peuvent générer du contenu partiellement mis en cache.
Désactivé|Les requêtes peuvent uniquement générer une version entièrement mise en cache du contenu demandé.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Prévalider le contenu mis en cache

**Objectif :** Détermine si du contenu mis en cache peut faire l’objet d’une revalidation anticipée avant l’expiration de sa durée de vie.

Définissez la durée avant expiration de la durée de vie du contenu demandé au cours de laquelle il pourra faire l’objet d’une revalidation anticipée.

Informations essentielles :

- Sélectionnez « Désactivé », dans la mesure où l’unité de temps nécessite la revalidation une fois la durée de vie du contenu mis en cache expirée. Le temps ne doit pas être spécifié, et est ignoré.

**Comportement par défaut :** désactivé. La revalidation peut uniquement avoir lieu après l’expiration de la durée de vie du contenu mis en cache.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>En-têtes spéciaux de proxy

**Objectif :** définit l’ensemble des [en-têtes de requête HTTP spécifiques à Verizon](cdn-verizon-http-headers.md) devant être transmis depuis un point de présence vers un serveur d’origine.

Informations essentielles :

- Chaque en-tête de requête CDN spécifique défini dans cette fonctionnalité est transmis à un serveur d’origine. Les en-têtes exclus ne sont pas transférés.
- Pour empêcher qu’un en-tête de requête propre à CDN ne soit transféré, vous devez le supprimer de la liste séparée par des espaces dans le champ de la liste des en-têtes.

Les en-têtes HTTP suivants sont inclus dans la liste par défaut :
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- Host

**Comportement par défaut :** tous les en-têtes de requête propres à CDN sont transmis au serveur d’origine.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Actualiser les fichiers de cache de zéro octet

**Objectif :** Détermine la façon dont les points de présence gèrent la requête d’un client HTTP liée à une ressource de cache de 0 octet.

Les valeurs autorisées sont :

Valeur|Résultats
--|--
activé|Force le point de présence à récupérer de nouveau la ressource à partir du serveur d’origine.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à fournir des ressources de cache valides sur requête.

Cette fonctionnalité n’est pas requise pour la mise en cache correcte et la distribution de contenu, mais peut être utile comme solution de contournement. Par exemple, des générateurs de contenu dynamique sur des serveurs d’origine peuvent entraîner par inadvertance l’envoi de réponses de 0 octet aux points de présence. Ces types de réponses sont généralement mis en cache par les points de réponse. Si vous savez qu’une réponse de 0 octet n’est jamais une réponse valide pour ce type de contenu, cette fonctionnalité peut empêcher le traitement de ces types de ressources sur vos clients.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Définir les codes d’état pouvant être mis en cache

**Objectif :** Définit l’ensemble des codes d’état qui peuvent générer du contenu mis en cache.

Par défaut, la mise en cache est uniquement activée pour les réponses 200 OK.

Définissez un ensemble de codes d’état souhaités séparés par un espace.

Informations essentielles :

- Activez la fonctionnalité Ignorer la requête non-cache d’origine. Si cette fonctionnalité n’est pas activée, les réponses non 200 OK ne peuvent pas être mises en cache.
- L’ensemble des codes d’état valides pour cette fonctionnalité sont les suivants : 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 et 505.
- Cette fonctionnalité ne peut pas être utilisée pour désactiver la mise en cache des réponses qui génèrent un code d’état 200 OK.

**Comportement par défaut :** la mise en cache est activée uniquement pour les réponses qui génèrent un code d’état 200 OK.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Définir un en-tête personnalisé d’adresse IP client

**Objectif :** ajoute un en-tête personnalisé qui identifie le client demandeur par adresse IP à la requête.

L’option de nom d’en-tête définit le nom de l’en-tête de requête personnalisée où est stockée l’adresse IP du client.

Cette fonctionnalité permet à un serveur d’origine du client de trouver les adresses IP du client via un en-tête de requête personnalisé. Si la requête est traitée à partir du cache, le serveur d’origine n’est pas informé de l’adresse IP du client. Par conséquent, il est recommandé que cette fonctionnalité soit utilisée avec les ressources qui ne sont pas mises en cache.

Assurez-vous que le nom d’en-tête spécifié ne correspond pas à un des noms suivants :

- Noms d’en-tête de requête standard. Vous trouverez une liste de noms d’en-tête standard dans [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Noms d’en-tête réservés :
    - forwarded-for
    - host
    - vary
    - via
    - avertissement
    - x-forwarded-for
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Rendre obsolète la remise de contenu en cas d’erreur

**Objectif :** Détermine si du contenu mis en cache qui a expiré est remis quand une erreur se produit pendant la revalidation du cache ou l’extraction du contenu demandé à partir du serveur d’origine du client.

Valeur|Résultats
-|-
activé|Le contenu est rendu obsolète pour le demandeur lorsqu’une erreur se produit lors d’une connexion à un serveur d’origine.
Désactivé|L’erreur du serveur d’origine est transférée au demandeur.

**Comportement par défaut :** Désactivé

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Rendre obsolète pendant la revalidation

**Objectif :** améliore les performances en permettant aux points de présence de rendre obsolète le contenu pour le demandeur pendant la revalidation.

Informations essentielles :

- Le comportement de cette fonctionnalité varie en fonction de l’unité de temps sélectionnée.
    - **Unité de temps :** spécifiez une durée, puis sélectionnez une unité de temps (par exemple, secondes, minutes, heures, etc.) pour permettre la distribution de contenu obsolète. Ce type d’installation permet au CDN d’étendre la durée pendant laquelle il peut fournir du contenu avant de demander une validation en fonction de la formule suivante : **TTL** + **Rendre obsolète pendant la revalidation**
    - **Off :** sélectionnez « Désactivé » pour demander la revalidation avant qu’une requête de contenu obsolète puisse être traitée.
        - Ne spécifiez pas de durée, car elle est inapplicable et sera ignorée.

**Comportement par défaut :** désactivé. La revalidation doit intervenir avant que le contenu demandé puisse être traité.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Authentification de jeton

**Objectif :** Détermine si l’authentification basée sur les jetons est appliquée à une requête.

Si l’authentification basée sur les jetons est activée, seules les requêtes qui fournissent un jeton chiffré et se conforment aux exigences définies par ce jeton seront respectées.

La clé de chiffrement qui est utilisée pour chiffrer et déchiffrer des valeurs de jeton est déterminée par les options de la clé primaire et de la clé de sauvegarde dans la page d’authentification du jeton. N’oubliez pas que les clés de chiffrement sont spécifiques de la plateforme.

**Comportement par défaut :** Désactivé.

Cette fonctionnalité est prioritaire sur la plupart des fonctionnalités à l’exception de la fonctionnalité de réécriture d’URL.

Valeur | Résultats
------|---------
activé | Protège le contenu demandé avec l’authentification basée sur les jetons. Seules les demandes des clients qui fournissent un jeton valide et répondent aux exigences seront respectées. Les transactions FTP sont exclues de l’authentification basée sur les jetons.
Désactivé| Restaure le comportement par défaut. Le comportement par défaut consiste à permettre à votre configuration d’authentification basée sur les jetons de déterminer si une demande sera sécurisée.

#### <a name="compatibility"></a>Compatibilité

N’utilisez pas Token Auth avec une condition de correspondance Always.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Code de refus d’authentification de jeton

**Objectif :** Détermine le type de réponse à retourner à un utilisateur quand une requête est refusée en raison de l’authentification basée sur les jetons.

Les codes de réponse disponibles sont répertoriés dans la table suivante.

Code de la réponse|Nom de la réponse|Description
-------------|-------------|--------
301|Déplacé de façon permanente|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location.
302|Trouvé|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location. Ce code d’état est la méthode standard du secteur en termes d’exécution d’une redirection.
307|Redirection temporaire|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location.
401|Non autorisé|L’association de ce code d’état avec l’en-tête de réponse WWW-Authenticate vous permet d’inviter un utilisateur à procéder à une authentification.
403|Interdit|Il s’agit du message d’état 403 Interdit standard qu’un utilisateur non autorisé voit lorsqu’il tente d’accéder à un contenu protégé.
404|Fichier introuvable|Ce code d’état indique que le client HTTP a été en mesure de communiquer avec le serveur, mais que le contenu demandé est introuvable.

#### <a name="compatibility"></a>Compatibilité

N’utilisez pas Token Auth Denial Code avec une condition de correspondance Always. Au lieu de cela, utilisez la section **Gestion des refus personnalisés** dans la page **Authentification du jeton** du portail **Gérer**. Pour plus d’informations, consultez [Sécurisation des ressources CDN Azure avec l’authentification du jeton](cdn-token-auth.md).

#### <a name="url-redirection"></a>Redirection d’URL

Cette fonctionnalité prend en charge la redirection de l’URL vers une URL définie par l’utilisateur lorsqu’elle est configurée pour renvoyer un code d’état 3xx. Cette URL définie par l’utilisateur peut être spécifiée en effectuant les opérations suivantes :

1. Sélectionnez un code de réponse 3xx pour la fonctionnalité Code de refus d’authentification de jeton.
2. Sélectionnez « Location » à partir de l’option de nom de l’en-tête facultatif.
3. Définissez l’option Optional Header Value (Valeur d’en-tête facultatif) sur l’URL souhaitée.

Si une URL n’est pas définie pour un code d’état 3xx, la page de réponse standard pour un code d’état 3xx sera renvoyée à l’utilisateur.

La redirection des URL est uniquement applicable pour les codes de réponse 3xx.

L’option de valeur d’en-tête facultative prend en charge les caractères alphanumériques, les guillemets et les espaces.

#### <a name="authentication"></a>Authentification

Cette fonctionnalité prend en charge la capacité à inclure l’en-tête WWW-Authenticate lors de la réponse à une demande non autorisée pour un contenu protégé par l’authentification basée sur les jetons. Si l’en-tête WWW-Authenticate a été défini sur « de base » dans votre configuration, l’utilisateur non autorisé est invité à entrer des informations d’identification de compte.

La configuration ci-dessus peut être obtenue en effectuant les opérations suivantes :

1. Sélectionnez « 401 » comme code de réponse pour la fonctionnalité Code de refus d’authentification de jeton.
2. Sélectionnez « WWW-Authenticate » à partir de l’option de nom de l’en-tête facultatif.
3. Définissez l’option Optional Header Value (Valeur d’en-tête facultatif) sur « de base ».

L’en-tête WWW-Authenticate est uniquement applicable pour les codes de réponse 401.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Ignorer la casse de l’URL pour l’authentification de jeton

**Objectif :** Détermine si les comparaisons d’URL effectuées par l’authentification basée sur les jetons respectent la casse.

Les paramètres affectés par cette fonction sont :

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Les valeurs autorisées sont :

Valeur|Résultats
---|----
activé|Force le point de présence à ignorer la casse lors de la comparaison des URL pour les paramètres d’authentification basée sur les jetons.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut pour les comparaisons d’URL pour l’authentification du jeton consiste à respecter la casse.

**Comportement par défaut :** Désactivé.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Paramètre d’authentification de jeton

**Objectif :** Détermine si le paramètre de chaîne de requête de l’authentification basée sur les jetons doit être renommé.

Informations essentielles :

- L’option Valeur définit le nom de paramètre de chaîne de requête par le biais duquel un jeton peut être spécifié.
- L’option Valeur ne peut pas être définie sur « ec_token ».
- Assurez-vous que le nom défini dans l’option Valeur contient uniquement des caractères d’URL valides.

Valeur|Résultats
----|----
activé|L’option Valeur définit le nom de paramètre de chaîne de requête par le biais duquel les jetons doivent être définis.
Désactivé|Un jeton peut être spécifié comme un paramètre de chaîne de requête non défini dans l’URL de requête.

**Comportement par défaut :** Désactivé. Un jeton peut être spécifié comme un paramètre de chaîne de requête non défini dans l’URL de requête.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Redirection d'URL

**Objectif :** Redirige les requêtes via l’en-tête Location.

La configuration de cette fonctionnalité nécessite de définir les options suivantes :

Option|Description
-|-
Code|Sélectionnez le code de réponse qui sera renvoyé au demandeur.
Source et modèle| Ces paramètres définissent un modèle d’URI de requête qui identifie le type de requêtes pouvant être redirigées. Seules les requêtes dont l’URL satisfait aux deux critères suivants seront redirigées : <br/> <br/> **Source (ou point d’accès au contenu) :** sélectionnez un chemin relatif qui identifie un serveur d’origine. Il s’agit de la section _/XXXX/_ et de votre nom de point de terminaison. <br/><br/> **Source (modèle) :** un modèle qui identifie les requêtes via un chemin relatif doit être défini. Ce modèle d’expression régulière doit définir un chemin d’accès commençant directement après le point d’accès au contenu sélectionné précédemment (voir ci-dessus). <br/> - Vérifiez que les critères d’URI de requête (c’est-à-dire, Source et Modèle) définis précédemment ne sont pas en conflit avec les conditions de correspondance définies pour cette fonctionnalité. <br/> - Spécifiez un modèle. Si vous utilisez une valeur vide comme modèle, toutes les chaînes sont mises en correspondance.
Destination| Définissez l’URL vers laquelle les requêtes ci-dessus seront redirigées. <br/><br/> Construisez dynamiquement cette URL à l’aide des éléments suivants : <br/> - Un modèle d’expression régulière <br/>- [Variables HTTP](cdn-http-variables.md) <br/><br/> Remplacez les valeurs capturées dans le modèle source dans le modèle de destination à l’aide de $_n_ où _n_ identifie une valeur par l’ordre dans lequel elle a été capturée. Par exemple, $1 représente la première valeur capturée dans le modèle source, tandis que $2 représente la deuxième valeur. <br/>

Il est fortement recommandé d’utiliser une URL absolue. L’utilisation d’une URL relative peut rediriger les URL CDN vers un chemin d’accès non valide.

**Exemple de scénario**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge qui correspond à l’URL CDN de base suivante : http:\//marketing.azureedge.net/brochures

Les requêtes éligibles sont redirigées vers l’URL CNAME Edge de base suivante : http:\//cdn.mydomain.com/resources

Cette redirection d’URL peut être obtenue via la configuration suivante : ![Redirection d’URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Points essentiels :**

- La fonctionnalité Redirection d’URL définit les URL de requête à rediriger. Par conséquent, aucune condition de correspondance supplémentaire n’est requise. Bien que la condition de correspondance ait été définie en tant que « Toujours », seules les requêtes pointant vers le dossier « brochures » du serveur d’origine du client « marketing » s’afficheront.
- Toutes les requêtes correspondantes seront redirigées vers l’URL CNAME Edge définie dans l’option Destination.
    - Exemple de scénario n°1 :
        - Exemple de requête (URL CDN) : http:\//marketing.azureedge.net/brochures/widgets.pdf
        - URL de requête (après redirection) : http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Exemple de scénario n°2 :
        - Exemple de requête (URL CNAME Edge) : http:\//marketing.mydomain.com/brochures/widgets.pdf
        - URL de requête (après redirection) : http:\//cdn.mydomain.com/resources/widgets.pdf  Exemple de scénario
    - Exemple de scénario n°3 :
        - Exemple de requête (URL CNAME Edge) : http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - URL de requête (après redirection) : http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- La variable Request Scheme (%{scheme}) est utilisée dans l’option Destination, qui garantit que le schéma de la requête reste inchangé après la redirection.
- Les segments d’URL qui ont été capturés à partir de la requête sont ajoutés à la nouvelle URL via « $1 ».

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Réécriture d’URL

**Objectif :** Réécrit l’URL de la requête.

Informations essentielles :

- La configuration de cette fonctionnalité nécessite de définir les options suivantes :

Option|Description
-|-
 Source et modèle | Ces paramètres définissent un modèle d’URI de requête qui identifie le type de requêtes pouvant être réécrites. Seules les requêtes dont l’URL satisfait aux deux critères suivants seront réécrites : <br/><br/>  - **Source (ou point d’accès au contenu) :** sélectionnez un chemin relatif qui identifie un serveur d’origine. Il s’agit de la section _/XXXX/_ et de votre nom de point de terminaison. <br/><br/> - **Source (modèle) :** un modèle qui identifie les requêtes via un chemin relatif doit être défini. Ce modèle d’expression régulière doit définir un chemin d’accès commençant directement après le point d’accès au contenu sélectionné précédemment (voir ci-dessus). <br/> Vérifiez que les critères d’URI de requête (c’est-à-dire, Source et Modèle) définis précédemment ne sont pas en conflit avec les conditions de correspondance définies pour cette fonctionnalité. Spécifiez un modèle. Si vous utilisez une valeur vide comme modèle, toutes les chaînes sont mises en correspondance.
 Destination  |Définissez l’URL relative vers laquelle les requêtes ci-dessus seront réécrites en : <br/>    1. Sélectionnant un point d’accès au contenu qui identifie un serveur d’origine. <br/>    2. Définissant un chemin d’accès relatif à l’aide des éléments suivants : <br/>        - Un modèle d’expression régulière <br/>        - [Variables HTTP](cdn-http-variables.md) <br/> <br/> Remplacez les valeurs capturées dans le modèle source dans le modèle de destination à l’aide de $_n_ où _n_ identifie une valeur par l’ordre dans lequel elle a été capturée. Par exemple, $1 représente la première valeur capturée dans le modèle source, tandis que $2 représente la deuxième valeur.

 Cette fonctionnalité permet aux points de présence de réécrire l’URL sans effectuer de redirection classique. Cela signifie que le demandeur reçoit un code de réponse identique à celui reçu si l’URL réécrite avait été demandée.

**Exemple de scénario nº 1**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge qui correspond à l’URL CDN de base suivante : http:\//marketing.azureedge.net/brochures/

Les requêtes éligibles sont redirigées vers l’URL CNAME Edge de base suivante : http:\//MyOrigin.azureedge.net/resources/

Cette redirection d’URL peut être obtenue via la configuration suivante : ![Redirection d’URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exemple de scénario nº 2**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge en majuscules vers son équivalent en minuscules à l’aide d’expressions régulières.

Cette redirection d’URL peut être obtenue via la configuration suivante : ![Redirection d’URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Points essentiels :**

- La fonctionnalité Réécriture d’URL définit les URL de demande à réécrire. Par conséquent, aucune condition de correspondance supplémentaire n’est requise. Bien que la condition de correspondance ait été définie sur « Toujours », seules les demandes pointant vers le dossier « brochures » du serveur d’origine du client « marketing » seront réécrites.

- Les segments d’URL qui ont été capturés à partir de la requête sont ajoutés à la nouvelle URL via « $1 ».

#### <a name="compatibility"></a>Compatibilité

Cette fonctionnalité inclut des critères de correspondance devant être remplis avant de pouvoir l’appliquer à une requête. Pour empêcher la définition de critères de correspondance conflictuels, cette fonctionnalité est incompatible avec les conditions de correspondance suivantes :

- Numéro AS
- Origine CDN
- Adresse IP du client
- Origine client
- Modèle de requête
- Répertoire du chemin d’URL
- Extension du chemin d’URL
- Nom de fichier du chemin d’URL
- Littéral du chemin d’URL
- Expression régulière du chemin d’URL
- Caractère générique du chemin d’URL
- Littéral de requête d’URL
- Paramètre de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Variable utilisateur

**Objectif :** À usage interne uniquement.

[Retour au début](#azure-cdn-from-verizon-premium-rules-engine-features)
## <a name="next-steps"></a>Étapes suivantes

- [Informations de référence du moteur de règles](cdn-verizon-premium-rules-engine-reference.md)
- [Expressions conditionnelles du moteur de règles](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Conditions de correspondance du moteur de règles](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Remplacement du comportement HTTP à l’aide du moteur de règles](cdn-verizon-premium-rules-engine.md)
- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
