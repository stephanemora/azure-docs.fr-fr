---
title: Codes d’erreur et accusés de réception CONTRL EDIFACT
description: En savoir plus sur les codes d’erreur et les accusés de réception CONTRL pour les messages EDIFACT dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/25/2021
ms.openlocfilehash: 056538b5a6b52fcae646f5f03c6e39c8fce6429f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768075"
---
# <a name="contrl-acknowledgments-and-error-codes-for-edifact-messages-in-azure-logic-apps"></a>Codes d’erreur et accusés de réception CONTRL pour les messages EDIFACT dans Azure Logic Apps

Dans Azure Logic Apps, vous pouvez créer des workflows qui gèrent les messages EDIFACT pour la communication EDI (échange de données informatisé) lorsque vous utilisez des opérations **EDIFACT**. Dans la messagerie EDI, les accusés de réception fournissent l’état du traitement d’un échange EDI. Lors de la réception d’un échange, l’[**action** Décodage EDIFACT](logic-apps-enterprise-integration-edifact-decode.md) peut renvoyer un ou plusieurs types d’accusés de réception à l’expéditeur, en fonction des types d’accusés de réception activés et du niveau de validation spécifié.

Cette rubrique fournit une petite vue d’ensemble de l’ACK CONTRL EDIFACT, les segments ACK CONTRL dans un échange et les codes d’erreur utilisés dans ces segments.

## <a name="contrl-as-technical-and-functional-acknowledgment"></a>CONTRL en tant qu’accusé de réception technique et fonctionnel

Pour les messages EDIFACT, l’accusé de réception (ACK) CONTRL sert d’accusé de réception technique *et* d’accusé de réception opérationnel. Le message CONTRL complet sert d’ACK fonctionnel, tandis que les sections de l’ACK fonctionnel sont réutilisées pour l’ACK technique. Par exemple, si vous sélectionnez les ACK techniques et fonctionnels dans les propriétés de l’accord pour le partenaire émetteur ou dans les propriétés globales, l’action de **Décodage EDIFACT** génère deux messages CONTRL, un ACK CONTRL technique et un fonctionnel. L’ACK CONTRL est conforme au schéma pour **EFACT_<*version-number*>_CONTRL.xsd**.

> [!NOTE]
> Avec le traitement des messages EDIFACT dans Azure Logic Apps, un message CONTRL, qu’il s’agisse d’un accusé de réception d’une acceptation ou d’un rejet, est envoyé en tant que réponse à un échange reçu contenant un ou plusieurs messages CONTRL. Dans Logic Apps, aucun message CONTRL n’est envoyé en réponse à un échange reçu contenant un ou plusieurs messages CONTRL. 
>
> Les erreurs rencontrées dans les messages CONTRL reçus doivent être signalées autrement que par l'intermédiaire d'un message CONTRL. Lorsqu’un échange contenant des messages de données comprend un ou plusieurs messages CONTRL, un message CONTRL généré en réponse à cet échange est créé comme si aucun message CONTRL n’avait été inclus dans l’échange reçu.

En guise d’accusé de réception technique, le message CONTRL indique que le destinataire de l’échange a reçu l’échange d’objet, en plus des éléments suivants :

* Vérifie des parties de l’échange pour confirmer la précision syntaxique des éléments de données copiés dans le segment Réponse d’échange (UCI) de rapports.
* Accepte la responsabilité d’informer l’expéditeur de l’acceptation ou du rejet des autres parties de l’échange.
* Prend des mesures raisonnables pour informer l’expéditeur.

> [!NOTE]
> Un rapport d’ACK technique CONTRL indique l’état `Rejected` uniquement lorsque le message EDIFACT entrant est un doublon ou qu’il existe des erreurs dans l’enveloppe, par exemple un problème avec le jeu de caractères. EDIFACT ne signale pas l’état `Interchange accepted with errors` dans un ACK technique CONTRL, car x12 utilise le champ TA104 dans un accusé de réception TA1. Si une partie du message EDIFACT est acceptée, l’ACK technique CONTRL indique l’état `Accepted`. Dans certains scénarios, si une partie du message est rejetée, l’ACK CONTRL signale toujours l’état `Accepted`. Dans ce cas, l’élément UCI5 peut signaler l’erreur.

En tant qu’accusé de réception fonctionnel, le message CONTRL indique l’état, tel que l’acceptation ou le rejet, pour l’échange, le groupe ou le message reçu, notamment les erreurs ou fonctionnalités non prises en charge. Le message indique également que le destinataire de l’échange :

