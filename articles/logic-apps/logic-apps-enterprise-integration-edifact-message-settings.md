---
title: Paramètres des messages EDIFACT
description: Guide de référence pour les paramètres des messages EDIFACT dans les accords pour Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 09/29/2021
ms.openlocfilehash: 05d39063a6b85eae3f247e44f10118b4cf99eded
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403159"
---
# <a name="reference-for-edifact-message-settings-in-agreements-for-azure-logic-apps"></a>Référence pour les paramètres de message EDIFACT dans les accords pour Azure Logic Apps

Cette référence décrit les propriétés que vous pouvez définir dans un accord EDIFACT pour spécifier comment traiter les messages entre [partenaires commerciaux](logic-apps-enterprise-integration-partners.md). Configurez ces propriétés selon l'accord conclu avec le partenaire qui échange des messages avec vous.

<a name="EDIFACT-inbound-messages"></a>

## <a name="edifact-receive-settings"></a>Paramètres de réception EDIFACT

![Capture d’écran montrant les paramètres d’accord EDIFACT pour les messages entrants dans le portail Azure.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-receive-settings.png)

### <a name="identifiers"></a>Identificateurs

| Propriété | Description |
|----------|-------------|
| **UNB6.1 (mot de passe de référence du destinataire)** | Valeur alphanumérique de 1-14 caractères. |
| **UNB6.2 (qualificateur de référence du destinataire)** | Valeur alphanumérique de 1-2 caractères. |
|||

### <a name="acknowledgments"></a>Remerciements

| Propriété | Description |
|----------|-------------|
| **Réception des messages (CONTRL)** | Renvoie des accusés de réception techniques (CONTRL) à l'expéditeur des échanges en fonction des paramètres d'envoi associés à l'accord. |
| **Accusé de réception (CONTRL)** | Renvoie des accusés de réception fonctionnels (CONTRL) à l'expéditeur des échanges en fonction des paramètres d'envoi associés à l'accord. |
|||

<a name="receive-settings-schemas"></a>

### <a name="schemas"></a>Schémas

| Propriété | Description |
|----------|-------------|
| **UNH2.1 (Type)** | Le type du document informatisé. |
| **UNH2.2 (Version)** | Numéro de version de message de 1-3 caractères. |
| **UNH2.3 (Version finale)** | Numéro de version finale de message de 1-3 caractères. |
| **UNH2.5 (Code affecté associé)** | Le code assigné est alphanumérique et composé de 1-6 caractères. |
| **UNG2.1 (ID de l’expéditeur d’application)** |Entrez une valeur numérique comprenant entre un et 35 caractères. |
| **UNG2.2 (Qualificateur de l’expéditeur d’application)** |Entrez une valeur alphanumérique comprenant quatre caractères maximum. |
| **Schéma** | Le schéma précédemment téléchargé que vous souhaitez utiliser à partir de l’un ou l’autre type de ressource : <p>- Consommation : Un compte d'intégration lié à votre application logique. <br>- Standard : Votre ressource d'application logique |
|||

### <a name="control-numbers"></a>Numéros de contrôle

| Propriété | Description |
|----------|-------------|
| **Interdire les doublons de numéros de contrôle d’échange** | Bloquer les échanges en double. L’action de décodage EDIFACT vérifie que le numéro de contrôle d’échange (UNB5) pour l’échange reçu ne correspond pas à un numéro de contrôle déjà traité. Si une correspondance est détectée, l’échange n’est pas traité. |
| **Check for duplicate UNB5 every (days) (Vérifier les doublons UNB5 tous les (jours))** | Si vous avez choisi d'interdire la duplication des numéros de contrôle d'échange, vous pouvez spécifier le nombre de jours entre les exécutions du contrôle. |
| **Interdire les numéros de contrôle de groupe en double** | Bloquez les échanges avec des numéros de contrôle de groupe en double (UNG5). |
| **Interdire les numéros de contrôle de document informatisé en double** | Bloquez les échanges avec des numéros de contrôle de document informatisé en double (UNH1). |
| **Numéro de contrôle d’accusé de réception EDIFACT** | Désignez les numéros de référence de document informatisé à utiliser dans un accusé de réception en entrant une valeur de préfixe, une plage de numéros de référence et un suffixe. |
|||

