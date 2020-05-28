---
title: ''
description: Vérification orthographique Bing effectue une vérification orthographique contextuelle du texte, en fournissant des suggestions incorporées pour les mots mal orthographiés. Cet article explique comment utiliser l’API REST Vérification orthographique Bing pour corriger les fautes d’orthographe dans une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1703f0049408381a86da73fb28696ef8708cc790
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139293"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Vérification de l’orthographe à l’aide de l’API Vérification orthographique Bing

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Vérification orthographique Bing effectue une vérification orthographique contextuelle du texte, en fournissant des suggestions incorporées pour les mots mal orthographiés. Cet article explique comment utiliser l’API REST Vérification orthographique Bing pour corriger les fautes d’orthographe dans une Xamarin.Forms application._

## <a name="overview"></a>Vue d'ensemble

L’API REST Vérification orthographique Bing a deux modes de fonctionnement, et un mode doit être spécifié lors de l’exécution d’une requête auprès de l’API :

- `Spell`corrige le texte abrégé (jusqu’à 9 mots) sans modification de la casse.
- `Proof`corrige le texte long, fournit des corrections de casse et des signes de ponctuation de base, et supprime les corrections agressives.

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

Une clé d’API doit être obtenue pour utiliser le API Vérification orthographique Bing. Vous pouvez l’obtenir à l’adresse [Try cognitive services](https://azure.microsoft.com/try/cognitive-services/)

Pour obtenir la liste des langues prises en charge par le API Vérification orthographique Bing, consultez [langues prises en charge](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Pour plus d’informations sur la API Vérification orthographique Bing, consultez [vérification orthographique Bing documentation](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Authentification

Chaque demande faite à l’API Vérification orthographique Bing nécessite une clé API qui doit être spécifiée comme valeur de l' `Ocp-Apim-Subscription-Key` en-tête. L’exemple de code suivant montre comment ajouter la clé API à l' `Ocp-Apim-Subscription-Key` en-tête d’une requête :

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Si vous ne transmettez pas une clé d’API valide à la API Vérification orthographique Bing, une erreur de réponse 401 est générée.

## <a name="performing-spell-checking"></a>Exécution de la vérification orthographique

La vérification orthographique peut être effectuée en effectuant une requête d’extraction ou de publication sur l' `SpellCheck` API à l’adresse `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` . Lors de l’exécution d’une demande d’extraction, le texte à vérifier orthographique est envoyé sous la forme d’un paramètre de requête. Lorsque vous effectuez une demande de publication, le texte à vérifier orthographique est envoyé dans le corps de la demande. Les demandes d’extraction sont limitées à la vérification orthographique de 1500 caractères en raison de la limite de longueur de chaîne du paramètre de requête. Par conséquent, les demandes de publication doivent généralement être effectuées, sauf si l’orthographe des chaînes courtes est vérifiée.

Dans l’exemple d’application, la `SpellCheckTextAsync` méthode appelle le processus de vérification orthographique :

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

La `SpellCheckTextAsync` méthode génère un URI de demande, puis envoie la demande à l' `SpellCheck` API, qui retourne une réponse JSON contenant le résultat. La réponse JSON est désérialisée, le résultat étant retourné à la méthode d’appel pour l’affichage.

### <a name="configuring-spell-checking"></a>Configuration de la vérification orthographique

Le processus de vérification orthographique peut être configuré en spécifiant des paramètres de requête HTTP :

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Cette méthode définit le texte comme étant vérifié par l’orthographe et le mode de vérification orthographique.

Pour plus d’informations sur l’API REST Vérification orthographique Bing, consultez API Vérification orthographique informations de [référence v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Envoi de la demande

La `SendRequestAsync` méthode effectue la demande d’extraction à l’API REST vérification orthographique Bing et retourne la réponse :

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Cette méthode envoie la requête d’extraction à l' `SpellCheck` API, avec l’URL de demande qui spécifie le texte à traduire et le mode de vérification orthographique. La réponse est ensuite lue et retournée à la méthode appelante.

L' `SpellCheck` API envoie le code d’état HTTP 200 (OK) dans la réponse, à condition que la demande soit valide, ce qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse. Pour obtenir la liste des objets de réponse, consultez la page [objets réponse](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Traitement de la réponse

La réponse de l’API est retournée au format JSON. Les données JSON suivantes affichent le message de réponse pour le texte mal orthographié `Go shappin tommorow` :

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

Le `flaggedTokens` tableau contient un tableau de mots dans le texte qui ont été marqués comme n’étant pas correctement orthographiés ou qui sont incorrects par programmation. Le tableau est vide si aucune erreur d’orthographe ou de grammaire n’est détectée. Les balises dans le tableau sont les suivantes :

- `offset`: décalage de base zéro entre le début de la chaîne de texte et le mot qui a été marqué.
- `token`: mot de la chaîne de texte qui n’est pas orthographié correctement ou qui est incorrect.
- `type`: type de l’erreur qui a provoqué l’indicateur du mot. Il existe deux valeurs possibles : `RepeatedToken` et `UnknownToken` .
- `suggestions`: tableau de mots qui corrigeront l’erreur d’orthographe ou de grammaire. Le tableau est constitué d’un `suggestion` et d’un `score` , qui indique le niveau de confiance que la correction suggérée est correcte.

Dans l’exemple d’application, la réponse JSON est désérialisée dans une `SpellCheckResult` instance, et le résultat est retourné à la méthode d’appel pour l’affichage. L’exemple de code suivant montre comment l' `SpellCheckResult` instance est traitée pour l’affichage :

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Ce code itère au sein de la `FlaggedTokens` collection et remplace tous les mots mal orthographiés ou incorrects dans le texte source par la première suggestion. Les captures d’écran suivantes montrent avant et après la vérification orthographique :

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> L’exemple ci-dessus utilise `Replace` pour des raisons de simplicité, mais dans une grande quantité de texte, il peut remplacer le mauvais jeton. L’API fournit la `offset` valeur qui doit être utilisée dans les applications de production pour identifier l’emplacement correct dans le texte source pour effectuer une mise à jour.

## <a name="summary"></a>Résumé

Cet article explique comment utiliser l’API REST Vérification orthographique Bing pour corriger les fautes d’orthographe dans une Xamarin.Forms application. Vérification orthographique Bing effectue une vérification orthographique contextuelle du texte, en fournissant des suggestions incorporées pour les mots mal orthographiés.

## <a name="related-links"></a>Liens connexes

- [Documentation Vérification orthographique Bing](/azure/cognitive-services/bing-spell-check/)
- [Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Cognitive Services todo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Informations de référence sur API Vérification orthographique Bing v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