* a reçu des niveaux référencés de l’échange confirmé ;
* a vérifié qu’aucune erreur syntaxique irrécupérable dans le niveau référencé confirmé n’empêche le traitement d’autres échanges ;
* a vérifié que toutes les parties confirmées des segments de service sont sémantiquement correctes, si aucune erreur n’est signalée ;
* se soumet aux actions requises dans les niveaux référencés confirmés des segments de service ;
* a accepté la responsabilité d’informer l’expéditeur par un autre moyen que l’envoi d’un message CONTRL dans les cas suivants :

  * Toutes les erreurs syntaxiques ou sémantiques se trouvent ensuite dans la partie correspondante.
  * La partie ne peut pas être traitée pour une autre raison une fois que la partie a été confirmée dans un message CONTRL envoyé.

* prend des mesures raisonnables pour s’assurer que ce type d’erreurs est détecté et informer l’expéditeur.

Le rejet implique que le destinataire de l’échange :

* n’arrive pas à accepter l’échange ou toute autre partie pertinente pour les raisons indiquées dans le message CONTRL ;
* n’entreprend aucune autre action sur les informations professionnelles contenues dans la partie rejetée de l’échange d’objet.

## <a name="contrl-technical-ack-segments"></a>Segments ACK technique CONTRL

Si vous sélectionnez l’option permettant de générer un accusé de réception technique dans un accord EDIFACT ou si le champ de message **UNB9** est défini sur `2`, un message CONTRL est généré en tant qu’ACK technique pour signaler les résultats de la réception de l’échange.

Le tableau suivant décrit les segments ACK technique CONTRL dans un échange et utilise les définitions suivantes :

* O = obligatoire
* F = facultatif

| Nom | Obligatoire ou facultatif | Description |
|------|-----------------------|-------------|
| En-tête de message UNH | M | Le segment d’en-tête de message (UNH) identifie et spécifie le message. |
| Réponse d’échange UCI | M | Identifie l’échange et indique la nature de la réception de l’échange. Le segment UCI a une occurrence maximale de `1` et, par conséquent, signale la première erreur trouvée dans un segment de contrôle. |
| Code de fin de message UNT | M | Une erreur est signalée dans l’élément de données UCI5 nommé `Syntax Error Code`. Pour les messages EDIFACT, il n’existe aucune condition `Accepted with errors`, comme pour les échanges X12. |
||||

### <a name="contrl-technical-ack-data-elements"></a>Éléments de données de l’ACK technique CONTRL

L'accusé de réception technique CONTRL inclut les éléments de données suivants :

| Élément | Nom | Utilisation |
|---------|------|-------|
| UNH1 | Numéro de référence du message | - |
| UNH2 | Sous-composants de l'identificateur de message | Sous-composants : <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Numéro de contrôle de l’échange | Mappé à partir du champ UNB5 du message reçu. |
| UCI2 | Expéditeur des échanges | Mappé à partir du champ UNB2 du message reçu. <p><p>- Obligatoire : le premier sous-composant, ou identification. <br>- Facultatif : le deuxième sous-composant, ou qualificateur du code, et le troisième composant, ou adresse de routage inverse. |
| UCI3 | Destinataire de l’échange | Mappé à partir du champ UNB3 du message reçu. <p><p>- Obligatoire : le premier sous-composant, ou identification. <br>Facultatif : le deuxième sous-composant, ou qualificateur du code. |
| UCI4 | Code d'action | Cet élément est obligatoire. Codes d’action : <p><p>- 8, si l’échange est accepté. <br>- 7, si l’échange est accepté, mais que certains documents informatisés sont rejetés. <br>- 4, si l’échange est rejeté en raison d’une erreur dans le segment UNA ou UNB. |
| UCI5 | Code d'erreur de syntaxe | Cet élément est conditionnellement facultatif et identifie la condition d’erreur, le cas échéant. |
| UCI6 | Balise de segment de service | Cet élément est conditionnellement facultatif et identifie le segment présentant la condition d’erreur dans l’élément de données UCI5. |
| UCI7 | Identification de l'élément de données | Identifie les éléments de données présentant la condition d’erreur dans l’élément de données UCI5. Sous-composants : <p><p>- Obligatoire : position de l’élément de données erroné dans le segment. <br>- Conditionnellement facultatif : position de l’élément de données de composant erroné dans le segment et occurrence de l’élément de données erroné dans le segment. |
| UCI8 | - | - |
| UNT1 | Nombre de segments | - |
| UNT2 | Numéro de référence du message | - |
||||

