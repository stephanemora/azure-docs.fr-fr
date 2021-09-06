---
title: Accusés de réception et codes d’erreur X12 997
description: Découvrez les accusés de réception fonctionnels et les codes d’erreur 997 pour les messages X12 dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: ec727997663286b732fe7bf1d00a50da2f706a0a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481389"
---
# <a name="997-functional-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Accusés de réception fonctionnels et codes d’erreur 997 pour les messages X12 dans Azure Logic Apps

Dans Azure Logic Apps, vous pouvez créer des workflows qui gèrent les messages X12 pour la communication EDI (échange de données informatisé) lorsque vous utilisez des opérations **X12**. Dans la messagerie EDI, les accusés de réception fournissent l’état d’un échange EDI. Lors de la réception d’un échange, l’[**action** Décodage X12](logic-apps-enterprise-integration-x12-decode.md) peut renvoyer un ou plusieurs types d’accusés de réception à l’expéditeur, en fonction des types d’accusés de réception activés et du niveau de validation spécifié.

Par exemple, le destinataire signale l’état de la validation de l’en-tête de groupe fonctionnel (GS) et du code de fin de groupe fonctionnel (GE) dans le message de réception X12, en envoyant un *accusé de réception fonctionnel 997 (ACK)* , ainsi que chaque erreur qui se produit pendant le traitement. L’action **Décodage X12** génère toujours un ACK 997 conforme à 4010, tandis que les [**actions** Encodage X12](logic-apps-enterprise-integration-x12-encode.md) et **Décodage X12** peuvent toutes deux valider un ACK 997 conforme à 5010.

Le récepteur envoie l’ACK 997 dans une enveloppe d’en-tête de groupe fonctionnel (GS) et de code de fin de groupe fonctionnel (GE). Toutefois, cette enveloppe GS et GE n’est pas différente de celle d’un autre document informatisé.

Cette rubrique fournit un aperçu de l’ACK 997 X12, y compris les segments ACK 997 dans un échange et les codes d’erreur utilisés dans ces segments. Pour plus d’informations à ce sujet, consultez la documentation suivante :