### <a name="validation"></a>Validation

Une fois que vous avez fini de configurer une ligne de validation, la ligne suivante s’affiche automatiquement. Si vous ne spécifiez aucune règle, la validation utilise la ligne étiquetée **Default**.

| Propriété | Description |
|----------|-------------|
| **Type de message** | Le type de message EDI. |
| **Validation EDI** | Effectuez une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| **Validation étendue** | Si le type de données n’est pas EDI, la validation s’effectue sur l’exigence des éléments de données et la répétition autorisée, les énumérations et la validation de la longueur des élément de données (min et max). |
| **Autoriser les zéros de début ou de fin** | Conservez les zéros de début ou de fin ainsi que les espaces supplémentaires. Ne supprimez pas ces caractères. |
| **Découper les zéros de début ou de fin** | Supprimez les zéros de début ou de fin ainsi que les caractères d’espace. |
| **Stratégie de séparateur de fin** | Générez des séparateurs de fin. <p> - **Non autorisé** : pour interdire les délimiteurs et les séparateurs dans l’échange reçu. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide. <p>- **Facultatif** : Acceptez les échanges avec ou sans délimiteurs et séparateurs de fin. <p>- **Obligatoire** : lorsque l’échange reçu doit contenir des délimiteurs et des séparateurs. |
|||

### <a name="internal-settings"></a>Paramètres internes

| Propriété | Description |
|----------|-------------|
| **Créer des balises XML vides si les séparateurs de fin sont autorisés** | Incluez des balises XML vides pour les séparateurs de fin de l’expéditeur de l’échange. |
| **Fractionner l’échange en documents informatisés - suspendre les documents informatisés en cas d’erreur** | Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée au document informatisé. Suspendez uniquement les documents informatisés dont la validation échoue. |
| **Fractionner l’échange en documents informatisés - suspendre l’échange en cas d’erreur** | Analyse chaque document informatisé d’un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Suspendez l’intégralité de l’échange lorsque la validation d’un ou plusieurs documents informatisés de l’échange échoue. |
| **Préserver l’échange - suspendre les documents informatisés en cas d’erreur** | Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot. Suspendez uniquement les documents informatisés dont la validation échoue, tout en continuant à traiter tous les autres documents informatisés. |
| **Préserver l’échange - suspendre l’échange en cas d’erreur** | Préserve l’échange : crée un document XML pour l’intégralité de l’échange par lot. Suspendez l’intégralité de l’échange lorsque la validation d’un ou plusieurs documents informatisés de l’échange échoue. |
|||

<a name="EDIFACT-outbound-messages"></a>

## <a name="edifact-send-settings"></a>Paramètres d'envoi EDIFACT

![Capture d’écran montrant les paramètres d’accord EDIFACT pour les messages sortants dans le portail Azure.](./media/logic-apps-enterprise-integration-edifact-message-settings/edifact-send-settings.png)

### <a name="identifiers"></a>Identificateurs

| Propriété | Description |
|----------|-------------|
| **UNB1.2 (version de la syntaxe)** | Valeur de 1 à 4. |
| **UNB2.3 (adresse de routage inverse de l’expéditeur)** | Valeur alphanumérique de 1-14 caractères. |
| **UNB3.3 (adresse de routage inverse du destinataire)** | Valeur alphanumérique de 1-14 caractères. |
| **UNB6.1 (mot de passe de référence du destinataire)** | Valeur alphanumérique de 1-14 caractères. |
| **UNB6.2 (qualificateur de référence du destinataire)** | Valeur alphanumérique de 1-2 caractères. |
| **UNB7 (identifiant de référence de l’application)** | Valeur alphanumérique de 1-14 caractères. |
|||

### <a name="acknowledgment"></a>Accusé de réception

