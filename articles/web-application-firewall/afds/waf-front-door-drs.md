---
title: Règles et groupes de règles DRS Azure Web Application Firewall sur Azure Front Door
description: Cet article fournit des informations sur les règles et groupes de règles DRS de Web Application Firewall.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 07/29/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: d1e6349dd2c809c2a18551037c3bd3a8970f6d68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562371"
---
# <a name="web-application-firewall-drs-rule-groups-and-rules"></a>Règles et groupes de règles DRS de Web Application Firewall

Le pare-feu d’applications web (WAF) d’Azure Front Door protège les applications web contre les vulnérabilités et le code malveillant exploitant une faille de sécurité. Les ensembles de règles managées par Azure fournissent un moyen simple de déployer une solution de protection contre diverses menaces de sécurité courantes. Dans la mesure où ces ensembles de règles sont gérées par Azure, les règles sont mises à jour en fonction des besoins pour assurer la protection contre les nouvelles signatures d’attaque.


## <a name="default-rule-sets"></a>Ensembles de règles par défaut

L’ensemble de règles par défaut managées par Azure inclut des règles de protection contre les catégories de menaces suivantes :

- Scripts intersites (XSS)
- Attaques Java
- Inclusion de fichier local (LFI)
- Injection de code PHP
- Exécution de commande à distance
- Inclusion de fichier distant (RFI)
- Fixation de session
- Protection contre les injections de code SQL
- Attaquants de protocole

Le numéro de version de l’ensemble de règles par défaut s’incrémente quand de nouvelles signatures d’attaque sont ajoutées à l’ensemble de règles.
L’ensemble de règles par défaut est activé en mode Détection par défaut dans vos stratégies WAF. Vous pouvez activer ou désactiver des règles individuellement dans l’ensemble de règles par défaut en fonction des exigences propres à votre application. Vous pouvez également définir des actions spécifiques (AUTORISER/BLOQUER/REDIRIGER/JOURNALISER) pour chaque règle.

Parfois, il peut se révéler utile d’omettre certains attributs de demande d’une évaluation de pare-feu d’applications web (WAF). Les jetons insérés par Active Directory qui sont utilisés pour l’authentification en sont un exemple courant. Vous pouvez configurer une liste d’exclusion pour une règle managée, pour un groupe de règles ou pour la totalité de l’ensemble de règles.  

L’action par défaut est BLOQUER. De plus, des règles personnalisées peuvent être configurées dans la même stratégie WAF si vous souhaitez ignorer les règles préconfigurées de l’ensemble de règles par défaut.

Les règles personnalisées sont toujours appliquées avant l’évaluation des règles de l’ensemble de règles par défaut. Si une demande correspond à une règle personnalisée, l’action de la règle correspondante est appliquée. La demande est bloquée ou transmise au back-end. Aucune autre règle personnalisée ou les règles de l’ensemble de règles par défaut ne sont traitées. Vous pouvez aussi supprimer l’ensemble de règles par défaut de vos stratégies WAF.

### <a name="microsoft-threat-intelligence-collection-rules"></a>Règles de collecte de Microsoft Threat Intelligence

Les règles de collecte de Microsoft Threat Intelligence sont écrites en partenariat avec l’équipe Microsoft Intelligence, afin de fournir une couverture accrue, des correctifs pour des vulnérabilités spécifiques et une meilleure réduction des faux positifs.

### <a name="anomaly-scoring-mode"></a>Mode de scoring d’anomalie

OWASP dispose de deux modes pour décider de bloquer ou non le trafic : le mode traditionnel et le mode de calcul de scoring d’anomalie.

En mode traditionnel, le trafic correspondant à une règle est considéré indépendamment de toute autre correspondance. Ce mode est facile à comprendre. Mais le manque d’informations sur le nombre de règles correspondant à une requête spécifique est une limitation. C’est ainsi que le mode de scoring d’anomalie a été introduit. C’est le mode par défaut pour 3 OWASP.*x*.

En mode de scoring d’anomalie, le trafic correspondant à une règle n’est pas immédiatement bloqué lorsque le pare-feu est en mode de prévention. Les règles ont un niveau de gravité spécifique : *Critique*, *Erreur*, *Avertissement* ou *Avis*. Ce niveau de gravité affecte à la demande une valeur numérique appelée le score d’anomalie. Par exemple, une correspondance de règle *Avertissement* ajoute 3 au score. Une correspondance de règle *Avertissement* ajoute 5.

|severity  |Valeur  |
|---------|---------|
|Critique     |5|
|Error        |4|
|Avertissement      |3|
|Avis       |2|

Le score d’anomalie doit atteindre le seuil de 5 pour que le trafic soit bloqué. Ainsi, une seule correspondance de règle *Critique* suffit pour que le pare-feu d’applications web bloque une requête, même en mode de prévention. Mais une correspondance de règle *Avertissement* augmente considérablement selon le score d’anomalie de 3, ce qui est insuffisant en soi pour bloquer le trafic.

### <a name="drs-20"></a>DRS 2.0

DRS 2.0 inclut 17 groupes de règles, comme illustré dans le tableau suivant. Chaque groupe contient plusieurs règles qui peuvent être désactivées.

> [!NOTE]
> DRS 2.0 est disponible uniquement sur Azure Front Door Premium.

