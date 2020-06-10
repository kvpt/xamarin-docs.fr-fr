---
titre : « reconnaissance des émotions perçues à l’aide de la API Visage » Description : «la API Visage prend une expression faciale dans une image en tant qu’entrée, et retourne des données qui incluent des niveaux de confiance sur un ensemble d’émotions pour chaque visage dans l’image. Cet article explique comment utiliser l’API Visage pour reconnaître l’émotion, pour évaluer une Xamarin.Forms application.»
ms. Prod : xamarin ms. AssetID : 19D36A7C-E8D8-43D1-BE80-48DE6C02879A ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 05/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="perceived-emotion-recognition-using-the-face-api"></a>Reconnaissance des émotions perçues à l’aide de l’API Visage

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

La API Visage peut effectuer la détection d’émotion pour détecter colère, satentant, dégoût, peur, bonheur, neutral, tristesse et surprise, dans une expression faciale basée sur des annotations perçues par des codeurs humains. Il est important de noter, cependant, que les expressions faciales seules ne peuvent pas nécessairement représenter les États internes des personnes.

En plus de retourner un résultat émotion pour une expression faciale, le API Visage peut également retourner un cadre englobant pour les visages détectés.

La reconnaissance d’émotion peut être effectuée via une bibliothèque cliente et via une API REST. Cet article se concentre sur l’exécution de la reconnaissance des émotions via l’API REST. Pour plus d’informations sur l’API REST, consultez [API REST face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Le API Visage peut également être utilisé pour reconnaître les expressions faciales des personnes en vidéo et peut retourner un résumé de leurs émotions. Pour plus d’informations, consultez [Comment analyser des vidéos en temps réel](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

Une clé d’API doit être obtenue pour utiliser le API Visage. Vous pouvez l’obtenir à l’adresse [Try cognitive services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Pour plus d’informations sur la API Visage, consultez [API visage](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Authentification

Chaque demande faite à l’API Visage nécessite une clé API qui doit être spécifiée comme valeur de l' `Ocp-Apim-Subscription-Key` en-tête. L’exemple de code suivant montre comment ajouter la clé API à l' `Ocp-Apim-Subscription-Key` en-tête d’une requête :

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Si vous ne transmettez pas une clé d’API valide à la API Visage, une erreur de réponse 401 est générée.

## <a name="perform-emotion-recognition"></a>Effectuer la reconnaissance des émotions

La reconnaissance d’émotions est effectuée en effectuant une demande de publication contenant une image à l' `detect` API à `https://[location].api.cognitive.microsoft.com/face/v1.0` , où `[location]]` est la région que vous avez utilisée pour obtenir votre clé API. Les paramètres de requête facultatifs sont les suivants :

- `returnFaceId`: indique s’il faut retourner les faceIds des visages détectés. La valeur par défaut est `true`.
- `returnFaceLandmarks`: indique s’il faut retourner les points de repère des visages détectés. La valeur par défaut est `false`.
- `returnFaceAttributes`: indique s’il faut analyser et retourner un ou plusieurs attributs de visage spécifiés. Les attributs de visage pris en charge incluent `age` , `gender` , `headPose` , `smile` , `facialHair` , `glasses` ,,,, `emotion` `hair` `makeup` `occlusion` , `accessories` , `blur` , `exposure` et `noise` . Notez que l’analyse des attributs de face présente un coût de calcul et de temps supplémentaire.

Le contenu de l’image doit être placé dans le corps de la demande de publication sous forme d’URL ou de données binaires.

> [!NOTE]
> Les formats de fichier image pris en charge sont JPEG, PNG, GIF et BMP, et la taille de fichier autorisée est comprise entre 1 Ko et 4 Mo.

Dans l’exemple d’application, le processus de reconnaissance d’émotions est appelé en appelant la `DetectAsync` méthode :

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Cet appel de méthode spécifie le flux contenant les données d’image, que faceIds doit retourner, qui ne doivent pas être retournées, et que l’émotion de l’image doit être analysée. Il spécifie également que les résultats seront retournés sous la forme d’un tableau d' `Face` objets. À son tour, la `DetectAsync` méthode appelle l' `detect` API REST qui effectue la reconnaissance des émotions :

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Cette méthode génère un URI de demande, puis envoie la demande à l' `detect` API via la `SendRequestAsync` méthode.

> [!NOTE]
> Vous devez utiliser la même région dans vos appels de API Visage que vous avez utilisée pour obtenir vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement de la `westus` région, le point de terminaison de détection de visage sera `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` .

### <a name="send-the-request"></a>Envoyer la demande

La `SendRequestAsync` méthode effectue la demande de publication au API visage et retourne le résultat sous la forme d’un `Face` tableau :

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Si l’image est fournie via un flux, la méthode génère la requête de publication en encapsulant le flux d’image dans une `StreamContent` instance, qui fournit du contenu http basé sur un flux. Sinon, si l’image est fournie via une URL, la méthode génère la requête de publication en encapsulant l’URL dans une `StringContent` instance, qui fournit le contenu http en fonction d’une chaîne.

La demande de publication est ensuite envoyée à l' `detect` API. La réponse est lue, désérialisée et retournée à la méthode appelante.

L' `detect` API envoie le code d’état HTTP 200 (OK) dans la réponse, à condition que la demande soit valide, ce qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse. Pour obtenir la liste des réponses d’erreur possibles, consultez [API REST face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="process-the-response"></a>Traiter la réponse

La réponse de l’API est retournée au format JSON. Les données JSON suivantes affichent un message de réponse standard qui fournit les données demandées par l’exemple d’application :

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Un message de réponse réussi est constitué d’un tableau d’entrées de visage classées par taille de rectangle facial dans l’ordre décroissant, tandis qu’une réponse vide indique l’absence de faces détectées. Chaque face reconnue comprend une série d’attributs de visage facultatifs, qui sont spécifiés par l' `returnFaceAttributes` argument de la `DetectAsync` méthode.

Dans l’exemple d’application, la réponse JSON est désérialisée en un tableau d' `Face` objets. Lors de l’interprétation des résultats de la API Visage, l’émotion détectée doit être interprétée comme émotion avec le score le plus élevé, car les scores sont normalisés pour additionner à un. Par conséquent, l’exemple d’application affiche l’émotion reconnue avec le score le plus élevé pour la face la plus élevée détectée dans l’image. Vous pouvez y parvenir avec le code suivant :

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

La capture d’écran suivante montre le résultat du processus de reconnaissance des émotions dans l’exemple d’application :

![](emotion-recognition-images/emotion-recognition.png "Emotion Recognition")

## <a name="related-links"></a>Liens connexes

- [API visage](/azure/cognitive-services/face/overview/).
- [Cognitive Services todo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API REST face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