| Propriété | Description |
|----------|-------------|
| **Réception des messages (CONTRL)** | Le partenaire hôte qui envoie le message demande un accusé de réception technique (CONTRL) du partenaire invité. |
| **Accusé de réception (CONTRL)** | Le partenaire hôte qui envoie le message attend un accusé de réception fonctionnel (CONTRL) du partenaire invité. |
| **Générer une boucle SG1/SG4 pour les documents informatisés acceptés** | Si vous avez choisi de demander un accusé de réception fonctionnel, ce paramètre force la génération de boucles SG1/SG4 dans les accusés fonctionnels pour les documents informatisés acceptés. |
|||

### <a name="schemas"></a>Schémas

| Propriété | Description |
|----------|-------------|
| **UNH2.1 (Type)** | Le type du document informatisé. |
| **UNH2.2 (Version)** | Le numéro de la version d'origine du message. |
| **UNH2.3 (Version finale)** | Numéro de la version finale du message. |
| **Schéma** | Le schéma précédemment téléchargé que vous souhaitez utiliser à partir de l’un ou l’autre type de ressource : <p>- Consommation : Un compte d'intégration lié à votre application logique. <br>- Standard : Votre ressource d'application logique |
|||

### <a name="envelopes"></a>Enveloppes

Une fois que vous avez fini de configurer une ligne d’enveloppe, la ligne suivante s’affiche automatiquement. Si vous ne spécifiez aucune règle, la validation utilise la ligne étiquetée **Default**.

| Propriété | Description |
|----------|-------------|
| **UNB8 (code de priorité de traitement)** | Une valeur alphabétique ne contenant pas plus d’un caractère. |
| **UNB10 (contrat de communication)** | Valeur alphanumérique de 1-40 caractères. |
| **UNB11 (indicateur de test)** | Indique que l’échange généré est un test de données. |
| **Appliquer le segment UNA (conseil de service de chaîne)** | Génère un segment UNA pour l'échange à envoyer. |
| **Appliquer des segments UNG (En-tête de groupe fonctionnel)** | Crée des segments de regroupement dans l’en-tête de groupe fonctionnel dans les messages envoyés au partenaire invité. Les valeurs suivantes sont utilisées pour créer les segments UNG : <p>- **Schéma** : le schéma précédemment téléchargé que vous souhaitez utiliser à partir de l’un ou l’autre type de ressource : <p>- Consommation : Un compte d'intégration lié à votre application logique. <p>- Standard : Votre ressource d'application logique <p>- **UNG1** : valeur alphanumérique de 1-6 caractères. <p>- **UNG 2.1** : valeur alphanumérique de 1-35 caractères. <p>- **UNG 2.2** : valeur alphanumérique de 1-4 caractères. <p>- **UNG 3.1** : valeur alphanumérique de 1-35 caractères. <p>- **UNG 3.2** : valeur alphanumérique de 1-4 caractères. <p>- **UNG6** : valeur alphanumérique de 1-3 caractères. <p>- **UNG 7.1** : valeur alphanumérique de 1-3 caractères. <p>- **UNG 7.2** : valeur alphanumérique de 1-3 caractères. <p>- **UNG 7.3** : valeur alphanumérique de 1-6 caractères. <p>- **UNG8** : valeur alphanumérique de 1-14 caractères. |
|||

### <a name="character-sets-and-separators"></a>Jeux de caractères et séparateurs

Outre le jeu de caractères, vous pouvez spécifier un jeu de délimiteurs différent à utiliser pour chaque type de message. Si aucun jeu de caractères n’est spécifié pour un schéma de message donné, le jeu de caractères **par défaut** est utilisé.