|Groupe de règles|Description|
|---|---|
|**[Généralités](#general-20)**|Groupe général|
|**[METHOD-ENFORCEMENT](#drs911-20)**|Verrouiller les méthodes (PUT, PATCH)|
|**[PROTOCOL-ENFORCEMENT](#drs920-20)**|Protéger contre les problèmes de protocole et d’encodage.|
|**[PROTOCOL-ATTACK](#drs921-20)**|Protéger contre les attaques par injection d’en-tête, dissimulation de requête et fractionnement de réponse.|
|**[APPLICATION-ATTACK-LFI](#drs930-20)**|Protéger contre les attaques par fichier et chemin d’accès.|
|**[APPLICATION-ATTACK-RFI](#drs931-20)**|Protéger contre les attaques par inclusion de fichier distant (RFI)|
|**[APPLICATION-ATTACK-RCE](#drs932-20)**|Protéger contre les attaques par exécution de code à distance.|
|**[APPLICATION-ATTACK-PHP](#drs933-20)**|Protéger contre les attaques par injection de code PHP.|
|**[APPLICATION-ATTACK-NodeJS](#drs934-20)**|Protéger contre les attaques Node JS|
|**[APPLICATION-ATTACK-XSS](#drs941-20)**|Protéger contre les attaques par exécution de scripts de site à site.|
|**[APPLICATION-ATTACK-SQLI](#drs942-20)**|Protéger contre les attaques par injection de code SQL.|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-20)**|Protéger contre les attaques par fixation de session.|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-20)**|Protéger contre les attaques JAVA|
|**[MS-ThreatIntel-WebShells](#drs9905-20)**|Protéger contre les attaques par l’interpréteur de commandes web|
|**[MS-ThreatIntel-AppSec](#drs9903-20)**|Protéger contre les attaques par AppSec|
|**[MS-ThreatIntel-SQLI](#drs99031-20)**|Protéger contre les attaques par SQLI|
|**[MS-ThreatIntel-CVEs](#drs99001-20)**|Protéger contre les attaques par CVE|

### <a name="drs-11"></a>DRS 1.1
|Groupe de règles|Description|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-11)**|Protéger contre les attaques par injection d’en-tête, dissimulation de requête et fractionnement de réponse.|
|**[APPLICATION-ATTACK-LFI](#drs930-11)**|Protéger contre les attaques par fichier et chemin d’accès.|
|**[APPLICATION-ATTACK-RFI](#drs931-11)**|Protection contre les attaques par inclusion de fichier distant (RFI)|
|**[APPLICATION-ATTACK-RCE](#drs932-11)**|Protection contre l’exécution de commandes à distance|
|**[APPLICATION-ATTACK-PHP](#drs933-11)**|Protéger contre les attaques par injection de code PHP.|
|**[APPLICATION-ATTACK-XSS](#drs941-11)**|Protéger contre les attaques par exécution de scripts de site à site.|
|**[APPLICATION-ATTACK-SQLI](#drs942-11)**|Protéger contre les attaques par injection de code SQL.|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-11)**|Protéger contre les attaques par fixation de session.|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-11)**|Protéger contre les attaques JAVA|
|**[MS-ThreatIntel-WebShells](#drs9905-11)**|Protéger contre les attaques par l’interpréteur de commandes web|
|**[MS-ThreatIntel-AppSec](#drs9903-11)**|Protéger contre les attaques par AppSec|
|**[MS-ThreatIntel-SQLI](#drs99031-11)**|Protéger contre les attaques par SQLI|
|**[MS-ThreatIntel-CVEs](#drs99001-11)**|Protéger contre les attaques par CVE|

### <a name="drs-10"></a>DRS 1.0

|Groupe de règles|Description|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-10)**|Protéger contre les attaques par injection d’en-tête, dissimulation de requête et fractionnement de réponse.|
|**[APPLICATION-ATTACK-LFI](#drs930-10)**|Protéger contre les attaques par fichier et chemin d’accès.|
|**[APPLICATION-ATTACK-RFI](#drs931-10)**|Protection contre les attaques par inclusion de fichier distant (RFI)|
|**[APPLICATION-ATTACK-RCE](#drs932-10)**|Protection contre l’exécution de commandes à distance|
|**[APPLICATION-ATTACK-PHP](#drs933-10)**|Protéger contre les attaques par injection de code PHP.|
|**[APPLICATION-ATTACK-SQLI](#drs942-10)**|Protéger contre les attaques par injection de code SQL.|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-10)**|Protéger contre les attaques par fixation de session.|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-10)**|Protéger contre les attaques JAVA|



### <a name="bot-rules"></a>Règles de bot

|Groupe de règles|Description|
|---|---|
|**[BadBots](#bot100)**|Protection contre les bots malveillants|
|**[GoodBots](#bot200)**|Identification des bots bienveillants|
|**[UnknownBots](#bot300)**|Identification des bots inconnus|



Les règles et groupes de règles suivants sont disponibles durant l’utilisation de Web Application Firewall sur Azure Front Door.

# <a name="drs-20"></a>[DRS 2.0](#tab/drs20)

## <a name="20-rule-sets"></a><a name="drs20"></a> Ensembles de règles 2.0

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-20"></a> <p x-ms-format-detection="none">Général</p>
|ID de la règle|Description|
|---|---|
|200002|Échec de l’analyse du corps de la requête.|
|200003|Échec de la validation stricte du corps de la requête à parties multiples|


### <a name="p-x-ms-format-detectionnonemethod-enforcementp"></a><a name="drs911-20"></a> <p x-ms-format-detection="none">METHOD ENFORCEMENT</p>
|ID de la règle|Description|
|---|---|
|911100|Méthode non autorisée par la stratégie|

### <a name="p-x-ms-format-detectionnoneprotocol-enforcementp"></a><a name="drs920-20"></a> <p x-ms-format-detection="none">PROTOCOL-ENFORCEMENT</p>
|ID de la règle|Description|
|---|---|
|920100|Ligne de requête HTTP non valide|
|920120|Tentative de contournement de données en plusieurs parties/de formulaire|
|920121|Tentative de contournement de données en plusieurs parties/de formulaire|
|920160|L’en-tête HTTP Content-Length n’est pas numérique.|
|920170|Requête GET ou HEAD avec contenu du corps.|
|920171|Requête GET ou HEAD avec Transfer-Encoding.|
|920180|En-tête Content-Length manquant dans la requête POST.|
|920190|Plage : dernière valeur d’octet non valide.|
|920200|Plage : champs trop nombreux (6 ou plus)|
|920201|Plage : champs trop nombreux pour la requête PDF (35 ou plus)|
|920210|Détection de données d’en-tête de connexion en conflit/multiples.|
|920220|Tentative d’attaque abusive d’encodage d’URL|
|920230|Détection d’encodage de plusieurs URL|
|920240|Tentative d’attaque abusive d’encodage d’URL|
|920260|Tentative d’attaque abusive Pleine/Moyenne largeur Unicode|
|920270|Caractère non valide dans la requête (caractère null)|
|920271|Caractère non valide dans la requête (caractères non imprimables)|
|920280|En-tête d’hôte manquant dans la requête|
|920290|En-tête d’hôte vide|
|920300|En-tête Accept manquant dans la requête|
|920310|Requête contenant un en-tête Accept vide|
|920311|Requête contenant un en-tête Accept vide|
|920320|En-tête User-Agent manquant|
|920330|En-tête User-Agent vide|
|920340|Requête contenant du contenu, mais dont l’en-tête Content-Type est manquant|
|920341|Requête dont le contenu nécessite l’en-tête Content-Type|
|920350|L’en-tête d’hôte est une adresse IP numérique|
|920420|Type de contenu de requête non autorisé par la stratégie|
|920430|Version du protocole HTTP non autorisée par la stratégie|
|920440|Extension de fichier URL limitée par la stratégie|
|920450|En-tête HTTP limité par la stratégie|
|920470|En-tête Content-type non conforme|
|920480|Jeu de caractères Content-Type de requête non autorisé par la stratégie|

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-20"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>

|ID de la règle|Description|
|---|---|
|921110|Attaque par dissimulation de requête HTTP|
|921120|Attaque par fractionnement de réponse HTTP|
|921130|Attaque par fractionnement de réponse HTTP|
|921140|Attaque par injection d’en-tête HTTP via les en-têtes|
|921150|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921151|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921160|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF et nom d’en-tête détecté)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-20"></a> <p x-ms-format-detection="none">Inclusion de fichier local (LFI, Local File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|930100|Attaque par traversée de chemin (/../)|
|930110|Attaque par traversée de chemin (/../)|
|930120|Tentative d’accès au fichier du système d’exploitation|
|930130|Tentative d’accès au fichier restreint|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-20"></a> <p x-ms-format-detection="none">Inclusion de fichier distant (RFI, Remote File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|931100|Attaque possible par inclusion de fichier distant : paramètre d’URL utilisant l’adresse IP|
|931110|Attaque possible par inclusion de fichier distant (RFI) : nom de paramètre vulnérable RFI commun utilisé avec la charge utile URL|
|931120|Attaque possible par inclusion de fichier distant : charge utile URL utilisée avec caractère point d’interrogation de fin (?)|
|931130|Attaque possible par inclusion de fichier distant : Référence/Lien hors domaine|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-20"></a> <p x-ms-format-detection="none">Exécution de commande à distance (RCE, Remote Command Execution)</p>
|ID de la règle|Description|
|---|---|
|932100|Exécution de commande à distance : Injection de commande Unix|
|932105|Exécution de commande à distance : Injection de commande Unix|
|932110|Exécution de commande à distance : Injection de commande Windows|
|932115|Exécution de commande à distance : Injection de commande Windows|
|932120|Exécution de commande à distance : commande Windows PowerShell détectée|
|932130|Exécution de commande à distance : expression de l’interpréteur de commandes Unix détectée|
|932140|Exécution de commande à distance : commande Windows FOR/IF détectée|
|932150|Exécution de commande à distance : Exécution de commande UNIX directe|
|932160|Exécution de commande à distance : code de l’interpréteur de commandes Unix détecté|
|932170|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932171|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932180|Tentative de chargement de fichier limitée|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-20"></a> <p x-ms-format-detection="none">Attaques par PHP</p>
|ID de la règle|Description|
|---|---|
|933100|Attaque par injection de code PHP : balise d’ouverture/fermeture détectée|
|933110|Attaque par injection de code PHP : Chargement de fichier de script PHP détecté|
|933120|Attaque par injection de code PHP : directive de configuration détectée|
|933130|Attaque par injection de code PHP : Variables détectées|
|933131|Attaque par injection de code PHP : Variables détectées|
|933140|Attaque par injection de code PHP : Flux d’E/S détecté|
|933150|Attaque par injection de code PHP : nom de fonction PHP à risque élevé détecté|
|933151|Attaque par injection de code PHP : Nom de fonction PHP à risque moyen détecté|
|933160|Attaque par injection de code PHP : appel de fonction PHP à risque élevé détecté|
|933161|Attaque par injection de code PHP : Appel de fonction PHP à valeur faible détecté|
|933170|Attaque par injection de code PHP : Injection d’objet sérialisé|
|933180|Attaque par injection de code PHP : appel de fonction variable détecté|
|933200|Attaque par injection de code PHP : schéma de wrapper détecté|
|933210|Attaque par injection de code PHP : appel de fonction variable détecté|

### <a name="p-x-ms-format-detectionnonenode-js-attacksp"></a><a name="drs934-20"></a> <p x-ms-format-detection="none">Attaques par Node.js</p>
|ID de la règle|Description|
|---|---|
|934100|Attaque par injection de code Node.js|

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-20"></a> <p x-ms-format-detection="none">XSS - Scripts intersites</p>
|ID de la règle|Description|
|---|---|
|941100|Attaque XSS détectée via libinjection|
|941101|Attaque XSS détectée via libinjection|
|941110|Filtre XSS - Catégorie 1 : vecteur de balise de script|
|941120|Filtre XSS - Catégorie 2 : vecteur de gestionnaire d’événements|
|941130|Filtre XSS - Catégorie 3 : vecteur d’attribut|
|941140|Filtre XSS - Catégorie 4 : vecteur URI JavaScript|
|941150|Filtre XSS - Catégorie 5 : attributs HTML non autorisés|
|941160|NoScript XSS InjectionChecker : Injection de code HTML|
|941170|NoScript XSS InjectionChecker : Injection d’attribut|
|941180|Mots clés de la liste rouge du validateur de nœuds|
|941190|XSS utilisant des feuilles de style|
|941200|XSS utilisant des frames VML|
|941210|XSS utilisant du JavaScript obfusqué|
|941220|XSS utilisant du VB Script obfusqué|
|941230|XSS utilisant la balise « embed »|
|941240|XSS utilisant l’attribut « import » ou « implementation »|
|941250|Filtre XSS IE - Attaque détectée.|
|941260|XSS utilisant la balise « meta »|
|941270|XSS utilisant le href « link »|
|941280|XSS utilisant la balise « base »|
|941290|XSS utilisant la balise « applet »|
|941300|XSS utilisant la balise « object »|
|941310|Filtre XSS d’encodage incorrectement formé US-ASCII - Attaque détectée.|
|941320|Possible attaque XSS détectée - Gestionnaire de balise HTML|
|941330|Filtre XSS IE - Attaque détectée.|
|941340|Filtre XSS IE - Attaque détectée.|
|941350|XSS IE d’encodage UTF-7 - Attaque détectée.|
|941360|Obfuscation JavaScript détectée.|
|941370|Variable globale JavaScript détectée|
|941380|Injection de modèle côté client AngularJS détectée|


### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-20"></a> <p x-ms-format-detection="none">SQLI - Injection de code SQL</p>
|ID de la règle|Description|
|---|---|
|942100|Attaque par injection de code SQL détectée via libinjection|
|942110|Attaque par injection de code SQL : Test d’injection commune détecté|
|942120|Attaque par injection de code SQL : Opérateur SQL détecté|
|942130|Attaque par injection de code SQL : Tautologie SQL détectée.|
|942140|Attaque par injection de code SQL : noms de base de données courants détectés|
|942150|Attaque par injection de code SQL|
|942160|Détecte des tests sqli à l’aveugle à l’aide de la méthode sleep() ou benchmarch().|
|942170|Détecte les tentatives d’injection par les méthodes benchmark (test d’évaluation) et sleep (mise en veille), y compris les requêtes conditionnelles|
|942180|Détecte les tentatives de contournement d’authentification SQL de base 1/3|
|942190|Détecte les tentatives de collecte d’informations et de d’exécution de code MSSQL|
|942200|Détecte les injections MySQL de type comment-/space-obfuscated et d’accent grave|
|942210|Détecte les tentatives d’injection de code SQL chaîné 1/2|
|942220|Pour rechercher les attaques de dépassement sur les entiers, l’extraction se fait à partir de skipfish, à l’exception de 3.0.00738585072007e-308 qui est l’incident « nombre magique ».|
|942230|Détecte des tentatives d’injection de code SQL conditionnel|
|942240|Détecte les tentatives de basculement de charset MySQL et d’attaque DoS MSSQL|
|942250|Détecte les injections de code MATCH AGAINST, MERGE et EXECUTE IMMEDIATE|
|942260|Détecte les tentatives de contournement d’authentification SQL de base 2/3|
|942270|Recherche d’injection de code SQL de base. Chaîne d’attaque courante pour mysql, oracle, etc.|
|942280|Détecte l’injection de Postgres pg_sleep, les attaques par délai d’attente et les tentatives d’arrêt de base de données|
|942290|Recherche de tentatives d’injection de code SQL MongoDB de base|
|942300|Détecte les injections de ch(a)r, les conditions et les commentaires MySQL|
|942310|Détecte des tentatives d’injection de code SQL chaîné 2/2|
|942320|Détecte des injections de fonctions/procédures stockées MySQL et PostgresSQL|
|942330|Détecte les sondes d’injection SQL classiques 1/2|
|942340|Détecte les tentatives de contournement d’authentification SQL de base 3/3|
|942350|Détecte l’injection de code UDF MySQL et autres tentatives de manipulation de données/structures|
|942360|Détecte l’injection SQL de base concaténée et les tentatives SQLLFI|
|942361|Détecte l’injection de code SQL de base par le biais du mot clé alter ou union|
|942370|Détecte les sondes d’injection SQL classiques 2/2|
|942380|Attaque par injection de code SQL|
|942390|Attaque par injection de code SQL|
|942400|Attaque par injection de code SQL|
|942410|Attaque par injection de code SQL|
|942430|Détection restreinte d’anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassés (12)|
|942440|Séquence de commentaire SQL détectée.|
|942450|Encodage hexadécimal SQL identifié|
|942460|Alerte de détection d’anomalies de métacaractères - Caractères non textuels répétitifs|
|942470|Attaque par injection de code SQL|
|942480|Attaque par injection de code SQL|
|942500|Commentaire en ligne MySQL détecté.|
|942510|Tentative de contournement SQLi par apostrophes ou apostrophes inversées détectée.|


### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-20"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|ID de la règle|Description|
|---|---|
|943100|Attaque possible par fixation de session : définition de valeurs de cookies en HTML|
|943110|Attaque possible par fixation de session : nom du paramètre SessionID avec référent hors domaine|
|943120|Attaque possible par fixation de session : nom du paramètre SessionID sans référent|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-20"></a> <p x-ms-format-detection="none">Attaques par Java</p>
|ID de la règle|Description|
|---|---|
|944100|Exécution de commande à distance : Apache Struts, Oracle WebLogic|
|944110|Détecte l’exécution de charge utile potentielle|
|944120|Exécution de charge utile et de commande à distance possible|
|944130|Classes Java suspectes|
|944200|Exploitation de la désérialisation Java Apache Commons|
|944210|Utilisation possible de la sérialisation Java|
|944240|Exécution de commande à distance : sérialisation Java|
|944250|Exécution de commande à distance : méthode Java suspecte détectée|

### <a name="p-x-ms-format-detectionnonems-threatintel-webshellsp"></a><a name="drs9905-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-WebShells</p>
|ID de la règle|Description|
|---|---|
|99005002|Tentative d’interaction avec l’interpréteur de commandes web (POST)|
|99005003|Tentative de chargement de l’interpréteur de commandes web (POST) - CHOPPER PHP|
|99005004|Tentative de chargement de l’interpréteur de commandes web (POST) - CHOPPER ASPX|

### <a name="p-x-ms-format-detectionnonems-threatintel-appsecp"></a><a name="drs9903-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-AppSec</p>
|ID de la règle|Description|
|---|---|
|99030001|Esquive par traversée de chemin dans les en-têtes (/.././../)|
|99030002|Esquive par traversée de chemin dans le corps de requête (/.././../)|

### <a name="p-x-ms-format-detectionnonems-threatintel-sqlip"></a><a name="drs99031-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-SQLI</p>
|ID de la règle|Description|
|---|---|
|99031001|Attaque par injection de code SQL : Test d’injection commune détecté|
|99031002|Séquence de commentaire SQL détectée.|

### <a name="p-x-ms-format-detectionnonems-threatintel-cvesp"></a><a name="drs99001-20"></a> <p x-ms-format-detection="none">MS-ThreatIntel-CVEs</p>
|ID de la règle|Description|
|---|---|
|99001001|Tentative d’exploitation de l’API REST (CVE-2020-5902) tmui de F5 avec des informations d’identification connues|

# <a name="drs-11"></a>[DRS 1.1](#tab/drs11)

## <a name="11-rule-sets"></a><a name="drs11"></a> Ensembles de règles 1.1

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-11"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>
|ID de la règle|Description|
|---|---|
|921110|Attaque par dissimulation de requête HTTP|
|921120|Attaque par fractionnement de réponse HTTP|
|921130|Attaque par fractionnement de réponse HTTP|
|921140|Attaque par injection d’en-tête HTTP via les en-têtes|
|921150|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921151|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921160|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF et nom d’en-tête détecté)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-11"></a> <p x-ms-format-detection="none">Inclusion de fichier local (LFI, Local File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|930100|Attaque par traversée de chemin (/../)|
|930110|Attaque par traversée de chemin (/../)|
|930120|Tentative d’accès au fichier du système d’exploitation|
|930130|Tentative d’accès au fichier restreint|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-11"></a> <p x-ms-format-detection="none">Inclusion de fichier distant (RFI, Remote File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|931100|Attaque possible par inclusion de fichier distant : paramètre d’URL utilisant l’adresse IP|
|931110|Attaque possible par inclusion de fichier distant (RFI) : nom de paramètre vulnérable RFI commun utilisé avec la charge utile URL|
|931120|Attaque possible par inclusion de fichier distant : charge utile URL utilisée avec caractère point d’interrogation de fin (?)|
|931130|Attaque possible par inclusion de fichier distant : Référence/Lien hors domaine|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-11"></a> <p x-ms-format-detection="none">Exécution de commande à distance (RCE, Remote Command Execution)</p>
|ID de la règle|Description|
|---|---|
|932100|Exécution de commande à distance : Injection de commande Unix|
|932105|Exécution de commande à distance : Injection de commande Unix|
|932110|Exécution de commande à distance : Injection de commande Windows|
|932115|Exécution de commande à distance : Injection de commande Windows|
|931120|Exécution de commande à distance : commande Windows PowerShell détectée|
|932130|Exécution de commande à distance : expression de l’interpréteur de commandes Unix détectée|
|932140|Exécution de commande à distance : commande Windows FOR/IF détectée|
|932150|Exécution de commande à distance : Exécution de commande UNIX directe|
|932160|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932170|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932171|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932180|Tentative de chargement de fichier limitée|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-11"></a> <p x-ms-format-detection="none">Attaques par PHP</p>
|ID de la règle|Description|
|---|---|
|933100|Attaque par injection de code PHP : balise d’ouverture PHP détectée|
|933110|Attaque par injection de code PHP : Chargement de fichier de script PHP détecté|
|933120|Attaque par injection de code PHP : directive de configuration détectée|
|933130|Attaque par injection de code PHP : Variables détectées|
|933140|Attaque par injection de code PHP : Flux d’E/S détecté|
|933150|Attaque par injection de code PHP : nom de fonction PHP à risque élevé détecté|
|933151|Attaque par injection de code PHP : Nom de fonction PHP à risque moyen détecté|
|933160|Attaque par injection de code PHP : appel de fonction PHP à risque élevé détecté|
|933170|Attaque par injection de code PHP : Injection d’objet sérialisé|
|933180|Attaque par injection de code PHP : appel de fonction variable détecté|

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-11"></a> <p x-ms-format-detection="none">XSS - Scripts intersites</p>
|ID de la règle|Description|
|---|---|
|941100|Attaque XSS détectée via libinjection|
|941101|Attaque XSS détectée via libinjection|
|941110|Filtre XSS - Catégorie 1 : vecteur de balise de script|
|941120|Filtre XSS - Catégorie 2 : vecteur de gestionnaire d’événements|
|941130|Filtre XSS - Catégorie 3 : vecteur d’attribut|
|941140|Filtre XSS - Catégorie 4 : vecteur URI Javascript|
|941150|Filtre XSS - Catégorie 5 : attributs HTML non autorisés|
|941160|NoScript XSS InjectionChecker : Injection de code HTML|
|941170|NoScript XSS InjectionChecker : Injection d’attribut|
|941180|Mots clés de la liste rouge du validateur de nœuds|
|941190|Filtre XSS IE - Attaque détectée.|
|941200|Filtre XSS IE - Attaque détectée.|
|941210|Filtre XSS IE - Attaque détectée.|
|941220|Filtre XSS IE - Attaque détectée.|
|941230|Filtre XSS IE - Attaque détectée.|
|941240|Filtre XSS IE - Attaque détectée.|
|941250|Filtre XSS IE - Attaque détectée.|
|941260|Filtre XSS IE - Attaque détectée.|
|941270|Filtre XSS IE - Attaque détectée.|
|941280|Filtre XSS IE - Attaque détectée.|
|941290|Filtre XSS IE - Attaque détectée.|
|941300|Filtre XSS IE - Attaque détectée.|
|941310|Filtre XSS d’encodage incorrectement formé US-ASCII - Attaque détectée.|
|941320|Possible attaque XSS détectée - Gestionnaire de balise HTML|
|941330|Filtre XSS IE - Attaque détectée.|
|941340|Filtre XSS IE - Attaque détectée.|
|941350|XSS IE d’encodage UTF-7 - Attaque détectée.|

### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-11"></a> <p x-ms-format-detection="none">SQLI - Injection de code SQL</p>
|ID de la règle|Description|
|---|---|
|942100|Attaque par injection de code SQL détectée via libinjection|
|942110|Attaque par injection de code SQL : Test d’injection commune détecté|
|942120|Attaque par injection de code SQL : Opérateur SQL détecté|
|942140|Attaque par injection de code SQL : noms de base de données courants détectés|
|942150|Attaque par injection de code SQL|
|942160|Détecte des tests sqli à l’aveugle à l’aide de la méthode sleep() ou benchmarch().|
|942170|Détecte les tentatives d’injection par les méthodes benchmark (test d’évaluation) et sleep (mise en veille), y compris les requêtes conditionnelles|
|942180|Détecte les tentatives de contournement d’authentification SQL de base 1/3|
|942190|Détecte les tentatives de collecte d’informations et de d’exécution de code MSSQL|
|942200|Détecte les injections MySQL de type comment-/space-obfuscated et d’accent grave|
|942210|Détecte les tentatives d’injection de code SQL chaîné 1/2|
|942220|Pour rechercher les attaques de dépassement sur les entiers, l’extraction se fait à partir de skipfish, à l’exception de 3.0.00738585072007e-308 qui est l’incident « nombre magique ».|
|942230|Détecte des tentatives d’injection de code SQL conditionnel|
|942240|Détecte les tentatives de basculement de charset MySQL et d’attaque DoS MSSQL|
|942250|Détecte les injections de code MATCH AGAINST, MERGE et EXECUTE IMMEDIATE|
|942260|Détecte les tentatives de contournement d’authentification SQL de base 2/3|
|942270|Recherche d’injection de code SQL de base. Chaîne d’attaque courante pour mysql, oracle, etc.|
|942280|Détecte l’injection de Postgres pg_sleep, les attaques par délai d’attente et les tentatives d’arrêt de base de données|
|942290|Recherche de tentatives d’injection de code SQL MongoDB de base|
|942300|Détecte les commentaires MySQL, les conditions et les injections de ch(a)r|
|942310|Détecte des tentatives d’injection de code SQL chaîné 2/2|
|942320|Détecte des injections de fonctions/procédures stockées MySQL et PostgresSQL|
|942330|Détecte les sondes d’injection SQL classiques 1/3|
|942340|Détecte les tentatives de contournement d’authentification SQL de base 3/3|
|942350|Détecte l’injection de code UDF MySQL et autres tentatives de manipulation de données/structures|
|942360|Détecte l’injection SQL de base concaténée et les tentatives SQLLFI|
|942361|Détecte l’injection de code SQL de base par le biais du mot clé alter ou union|
|942370|Détecte les sondes d’injection SQL classiques 2/3|
|942380|Attaque par injection de code SQL|
|942390|Attaque par injection de code SQL|
|942400|Attaque par injection de code SQL|
|942410|Attaque par injection de code SQL|
|942430|Détection restreinte d’anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassés (12)|
|942440|Séquence de commentaire SQL détectée.|
|942450|Encodage hexadécimal SQL identifié|
|942470|Attaque par injection de code SQL|
|942480|Attaque par injection de code SQL|

### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-11"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|ID de la règle|Description|
|---|---|
|943100|Attaque possible par fixation de session : définition de valeurs de cookies en HTML|
|943110|Attaque possible par fixation de session : nom du paramètre SessionID avec référent hors domaine|
|943120|Attaque possible par fixation de session : nom du paramètre SessionID sans référent|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-11"></a> <p x-ms-format-detection="none">Attaques par Java</p>
|ID de la règle|Description|
|---|---|
|944100|Exécution de commande à distance : classe Java suspecte détectée|
|944110|Attaque possible par fixation de session : définition de valeurs de cookies en HTML|
|944120|Exécution de commande à distance : sérialisation Java (CVE-2015-5842)|
|944130|Classe Java suspecte détectée|
|944200|Octets magiques détectés, probable sérialisation Java en cours d’utilisation|
|944210|Octets magiques détectés, codés en base64 ; probable sérialisation Java en cours d’utilisation|
|944240|Exécution de commande à distance : sérialisation Java (CVE-2015-5842)|
|944250|Exécution de commande à distance : méthode Java suspecte détectée|

### <a name="p-x-ms-format-detectionnonems-threatintel-webshellsp"></a><a name="drs9905-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-WebShells</p>
|ID de la règle|Description|
|---|---|
|99005002|Tentative d’interaction avec l’interpréteur de commandes web (POST)|
|99005003|Tentative de chargement de l’interpréteur de commandes web (POST) - CHOPPER PHP|
|99005004|Tentative de chargement de l’interpréteur de commandes web (POST) - CHOPPER ASPX|

### <a name="p-x-ms-format-detectionnonems-threatintel-appsecp"></a><a name="drs9903-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-AppSec</p>
|ID de la règle|Description|
|---|---|
|99030001|Esquive par traversée de chemin dans les en-têtes (/.././../)|
|99030002|Esquive par traversée de chemin dans le corps de requête (/.././../)|

### <a name="p-x-ms-format-detectionnonems-threatintel-sqlip"></a><a name="drs99031-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-SQLI</p>
|ID de la règle|Description|
|---|---|
|99031001|Attaque par injection de code SQL : Test d’injection commune détecté|
|99031002|Séquence de commentaire SQL détectée.|

### <a name="p-x-ms-format-detectionnonems-threatintel-cvesp"></a><a name="drs99001-11"></a> <p x-ms-format-detection="none">MS-ThreatIntel-CVEs</p>
|ID de la règle|Description|
|---|---|
|99001001|Tentative d’exploitation de l’API REST (CVE-2020-5902) tmui de F5 avec des informations d’identification connues|

# <a name="drs-10"></a>[DRS 1.0](#tab/drs10)

## <a name="10-rule-sets"></a><a name="drs10"></a> Ensembles de règles 1.0

### <a name="p-x-ms-format-detectionnoneprotocol-attackp"></a><a name="drs921-10"></a> <p x-ms-format-detection="none">PROTOCOL-ATTACK</p>
|ID de la règle|Description|
|---|---|
|921110|Attaque par dissimulation de requête HTTP|
|921120|Attaque par fractionnement de réponse HTTP|
|921130|Attaque par fractionnement de réponse HTTP|
|921140|Attaque par injection d’en-tête HTTP via les en-têtes|
|921150|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921151|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF détecté)|
|921160|Attaque par injection d’en-tête HTTP via la charge utile (CR/LF et nom d’en-tête détecté)|

### <a name="p-x-ms-format-detectionnonelfi---local-file-inclusionp"></a><a name="drs930-10"></a> <p x-ms-format-detection="none">Inclusion de fichier local (LFI, Local File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|930100|Attaque par traversée de chemin (/../)|
|930110|Attaque par traversée de chemin (/../)|
|930120|Tentative d’accès au fichier du système d’exploitation|
|930130|Tentative d’accès au fichier restreint|

### <a name="p-x-ms-format-detectionnonerfi---remote-file-inclusionp"></a><a name="drs931-10"></a> <p x-ms-format-detection="none">Inclusion de fichier distant (RFI, Remote File Inclusion)</p>
|ID de la règle|Description|
|---|---|
|931100|Attaque possible par inclusion de fichier distant : paramètre d’URL utilisant l’adresse IP|
|931110|Attaque possible par inclusion de fichier distant (RFI) : nom de paramètre vulnérable RFI commun utilisé avec la charge utile URL|
|931120|Attaque possible par inclusion de fichier distant : charge utile URL utilisée avec caractère point d’interrogation de fin (?)|
|931130|Attaque possible par inclusion de fichier distant : Référence/Lien hors domaine|

### <a name="p-x-ms-format-detectionnonerce---remote-command-executionp"></a><a name="drs932-10"></a> <p x-ms-format-detection="none">Exécution de commande à distance (RCE, Remote Command Execution)</p>
|ID de la règle|Description|
|---|---|
|932100|Exécution de commande à distance : Injection de commande Unix|
|932105|Exécution de commande à distance : Injection de commande Unix|
|932110|Exécution de commande à distance : Injection de commande Windows|
|932115|Exécution de commande à distance : Injection de commande Windows|
|932120|Exécution de commande à distance : commande Windows PowerShell détectée|
|932130|Exécution de commande à distance : expression de l’interpréteur de commandes Unix détectée|
|932140|Exécution de commande à distance : commande Windows FOR/IF détectée|
|932150|Exécution de commande à distance : Exécution de commande UNIX directe|
|932160|Exécution de commande à distance : code de l’interpréteur de commandes Unix détecté|
|932170|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932171|Exécution de commande à distance : Shellshock (CVE-2014-6271)|
|932180|Tentative de chargement de fichier limitée|

### <a name="p-x-ms-format-detectionnonephp-attacksp"></a><a name="drs933-10"></a> <p x-ms-format-detection="none">Attaques par PHP</p>
|ID de la règle|Description|
|---|---|
|933100|Attaque par injection de code PHP : balise d’ouverture/fermeture détectée|
|933110|Attaque par injection de code PHP : Chargement de fichier de script PHP détecté|
|933120|Attaque par injection de code PHP : directive de configuration détectée|
|933130|Attaque par injection de code PHP : Variables détectées|
|933131|Attaque par injection de code PHP : Variables détectées|
|933140|Attaque par injection de code PHP : Flux d’E/S détecté|
|933150|Attaque par injection de code PHP : nom de fonction PHP à risque élevé détecté|
|933151|Attaque par injection de code PHP : Nom de fonction PHP à risque moyen détecté|
|933160|Attaque par injection de code PHP : appel de fonction PHP à risque élevé détecté|
|933161|Attaque par injection de code PHP : Appel de fonction PHP à valeur faible détecté|
|933170|Attaque par injection de code PHP : Injection d’objet sérialisé|
|933180|Attaque par injection de code PHP : appel de fonction variable détecté|

### <a name="p-x-ms-format-detectionnonexss---cross-site-scriptingp"></a><a name="drs941-10"></a> <p x-ms-format-detection="none">XSS - Scripts intersites</p>
|ID de la règle|Description|
|---|---|
|941100|Attaque XSS détectée via libinjection|
|941101|Attaque XSS détectée via libinjection|
|941110|Filtre XSS - Catégorie 1 : vecteur de balise de script|
|941120|Filtre XSS - Catégorie 2 : vecteur de gestionnaire d’événements|
|941130|Filtre XSS - Catégorie 3 : vecteur d’attribut|
|941140|Filtre XSS - Catégorie 4 : vecteur URI JavaScript|
|941150|Filtre XSS - Catégorie 5 : attributs HTML non autorisés|
|941160|NoScript XSS InjectionChecker : Injection de code HTML|
|941170|NoScript XSS InjectionChecker : Injection d’attribut|
|941180|Mots clés de la liste rouge du validateur de nœuds|
|941190|XSS utilisant des feuilles de style|
|941200|XSS utilisant des frames VML|
|941210|XSS utilisant du JavaScript obfusqué|
|941220|XSS utilisant du VB Script obfusqué|
|941230|XSS utilisant la balise « embed »|
|941240|XSS utilisant l’attribut « import » ou « implementation »|
|941250|Filtre XSS IE - Attaque détectée.|
|941260|XSS utilisant la balise « meta »|
|941270|XSS utilisant le href « link »|
|941280|XSS utilisant la balise « base »|
|941290|XSS utilisant la balise « applet »|
|941300|XSS utilisant la balise « object »|
|941310|Filtre XSS d’encodage incorrectement formé US-ASCII - Attaque détectée.|
|941320|Possible attaque XSS détectée - Gestionnaire de balise HTML|
|941330|Filtre XSS IE - Attaque détectée.|
|941340|Filtre XSS IE - Attaque détectée.|
|941350|XSS IE d’encodage UTF-7 - Attaque détectée.|

### <a name="p-x-ms-format-detectionnonesqli---sql-injectionp"></a><a name="drs942-10"></a> <p x-ms-format-detection="none">SQLI - Injection de code SQL</p>
|ID de la règle|Description|
|---|---|
|942100|Attaque par injection de code SQL détectée via libinjection|
|942110|Attaque par injection de code SQL : Test d’injection commune détecté|
|942120|Attaque par injection de code SQL : Opérateur SQL détecté|
|942140|Attaque par injection de code SQL : noms de base de données courants détectés|
|942150|Attaque par injection de code SQL|
|942160|Détecte des tests sqli à l’aveugle à l’aide de la méthode sleep() ou benchmarch().|
|942170|Détecte les tentatives d’injection par les méthodes benchmark (test d’évaluation) et sleep (mise en veille), y compris les requêtes conditionnelles|
|942180|Détecte les tentatives de contournement d’authentification SQL de base 1/3|
|942190|Détecte les tentatives de collecte d’informations et de d’exécution de code MSSQL|
|942200|Détecte les injections MySQL de type comment-/space-obfuscated et d’accent grave|
|942210|Détecte les tentatives d’injection de code SQL chaîné 1/2|
|942220|Pour rechercher les attaques de dépassement sur les entiers, l’extraction se fait à partir de skipfish, à l’exception de 3.0.00738585072007e-308 qui est l’incident « nombre magique ».|
|942230|Détecte des tentatives d’injection de code SQL conditionnel|
|942240|Détecte les tentatives de basculement de charset MySQL et d’attaque DoS MSSQL|
|942250|Détecte les injections de code MATCH AGAINST, MERGE et EXECUTE IMMEDIATE|
|942260|Détecte les tentatives de contournement d’authentification SQL de base 2/3|
|942270|Recherche d’injection de code SQL de base. Chaîne d’attaque courante pour mysql, oracle, etc.|
|942280|Détecte l’injection de Postgres pg_sleep, les attaques par délai d’attente et les tentatives d’arrêt de base de données|
|942290|Recherche de tentatives d’injection de code SQL MongoDB de base|
|942300|Détecte les injections de ch(a)r, les conditions et les commentaires MySQL|
|942310|Détecte des tentatives d’injection de code SQL chaîné 2/2|
|942320|Détecte des injections de fonctions/procédures stockées MySQL et PostgresSQL|
|942330|Détecte les sondes d’injection SQL classiques 1/2|
|942340|Détecte les tentatives de contournement d’authentification SQL de base 3/3|
|942350|Détecte l’injection de code UDF MySQL et autres tentatives de manipulation de données/structures|
|942360|Détecte l’injection SQL de base concaténée et les tentatives SQLLFI|
|942361|Détecte l’injection de code SQL de base par le biais du mot clé alter ou union|
|942370|Détecte les sondes d’injection SQL classiques 2/2|
|942380|Attaque par injection de code SQL|
|942390|Attaque par injection de code SQL|
|942400|Attaque par injection de code SQL|
|942410|Attaque par injection de code SQL|
|942430|Détection restreinte d’anomalies de caractères SQL (args) : nombre de caractères spéciaux dépassés (12)|
|942440|Séquence de commentaire SQL détectée.|
|942450|Encodage hexadécimal SQL identifié|
|942470|Attaque par injection de code SQL|
|942480|Attaque par injection de code SQL|

### <a name="p-x-ms-format-detectionnonesession-fixationp"></a><a name="drs943-10"></a> <p x-ms-format-detection="none">SESSION-FIXATION</p>
|ID de la règle|Description|
|---|---|
|943100|Attaque possible par fixation de session : définition de valeurs de cookies en HTML|
|943110|Attaque possible par fixation de session : nom du paramètre SessionID avec référent hors domaine|
|943120|Attaque possible par fixation de session : nom du paramètre SessionID sans référent|

### <a name="p-x-ms-format-detectionnonejava-attacksp"></a><a name="drs944-10"></a> <p x-ms-format-detection="none">Attaques par Java</p>
|ID de la règle|Description|
|---|---|
|944100|Exécution de commande à distance : Apache Struts, Oracle WebLogic|
|944110|Détecte l’exécution de charge utile potentielle|
|944120|Exécution de charge utile et de commande à distance possible|
|944130|Classes Java suspectes|
|944200|Exploitation de la désérialisation Java Apache Commons|
|944210|Utilisation possible de la sérialisation Java|
|944240|Exécution de commande à distance : sérialisation Java|
|944250|Exécution de commande à distance : méthode Java suspecte détectée|

# <a name="bot-rules"></a>[Règles de bot](#tab/bot)

## <a name="bot-manager-rule-sets"></a><a name="bot"></a> Ensembles de règles du gestionnaire de bots

### <a name="p-x-ms-format-detectionnonebad-botsp"></a><a name="bot100"></a> <p x-ms-format-detection="none">Bots malveillants</p>
|ID de la règle|Description|
|---|---|
|Bot100100|Bots malveillants détectés par le renseignement sur les menaces|
|Bot100200|Bots malveillants ayant falsifié leur identité|

### <a name="p-x-ms-format-detectionnonegood-botsp"></a><a name="bot200"></a> <p x-ms-format-detection="none">Bots bienveillants</p>
|ID de la règle|Description|
|---|---|
|Bot200100|Robots de moteur de recherche|
|Bot200200|Robots de moteur de recherche non vérifiés|

### <a name="p-x-ms-format-detectionnoneunknown-botsp"></a><a name="bot300"></a> <p x-ms-format-detection="none">Bots inconnus</p>
|ID de la règle|Description|
|---|---|
|Bot300100|Identité non spécifiée|
|Bot300200|Outils et infrastructures pour les attaques et l’indexation sur le web|
|Bot300300|Kits SDK et clients HTTP à usage général|
|Bot300400|Agents de service|
|Bot300500|Services de supervision de l’intégrité du site|
|Bot300600|Bots inconnus détectés par le renseignement sur les menaces|
|Bot300700|Autres bots|

---


## <a name="next-steps"></a>Étapes suivantes

- [Règles personnalisées pour le pare-feu d’applications web avec Azure Front Door](waf-front-door-custom-rules.md)