## <a name="contrl-functional-ack-segments"></a>Segments ACK fonctionnel CONTRL

Si vous sélectionnez l’option permettant de générer un accusé de réception fonctionnel dans un accord EDIFACT ou si le champ de message **UNB9** est défini sur `1`, un message CONTRL est généré en tant qu’ACK fonctionnel pour signaler les résultats de la vérification de la syntaxe de l’échange.

Le tableau suivant décrit les segments ACK fonctionnel CONTRL dans un échange et utilise les définitions suivantes :

* O = obligatoire
* F = facultatif

| Nom | Obligatoire ou facultatif | Description |
|------|-----------------------|-------------|
| En-tête de message UNH | M | Le segment d’en-tête de message (UNH) identifie et spécifie le message. |
| Segment UCI | M | Identifie l’échange, indique l’état de la réception de l’échange et contient des références aux segments UNA, UNB et UNZ dans l’échange reçu. Le segment UCI a une occurrence maximale de `1` et, par conséquent, signale la première erreur trouvée dans un segment de contrôle. |
| Segment UCF | M, si le segment UNG existe | Identifie un segment de groupe, qui est encapsulé par l’en-tête UNG et l’amorce de fin UNE, et indique la nature des erreurs. |
| Segment UCM | M | Identifie un segment de message, qui est encapsulé par l’en-tête UNH et l’amorce de fin UNT, et indique la nature des erreurs. |
| Segment UCS | M | Identifie un document informatisé et indique la nature des erreurs. |
| Segment UCD | O, conditionnellement | Identifie un élément de données composites (de composant) erroné et indique la nature de l’erreur. |
| Code de fin de message UNT | M | Lorsqu’un accusé de réception fonctionnel CONTRL reçu contient uniquement des segments UNH, UCI et UNT, le pipeline EDIReceive traite l’accusé de réception comme un ACK technique de reçu CONTRL. À un niveau de rapport, chaque instance de segment ne peut signaler qu’une seule erreur (par exemple, les segments UCI, UCF, UCM, UCS et UCD). |
||||

### <a name="sg-loops-and-contrl-functional-ack-structure"></a>Boucles SG et structure de l’ACK fonctionnel CONTRL

La structure de l’ACK fonctionnel CONTRL diffère en fonction du nombre de groupes (un ou plusieurs) compris dans l’échange reçu.

* Si l’échange comporte un seul groupe, l’ACK contient un segment UCF par groupe. Chaque segment UCF contient un segment UCM par message. Chaque segment UCM comprend également une série de segments UCS et UCD en tandem.

  Le formulaire XML du message ACK comprend les éléments de boucle suivants :

  * un élément SG3Loop qui encapsule chaque segment UCF ;
  * un élément SG4Loop qui encapsule chaque élément UCM ;
  * un élément SG5Loop qui encapsule chaque paire d’éléments UCS et UCD.

  Les étiquettes de la boucle SG n’existent pas dans le format de message EDI natif.

* Si l’échange n’inclut aucun groupe, l’ACK ne contient aucun segment UCF. À la place, l’ACK comprend un segment UCM par message. Chaque segment UCM comprend une série de segments UCS et UCD en tandem.

* Le formulaire XML du message ACK comprend les éléments de boucle suivants :

  * un élément SG1Loop qui encapsule chaque élément UCM ;
  * un élément SG2Loop qui encapsule chaque paire d’éléments UCS et UCD.

  Comme pour les échanges incluant des groupes, les étiquettes SG n’existent pas dans le format ACK natif.

En fonction de leur utilisation par défaut et industrielle, les boucles SG1/SG4 ne sont pas prévues dans les documents informatisés acceptés. Toutefois, pour prendre en charge la conformité aux normes, vous pouvez forcer la génération de boucles SG1/SG4 en procédant comme suit :
 
1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre compte d’intégration.
1. Dans le menu du compte d’intégration, sous **Paramètres**, sélectionnez **Accords**.
1. Ouvrez votre accord EDIFACT et sélectionnez **Paramètres d’envoi**.
1. Sous **Accusé de réception**, sélectionnez **Accusé de réception (CONTRL)** . Vous pouvez désormais sélectionner **Générer une boucle SG1/SG4 pour les documents informatisés acceptés**.

   Si cette case est cochée, le pipeline de réception génère des boucles SG1/SG4 que le document informatisé soit accepté ou rejeté. Dans le cas contraire, ces boucles sont générées uniquement pour les documents informatisés erronés dans lesquels UCM5 n’est pas égal à `7`.

