---
title: Xamarin.FormsClasse d’appareil
description: Cet article explique comment utiliser la Xamarin.Forms classe d’appareil pour contrôler précisément les fonctionnalités et les dispositions sur la base de chaque plateforme.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ba4e93b8f364d6887439b05017a9cd373dce5985
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572323"
---
# <a name="xamarinforms-device-class"></a>Xamarin.FormsClasse d’appareil

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La [`Device`](xref:Xamarin.Forms.Device) classe contient un certain nombre de propriétés et de méthodes pour aider les développeurs à personnaliser la disposition et les fonctionnalités en fonction de chaque plateforme.

En plus des méthodes et des propriétés pour cibler le code à des tailles et des types de matériel spécifiques, la `Device` classe comprend des méthodes qui peuvent être utilisées pour interagir avec des contrôles d’interface utilisateur à partir de threads d’arrière-plan. Pour plus d’informations, consultez [interagir avec l’interface utilisateur à partir de threads d’arrière-plan](#interact-with-the-ui-from-background-threads).

## <a name="provide-platform-specific-values"></a>Fournir des valeurs spécifiques à la plateforme

Avant le Xamarin.Forms , la plateforme sur laquelle l’application était exécutée pouvait être obtenue en examinant la [`Device.OS`](xref:Xamarin.Forms.Device.OS) propriété et en la comparant aux [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS) valeurs d' [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone) énumération,, et [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) . De même, l’une des [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges peut être utilisée pour fournir des valeurs spécifiques à la plateforme à un contrôle.

Toutefois, depuis Xamarin.Forms 2.3.4, ces API sont dépréciées et remplacées. La [`Device`](xref:Xamarin.Forms.Device) classe contient maintenant des constantes de chaîne publiques qui identifient les plateformes – [`Device.iOS`](xref:Xamarin.Forms.Device.iOS) , [`Device.Android`](xref:Xamarin.Forms.Device.Android) , `Device.WinPhone` (déconseillées), `Device.WinRT` (déconseillées), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP) et [`Device.macOS`](xref:Xamarin.Forms.Device.macOS) . De même, les [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges ont été remplacées par [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) les [`On`](xref:Xamarin.Forms.On) API et.

En C#, les valeurs spécifiques à la plateforme peuvent être fournies en créant une `switch` instruction sur la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propriété, puis en fournissant `case` des instructions pour les plateformes requises :

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

Les [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes et fournissent les mêmes fonctionnalités en XAML :

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique qui doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible. Dans la [`On`](xref:Xamarin.Forms.On) classe, l' [`Platform`](xref:Xamarin.Forms.On.Platform) attribut peut accepter une `string` valeur unique ou plusieurs valeurs délimitées par des virgules `string` .

> [!IMPORTANT]
> La fourniture d’une valeur d’attribut incorrecte `Platform` dans la `On` classe ne génère pas d’erreur. Au lieu de cela, le code s’exécute sans que la valeur propre à la plateforme soit appliquée.

L' `OnPlatform` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="deviceidiom"></a>Appareil. idiome

La `Device.Idiom` propriété peut être utilisée pour modifier les dispositions ou les fonctionnalités en fonction de l’appareil sur lequel l’application s’exécute. L' [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) énumération contient les valeurs suivantes :

- **Téléphone** : iPhone, iPod touch et appareils Android plus étroits que 600 DIP ^
- **Tablette** -iPad, appareils Windows et appareils Android plus larges que 600 DIP ^
- **Ordinateur de bureau** : retournés uniquement dans les [applications UWP](~/xamarin-forms/platform/windows/installation/index.md) sur les ordinateurs de bureau Windows 10 (retours `Phone` sur les appareils mobiles Windows, y compris dans les scénarios continus)
- **TV** -appareils TV Tizen
- **Regarder** : appareils Tizen Watch
- **Non pris en charge** – inutilisé

*^ les DIP ne sont pas nécessairement le nombre de pixels physiques*

La `Idiom` propriété est particulièrement utile pour créer des dispositions qui tirent parti des écrans plus grands, comme suit :

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe fournit les mêmes fonctionnalités en XAML :

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

La [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique qui doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible.

L' `OnIdiom` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Pour plus d’informations, consultez [OnIdiom Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom-markup-extension).

## <a name="deviceflowdirection"></a>Device. FlowDirection

La [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur récupère une [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valeur d’énumération qui représente le sens du déroulement actuel utilisé par l’appareil. La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Ces valeurs sont les suivantes :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, la [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur peut être récupérée à l’aide de l' `x:Static` extension de balisage :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Le code équivalent en C# est le suivant :

```csharp
this.FlowDirection = Device.FlowDirection;
```

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. styles

La [ `Styles` propriété](~/xamarin-forms/user-interface/styles/index.md) contient des définitions de style intégrées qui peuvent être appliquées à une propriété de certains contrôles (telle que `Label` ) `Style` . Les styles disponibles sont les suivants :

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Appareil. GetNamedSize

`GetNamedSize`peut être utilisé lors de la définition [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) dans du code C# :

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Appareil. GetNamedColor

Xamarin.Forms4,6 introduit la prise en charge des couleurs nommées. Une couleur nommée est une couleur qui a une valeur différente selon le mode système (par exemple, clair ou sombre) qui est actif sur l’appareil. Sur Android, les couleurs nommées sont accessibles via la classe [R. Color](https://developer.android.com/reference/android/R.color#constants_2) . Sur iOS, les couleurs nommées sont appelées [couleurs système](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors). Sur la plateforme Windows universelle, les couleurs nommées sont appelées [ressources de thème XAML](/windows/uwp/design/controls-and-patterns/xaml-theme-resources).

La `GetNamedColor` méthode peut être utilisée pour récupérer des couleurs nommées sur Android, iOS et UWP. La méthode prend un `string` argument et retourne [`Color`](xref:Xamarin.Forms.Color) :

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`est retourné lorsqu’un nom de couleur est introuvable ou lorsque `GetNamedColor` est appelé sur une plateforme non prise en charge.

> [!NOTE]
> Étant donné que la `GetNamedColor` méthode retourne un `Color` qui est spécifique à une plateforme, elle doit généralement être utilisée conjointement avec la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propriété.

La `NamedPlatformColor` classe contient les constantes qui définissent les couleurs nommées pour Android, iOS et UWP :

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Appareil. StartTimer

La `Device` classe a également une `StartTimer` méthode qui fournit un moyen simple de déclencher des tâches dépendantes du temps qui fonctionnent dans du Xamarin.Forms code commun, y compris une bibliothèque de .NET standard. Transmettez un `TimeSpan` pour définir l’intervalle et revenez `true` pour que la minuterie continue à s’exécuter ou `false` pour l’arrêter après l’appel en cours.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si le code à l’intérieur du minuteur interagit avec l’interface utilisateur (telle que la définition du texte d’un ou de l’affichage d’une `Label` alerte), il doit être effectué à l’intérieur d’une `BeginInvokeOnMainThread` expression (voir ci-dessous).

> [!NOTE]
> Les `System.Timers.Timer` `System.Threading.Timer` classes et sont .NET standard alternatives à l’utilisation de la `Device.StartTimer` méthode.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir avec l’interface utilisateur à partir de threads d’arrière-plan

La plupart des systèmes d’exploitation, y compris iOS, Android et le plateforme Windows universelle, utilisent un modèle à thread unique pour le code impliquant l’interface utilisateur. Ce thread est souvent appelé thread *principal* ou thread d' *interface utilisateur*. Une conséquence de ce modèle est que tout le code qui accède aux éléments de l’interface utilisateur doit s’exécuter sur le thread principal de l’application.

Les applications utilisent parfois des threads d’arrière-plan pour exécuter des opérations potentiellement longues, telles que la récupération de données à partir d’un service Web. Si le code qui s’exécute sur un thread d’arrière-plan doit accéder aux éléments de l’interface utilisateur, il doit exécuter ce code sur le thread principal.

La `Device` classe comprend les `static` méthodes suivantes qui peuvent être utilisées pour interagir avec des éléments d’interface utilisateur à partir de threads d’arrière-plan :

| Méthode | Arguments | Retours | Objectif |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Appelle un `Action` sur le thread principal et n’attend pas qu’il se termine. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Appelle un `Func<T>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Appelle un `Action` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Appelle un `Func<Task<T>>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Appelle un `Func<Task>` sur le thread principal, puis attend qu’il se termine. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retourne le `SynchronizationContext` pour le thread principal. |

Le code suivant illustre un exemple d’utilisation de la `BeginInvokeOnMainThread` méthode :

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Liens connexes

- [Exemple d’appareil](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Styles, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [API de l'appareil](xref:Xamarin.Forms.Device)
