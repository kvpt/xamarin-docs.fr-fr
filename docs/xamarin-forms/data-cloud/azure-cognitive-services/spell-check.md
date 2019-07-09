---
title: Vérification orthographique à l’aide de l’API vérification orthographique Bing
description: Vérification orthographique Bing effectue une correction orthographique contextuelle pour le texte, en fournissant des suggestions inline pour les mots mal orthographiés. Cet article explique comment utiliser la vérification orthographique REST Bing pour corriger les fautes d’orthographe dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 9a54743ed7dc3ce23c3306589c0bae1e0fd3206c
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658676"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Vérification orthographique à l’aide de l’API vérification orthographique Bing

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Vérification orthographique Bing effectue une correction orthographique contextuelle pour le texte, en fournissant des suggestions inline pour les mots mal orthographiés. Cet article explique comment utiliser la vérification orthographique REST Bing pour corriger les fautes d’orthographe dans une application Xamarin.Forms._

## <a name="overview"></a>Vue d'ensemble

La vérification orthographique REST Bing a deux modes de fonctionnement, et un mode doit être spécifié lorsque vous élaborez une demande à l’API :

- `Spell` corrige un texte court (mots jusqu'à 9) sans aucune modification de la casse.
- `Proof` corrige le texte long, fournit des corrections de la casse et ponctuation et supprime des corrections agressives.

Une clé API doit être obtenue pour utiliser la vérification orthographique Bing. Cela peut être obtenue au [essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)

Pour obtenir la liste des langues prises en charge par l’API vérification orthographique Bing, consultez [langues prises en charge](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Pour plus d’informations sur la vérification orthographique Bing, consultez [Documentation de vérification orthographique Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Authentification

Chaque demande adressée à l’API vérification orthographique Bing nécessite une clé d’API qui doit être spécifiée comme valeur de la `Ocp-Apim-Subscription-Key` en-tête. L’exemple de code suivant montre comment ajouter la clé API pour le `Ocp-Apim-Subscription-Key` en-tête d’une demande :

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Transmettez une clé API valide pour la vérification orthographique Bing entraîne une erreur de 401 réponse.

## <a name="performing-spell-checking"></a>Effectuer la vérification orthographique

Vérification orthographique peut être obtenue en effectuant une demande GET ou POST pour la `SpellCheck` à l’API `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Lorsque vous élaborez une demande GET, le texte à vérifier l’orthographe est envoyé comme paramètre de requête. Lorsque vous élaborez une demande POST, le texte à vérifier l’orthographe est envoyé dans le corps de la demande. Les requêtes GET sont limités à 1 500 caractères en raison de la limite de longueur de chaîne de paramètre requête de correction orthographique. Par conséquent, les requêtes POST généralement doivent être effectuées, sauf si les chaînes courtes sont en cours de vérifier l’orthographe.

Dans l’exemple d’application, le `SpellCheckTextAsync` méthode appelle le processus de correction orthographique :

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

Le `SpellCheckTextAsync` méthode génère un URI de demande et envoie ensuite la demande à la `SpellCheck` API, qui renvoie une réponse JSON qui contient le résultat. La réponse JSON est désérialisée, le résultat ne soit retourné à la méthode d’appel pour l’affichage.

### <a name="configuring-spell-checking"></a>Configuration de la vérification orthographique

Le processus de correction orthographique peut être configuré en spécifiant les paramètres de requête HTTP :

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Cette méthode définit le texte à vérifier l’orthographe et le mode de vérification orthographique.

Pour plus d’informations sur la vérification orthographique REST Bing, consultez [référence v7 de l’API vérification orthographique](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Envoi de la demande

Le `SendRequestAsync` méthode rend la requête GET pour la vérification orthographique REST Bing et renvoie la réponse :

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Cette méthode envoie la demande GET à le `SpellCheck` API, avec l’URL de requête spécifiant le texte à traduire et le mode de vérification orthographique. La réponse est ensuite lire et retournée à la méthode appelante.

Le `SpellCheck` API enverra un code d’état HTTP 200 (OK) dans la réponse, à condition que la demande est valide, ce qui indique que la demande a réussi et que les informations demandées figurent dans la réponse. Pour obtenir la liste des objets de réponse, consultez [les objets de réponse](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Traitement de la réponse

La réponse de l’API est renvoyée au format JSON. Les données JSON suivantes affiche le message de réponse pour le mot mal orthographié `Go shappin tommorow`:

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

Le `flaggedTokens` tableau contient un tableau de mots dans le texte qui ont été marqués comme ne pas correctement orthographiés ou sont grammaticalement incorrect. Le tableau sera vide si aucune fautes de grammaire et ne se trouvent. Les balises dans le tableau sont :

- `offset` : décalage de base zéro à partir du début de la chaîne de texte pour le mot qui a été signalé.
- `token` – le mot dans la chaîne de texte qui n’est pas correctement orthographié ou est grammaticalement incorrect.
- `type` – le type de l’erreur qui a provoqué le mot marquage. Il existe deux valeurs possibles : `RepeatedToken` et `UnknownToken`.
- `suggestions` – un tableau de mots qui corrige l’erreur d’orthographe ou de grammaire. Le tableau se compose d’un `suggestion` et un `score`, qui indique le niveau de confiance que la correction suggérée est correcte.

Dans l’exemple d’application, la réponse JSON est désérialisée dans un `SpellCheckResult` instance, avec le résultat ne soit retourné à la méthode d’appel pour l’affichage. Le code suivant montre l’exemple comment la `SpellCheckResult` traitement pour l’affichage de l’instance :

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Ce code effectue une itération dans le `FlaggedTokens` collection et remplace tout mot mal orthographié ou mots grammaticalement incorrects dans le texte source avec la première suggestion. Les captures d’écran suivantes montrent l’avant et après la vérification orthographique :

![](spell-check-images/before-spell-check.png "Avant la vérification orthographique")

![](spell-check-images/after-spell-check.png "Après vérification orthographique")

> [!NOTE]
> L’exemple ci-dessus utilise `Replace` par souci de simplicité, mais sur une grande quantité de texte, il pourrait remplacer le jeton incorrect. L’API fournit le `offset` valeur qui doit être utilisée dans les applications de production pour identifier l’emplacement approprié dans le texte source pour effectuer une mise à jour.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser la vérification orthographique REST Bing pour corriger les fautes d’orthographe dans une application Xamarin.Forms. Vérification orthographique Bing effectue une correction orthographique contextuelle pour le texte, en fournissant des suggestions inline pour les mots mal orthographiés.

## <a name="related-links"></a>Liens associés

- [Documentation de vérification orthographique Bing](/azure/cognitive-services/bing-spell-check/)
- [Utiliser un Service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [TODO Cognitive Services (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Référence de vérification orthographique Bing v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