### <a name="contrl-functional-ack-data-elements"></a>Éléments de données de l’ACK fonctionnel CONTRL

Le message CONTRL contient plusieurs éléments de données obligatoires qui sont copiés à partir de l’échange reçu. Lorsqu’un élément de données de l’échange est manquant ou syntaxiquement non valide, un message CONTRL syntaxiquement valide ne peut pas être généré. L’erreur doit alors être signalée autrement que par le biais d’un message CONTRL.

La notification de transactions opérationnelles CONTRL inclut les éléments de données suivants :

| Élément | Nom | Utilisation |
|---------|------|-------|
| UNH1 | Numéro de référence du message | - |
| UNH2 | Sous-composants de l'identificateur de message | Sous-composants : <p><p>- 1 = CONTRL <br>- 2 = 4 <br>- 3 = 1 <br>- 4 = UN |
| UCI1 | Numéro de contrôle de l’échange | Mappé à partir du champ UNB5 du message reçu. |
| UCI2 | Expéditeur des échanges | Mappé à partir du champ UNB2 du message reçu. <p><p>- Obligatoire : le premier sous-composant, ou identification. <br>- Facultatif : le deuxième sous-composant, ou qualificateur du code, et le troisième composant, ou adresse de routage inverse. |
| UCI3 | Destinataire de l’échange | Mappé à partir du champ UNB3 du message reçu. <p><p>- Obligatoire : le premier sous-composant, ou identification. <br>Facultatif : le deuxième sous-composant, ou qualificateur du code. |
| UCI4 | Code d'action | Cet élément est obligatoire. Codes d’action : <p><p>- 8, si l’échange est accepté. <br>- 7, si l’échange est accepté, mais que certains documents informatisés sont rejetés. <br>- 4, si l’échange est rejeté en raison d’une erreur dans le segment UNA ou UNB. |
| UCI5 | Code d'erreur de syntaxe | Cet élément est conditionnellement facultatif et identifie la condition d’erreur, le cas échéant. |
| UCI6 | Balise de segment de service | Cet élément est conditionnellement facultatif et identifie le segment présentant la condition d’erreur dans l’élément de données UCI5. |
| UCI7 | Identification de l'élément de données | Identifie les éléments de données présentant la condition d’erreur dans l’élément de données UCI5. Sous-composants : <p><p>- Obligatoire : position de l’élément de données erroné dans le segment. <br>- Conditionnellement facultatif : position de l’élément de données de composant erroné dans le segment et occurrence de l’élément de données erroné dans le segment. |
| UCI8 | - | - |
| UCF1 | Numéro de référence du groupe | Cet élément est obligatoire et est mappé à partir du champ UNG5 dans le message reçu. |
| UCF2 | Identification de l’expéditeur de l’application | Cet élément est conditionnellement facultatif et est mappé à partir du champ UNG2 dans le message reçu avec les sous-composants. |
| UCF3 | Identification du destinataire de l’application | Cet élément est conditionnellement facultatif et est mappé à partir du champ UNG3 dans le message reçu avec les sous-composants. |
| UCF4 | Action codée | Cet élément est obligatoire et le code s’applique à ce niveau, ainsi qu’à tous les niveaux inférieurs. Codes d’action : <p><p>- 7, si l’échange est accepté. <br>- 4, si l’échange est rejeté en raison d’une erreur dans le segment UNA ou UNB. |
| UCF5 | Erreur de syntaxe, codée | Cet élément est conditionnellement facultatif et identifie la condition d’erreur dans le groupe, le cas échéant. |
| UCF6 | Balise de segment de service | Cet élément est conditionnellement facultatif et identifie le segment erroné dans le groupe. |
| UCF7 | Identification de l'élément de données | Identifie les éléments de données disposant de la condition d'erreur spécifiée dans l'élément de données UCF5. Sous-composants : <p><p>- Obligatoire : position et occurrence de l’élément de données erroné dans le segment. <br>- Conditionnellement facultatif : position de l’élément de données de composant erroné dans le segment. |
| UCM1 | Numéro de référence du message| Cet élément est obligatoire et est mappé à partir du champ UNH1 dans le message reçu. |
| UCM2 | Identificateur du message | Cet élément est conditionnellement facultatif et est mappé à partir du champ UNH2 dans le message reçu avec les sous-composants. |
| UCM3 | Action codée | Cet élément est obligatoire et le code s’applique à ce niveau, ainsi qu’à tous les niveaux inférieurs. Codes d’action : <p><p>- 7, si l’échange est accepté. <br>- 4, si l’échange est rejeté en raison d’une erreur dans le segment UNA ou UNB. |
| UCM4 | Erreur de syntaxe, codée | Cet élément est conditionnellement facultatif et identifie la condition d’erreur dans le groupe, le cas échéant. |
| UCM5 | Balise de segment de service | Cet élément est conditionnellement facultatif et identifie le segment UNH ou UNT erroné. |
| UCM7 | Identification de l'élément de données | Identifie les éléments de données disposant de la condition d'erreur spécifiée dans l'élément de données UCM5. Sous-composants : <p><p>- Obligatoire : position et occurrence de l’élément de données erroné dans le segment. <br>- Conditionnellement facultatif : position de l’élément de données de composant erroné dans le segment. |
| UCS1 | Position du segment dans le corps du message | Cet élément est obligatoire et représente le nombre de positions du segment erroné, en commençant par UNH défini sur `1`. Pour signaler qu’un segment est manquant, la valeur est le nombre de positions numérique du dernier segment qui a été traité avant la position où est supposé se trouver le segment manquant. Un groupe de segments manquant est identifié en désignant le premier segment du groupe comme manquant. |
| UCS2 | Erreur de syntaxe codée | Cet élément est conditionnellement facultatif et identifie la condition d’erreur dans le groupe, le cas échéant. |
| UCD1 | Erreur de syntaxe codée| Cet élément est conditionnellement facultatif et identifie la condition d’erreur dans le groupe, le cas échéant. <p><p>**Remarque** : si un échec de validation XSD se produit, l’élément de données UCD1 signale une valeur du code de `12, Invalid Value`. |
| UCD2 | Identification de l'élément de données | Identifie les éléments de données disposant de la condition d'erreur spécifiée dans l'élément de données UCD1. Sous-composants : <p><p>- Obligatoire : position et occurrence de l’élément de données erroné dans le segment. <br>- Conditionnellement facultatif : position de l’élément de données de composant erroné dans le segment. |
| UNT1 | Nombre de segments | - |
| UNT2 | Numéro de référence du message | - |
||||

