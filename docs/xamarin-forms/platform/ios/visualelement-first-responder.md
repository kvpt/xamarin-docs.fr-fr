---
titre : « VisualElement First répondeur sur iOS » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités qui ne sont disponibles que sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet à un objet VisualElement de devenir le premier répondeur aux événements tactiles.
ms. Prod : xamarin ms. AssetID : 3A77BA02-B87A-44EC-AC51-9D3130EF314C ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/15/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-first-responder-on-ios"></a>VisualElement premier répondeur sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet à un [`VisualElement`](xref:Xamarin.Forms.VisualElement) objet de devenir le premier répondeur aux événements tactiles, plutôt qu’à la page contenant l’élément. Il est consommé en XAML en affectant `VisualElement.CanBecomeFirstResponder` à la propriété pouvant être liée la valeur `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

La `VisualElement.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `VisualElement.SetCanBecomeFirstResponder` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour définir le `VisualElement` pour qu’il devienne le premier répondeur pour les événements tactiles. En outre, la `VisualElement.CanBecomeFirstResponder` méthode peut être utilisée pour retourner si le `VisualElement` est le premier répondeur aux événements tactiles.

Le résultat est qu’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) peut devenir le premier répondeur pour les événements tactiles, plutôt que la page contenant l’élément. Cela permet des scénarios tels que les applications de conversation qui n’ignorent pas un clavier quand un [`Button`](xref:Xamarin.Forms.Button) est frappé.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