| Propriété | Description |
|----------|-------------|
| **UNB1.1 (identificateur système)** | Jeu de caractères EDIFACT à appliquer à l’échange sortant. |
| **Schéma** | Le schéma précédemment téléchargé que vous souhaitez utiliser à partir de l’un ou l’autre type de ressource : <p>- Consommation : Un compte d'intégration lié à votre application logique. <p>- Standard : Votre ressource d'application logique <p>Pour le schéma sélectionné, sélectionnez le jeu de séparateurs à utiliser, selon les descriptions de séparateurs suivantes. Une fois que vous avez fini de configurer une ligne de schéma, la ligne suivante s’affiche automatiquement. |
| **Type d’entrée** | Le type d'entrée pour le message. |
| **Séparateur de composants** | Caractère unique à utiliser pour séparer les éléments de données composites. |
| **Séparateur d'éléments de données** | Un caractère unique à utiliser pour séparer les éléments de données simples au sein des éléments de données composites. |
| **UNA3 (Notation décimale)** | Notation à utiliser dans l’échange sortant. |
| **UNA4 (Indicateur de version)** | Une valeur pour l'indicateur de version qui indique que le caractère suivant n'est pas un séparateur de syntaxe, un terminateur ou un caractère d'échappement, mais qu'il fait partie des données d'origine. |
| **UNA5 (Séparateur de répétition)** | Une valeur pour le séparateur de répétition utilisé pour séparer les segments qui se répètent dans un document informatisé. |
| **Terminateur de segment** | Un caractère unique qui indique la fin d’un segment EDI. |
| **Suffixe** | Le caractère utilisé avec l’identificateur de segment. Si vous indiquez un suffixe, l'élément de données Terminateur de segment peut être vide. Si le terminateur de segment est vide, vous devez désigner un suffixe. |
|||

### <a name="control-numbers"></a>Numéros de contrôle

| Propriété | Description |
|----------|-------------|
| **UNB5 (Numéro de contrôle de l’échange)** | Un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer un échange sortant. Le numéro de contrôle est obligatoire, mais le préfixe et le suffixe sont facultatifs. Le numéro de contrôle est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes. |
| **UNG5 (Numéro de contrôle de groupe)** | Un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer le numéro de contrôle du groupe. Le numéro de contrôle est obligatoire, mais le préfixe et le suffixe sont facultatifs. Le numéro de contrôle est incrémenté pour chaque nouveau message jusqu’à ce que la valeur maximale soit atteinte ; le préfixe et le suffixe restent les mêmes. |
| **UNH1 (Numéro de référence de l’en-tête de message)** | Un préfixe, une plage de valeurs pour le numéro de contrôle de l’échange, ainsi qu’un suffixe. Ces valeurs sont utilisées pour générer le numéro de référence de l’en-tête de message. Le numéro de référence est obligatoire, mais le préfixe et le suffixe sont facultatifs. Le préfixe et le suffixe sont facultatifs, tandis que le numéro de référence est obligatoire. Le numéro de référence est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes. |
|||

### <a name="validation"></a>Validation

Une fois que vous avez fini de configurer une ligne de validation, la ligne suivante s’affiche automatiquement. Si vous ne spécifiez aucune règle, la validation utilise la ligne étiquetée **Default**.

| Propriété | Description |
|----------|-------------|
| **Type de message** | Le type de message EDI. |
| **Validation EDI** | Effectuez une validation EDI sur les types de données selon les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vides et les séparateurs de fin. |
| **Validation étendue** | Si le type de données n’est pas EDI, la validation s’effectue sur la spécification d’élément de données et la répétition autorisée, les énumérations et la validation de la longueur d’élément de données (min/max). |
| **Autoriser les zéros de début ou de fin** | Conservez les zéros de début ou de fin ainsi que les espaces supplémentaires. Ne supprimez pas ces caractères. |
| **Découper les zéros de début ou de fin** | Supprimez les zéros de début ou de fin. |
| **Stratégie de séparateur de fin** | Générez des séparateurs de fin. <p>- **Non autorisé** : pour interdire les délimiteurs et les séparateurs dans l’échange envoyé. Si l’échange contient des délimiteurs et des séparateurs, il est déclaré non valide. <p>- **Facultatif** : Envoyez des échanges avec ou sans délimiteurs et séparateurs de fin. <p>-  **Obligatoire** : si l’échange envoyé doit contenir des délimiteurs et des séparateurs. |
|||

## <a name="next-steps"></a>Étapes suivantes

[Échanger des messages EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)