## <a name="contrl-ack-error-codes"></a>Codes d’erreur de l’ACK CONTRL

Ces erreurs s’appliquent aux niveaux de l’échange, du groupe, du message et des données. Lorsqu’une erreur prise en charge est détectée, l’intégralité de l’échange, du groupe ou du document informatisé est rejeté. Les échanges EDIFACT ne disposent pas d’une condition `Accepted with errors` comme pour les messages X12.

### <a name="standard-edifact-contrl-ack-error-codes"></a>Codes d’erreur de l’ACK CONTRL EDIFACT standard

Le tableau suivant répertorie les codes d’erreur pris en charge, tels que définis par la spécification EDIFACT, qui sont utilisés dans le champ UCI5 de l’ACK CONTRL pour le traitement des messages EDIFACT dans Azure Logic Apps.

| Code d'erreur | Condition | Cause | Pris en charge ? |
|------------|-----------|-------|------------|
| 2 | Version ou niveau de la syntaxe non pris en charge | Notification indiquant que la version et/ou le niveau de la syntaxe n’est pas pris en charge par le destinataire. | Non |
| 7 | Le destinataire de l'échange n'est pas le destinataire réel | Notification indiquant que le destinataire de l’échange (S003) est différent du destinataire réel. | Non |
| 12 | Valeur non valide | Indique que la valeur d'un élément de données autonomes ou composites n'est pas conforme aux spécifications appropriées pour cette valeur. | Oui |
| 13 | Manquant | Notification indiquant qu’un service, ou segment utilisateur, un élément de données, un élément de données composites, ou de composant, obligatoire, ou requis, est manquant. | Oui |
| 14 | Valeur non prise en charge à cette position | Notification indiquant que le destinataire ne prend pas en charge l’utilisation de la valeur spécifique d’un élément de données autonomes ou composites (de composant) identifié dans la position où il est utilisé. La valeur peut être valide en fonction des spécifications appropriées et peut être prise en charge si elle est utilisée à un autre position. | Non |
| 15 | Non pris en charge à cette position | Indique que le destinataire ne prend pas en charge l'utilisation du type de segment, ou du type d'élément de données autonomes ou composites à cette position. | Oui |
| 16 | Trop de composants | Indique que le segment identifié contient trop d'éléments de données ou que l'élément de données composites identifié contient trop de composants. | Oui |
| 17 | Aucun accord | Aucun accord n'existe pour autoriser la réception d'un échange, d'un groupe, d'un message ou d'un package avec la valeur de l'élément de données autonomes ou composites identifié. | Non |
| 18 | Erreur non spécifiée | Indique qu'une erreur a été identifiée, mais que la nature de celle-ci n'est pas signalée. | Non |
| 19 | Notation décimale non valide | Indique que le caractère indiqué en tant que notation décimale dans le segment UNA est non valide, ou que la notation décimale utilisée dans un élément de données n'est pas cohérente avec celle indiquée dans le segment UNA. | Non |
| 20 | Caractère non valide en tant que caractère de service | Indique qu'un caractère détecté dans le segment UNA est non valide en tant que caractère de service. | Non |
| 21 | Caractère(s) non valide(s) | Notification indiquant qu’un ou plusieurs caractères de l’échange sont considérés comme non valides par l’identificateur de syntaxe indiqué dans le segment UNB. Le caractère non valide fait partie du niveau référencé, ou est immédiatement suivi par la partie identifiée de l'échange. | Oui |
| 22 | Caractère(s) de service non valide(s) | Notification indiquant que les caractères de service utilisés dans l’échange ne sont pas valides comme indiqué dans le segment UNA ou ne correspondent pas à ceux par défaut. Si le code est utilisé dans le segment UCS ou UCD, le caractère non valide est suivi immédiatement par une partie identifiée de l'échange. | Non |
| 23 | Expéditeur de l'échange inconnu | Indique que l'expéditeur de l'échange (S002) est inconnu. | Non |
| 24 | Trop ancien | Indique que l'échange ou le groupe reçu est antérieur à la limite spécifiée dans l'IA ou déterminée par le destinataire. | Non |
| 25 | L'indicateur Test n'est pas pris en charge | Indique que le traitement du test ne peut pas être effectué pour l'échange, le groupe, le message ou le package identifié. | Non |
| 26 | Doublon détecté | Indique qu'un éventuel doublon d'un échange, d'un groupe, d'un message ou d'un package précédemment reçu a été détecté. La transmission précédente peut avoir été rejetée. | Oui |
| 27 | La fonction de sécurité n'est pas prise en charge | Indique qu'une fonction de sécurité associée au niveau référencé ou à l'élément de données n'est pas prise en charge. | Non |
| 28 | Les références ne correspondent pas | Indique que le contrôle de référence du segment UNB, UNG, UNH, UNO, USH ou USD ne correspond pas, respectivement, au contrôle de référence du segment UNZ, UNE, UNT, UNP, UST ou USU. | Non |
| 29 | Le nombre de contrôles ne correspond pas au nombre d'instances reçues | Notification indiquant le nombre de groupes, de messages ou de segments qui ne correspond pas au nombre donné dans le segment UNZ, UNE, UNT ou UST. Ou bien, la longueur d’un objet ou des données chiffrées n’est pas égale à la longueur indiquée dans le segment UNO, UNP, USD ou USU. | Oui |
| 30 | Groupes et messages/packages mélangés | Notification indiquant que les groupes ont été mélangés avec les messages ou les packages hors des groupes de l’échange. | Non |
| 31 | Plusieurs types de messages dans le groupe | Indique que différents types de messages sont contenus dans un groupe fonctionnel. | Oui |
| 32 | Niveau inférieur vide | Notification indiquant l’une des conditions suivantes : <p><p>- L’échange ne contient aucun message, package ou groupe. <br>- Un groupe ne contient aucun message ou package. | Non |
| 33 | Occurrence non valide hors du message, du package ou du groupe|Indique la présence d'un segment ou d'un élément de données non valide dans l'échange, entre les messages, les packages ou les groupes. Le rejet est signalé au niveau supérieur. | Oui |
| 34 | Indicateur d'imbrication non autorisé | Notification indiquant que l’imbrication explicite a été utilisée dans un message dans lequel elle ne doit pas l’être. | Non |
| 35 | Trop d'éléments de données ou de répétitions de segments | Indique qu'un élément de données autonomes ou composites, ou un segment, est répété trop souvent. | Oui |
| 36 | Trop de répétitions de groupes de segments | Indique qu'un groupe de segments est répété trop souvent. | Oui |
| 37 | Type de caractère non valide | Notification indiquant l’une des conditions suivantes : <p><p>- Un ou plusieurs caractères numériques sont utilisés dans un élément de données alphabétiques (composant). <br>- Un ou plusieurs caractères alphabétiques sont utilisés dans un élément de données numériques (composant). | Oui |
| 38 | Chiffre manquant devant le signe décimal | Indique qu'un signe décimal n'est pas précédé d'un ou plusieurs chiffres. | Oui |
| 39 | Élément de données trop long | Indique que la longueur de l'élément de données reçu dépasse la longueur maximale spécifiée dans la description de l'élément de données. | Oui |
| 40 | Élément de données trop court | Indique que la longueur de l'élément de données reçu est inférieure à la longueur minimale spécifiée dans la description de l'élément de données. | Oui |
| 41 | Erreur permanente du réseau de communication | Indique qu'une erreur permanente a été signalée par le réseau de communication utilisé pour le transfert de l'échange. La retransmission d’un échange identique avec les mêmes paramètres au niveau du réseau va échouer. | Non |
| 42 | Erreur temporaire du réseau de communication | Indique qu'une erreur temporaire a été signalée par le réseau de communication utilisé pour le transfert de l'échange. La retransmission d’un échange identique peut réussir. | Non |
| 43 | Destinataire de l'échange inconnu | Indique que le destinataire de l'échange est inconnu du fournisseur réseau. | Non |
| 45 | Séparateur de fin | Notification indiquant l’une des conditions suivantes : <p><p>- Le dernier caractère avant le terminateur de segment est un séparateur d’éléments de données, un séparateur d’éléments de données de composant ou un séparateur d’éléments de données répétées. <br>- Le dernier caractère avant un séparateur d’éléments de données est un séparateur d’éléments de données de composant ou répétées. | Oui |
| 46 | Jeu de caractères non pris en charge | Notification indiquant l’une des conditions suivantes : <p><p>- Un ou plusieurs caractères utilisés ne sont pas présents dans le jeu de caractères défini par l’identificateur de syntaxe. <br>- Le jeu de caractères identifié par la séquence d’échappement pour la technique d’extension de code n’est pas pris en charge par le destinataire. | Oui |
| 47 | La fonctionnalité d'enveloppe n'est pas prise en charge. | Indique que la structure d'enveloppe rencontrée n'est pas prise en charge par le destinataire. | Oui |
| 48 | Condition des notes de dépendance violée | Indique qu'une violation de condition de dépendance a produit une condition d'erreur. | Non |
|||||

