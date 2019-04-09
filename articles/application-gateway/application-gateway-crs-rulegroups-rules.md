---
title: Règles et groupes de règles CRS de pare-feu d’applications web Azure Application Gateway
description: Cette page fournit des informations sur les règles et groupes CRS de pare-feu d’applications web.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 61ab41eed7703c82c2e5ef2a3b5412a9f56389ba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279701"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Liste des règles et groupes de règles CRS de pare-feu d’applications web proposées

Le pare-feu d’applications web Application Gateway protège les applications web contre les vulnérabilités et failles de sécurité. Cette protection s’effectue via des règles définies basées sur les ensembles de règles de base OWASP 2.2.9 ou 3.0. Ces règles peuvent être désactivées une par une. Cet article contient les règles et ensembles de règles actuellement proposées.

Les tableaux suivants correspondent aux groupes de règles et règles disponibles lors de l’utilisation de la passerelle d’Application Gateway avec le pare-feu d’applications web.  Chaque tableau représente les règles trouvées dans un groupe de règles pour une version CRS spécifique.

## <a name="owasp30"></a> OWASP_3.0

### <a name="General"></a> <p x-ms-format-detection="none">Généralités</p>

|ID de la règle|Description|
|---|---|
|200004|Possible limite multipart sans correspondance.|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|ID de la règle|Description|
|---|---|
|911100|Méthode non autorisée par la stratégie|


### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|ID de la règle|Description|
|---|---|
|913100|Détection d’agent utilisateur associé au scanner de sécurité|
|913110|Détection d’en-tête de requête associé au scanner de sécurité|
|913120|Détection de nom de fichier/argument associé au scanner de sécurité|
|913101|Détection d’agent utilisateur associé aux scripts/client HTTP générique|
|913102|Détection d’agent utilisateur associé à l’analyseur web/robot|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ID de la règle|Description|
|---|---|
|920100|Ligne de requête HTTP non valide|
|920130|Échec de l’analyse du corps de la requête.|
|920140|Échec de la validation stricte du corps de la requête en plusieurs parties  =     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|L’en-tête HTTP Content-Length n’est pas numérique.|
|920170|Requête GET ou HEAD avec contenu du corps.|
|920180|En-tête Content-Length manquant dans la requête POST.|
|920190|Plage = dernière valeur d’octet non valide.|
|920210|Détection de données d’en-tête de connexion en conflit/multiples.|
|920220|Tentative d’attaque abusive d’encodage d’URL|
|920240|Tentative d’attaque abusive d’encodage d’URL|
|920250|Tentative d’attaque abusive d’encodage UTF8|
|920260|Tentative d’attaque abusive Pleine/Moyenne largeur Unicode|
|920270|Caractère non valide dans la requête (caractère null)|
|920280|En-tête d’hôte manquant dans la requête|
|920290|En-tête d’hôte vide|
|920310|Requête contenant un en-tête Accept vide|
|920311|Requête contenant un en-tête Accept vide|
|920330|En-tête User-Agent vide|
|920340|Requête contenant du contenu mais dont l’en-tête Content-Type est manquant|
|920350|L’en-tête d’hôte est une adresse IP numérique|
|920380|Trop d’arguments dans la requête|
|920360|Nom d’argument trop long|
|920370|Valeur d’argument trop longue|
|920390|Taille totale d’arguments dépassée|
|920400|Taille du fichier chargé trop volumineuse|
|920410|Taille totale des fichiers chargés trop volumineuse|
|920420|Type de contenu de requête non autorisé par la stratégie|
|920430|Version du protocole HTTP non autorisée par la stratégie|
|920440|Extension de fichier URL limitée par la stratégie|
|920450|En-tête HTTP limité par la stratégie (%@{MATCHED_VAR})|
|920200|Plage = Champs trop nombreux (6 ou plus)|
|920201|Plage = Champs trop nombreux pour la requête PDF (35 ou plus)|
|920230|Détection d’encodage de plusieurs URL|
|920300|En-tête Accept manquant dans la requête|
|920271|Caractère non valide dans la requête (caractères non imprimables)|
|920320|En-tête User-Agent manquant|
|920272|Caractère non valide dans la requête (en dehors des caractères imprimables avant ascii 127)|
|920202|Plage = Champs trop nombreux pour la requête PDF (6 ou plus)|
|920273|Caractère non valide dans la requête (en dehors de l’ensemble très strict)|
|920274|Caractère non valide dans les en-têtes de requête (en dehors de l’ensemble très strict)|
|920460|Caractères d’échappement anormale|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ID de la règle|Description|
|---|---|
|921100|Attaque par dissimulation de requête HTTP.|
|921110|Attaque par dissimulation de requête HTTP|
|921120|Attaque par fractionnement de réponse HTTP|
|921130|Attaque par fractionnement de réponse HTTP|
|921140|Attaque par injection d’en-tête HTTP via les en-têtes|
|921150|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921160|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF et nom d’en-tête détecté)|
|921151|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921170|Pollution du paramètre HTTP|
|921180|Pollution du paramètre HTTP (% @{TX.1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ID de la règle|Description|
|---|---|
|930100|Attaque par traversée de chemin (/../)|
|930110|Attaque par traversée de chemin (/../)|
|930120|Tentative d’accès au fichier du système d’exploitation|
|930130|Tentative d’accès au fichier restreint|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ID de la règle|Description|
|---|---|
|931100|Possible attaque par inclusion de fichier distant = Paramètre d’URL utilisant l’adresse IP|
|931110|Possible attaque par inclusion de fichier distant = Nom de paramètre vulnérable RFI commun utilisé avec la charge utile URL|
|931120|Possible attaque par inclusion de fichier distant = Charge utile URL utilisée avec caractère point d’interrogation de fin (?)|
|931130|Attaque possible par inclusion de fichier distant = Référence/Lien hors domaine|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ID de la règle|Description|
|---|---|
|932120|Exécution de la commande à distance = Commande Windows PowerShell détectée|
|932130|Exécution de la commande à distance = Expression Shell Unix détectée|
|932140|Exécution de la commande à distance = Commande Windows FOR/IF détectée|
|932160|Exécution de la commande à distance = Code Shell Unix détecté|
|932170|Exécution de la commande à distance = Shellshock (CVE-2014-6271)|
|932171|Exécution de la commande à distance = Shellshock (CVE-2014-6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ID de la règle|Description|
|---|---|
|933100|Attaque par injection de code PHP = Balise d’ouverture/fermeture détectée|
|933110|Attaque par injection de code PHP = Chargement de fichiers de script PHP détecté|
|933120|Attaque par injection de code PHP = Directive de configuration détectée|
|933130|Attaque par injection de code PHP = Variables détectées|
|933150|Attaque par injection de code PHP = Nom de fonction PHP à risque élevé détecté|
|933160|Attaque par injectionde code PHP = Nom d’appel de fonction PHP à risque élevé détecté|
|933180|Attaque par injection de code PHP = Appel de fonction variable détecté|
|933151|Attaque par injection de code PHP = Nom de fonction PHP à risque modéré détecté|
|933131|Attaque par injection de code PHP = Variables détectées|
|933161|Attaque par injection de code PHP = Appel de fonction PHP à risque faible détecté|
|933111|Attaque par injection de code PHP = Chargement de fichiers de script PHP détecté|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ID de la règle|Description|
|---|---|
|941100|Attaque XSS détectée via libinjection|
|941110|Filtre XSS - Catégorie 1 = vecteur de balise de script|
|941130|Filtre XSS - Catégorie 3 = vecteur d’attribut|
|941140|Filtre XSS - Catégorie 4 = vecteur URI Javascript|
|941150|Filtre XSS - Catégorie 5 = attributs HTML non autorisés|
|941180|Mots clés de la liste rouge du validateur de nœuds|
|941190|XSS à l’aide de feuilles de style|
|941200|XSS à l’aide d’images VML|
|941210|XSS à l’aide de Javascript obscurci|
|941220|À l’aide de XSS obscurcie Script VB|
|941230|Balise XSS à l’aide de « incorporer »|
|941240|XSS à l’aide d’attribut 'import' ou 'implementation'|
|941260|XSS à l’aide de la balise « meta »|
|941270|XSS à l’aide de href 'link /'|
|941280|XSS à l’aide de la balise 'base'|
|941290|XSS à l’aide de la balise « applet »|
|941300|XSS à l’aide de la balise 'object'|
|941310|Filtre XSS d’encodage incorrectement formé US-ASCII - Attaque détectée.|
|941330|Filtre XSS IE - Attaque détectée.|
|941340|Filtre XSS IE - Attaque détectée.|
|941350|XSS IE d’encodage UTF-7 - Attaque détectée.|
|941320|Possible attaque XSS détectée - Gestionnaire de balise HTML|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ID de la règle|Description|
|---|---|
|942100|Attaque par injection de code SQL détectée via libinjection|
|942110|Attaque par injection de code SQL : Test d’injection commune détecté|
|942130|Attaque par injection de code SQL : Tautologie SQL détectée.|
|942140|Attaque par injection de code SQL = Noms de base de données courants détectés|
|942160|Détecte des tests sqli à l’aveugle à l’aide de la méthode sleep() ou benchmarch().|
|942170|Détecte les tentatives d’injection par les méthodes benchmark (test d’évaluation) et sleep (mise en veille), y compris les requêtes conditionnelles|
|942190|Détecte les tentatives de collecte d’informations et de d’exécution de code MSSQL|
|942200|Détecte les injections MySQL de type comment-/space-obfuscated et d’accent grave|
|942230|Détecte des tentatives d’injection de code SQL conditionnel|
|942260|Détecte les tentatives de contournement d’authentification SQL de base 2/3|
|942270|Recherche d’injection de code SQL de base. Chaîne d’attaque courante pour mysql oracle entre autres.|
|942290|Recherche de tentatives d’injection de code SQL MongoDB de base|
|942300|Détecte les commentaires MySQL, les conditions et les injections de ch(a)r|
|942320|Détecte des injections de fonctions/procédures stockées MySQL et PostgresSQL|
|942330|Détecte les sondes d’injection SQL classiques 1/2|
|942340|Détecte les tentatives de contournement d’authentification SQL de base 3/3|
|942350|Détecte l’injection de code UDF MySQL et autres tentatives de manipulation de données/structures|
|942360|Détecte l’injection SQL de base concaténée et les tentatives SQLLFI|
|942370|Détecte les sondes d’injection SQL classiques 2/2|
|942150|Attaque par injection de code SQL|
|942410|Attaque par injection de code SQL|
|942430|Détection restreinte d’anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassés (12)|
|942440|Séquence de commentaire SQL détectée.|
|942450|Encodage hexadécimal SQL identifié|
|942251|Détecte des injections de code HAVING|
|942460|Alerte de détection d’anomalies de métacaractères - Caractères non textuels répétitifs|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ID de la règle|Description|
|---|---|
|943100|Possible attaque par fixation de session = Définition de valeurs de cookies en HTML|
|943110|Possible attaque par fixation de session = Nom du paramètre SessionID avec référent hors domaine|
|943120|Possible attaque par fixation de session = Nom du paramètre SessionID sans référent|

## <a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|ID de la règle|Description|
|---|---|
|960911|Ligne de requête HTTP non valide|
|981227|Erreur Apache = URI non valide dans la requête.|
|960912|Échec de l’analyse du corps de la requête.|
|960914|Échec de la validation stricte du corps de la requête en plusieurs parties  =     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|L’analyseur multipart a détecté une possible limite sans correspondance.|
|960016|L’en-tête HTTP Content-Length n’est pas numérique.|
|960011|Requête GET ou HEAD avec contenu du corps.|
|960012|En-tête Content-Length manquant dans la requête POST.|
|960902|Utilisation incorrecte de l’encodage d’identités.|
|960022|En-tête attendu non autorisé pour HTTP 1.0.|
|960020|L’en-tête Pragma requiert un en-tête Cache-Control pour les requêtes HTTP/1.1.|
|958291|Plage = Le champ existe et il commence par 0.|
|958230|Plage = Dernière valeur d’octet non valide.|
|958295|Détection de données d’en-tête de connexion en conflit/multiples.|
|950107|Tentative d’attaque abusive d’encodage d’URL|
|950109|Détection d’encodage de plusieurs URL|
|950108|Tentative d’attaque abusive d’encodage d’URL|
|950801|Tentative d’attaque abusive d’encodage UTF8|
|950116|Tentative d’attaque abusive Pleine/Moyenne largeur Unicode|
|960901|Caractère non valide dans la requête|
|960018|Caractère non valide dans la requête|

### <a name="crs21"></a> crs_21_protocol_anomalies

|ID de la règle|Description|
|---|---|
|960008|En-tête d’hôte manquant dans la requête|
|960007|En-tête d’hôte vide|
|960015|En-tête Accept manquant dans la requête|
|960021|Requête contenant un en-tête Accept vide|
|960009|En-tête User-Agent manquant dans la requête|
|960006|En-tête User-Agent vide|
|960904|Requête contenant du contenu mais dont l’en-tête Content-Type est manquant|
|960017|L’en-tête d’hôte est une adresse IP numérique|

### <a name="crs23"></a> crs_23_request_limits

|ID de la règle|Description|
|---|---|
|960209|Nom d’argument trop long|
|960208|Valeur d’argument trop longue|
|960335|Trop d’arguments dans la requête|
|960341|Taille totale d’arguments dépassée|
|960342|Taille du fichier chargé trop volumineuse|
|960343|Taille totale des fichiers chargés trop volumineuse|

### <a name="crs30"></a> crs_30_http_policy

|ID de la règle|Description|
|---|---|
|960032|Méthode non autorisée par la stratégie|
|960010|Type de contenu de requête non autorisé par la stratégie|
|960034|Version du protocole HTTP non autorisée par la stratégie|
|960035|Extension de fichier URL limitée par la stratégie|
|960038|En-tête HTTP limité par la stratégie|

### <a name="crs35"></a> crs_35_bad_robots

|ID de la règle|Description|
|---|---|
|990002|La requête indique qu’un système d’analyse de la sécurité a analysé le site|
|990901|La requête indique qu’un système d’analyse de la sécurité a analysé le site|
|990902|La requête indique qu’un système d’analyse de la sécurité a analysé le site|
|990012|Analyseur de sites web non fiables|

### <a name="crs40"></a> crs_40_generic_attacks

|ID de la règle|Description|
|---|---|
|960024|Alerte de détection d’anomalies de métacaractères - Caractères non textuels répétitifs|
|950008|Injection de balises ColdFusion non documentées|
|950010|Attaque par injection de code LDAP|
|950011|Attaque par injection de code SSI|
|950018|URL XSS Universal PDF détectée.|
|950019|Attaque par injection d’e-mail|
|950012|Attaque par dissimulation de requête HTTP.|
|950910|Attaque par fractionnement de réponse HTTP|
|950911|Attaque par fractionnement de réponse HTTP|
|950117|Attaque par inclusion de fichier distant|
|950118|Attaque par inclusion de fichier distant|
|950119|Attaque par inclusion de fichier distant|
|950120|Attaque possible par inclusion de fichier distant = Référence/Lien hors domaine|
|981133|Règle 981133|
|981134|Règle 981134|
|950009|Attaque par fixation de session|
|950003|Fixation de session|
|950000|Fixation de session|
|950005|Tentative d’accès au fichier distant|
|950002|Accès aux commandes système|
|950006|Injection de commandes système|
|959151|Attaque par injection de code PHP|
|958976|Attaque par injection de code PHP|
|958977|Attaque par injection de code PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|ID de la règle|Description|
|---|---|
|981231|Séquence de commentaire SQL détectée.|
|981260|Encodage hexadécimal SQL identifié|
|981320|Attaque par injection de code SQL = Noms de base de données courants détectés|
|981300|Règle 981300|
|981301|Règle 981301|
|981302|Règle 981302|
|981303|Règle 981303|
|981304|Règle 981304|
|981305|Règle 981305|
|981306|Règle 981306|
|981307|Règle 981307|
|981308|Règle 981308|
|981309|Règle 981309|
|981310|Règle 981310|
|981311|Règle 981311|
|981312|Règle 981312|
|981313|Règle 981313|
|981314|Règle 981314|
|981315|Règle 981315|
|981316|Règle 981316|
|981317|Alerte de détection d’anomalies d’instructions SQL SELECT|
|950007|Attaque par injection de code SQL aveugle|
|950001|Attaque par injection de code SQL|
|950908|Attaque par injection de code SQL.|
|959073|Attaque par injection de code SQL|
|981272|Détecte des tests sqli à l’aveugle à l’aide de la méthode sleep() ou benchmarch().|
|981250|Détecte les tentatives d’injection par les méthodes benchmark (test d’évaluation) et sleep (mise en veille), y compris les requêtes conditionnelles|
|981241|Détecte des tentatives d’injection de code SQL conditionnel|
|981276|Recherche d’injection de code SQL de base. Chaîne d’attaque courante pour mysql oracle entre autres.|
|981270|Recherche de tentatives d’injection de code SQL MongoDB de base|
|981253|Détecte des injections de fonctions/procédures stockées MySQL et PostgresSQL|
|981251|Détecte l’injection de code UDF MySQL et autres tentatives de manipulation de données/structures|

### <a name="crs41xss"></a> crs_41_xss_attacks

|ID de la règle|Description|
|---|---|
|973336|Filtre XSS - Catégorie 1 = Vecteur de balise de script|
|973338|Filtre XSS - Catégorie 3 = Vecteur URI Javascript|
|981136|Règle 981136|
|981018|Règle 981018|
|958016|Attaque par scripts intersites (XSS)|
|958414|Attaque par scripts intersites (XSS)|
|958032|Attaque par scripts intersites (XSS)|
|958026|Attaque par scripts intersites (XSS)|
|958027|Attaque par scripts intersites (XSS)|
|958054|Attaque par scripts intersites (XSS)|
|958418|Attaque par scripts intersites (XSS)|
|958034|Attaque par scripts intersites (XSS)|
|958019|Attaque par scripts intersites (XSS)|
|958013|Attaque par scripts intersites (XSS)|
|958408|Attaque par scripts intersites (XSS)|
|958012|Attaque par scripts intersites (XSS)|
|958423|Attaque par scripts intersites (XSS)|
|958002|Attaque par scripts intersites (XSS)|
|958017|Attaque par scripts intersites (XSS)|
|958007|Attaque par scripts intersites (XSS)|
|958047|Attaque par scripts intersites (XSS)|
|958410|Attaque par scripts intersites (XSS)|
|958415|Attaque par scripts intersites (XSS)|
|958022|Attaque par scripts intersites (XSS)|
|958405|Attaque par scripts intersites (XSS)|
|958419|Attaque par scripts intersites (XSS)|
|958028|Attaque par scripts intersites (XSS)|
|958057|Attaque par scripts intersites (XSS)|
|958031|Attaque par scripts intersites (XSS)|
|958006|Attaque par scripts intersites (XSS)|
|958033|Attaque par scripts intersites (XSS)|
|958038|Attaque par scripts intersites (XSS)|
|958409|Attaque par scripts intersites (XSS)|
|958001|Attaque par scripts intersites (XSS)|
|958005|Attaque par scripts intersites (XSS)|
|958404|Attaque par scripts intersites (XSS)|
|958023|Attaque par scripts intersites (XSS)|
|958010|Attaque par scripts intersites (XSS)|
|958411|Attaque par scripts intersites (XSS)|
|958422|Attaque par scripts intersites (XSS)|
|958036|Attaque par scripts intersites (XSS)|
|958000|Attaque par scripts intersites (XSS)|
|958018|Attaque par scripts intersites (XSS)|
|958406|Attaque par scripts intersites (XSS)|
|958040|Attaque par scripts intersites (XSS)|
|958052|Attaque par scripts intersites (XSS)|
|958037|Attaque par scripts intersites (XSS)|
|958049|Attaque par scripts intersites (XSS)|
|958030|Attaque par scripts intersites (XSS)|
|958041|Attaque par scripts intersites (XSS)|
|958416|Attaque par scripts intersites (XSS)|
|958024|Attaque par scripts intersites (XSS)|
|958059|Attaque par scripts intersites (XSS)|
|958417|Attaque par scripts intersites (XSS)|
|958020|Attaque par scripts intersites (XSS)|
|958045|Attaque par scripts intersites (XSS)|
|958004|Attaque par scripts intersites (XSS)|
|958421|Attaque par scripts intersites (XSS)|
|958009|Attaque par scripts intersites (XSS)|
|958025|Attaque par scripts intersites (XSS)|
|958413|Attaque par scripts intersites (XSS)|
|958051|Attaque par scripts intersites (XSS)|
|958420|Attaque par scripts intersites (XSS)|
|958407|Attaque par scripts intersites (XSS)|
|958056|Attaque par scripts intersites (XSS)|
|958011|Attaque par scripts intersites (XSS)|
|958412|Attaque par scripts intersites (XSS)|
|958008|Attaque par scripts intersites (XSS)|
|958046|Attaque par scripts intersites (XSS)|
|958039|Attaque par scripts intersites (XSS)|
|958003|Attaque par scripts intersites (XSS)|
|973300|Possible attaque XSS détectée - Gestionnaire de balise HTML|
|973301|Attaque XSS détectée|
|973302|Attaque XSS détectée|
|973303|Attaque XSS détectée|
|973304|Attaque XSS détectée|
|973305|Attaque XSS détectée|
|973306|Attaque XSS détectée|
|973307|Attaque XSS détectée|
|973308|Attaque XSS détectée|
|973309|Attaque XSS détectée|
|973311|Attaque XSS détectée|
|973313|Attaque XSS détectée|
|973314|Attaque XSS détectée|
|973331|Filtre XSS IE - Attaque détectée.|
|973315|Filtre XSS IE - Attaque détectée.|
|973330|Filtre XSS IE - Attaque détectée.|
|973327|Filtre XSS IE - Attaque détectée.|
|973326|Filtre XSS IE - Attaque détectée.|
|973346|Filtre XSS IE - Attaque détectée.|
|973345|Filtre XSS IE - Attaque détectée.|
|973324|Filtre XSS IE - Attaque détectée.|
|973323|Filtre XSS IE - Attaque détectée.|
|973348|Filtre XSS IE - Attaque détectée.|
|973321|Filtre XSS IE - Attaque détectée.|
|973320|Filtre XSS IE - Attaque détectée.|
|973318|Filtre XSS IE - Attaque détectée.|
|973317|Filtre XSS IE - Attaque détectée.|
|973329|Filtre XSS IE - Attaque détectée.|
|973328|Filtre XSS IE - Attaque détectée.|

### <a name="crs42"></a> crs_42_tight_security

|ID de la règle|Description|
|---|---|
|950103|Attaque par traversée de chemin|

### <a name="crs45"></a> crs_45_trojans

|ID de la règle|Description|
|---|---|
|950110|Accès par porte dérobée|
|950921|Accès par porte dérobée|
|950922|Accès par porte dérobée|

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment désactiver des règles WAF, consultez : [Personnaliser les règles de pare-feu d’application web](application-gateway-customize-waf-rules-portal.md)