* [Accusés de réception techniques et codes d’erreur X12 TA1](logic-apps-enterprise-integration-x12-ta1-acknowledgment.md)
* [Échanger des messages XS2 pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-x12.md)
* [Échanger des messages EDIFACT pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-edifact.md)
* [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md)
* [Solutions d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

<a name="997-ack-segments"></a>

## <a name="997-ack-segments"></a>Segments ACK 997

Le tableau suivant décrit les segments ACK 997 dans un échange et utilise les définitions suivantes :

* O = obligatoire
* F = facultatif

| Position | ID de segment | Name | Désignation requise <br>(Dés. req.) | Utilisation maximale | Répétition de boucle |
|----------|------------|------|--------------------------------------|-------------|-------------|
| 010 | ST | En-tête de document informatisé, pour l’accusé de réception | M | 1 | - |
| 020 | AK1 | En-tête de réponse du groupe fonctionnel | M | 1 | - |
| 030 | AK2 | En-tête de réponse du document informatisé | O | 1 | 999999 <br>(ID boucle = AK2) |
| 040 | AK3 | Note de segment de données | O | 1 | 999999 <br>(ID de boucle = AK2 ou AK3) |
| 050 | AK4 | Note d'élément de données| O | 9 9 | - |
| 060 | AK5 | Code de fin de réponse du document informatisé | M | 1 | - |
| 070 | AK9 | Code de fin de réponse du groupe fonctionnel | M | 1 | - |
| 080 | SE | Code de fin du document informatisé, pour l’accusé de réception | M | 1 | - |
|||||||

Les sections ci-dessous fournissent des informations supplémentaires sur chaque segment AK. Dans la boucle AK2 à AK5, les segments fournissent des informations sur une erreur avec un document informatisé.

### <a name="ak1"></a>AK1

Le segment AK1 obligatoire identifie le groupe fonctionnel à soumettre à accusé de réception en utilisant les éléments de données suivants :

| Élément | Description |
|---------|-------------|
| AK101 | Obligatoire, identifie l’ID de groupe fonctionnel (GS01) du groupe fonctionnel à soumettre à accusé de réception. |
| AK102 | Obligatoire, identifie le numéro de contrôle de groupe (GS06 et GE02) du groupe fonctionnel à soumettre à accusé de réception. |
| AK103 | Facultatif, identifie la version d’implémentation d’EDI envoyée dans le GS08 de la transaction d’origine. AK103 prend en charge un ACK 997 entrant conforme à 5010. |
|||

### <a name="ak2"></a>AK2

Le segment AK2 facultatif contient un accusé de réception pour un document informatisé dans le groupe fonctionnel reçu. S’il existe plusieurs segments AK2, ils sont envoyés sous la forme d’une série de boucles. Chaque boucle AK2 identifie un document informatisé selon l’ordre reçu. Si un document informatisé est erroné, une boucle AK2 contient les segments AK3, AK4 et AK5. Pour plus d’informations, veuillez consulter la description des segments plus loin dans cette rubrique.

Le segment AK2 identifie le document informatisé à l’aide des éléments de données suivants :

| Élément | Description |
|---------|-------------|
| AK201 | Obligatoire, identifie l’ID de document informatisé (ST01) du document informatisé à soumettre à accusé de réception. |
| AK202 | Obligatoire, identifie le numéro de contrôle de document informatisé (ST02 et SE02) du document informatisé à soumettre à accusé de réception. |
| AK203 | Facultatif, identifie la version d’implémentation d’EDI envoyée dans le ST03 de la transaction d’origine. AK203 prend en charge un ACK 997 entrant conforme à 5010. |
|||

#### <a name="generate-ak2-segments"></a>Générer des segments AK2

Vous pouvez spécifier que des segments AK2 soient générés pour *tous* les documents informatisés acceptés et rejetés, ou *uniquement* pour les documents informatisés rejetés. Sinon, Azure Logic Apps génère des boucles AK2 *uniquement* pour les documents informatisés rejetés. Si un accord n’est pas résolu pour l’échange faisant l’objet d’une réponse, les paramètres de génération de l’accusé de réception 997 sont par défaut les paramètres d’accord de secours, et les segments AK2 ne sont pas générés pour les documents informatisés acceptés.

Pour que Azure Logic Apps générer des segments AK2 pour les documents informatisés acceptés où AK501 = = A, procédez comme suit :

1. Dans la Portail Azure, ouvrez votre compte d’intégration, puis ouvrez l’artefact d’accord X12 entre vos partenaires commerciaux X12.

1. Ouvrez le volet **Paramètres de réception** et vérifiez que l’option **FA attendu** est sélectionnée. Vous pouvez alors sélectionner **Inclure boucle AK2 / IK2**.

### <a name="ak3"></a>AK3

Le segment AK3 facultatif signale des erreurs dans un segment de données et identifie l'emplacement du segment de données. Un segment AK3 est créé pour chaque segment d'un document informatisé comportant une ou plusieurs erreurs. S’il existe plusieurs segments AK3, ils sont envoyés sous la forme d’une série de boucles avec un segment par boucle. Le segment AK3 spécifie l’emplacement de chaque segment erroné et indique le type d’erreur syntaxique trouvée à cet emplacement à l’aide des éléments de données suivants :

| Élément | Description |
|---------|-------------|
| AK301 | Obligatoire, identifie le segment erroné avec l’ID de segment X12, par exemple NM1. |
| AK302 | Obligatoire, identifie le nombre de segments du segment erroné. Le segment ST a la valeur `1` et chaque segment incrémente le numéro de segment d’une unité. |
| AK303 | Obligatoire, identifie une boucle limitée, qui est une boucle placée entre un segment de début de boucle (LS) et un segment de fin de boucle (LE). AK303 contient les valeurs des segments LS et LE qui lient le segment erroné. |
| AK304 | Facultatif, spécifie le code de l’erreur dans le segment de données. Bien que AK304 soit facultatif, l’élément est obligatoire lorsqu’il existe une erreur pour le segment identifié. Pour les codes d’erreur AK304, consultez [997 ACK error codes - Data Segment Note](#997-ack-error-codes) (Codes d’erreur ACK 997 - Note de segment de données). |
|||

### <a name="ak4"></a>AK4

Le segment AK4 facultatif signale des erreurs dans un élément de données ou une structure de données composites et identifie l'emplacement de l'élément de données. Un segment AK4 est envoyé lorsque l’élément de données AK304 est `"8", "Segment has data element errors"` et peut se répéter jusqu’à 99 fois dans chaque segment AK3. Le segment AK4 spécifie l’emplacement de chaque élément de données ou structure de données composites erroné et indique le type d’erreur syntaxique trouvée à cet emplacement à l’aide des éléments de données suivants :

| Élément | Description |
|---------|-------------|
| AK401 | Obligatoire, un élément de données composites avec les champs suivants : AK41.1, AK41.2 et AK41.3 <p><p>- AK401.1 : identifie l’élément de données ou la structure de données composites erroné à l’aide de son numéro. Par exemple, si le second élément de données dans le segment comporte une erreur, AK401 est égal à `2`. <br>AK401.2 : identifie le numéro de l’élément de données des composants dans une structure de données composites qui comporte une erreur. Lorsque AK401 signale une erreur dans une structure de données non composites, AK401.2 n’a pas de valeur. <br>- AK41.3 : facultatif, ce champ correspond à la position de l’élément de données répétées. AK41.3 prend en charge un ACK 997 entrant conforme à 5010. |
| AK402 | Facultatif, identifie le numéro d’élément de données X12 simple de l’élément erroné. Par exemple, NM101 est le numéro d’élément de données X12 simple 98. |
| AK403 | Obligatoire, signale l’erreur de l’élément identifié. Pour les codes d’erreur AK403, consultez [997 ACK error codes - Data Segment Note](#997-ack-error-codes) (Codes d’erreur ACK 997 - Note d’éléments de données). |
| AK404 | Facultatif, contient une copie de l’élément de données identifié erroné. AK404 n'est pas utilisé si l'erreur indique un caractère non valide. |
|||

### <a name="ak5"></a>AK5

Le segment AK5 indique si le document informatisé identifié dans le segment AK2 est accepté ou rejeté et pourquoi. Le segment AK5 est obligatoire lorsque la boucle AK2 facultative est incluse dans l’accusé de réception. Le segment AK4 spécifie l’état du document informatisé à l’aide d’un élément de données obligatoire et fournit des codes d’erreur à l’aide d’un à cinq éléments de données facultatifs, en fonction de la syntaxe du document informatisé.

| Élément | Description |
|---------|-------------|
| AK501 | Obligatoire, spécifie si le document informatisé identifié est accepté ou rejeté. Pour les codes d’erreur AK501, consultez [997 ACK error codes - Transaction Response Trailer](#997-ack-error-codes) (Codes d’erreur ACK 997 - Code de fin de réponse de transaction). |
| AK502 - AK506 | Facultatif, indiquez la nature de l’erreur. Pour les codes d’erreur AK502, consultez [997 ACK error codes - Transaction Response Trailer](#997-ack-error-codes) (Codes d’erreur ACK 997 - Code de fin de réponse de document informatisé). |
|||

### <a name="ak9"></a>AK9

Le segment AK9 obligatoire indique si le groupe fonctionnel identifié dans le segment AK1 est accepté ou rejeté et pourquoi. Le segment AK9 spécifie l’état du document informatisé et la nature de toute erreur à l’aide de quatre éléments de données obligatoires. Le segment spécifie toutes les erreurs signalées en utilisant entre un et cinq éléments facultatifs.

| Élément | Description |
|---------|-------------|
| AK901 | Obligatoire, spécifie si le groupe fonctionnel identifié dans AK1 est accepté ou rejeté. Pour les codes d’erreur AK901, consultez [997 ACK error codes - Transaction Response Trailer](#997-ack-error-codes) (Codes d’erreur ACK 997 - Code de fin de réponse de groupe fonctionnel). |
| AK902 | Obligatoire, spécifie le nombre de documents informatisés inclus dans le code de fin du groupe fonctionnel identifié (GE01). |
| AK903 | Obligatoire, spécifie le nombre de documents informatisés reçus. |
| AK904 | Obligatoire, spécifie le nombre de documents informatisés acceptés dans le groupe fonctionnel identifié. |
| AK905 - AK909 | Facultatif, indique une à cinq erreurs relevées dans le groupe fonctionnel identifié. Pour les codes d’erreur AK905 à AK909, consultez [997 ACK error codes - Transaction Response Trailer](#997-ack-error-codes) (Codes d’erreur ACK 997 - Code de fin de réponse de groupe fonctionnel). |
|||

<a name="997-ack-error-codes"></a>

## <a name="997-ack-error-codes"></a>Codes d’erreur ACK 997

Cette section décrit les codes d’erreur utilisés dans les [Segments ACK 997](#997-ack-segments).  Chaque tableau répertorie les codes d’erreur pris en charge et non pris en charge, tels que définis par la spécification X12, pour le traitement des messages X12 dans Azure Logic Apps.

### <a name="ak304-error-codes---data-segment-note"></a>Codes d’erreur AK304 - Note de segment de données

Le tableau suivant répertorie les codes d’erreur utilisés dans l’élément de données AK304 du segment AK3 (note de segment de données) :

| Code d'erreur | Condition | Pris en charge ? |
|------------|-----------|------------|
| 1 | ID de segment non reconnu | Oui |
| 2 | Segment inattendu | Oui |
| 3 | Segment obligatoire manquant | Oui |
| 4 | Une boucle se produit après un nombre de fois maximal | Oui |
| 5 | Un segment dépasse l’utilisation maximale | Oui |
| 6 | Segment absent du document informatisé défini | Oui |
| 7 | Segment non placé dans l’ordre correct | Oui |
| 8 | Un segment comporte des erreurs d’élément de données | Yes |
| 511 | Séparateurs de fin détectés (code personnalisé) | Yes |
||||

### <a name="ak403-error-codes----data-element-note"></a>Codes d’erreur AK403 - Note d’élément de données

Le tableau suivant répertorie les codes d’erreur utilisés dans l’élément de données AK403 du segment AK4 (note d’élément de données) :

| Code d'erreur | Condition | Pris en charge ? |
|------------|-----------|------------|
| 1 | Élément de données obligatoire manquant | Oui |
| 2 | Élément de données obligatoire conditionnel manquant | Oui |
| 3 | Trop d'éléments de données | Oui |
| 4 | L'élément de données est trop court | Oui |
| 5 | L'élément de données est trop long | Oui |
| 6 | Caractère non valide dans l'élément de données | Oui |
| 7 | Valeur de code non valide | Oui |
| 8 | Date non valide | Oui |
| 9 | Heure non valide | Oui |
| 10 | Condition d’exclusion non respectée | Yes |
||||

### <a name="ak501-error-codes---transaction-set-response-trailer"></a>Codes d’erreur AK501 - Code de fin de réponse du document informatisé

Le tableau suivant répertorie les codes d’erreur utilisés dans l’élément de données AK501 du segment AK5 (code de fin de réponse du document informatisé) :

| Code d'erreur | Condition | Pris en charge ? |
|------------|-----------|------------|
| Un | Accepté | Yes |
| E | Accepté mais des erreurs ont été relevées | Yes <p><p>**Remarque** : aucun des codes d’erreur ne génère l’état `E`. |
| M | Rejeté, échec du code d'authentification de message (MAC) | No |
| P | Partiellement accepté, au moins un document informatisé a été rejeté | Yes |
| R | Rejeté | Yes |
| W | Rejeté, l'assurance a échoué les tests de validité | No |
| X | Rejeté, impossible d'analyser le contenu après le déchiffrement | No |
||||

### <a name="ak502-to-ak506-error-codes---transaction-set-response-trailer"></a>Codes d’erreur AK502 à AK506 - Code de fin de réponse du document informatisé

Le tableau suivant répertorie les codes d’erreur utilisés dans les éléments de données AK502 à AK506 du segment AK5 (code de fin de réponse du document informatisé) :

| Code d'erreur | Condition | Pris en charge ou <br>mis en corrélation avec AK501 ? |
|------------|-----------|-----------------------------------------|
| 1 | Document informatisé non pris en charge | Oui, R |
| 2 | Code de fin de document informatisé manquant | Oui, R |
| 3 | Le numéro de contrôle de document informatisé dans l'en-tête et le code de fin ne correspondent pas | Oui, R |
| 4 | Le nombre de segments inclus ne correspond pas au nombre réel | Oui, R |
| 5 | Un ou plusieurs segments erronés | Oui, R |
| 6 | Identificateur de document informatisé manquant ou non valide | Oui, R |
| 7 | Numéro de contrôle de document informatisé manquant ou non valide, il y a peut-être un numéro de transaction en double | Oui, R |
| 8 à 27 | - | No |
||||

### <a name="ak901-error-codes---functional-group-response-trailer"></a>Codes d’erreur AK901 - Code de fin de réponse du groupe fonctionnel

Le tableau suivant répertorie les codes d’erreur utilisés dans les éléments de données AK901 du segment AK9 (code de fin de réponse du groupe fonctionnel) :

| Code d'erreur | Condition | Pris en charge ou <br>mis en corrélation avec AK501 ? |
|------------|-----------|-----------------------------------------|
| Un | Accepté | Yes |
| E | Accepté, mais des erreurs ont été relevées | Yes |
| M | Rejeté, échec du code d'authentification de message (MAC) | No |
| P | Partiellement accepté, au moins un document informatisé a été rejeté | Yes |
| R | Rejeté | Yes |
| W | Rejeté, l'assurance a échoué les tests de validité | No |
| X | Rejeté, impossible d'analyser le contenu après le déchiffrement | No |
||||

### <a name="ak905-to-ak909-error-codes---functional-group-response-trailer"></a>Codes d’erreur AK905 à AK909 - Code de fin de réponse du groupe fonctionnel

Le tableau suivant répertorie les codes d’erreur utilisés dans les éléments de données AK905 à AK909 du segment AK9 (code de fin de réponse du groupe fonctionnel) :

| Code d'erreur | Condition | Pris en charge ou <br>mis en corrélation avec AK501 ? |
|------------|-----------|-----------------------------------------|
| 1 | Groupe fonctionnel non pris en charge | Non |
| 2 | Version de groupe fonctionnel non prise en charge | Non |
| 3 | Code de fin de groupe fonctionnel manquant | Oui |
| 4 | Le numéro de contrôle de groupe dans l'en-tête et le code de fin du groupe fonctionnel ne correspondent pas | Oui |
| 5 | Le nombre de documents informatisés inclus ne correspond pas au nombre réel | Oui |
| 6 | Ce numéro de contrôle de groupe ne respecte pas la syntaxe, il y a peut-être un numéro de contrôle de groupe en double | Yes |
| 7 à 26 | - | Non |
||||

## <a name="next-steps"></a>Étapes suivantes

* [Échanger des messages XS2 pour l’intégration d’entreprise B2B](logic-apps-enterprise-integration-x12.md)