### <a name="azure-logic-apps-contrl-ack-error-codes"></a>Codes d’erreur de l’ACK CONTRL d’Azure Logic Apps

Le tableau suivant répertorie les codes d’erreur personnalisés qui ne sont pas définis par la spécification EDIFACT, mais qui sont utilisés dans le champ UCI5 de l’ACK CONTRL pour le traitement des messages EDIFACT et qui sont spécifiques à Azure Logic Apps.

| Code d'erreur | Condition | Cause |
|------------|-----------|-------|
| 70 | Document informatisé manquant ou identificateur de document informatisé non valide | Indique que l'identificateur de document informatisé est manquant ou non valide. |
| 71 | Document informatisé ou numéro de contrôle du groupe incohérent | Indique une incohérence entre le document informatisé ou les numéros de contrôle du groupe |
| 72 | ID de segment non reconnu | Indique que l'ID du segment n'est pas reconnu. |
| 73 | XML n'est pas à la position correcte | Indique qu'un problème est survenu lors de la sérialisation de l'élément racine XML. |
| 74 | Répétitions de groupes de segments insuffisantes | Indique que le nombre de répétitions d'un groupe de segments est inférieur au nombre requis. |
| 75 | Répétitions de segments insuffisantes | Indique que le nombre de répétitions de segments est inférieur au nombre requis. |
| 76 | Éléments de données trouvés insuffisants | Indique que le nombre d'éléments de données trouvés est insuffisant. |
||||

## <a name="next-steps"></a>Étapes suivantes

[Échanger des messages EDIFACT](logic-apps-enterprise-integration-edifact.md)
