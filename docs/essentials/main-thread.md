---
title : " Xamarin.Essentials : MainThread" Description : "la classe MainThread permet aux applications d’exécuter du code sur le thread d’exécution principal."
ms. AssetID : CD6D51E7-D933-4FE7-A7F7-392EF27812E1 Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 08/20/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

La classe **MainThread** permet aux applications d’exécuter du code sur le thread d’exécution principal, et de déterminer si un bloc de code donné est en cours d’exécution sur le thread principal.

## <a name="background"></a>Arrière-plan

La plupart des systèmes d’exploitation, notamment iOS, Android et la plateforme Windows universelle, utilisent un modèle monothread pour le code qui touche à l’interface utilisateur. Ce modèle est nécessaire pour sérialiser les événements de l’interface utilisateur, et en particulier les frappes et entrées tactiles. Ce thread est souvent appelé _thread principal_, _thread d’interface utilisateur_ ou _thread d’IU_. Son inconvénient est que tout le code qui accède aux éléments d’interface utilisateur doit s’exécuter sur le thread principal de l’application.

Les applications ont parfois besoin d’utiliser des événements qui appellent le Gestionnaire d’événements sur un thread secondaire d’exécution. (Les Xamarin.Essentials classes [`Accelerometer`](accelerometer.md) , [`Compass`](compass.md) , [`Gyroscope`](gyroscope.md) , [`Magnetometer`](magnetometer.md) et [`OrientationSensor`](orientation-sensor.md) peuvent toutes retourner des informations sur un thread secondaire lorsqu’elles sont utilisées avec des vitesses plus rapides.) Si le gestionnaire d’événements doit accéder aux éléments de l’interface utilisateur, il doit exécuter ce code sur le thread principal. La classe **MainThread** permet à l’application de se charger de cette exécution.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>Exécuter du code sur le thread principal

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour exécuter du code sur le thread principal, appelez la méthode statique `MainThread.BeginInvokeOnMainThread`. L’argument est un [`Action`](xref:System.Action) objet, qui est simplement une méthode sans argument et sans valeur de retour :

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Il est également possible de définir une méthode distincte pour le code qui doit s’exécuter sur le thread principal :

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Vous pouvez alors exécuter cette méthode sur le thread principal en y ajoutant une référence dans la méthode `BeginInvokeOnMainThread` :

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Formsa une méthode appelée[`Device.BeginInvokeOnMainThread(Action)`](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)
> qui fait la même chose que `MainThread.BeginInvokeOnMainThread(Action)`.
> Si vous pouvez utiliser l’une ou l’autre des méthodes dans une Xamarin.Forms application, déterminez si le code appelant a besoin d’une dépendance sur Xamarin.Forms . Si ce n’est pas le cas, `MainThread.BeginInvokeOnMainThread(Action)` est probablement une meilleure solution.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Déterminer si le code s’exécute sur le thread principal

La classe `MainThread` permet également à l’application déterminer si un bloc de code donné est en cours d’exécution sur le thread principal. La propriété `IsMainThread` retourne `true` si le code appelant la propriété s’exécute sur le thread principal. Un programme peut utiliser cette propriété pour exécuter un code différent pour le thread principal et un thread secondaire :

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Vous vous demandez peut-être s’il faut vérifier si le code s’exécute sur un thread secondaire avant d’appeler `BeginInvokeOnMainThread`, par exemple ainsi :

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

On peut imaginer que cette vérification améliore les performances lorsque le bloc de code est déjà en cours d’exécution sur le thread principal.

_Toutefois, elle n’est pas nécessaire._ Les implémentations de `BeginInvokeOnMainThread` sur la plateforme vérifient directement si l’appel est effectué sur le thread principal. La perte de performances est très faible si l’on appelle `BeginInvokeOnMainThread` alors que ce n’est pas vraiment nécessaire.

## <a name="additional-methods"></a>Autres méthodes

La classe `MainThread` comprend les méthodes `static` supplémentaires suivantes qui peuvent être utilisées pour interagir avec des éléments d’interface utilisateur à partir de threads d’arrière-plan :

| Méthode | Arguments | Retours | Objectif |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Appelle un `Func<T>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Appelle un `Action` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Appelle un `Func<Task<T>>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Appelle un `Func<Task>` sur le thread principal, puis attend qu’il se termine. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retourne le `SynchronizationContext` pour le thread principal. |

## <a name="api"></a>API

- [Code source de MainThread](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Documentation de l'API MainThread](xref:Xamarin.Essentials.MainThread)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Main-Thread-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
