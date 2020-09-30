---
ms.openlocfilehash: 5fe6bab32a70c8af03bdca2151312d44fbf3834e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91451434"
---
Les demandes REST sont établies via HTTP en utilisant les mêmes verbes HTTP que ceux utilisés par les navigateurs web pour récupérer des pages et envoyer des données aux serveurs. Dans cet exercice, vous allez créer une classe qui utilise le verbe GET pour récupérer des données à partir de l’API web [OpenWeatherMap](https://openweathermap.org/). Cette API web peut être utilisée pour récupérer des données de prévisions météorologiques pour un emplacement spécifié. L’utilisation de cette API web suppose de vous inscrire au préalable pour obtenir une clé API.

> [!div class="nextstepaction"]
> [S’inscrire pour obtenir une clé API](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **l’Explorateur de solutions**, dans le projet **WebServiceTutorial**, ajoutez une classe nommée `Constants` au projet. Puis, dans **Constants.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Ce code définit deux constantes. La constante `OpenWeatherMapEndpoint` définit le point de terminaison sur lequel seront effectuées les demandes, et la constante `OpenWeatherMapAPIKey` définit votre clé API personnelle pour le service [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Vous devez définir votre clé API OpenWeatherMap personnelle comme valeur de la constante `OpenWeatherMapAPIKey`.

1. Dans **l’Explorateur de solutions**, dans le projet **WebServicesTutorial**, ajoutez une classe nommée `WeatherData` au projet. Puis, dans **WeatherData.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Ce code définit quatre classes qui seront utilisées pour modéliser les données JSON extraites du service web. Chaque propriété est ornée d’un attribut `JsonProperty`, qui contient un nom de champ JSON. Newtonsoft.Json utilisera ce mappage des noms de champs JSON aux propriétés CLR lors de la désérialisation des données JSON en objets de modèle.

    > [!NOTE]
    > Les définitions de classe ci-dessus ont été simplifiées et ne modélisent pas entièrement les données JSON extraites du service web. Pour obtenir un exemple de modèle de données complet, consultez l’exemple [Weather App](/samples/xamarin/xamarin-forms-samples/weather/).

1. Dans **l’Explorateur de solutions**, dans le projet **WebServiceTutorial**, ajoutez une classe nommée `RestService` au projet. Puis, dans **RestService.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Ce code définit une méthode unique, `GetWeatherDataAsync`, qui récupère les données météorologiques pour un emplacement spécifié à partir de l’API web [OpenWeatherMap](https://openweathermap.org/). Cette méthode utilise la méthode `HttpClient.GetAsync` pour envoyer une requête GET à l’API web spécifiée par l’argument `uri`. L’API web envoie une réponse qui est stockée dans un objet `HttpResponseMessage`. La réponse inclut un code d’état HTTP, qui indique si la requête HTTP a réussi ou échoué. Si la requête aboutit, l’API web répond avec le code d’état HTTP 200 (OK) et une réponse JSON, qui se trouve dans la propriété `HttpResponseMessage.Content`. Ces données JSON sont lues sur un `string` à l’aide de la méthode `HttpContent.ReadAsStringAsync`, avant d’être désérialisées dans un objet `WeatherData` à l’aide de la méthode `JsonConvert.DeserializeObject`. Cette méthode utilise les mappages entre les noms de champ JSON et les propriétés CLR, qui sont définies dans la classe `WeatherData`, pour effectuer la désérialisation.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, dans le projet **WebServiceTutorial**, ajoutez une classe nommée `Constants` au projet. Puis, dans **Constants.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Ce code définit deux constantes. La constante `OpenWeatherMapEndpoint` définit le point de terminaison sur lequel seront effectuées les demandes, et la constante `OpenWeatherMapAPIKey` définit votre clé API personnelle pour le service [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Vous devez définir votre clé API OpenWeatherMap personnelle comme valeur de la constante `OpenWeatherMapAPIKey`.

1. Dans **Panneau Solutions**, dans le projet **WebServicesTutorial**, ajoutez une classe nommée `WeatherData` au projet. Puis, dans **WeatherData.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Ce code définit quatre classes qui seront utilisées pour modéliser les données JSON extraites du service web. Chaque propriété est ornée d’un attribut `JsonProperty`, qui contient un nom de champ JSON. Newtonsoft.Json utilisera ce mappage des noms de champs JSON aux propriétés CLR lors de la désérialisation des données JSON en objets de modèle.

    > [!NOTE]
    > Les définitions de classe ci-dessus ont été simplifiées et ne modélisent pas entièrement les données JSON extraites du service web. Pour obtenir un exemple de modèle de données complet, consultez l’exemple [Weather App](/samples/xamarin/xamarin-forms-samples/weather/).

1. Dans **Panneau Solutions**, dans le projet **WebServiceTutorial**, ajoutez une classe nommée `RestService` au projet. Puis, dans **RestService.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Ce code définit une méthode unique, `GetWeatherDataAsync`, qui récupère les données météorologiques pour un emplacement spécifié à partir de l’API web [OpenWeatherMap](https://openweathermap.org/). Cette méthode utilise la méthode `HttpClient.GetAsync` pour envoyer une requête GET à l’API web spécifiée par l’argument `uri`. L’API web envoie une réponse qui est stockée dans un objet `HttpResponseMessage`. La réponse inclut un code d’état HTTP, qui indique si la requête HTTP a réussi ou échoué. Si la requête aboutit, l’API web répond avec le code d’état HTTP 200 (OK) et une réponse JSON, qui se trouve dans la propriété `HttpResponseMessage.Content`. Ces données JSON sont lues sur un `string` à l’aide de la méthode `HttpContent.ReadAsStringAsync`, avant d’être désérialisées dans un objet `WeatherData` à l’aide de la méthode `JsonConvert.DeserializeObject`. Cette méthode utilise les mappages entre les noms de champ JSON et les propriétés CLR, qui sont définies dans la classe `WeatherData`, pour effectuer la désérialisation.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.